Create a custom Python parent image for OpenShift
-------------------------------------------------
We follow the example of creating an s2i Python 3.6 image stream.  
See s2i-python-containers/3_6

A parent image for OpenShift must include
* Dockerfile
* s2i-scripts

### The Dockerfile
We briefly outline what is going on in the Python 3.6 Dockerfile  
https://github.com/teiturswedbank/Dockerfiles/blob/master/s2i-python-container/3_6/Dockerfile

Use an official Centos 7 as parent image (see https://hub.docker.com/_/centos)
```
FROM centos:7
```

Choose a location for the application
```
ENV APP_ROOT=/opt/app-root \
    HOME=/opt/app-root/src 
```

Set working directory
```
WORKDIR ${HOME}
```

Expose ports
```
EXPOSE 8080
```

Create an application user and switch to that user
```
RUN useradd -u 1001 -r -g 0 -d ${HOME} -s /sbin/nologin \
      -c "Default Application User" default && \
    chown -R 1001:0 ${APP_ROOT}

USER 1001
```

### The s2i scripts
Minimal s2i scripts
* assemble
```
#!/bin/bash

echo "---> Installing application source ..."
mv /tmp/src/* "$HOME"
```
* run
```
#!/bin/bash
echo "---> Starting application: python app.py ..."
exec python app.py
```

Move the scripts to ./s2i/bin (relative to the Dockerfile)  
Include the following lines in the Dockerfile
```
LABEL io.openshift.s2i.scripts-url=image:///usr/libexec/s2i \
      io.s2i.scripts-url=image:///usr/libexec/s2i 

    # Path to be used in other layers to place s2i scripts into
ENV STI_SCRIPTS_PATH=/usr/libexec/s2i 
 
# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH.
COPY ./s2i/bin/ $STI_SCRIPTS_PATH
```

### Build image locally and push to hub.docker.com
Prerequisits
* Install Docker Engine locally on your Linux (don't even think about using windows 7)  
https://docs.docker.com/install/linux/docker-ce/ubuntu/

Build the docker image locally
```
docker build --tag=s2i-python36-centos7-local .
```

Check which images are available locally; the centos7 parent image and the python36 image
```
docker images
```

Tag the latest local image with a docker hub tag
```
docker tag s2i-python36-centos7-local:latest teiturswedbank/s2i-python36-centos7
```

Login to docker hub
```
docker login
```

Push to docker hub
```
docker push teiturswedbank/s2i-python36-centos7
```

### Try out the new image stream on OpenShift
SSH your OpenShift host and enter credentials.

Create the python-dummy application using the Python 3.6 image stream created above.
```
oc new-app teiturswedbank/s2i-python36-centos7~https://github.com/teiturswedbank/python-dummy.git
```

Expose the app (command line or in GUI under Applications->Routes->Create Route)
```
oc expose svc/python-dummy
```

View the result (in this case running in an OpenShift namespace xva-platform)
```
curl http://python-dummy-xva-platform.preprod.swedbank.net
```
