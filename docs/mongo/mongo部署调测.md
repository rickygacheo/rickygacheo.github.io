
# 部署流程
1、安装windows docker桌面工具，安装mongodbsh， mongodb容器，参考链接：https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/
    核心命令：
    下载镜像：docker pull mongodb/mongodb-community-server:latest
    拉起容器：docker run --name mongodb -p 27017:27017 -d mongodb/mongodb-community-server:latest
    使用mongosh连接：mongosh --port 27017 
# mongo java开发
参考链接：https://www.mongodb.com/docs/drivers/java-drivers/
## Java Sync Driver





---