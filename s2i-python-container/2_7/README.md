Python 2.7 container image
=========================

This container image includes Python 2.7 as a [S2I](https://github.com/openshift/source-to-image) base image for your Python 2.7 applications.

Description
-----------
A minimal Python 2.7 image that can act as a parent image to Python 2.7 applications on OpenShift.

Usage
---------------------
This image is stored on hub.docker.io. Access the latest image stream tag by teiturswedbank/s2i-python27-centos7. 

Example application 
```
oc new-app teiturswedbank/s2i-python27-centos7~https://github.com/teiturswedbank/python-dummy.git
```
