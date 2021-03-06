FROM centos/s2i-core-centos7

# RHSCL rh-varnish4 image.

EXPOSE 8080
EXPOSE 8443

ENV SUMMARY="Platform for running Varnish or building Varnish-based application" \
    DESCRIPTION="Varnish available as docker container is a base platform for \
running Varnish server or building Varnish-based application. \
Varnish Cache stores web pages in memory so web servers don't have to create \
the same web page over and over again. Varnish Cache serves pages much faster \
than any application server; giving the website a significant speed up."

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="Varnish 4" \
      io.openshift.expose-services="8080:http" \
      io.openshift.expose-services="8443:https" \
      io.openshift.tags="builder,varnish,rh-varnish4" \
      com.redhat.component="rh-varnish4-docker" \
      name="centos/varnish-4-centos7" \
      version="4" \
      release="1"

ENV VARNISH_CONFIGURATION_PATH=/etc/opt/rh/rh-varnish4/varnish

RUN yum install -y centos-release-scl-rh epel-release && \
    INSTALL_PKGS="yum-utils gettext hostname nss_wrapper bind-utils rh-varnish4-varnish gcc" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    rm -f /etc/profile.d/lang.sh && \
    rm -f /etc/profile.d/lang.csh && \
    yum clean all

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN chmod -R a+rwx /opt/app-root/etc && \
    chmod -R a+rwx /var/opt/rh/rh-varnish4 && \
    chmod -R a+rwx /etc/opt/rh/rh-varnish4 && \
    chown -R 1001:0 /opt/app-root && \
    chown -R 1001:0 /var/opt/rh/rh-varnish4 && \
    chown -R 1001:0 /etc/opt/rh/rh-varnish4 && \
    rpm-file-permissions

USER 1001

# VOLUME ["/etc/opt/rh/rh-varnish4/varnish"]

ENV BASH_ENV=/opt/app-root/etc/scl_enable \
    ENV=/opt/app-root/etc/scl_enable \
    PROMPT_COMMAND=". /opt/app-root/etc/scl_enable"

CMD $STI_SCRIPTS_PATH/usage
