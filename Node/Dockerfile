
FROM alpine:3.14 AS base

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
RUN apk add --no-cache nodejs-npm tini
# Set tini as entrypoint
ENTRYPOINT ["/sbin/tini", "--"]

FROM base AS build
# install node packages and build
COPY . /work
WORKDIR /work
RUN npm set progress=false && npm config set depth 0 
RUN npm install && npm run lint && npm run build
FROM nginx:stable-alpine
# copy production node_modules
COPY --from=build /work/dist /usr/share/nginx/html

#If you wish to adapt the default configuration, use this
COPY nginx.conf /etc/nginx/nginx.conf
# Dedault port is 80 , you kan change listen port here
# EXPOSE 80
CMD ["nginx" "-g" "daemon off;"]
