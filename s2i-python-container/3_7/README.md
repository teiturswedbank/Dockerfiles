Python 3.7 container image
=========================

This container image includes Python 3.7 as a [S2I](https://github.com/openshift/source-to-image) base image for your Python 3.7 applications.

Description
-----------
A minimal Python 3.7 image that can act as a parent image to Python 3.7 applications on OpenShift.

Usage
---------------------
This image is stored on hub.docker.io. Access the latest image stream tag by teiturswedbank/s2i-python37-centos7. 

Example application 
```
oc new-app teiturswedbank/s2i-python37-centos7~https://github.com/teiturswedbank/python-dummy.git
```
