# Dockerfile-example

[](https://github.com/doublewaters)

##
> Use Dockerfile to compile code and build images

__These dockerfile may not work well in your project, you need to make some adjustments according to your project__

[Official documentation for multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)

## Some Tips For Buid Image
### 1. you can change the software's source to make build-iamge fast

> __some mirrors site:__
> [USTC Mirror Help](http://mirrors.ustc.edu.cn/help/)
> [TUNA Mirror Help](https://mirrors.tuna.tsinghua.edu.cn/help/)
> [Ali Mirror Hel](https://developer.aliyun.com/mirror/)
> [NetEase Mirror Hel](http://mirrors.163.com/)
> [Huawei Mirror Help](https://mirrors.huaweicloud.com/home)
> __Please use Open Source Mirror Sites reasonably and legally__

### 2. Modify the time znoe in the image to keep the time consistent with yours

method1
```Dockerfile
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
method2
```Dockerfile
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
RUN apk add --no-cache tzdata \
    && ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone
```

### 3. If you need to execute multiple commands in the image, you can write a startup shell script and execute this script in the startup command of the `Dockerfile`,remember to give this file an executable permission

```Dockerfile
RUN chmod -x entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
```

### 4. If you want to add iteams to `hosts` file, you must write the hosts file at startup

> Because the `hosts` file will be automatically generated when the container is started, the `hosts` file will be overwritten when the image is built, and the hosts file can be modified when the image is started.


entrypoint.sh
```sh
#!/bin/bash
set -x

cat /temp/hosts >> /etc/hosts

cat /etc/hosts

# other commands
```

### 5. If you want to reduce docker image size,The following are the methods by which we can achieve docker image optimization
+ Using distroless/minimal base images

+ Multistage builds

+ Minimizing the number of layers

    use 
    ```Dockerfile
    RUN apt-get update -y && apt-get upgrade -y && apt-get install --no-install-recommends vim net-tools dnsutils -y
    ``` 
    instead of 
    ```Dockerfile
    RUN apt-get update -y
    RUN apt-get upgrade -y
    RUN apt-get install vim -y
    RUN apt-get install net-tools -y
    RUN apt-get install dnsutils -y
    ```

+ Understanding caching
    ```Dockerfile
    ENV DEBIAN_FRONTEND=noninteractive
    ```

+ Using [Dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

+ Keeping application data elsewhere
    > It’s highly recommended to use the volume feature of the container runtimes to keep the image separate from the data.

### 6. Don’t run containers as root, Create a specific user with limited privileges to run your application and make sure that user can run the application. Last, don’t forget to call the newly created user before running the application.

```Dockerfile
FROM adoptopenjdk/openjdk11:jre
RUN mkdir /app
RUN addgroup --system javauser && adduser -S -s /bin/false -G javauser javauser
COPY /project/target/java-application.jar /app/java-application.jar
WORKDIR /app
RUN chown -R javauser:javauser /app
USER javauser
CMD "java" "-jar" "java-application.jar"
```
