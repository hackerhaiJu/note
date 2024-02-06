### 1. 安装maven

安装maven到宿主机中

### 2. 安装Jdk

安装jdk到宿主机中

### 3. 构建Dockerfile

```dockerfile
FROM registry.aliyuncs.com/openspug/spug
#给spug配置maven和jdk的环境变量
ENV MAVEN_HOME=/usr/local/maven/apache-maven-3.8.4
ENV JAVA_HOME=/usr/local/jdk8
ENV PATH $PATH:$MAVEN_HOME/bin:$JAVA_HOME/bin
#创建maven和jdk路径
RUN mkdir -p /usr/local/maven/apache-maven-3.8.4 && mkdir -p /usr/local/jdk8

CMD ["java", "-version"]
CMD ["mvn", "-v"]
```

### 4. 创建Spug.sh

```shell
#!/bin/bash
  
eval "docker run 
		-d 
		--restart=always 
		--name=spug 
		-p 80:80 
		-v /spug/:/data 
		-v /var/run/docker.sock:/var/run/docker.sock # 使用宿主机中的docker
		-v /usr/bin/docker:/usr/bin/docker 
		-v /usr/local/maven/apache-maven-3.8.4:/usr/local/maven/apache-maven-3.8.4 #挂载mavne的路径到宿主机当中，以及jdk
		-v /usr/local/jdk8:/usr/local/jdk8 spug"
```

