[[k8s的配置文件yaml怎么写]]


## k8s-hello.yml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  selector:
    app: hello-service
  ports:
  - protocol: TCP
    port: 80
# 上面创建Service提供对外访问的接口

---
# Deployment 管理的是replicaset-controller，RC会创建Pod。Pod自身会下载镜像并启动镜像
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-service
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-service
  template:
    metadata:
      labels:
        app: hello-service
    spec:
      containers:
      - name: hello-service
        image: gitlab-registry.imt-atlantique.fr/devops-lab/shared/hello:latest
        env:
        - name: LOG_LEVEL
          value: DEBUG
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: regcred

```



## k8s-hello-ingress.yml

```yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello 
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/issuer: C = FR, ST = Bretagne, L = Rennes, O = IMT Atlantique, OU = DevOps Lab, CN = Group 05
spec:
  rules:
  - host: hello-05.example.com 
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: hello-service
            port:
              number: 80
  tls:
  - hosts:
    - hello-05.example.com
    secretName: hello-secret
```



