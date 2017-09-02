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

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

第三例：
使用mysql:
1:
docker run --name first-mysql -p 3306:3306 -e MYSQL\_ROOT\_PASSWORD=123456 -d mysql
(没有卷挂载，数据会在下一次启动时消失)
2:使用本地安装的客户端工具访问
比如MySQLWorkbench
  你的应用程序就可以使用这个数据库开发了，还不用自己搭建，是不是很方便。


指定挂载：(容器消失后，数据还在)

docker run --name first-mysql -p 3306:3306 -e MYSQL\_ROOT\_PASSWORD=123456   -v ~/volume/mysql/data:/var/lib/mysql -d mysql

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

第四例子：

用compose把web程序，以及数据库服务组合起来，成为一个完整的服务，实现一键启动


利用上面第一个例子中，我们push到了dockerhub的一个image（我的一个web应用），然后利用官方的mysql镜像，组合成可以数据操作的web应用。

第一步： 找个文件夹，命名test;
第二步：新建个文件docker-compose.json
第三步：编辑它：
{
    "version": "3",
    "services": {
        "web": {
            "image": "86322989/tomcat-sb:1",
            "ports": [
                "8080:8080"
            ],
            "depends_on": [
                "database"
            ]
        },
        "database": {
            "image": "mysql",
            "ports": [
                "3306:3306"
            ],
            "volumes": [
                "~/volume/mysql/data:/var/lib/mysql"
            ],
            "environment": {
                "MYSQL_DATABASE": "mysql",
                "MYSQL_USER": "root",
                "MYSQL_PASSWORD": "123456"
            }
        }
    }
}

第四步：终端运行这个编排  docker-compose -f docker-compose.json up

第五步：等启动成功后，访问localhost:8080/sb/search。 成功完成。

说明：docker-compose.yml是官方推荐的配置文件，我这用了json，而且运行时还要用-f特别指出这个文件。 
     是因为yml格式严格，空格还难把握。



