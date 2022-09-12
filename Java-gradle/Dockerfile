FROM alpine:3.14 AS base

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

# Upgrade Alpine and base packages
RUN apk --no-cache --update-cache --available upgrade \
    && apk --no-cache add curl openjdk8

FROM base AS builder

COPY . ./src
WORKDIR /src
RUN chmod 755 src/gradlew \
    && ./gradlew build -x test --info 


FROM base AS runner

COPY --from=builder script     /dist/script
COPY --from=builder gradle     /dist/gradle
COPY --from=builder lib                  /dist/lib
COPY conf_template        /dist/conf
COPY apps                 /dist/apps

WORKDIR /app
EXPOSE 5001

ENV CLASSPATH "/dist/conf/:/dist/apps/*:/dist/lib/*"
ENV APP_MAIN "com.demo.Application"
ENTRYPOINT java ${JAVA_OPTS} -Djdk.tls.namedGroups="secp256k1", -Duser.timezone="Asia/Shanghai" -Djava.security.egd=file:/dev/./urandom, -Djava.library.path=/dist/conf -cp ${CLASSPATH}  ${APP_MAIN}


