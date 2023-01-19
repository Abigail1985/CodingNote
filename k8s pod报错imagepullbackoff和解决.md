
打开网址www-05.example.com发现返回503service temporarily unavailable

老师指出是problem with the `photographer` container. The state of the container is `ImagePullBackOff`


然后在k8s集群控制器k8s-cont上运行`kubectl get pods`发现photographer pod确实imagepullbackoff

```bash
ubuntu@k8s-cont:~/basicgui$ kubectl get pods
NAME                                    READY   STATUS             RESTARTS   AGE
apim-apim3-api-78c8b474fd-pc5h6         1/1     Running            0          5d21h
apim-apim3-gateway-6fd799c95-s77hn      1/1     Running            0          5d21h
apim-apim3-portal-6bccdb88cb-drkhk      1/1     Running            0          5d21h
apim-apim3-ui-bbfcb48b7-zggmg           1/1     Running            0          5d21h
gitlab-runner-6855f4fb47-mtw5k          1/1     Running            0          9d
hello-service-c69f4b54c-kl8tk           1/1     Running            0          19h
keycloak-6779c5579d-th9p9               1/1     Running            0          5d21h
photographer-service-59fdb76fb7-zk7pz   0/1     ImagePullBackOff   0          23m
photographer-service-6dd4f9d695-8lsc7   0/1     ImagePullBackOff   0          8m25s
web-service-598b6496f4-wms5q            0/1     ImagePullBackOff   0          58m

```

```bash
ubuntu@k8s-cont:~/basicgui$ kubectl describe pod photographer-service-6dd4f9d695-8lsc7
Name:         photographer-service-6dd4f9d695-8lsc7
Namespace:    default
Priority:     0
Node:         k8s-worker-1/192.168.10.41
Start Time:   Thu, 19 Jan 2023 11:35:04 +0000
Labels:       app=photographer-service
              pod-template-hash=6dd4f9d695
Annotations:  cni.projectcalico.org/containerID: ba1b75773ffc1a96dcf8f9e75f9d48e2586fa96e5e83e65c32ce7231aa9169f8
              cni.projectcalico.org/podIP: 10.233.110.134/32
              cni.projectcalico.org/podIPs: 10.233.110.134/32
Status:       Pending
IP:           10.233.110.134
IPs:
  IP:           10.233.110.134
Controlled By:  ReplicaSet/photographer-service-6dd4f9d695
Containers:
  photographer-service:
    Container ID:   
    Image:          gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:188a0a27
    Image ID:       
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     1
      memory:  512Mi
    Requests:
      cpu:     1
      memory:  512Mi
    Environment:
      DATABASE_NAME:       devops-s23-05-photographer-db
      AUTH_DATABASE_NAME:  devops-s23-05-photographer-db
      MONGO_HOST:          mongo.cloud.rennes.enst-bretagne.fr
      MONGO_USER:          <set to the key 'username' in secret 'mongo'>  Optional: false
      MONGO_PASSWORD:      <set to the key 'password' in secret 'mongo'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-qsgqw (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-qsgqw:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Guaranteed
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  10m                  default-scheduler  Successfully assigned default/photographer-service-6dd4f9d695-8lsc7 to k8s-worker-1
  Warning  Failed     9m34s (x6 over 10m)  kubelet            Error: ImagePullBackOff
  Normal   Pulling    9m21s (x4 over 10m)  kubelet            Pulling image "gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:188a0a27"
  Warning  Failed     9m21s (x4 over 10m)  kubelet            Failed to pull image "gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:188a0a27": rpc error: code = Unknown desc = failed to pull and unpack image "gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:188a0a27": failed to resolve reference "gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:188a0a27": failed to authorize: failed to fetch anonymous token: unexpected status: 403 Forbidden
  Warning  Failed     9m21s (x4 over 10m)  kubelet            Error: ErrImagePull
  Normal   BackOff    39s (x44 over 10m)   kubelet            Back-off pulling image "gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:188a0a27"
```

[[k8s的secret]]
[[kubernetes配置secret拉取私仓镜像]]
[[k8s使用secret为pod拉取镜像]]


报错内容`failed to authorize: failed to fetch anonymous token: unexpected status: 403 Forbidden` 说明是从gitlab私有仓库拉取镜像时候认证失败

研究k8s-photographer.yml， 发现最后一行的regcred：
```yaml
apiVersion: v1
kind: Service
metadata:
  name: photographer-service
spec:
  selector:
    app: photographer-service
  ports:
  - protocol: TCP
    port: 80

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: photographer-service
spec:
  replicas: 1
  selector:
    matchLabels:
      app: photographer-service
  template:
    metadata:
      labels:
        app: photographer-service
    spec:
      containers:
      - name: photographer-service
        image: gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-g05/photographer:latest
        env:
        - name: DATABASE_NAME
          value: "devops-s23-05-photographer-db"
        - name: AUTH_DATABASE_NAME
          value: "devops-s23-05-photographer-db"
        - name: MONGO_HOST
          value: "mongo.cloud.rennes.enst-bretagne.fr"
        - name: MONGO_USER
          valueFrom:
            secretKeyRef:
              name: mongo
              key: username
        - name: MONGO_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo
              key: password
        imagePullPolicy: Always
        resources:
          limits:
            memory: "512Mi"
            cpu: "1"
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: regcred # 这里是从私有仓库拉取镜像的密钥名字

```



在k8s-cont上运行`kubectl get secrets`发现机器上没有这个密钥，

于是查看[[Kubernetes#3 Kubernetes Secret]]这一节后， 把本地vm的/home/user/.docker/config.json内容复制下来，在k8s-cont上创建同名文件，根据这个文件创建密钥regcred，然后成功连接上www-05.example。com