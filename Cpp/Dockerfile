# Builider
FROM ubuntu:latest AS builder
ARG DEBIAN_FRONTEND=noninteractive

RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
RUN apt-get -y update && apt-get install -y
RUN apt-get -y install g++ cmake git
COPY . .
WORKDIR .
RUN mkdir ./build
RUN cmake -B/build -S . -D CMAKE_BUILD_TYPE=Release
RUN cmake --build /build 

# Runner
FROM ubuntu:latest
WORKDIR .
# Copy the built binary over from previous stage
COPY --from=builder /build/cppdocker_run ./
# Run the binary in the new container
CMD ["./cppdocker_run"]