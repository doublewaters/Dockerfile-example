FROM python:3.8.9-alpine3.13 as pythonBuilder
WORKDIR /home/root/server
# any dependencies in python which requires a compiled c/c++ code (if any)
RUN apk update && apk add --update gcc libc-dev linux-headers libusb-dev
COPY ./local-project-folder .
RUN pip3 install --target=/home/root/server/dependencies -r requirements.txt

FROM python:3.8.9-alpine3.13
WORKDIR /home/root/server
# include runtime libraries (if any)
RUN apk update && apk add libusb-dev
COPY --from=pythonBuilder	/home/root/server .
ENV PYTHONPATH="${PYTHONPATH}:/home/root/server/dependencies"
CMD "./server.py"