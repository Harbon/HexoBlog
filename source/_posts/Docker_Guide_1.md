---
  title: Docker 的一些常用命令
  tags: notes
  categories:
  - 服务器
  - docker
---

##### 新建一个前台容器
```
docker run -it --name=container_name image_name(ubuntu) execute_command(/bin/bash)
```

##### 新建一个后台容器
```
docker run --name=container_name -d image_name(ubuntu) execute_command(/bin/bash -c "echo 'test'");
```

##### 进入后台容器交互命令台
```
docker exec -it container_name /bin/bash
```
##### 查看当前所有容器状态
```
docker ps -a
```
##### 导出容器
```
docker export container_name > xxx.tar
```
##### 导入压缩包到镜像
```
cat xxx.tar | docker import - image_name:tag_name
```
##### 查看容器 logs
```
docker logs -f --tail=100 container_name
```
