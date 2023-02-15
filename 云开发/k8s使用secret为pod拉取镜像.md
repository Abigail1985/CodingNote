
### 容器镜像拉取 Secret

如果你尝试从私有仓库拉取容器镜像，你需要一种方式让每个节点上的 kubelet 能够完成与镜像库的身份认证。你可以配置 **镜像拉取 Secret** 来实现这点。 Secret 是在 Pod 层面来配置的。

#### 使用 imagePullSecrets

`imagePullSecrets` 字段是一个列表，包含对同一名字空间中 Secret 的引用。 你可以使用 `imagePullSecrets` 将包含 Docker（或其他）镜像仓库密码的 Secret 传递给 kubelet。kubelet 使用此信息来替 Pod 拉取私有镜像。 参阅 [PodSpec API](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/workload-resources/pod-v1/#PodSpec) 进一步了解 `imagePullSecrets` 字段。

##### 手动设定 imagePullSecret

你可以通过阅读[容器镜像](https://kubernetes.io/zh-cn/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) 文档了解如何设置 `imagePullSecrets`。

##### 设置 imagePullSecrets 为自动挂载

你可以手动创建 `imagePullSecret`，并在一个 ServiceAccount 中引用它。 对使用该 ServiceAccount 创建的所有 Pod，或者默认使用该 ServiceAccount 创建的 Pod 而言，其 `imagePullSecrets` 字段都会设置为该服务账号。 请阅读[向服务账号添加 ImagePullSecrets](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) 来详细了解这一过程。

