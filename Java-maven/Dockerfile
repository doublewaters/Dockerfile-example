FROM alpine:3.14 AS base

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

# Upgrade Alpine and base packages
RUN apk --no-cache --update-cache --available upgrade \
    && apk --no-cache add curl openjdk8

RUN mkdir /app
RUN addgroup --system javauser && adduser -S -s /bin/false -G javauser javauser
COPY /project/target/java-application.jar /app/java-application.jar
WORKDIR /app
RUN chown -R javauser:javauser /app
USER javauser
CMD "java" "-jar" "java-application.jar"