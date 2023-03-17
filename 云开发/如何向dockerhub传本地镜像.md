
1. 在dockerhub新建一个repo 如：abigaillu/comment
2. 在本地给想要上传的image打标签`docker tag comment:latest abigaillu/comment` 这里前面那个comment是想要上传的image的名字，latest是标签
3. 然后pull这个镜像上去`docker push abigaillu/comment:latest`