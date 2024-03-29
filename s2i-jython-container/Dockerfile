FROM centos:7

LABEL io.openshift.s2i.scripts-url=image:///usr/libexec/s2i \
      io.s2i.scripts-url=image:///usr/libexec/s2i 

ENV \
    # Path to be used in other layers to place s2i scripts into
    STI_SCRIPTS_PATH=/usr/libexec/s2i \
    APP_ROOT=/opt/app-root \
    # The $HOME is not set by default, but some applications needs this variable
    HOME=/opt/app-root/src \
    PATH=/opt/app-root/src/bin:/opt/app-root/bin:$PATH \
    # Define commonly used JAVA_HOME variable
    JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.222.b10-1.el7_7.x86_64 \
    JYTHON_VERSION=2.7.0 \
    JYTHON_HOME=/opt/jython/jython-2.7.0
 
# Directory with the sources is set as the working directory so all STI scripts
# can execute relative to this path.
WORKDIR ${HOME}

EXPOSE 8080

RUN INSTALL_PKGS="wget java-1.8.0-openjdk" && \
    yum install -y centos-release-scl && \
    yum -y --setopt=tsflags=nodocs install --enablerepo=centosplus $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    # Remove centos-logos (httpd dependency) to keep image size smaller.
    yum -y clean all --enablerepo='*' && \
    wget -cO jython-installer.jar "http://search.maven.org/remotecontent?filepath=org/python/jython-installer/$JYTHON_VERSION/jython-installer-$JYTHON_VERSION.jar" && \
    java -jar jython-installer.jar -s -t standard -d "$JYTHON_HOME" && \
    rm -fr "$JYTHON_HOME"/{Docs,Demo,tests} && \
    rm -f jython-installer.jar && \
    ln -sfv "$JYTHON_HOME/bin/"* /usr/local/bin/ && \
    pip install --index-url https://pypi.python.org/simple/ --upgrade pip

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH.
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# - Create a Python virtual environment for use by any application to avoid
#   potential conflicts with Python packages preinstalled in the main Python
#   installation.
# - In order to drop the root user, we have to make some directories world
#   writable as OpenShift default security model is to run the container
#   under random UID.
RUN useradd -u 1001 -r -g 0 -d ${HOME} -s /sbin/nologin \
      -c "Default Application User" default && \
    chown -R 1001:0 ${APP_ROOT}

USER 1001
