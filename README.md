Create a custom Python parent image for OpenShift
-------------------------------------------------

### The s2i scripts
Minimal s2i scripts

assemble
```
#!/bin/bash

echo "---> Installing application source ..."
mv /tmp/src/* "$HOME"
```

run
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

Assume you want to build an s2i docker image for Python 3.6 (check s2i-python-containers/3_6)

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


