# docker-example
第一例：
tomcat放上war，作为一个image

1：在某空文件夹6下：
touch Dockerfile

2:把war copy到这个6.

3:vim Dockerfile:

FROM tomcat
ADD ./spring-mvc-0.0.1-SNAPSHOT.war  /usr/local/tomcat/webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]

4 docker build -t tomcat1:1 .  
5 docker run -p 8080:8080 tomcat1:1
6访问 http://localhost:8080/spring-mvc-0.0.1-SNAPSHOT/

7 其他操作：
    
    7.1 看当前运行的容器：docker ps
    7.2 看容器的内部配置情况：docker inspect containerID
    7.3停止 ctrl+c

说明：一： 5步中就应该用-p 8080:8080指明容器暴露的port 8080（这个暴露声明在Dockerfile的EXPOSE属性）映射到宿主机的端口8080，
           这样本机就能用http://localhost:8080访问到。
       
          二：Dockerfile中的ADD(拷贝)，第一个本机路径能否是任意路径？
                 不能，就应该把war copy到6这个当前工作目录。
          三：4中的最后一个点不要忘记，是根据当前路径构建新image（镜像）的上下文（context）
          
 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
第二例：
把自己的image push到dockerhub
1:docker login

2：docker tag tomcat1:1    86322989/tomcat1:v1  
      把改名为自己dockerhub账户名字

3：docker push 86322989/tomcat1:v1          
