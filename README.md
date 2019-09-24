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
Build the docker image locally
```
docker build --tag=<local tag for docker image> .
```
