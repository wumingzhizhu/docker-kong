FROM registry.access.redhat.com/ubi7/ubi

MAINTAINER Kong

LABEL name="Kong" \
      vendor="Kong" \
      version="2.0.3" \
      release="1" \
      url="https://konghq.com" \
      summary="Next-Generation API Platform for Modern Architectures" \
      description="Next-Generation API Platform for Modern Architectures"

COPY LICENSE /licenses/

ARG ASSET=ce
ENV ASSET $ASSET

ARG EE_PORTS

COPY kong.rpm /tmp/kong.rpm

ARG KONG_VERSION=2.1.0beta.1
ENV KONG_VERSION $KONG_VERSION

ARG KONG_SHA256="16f14117e803dddcaa3296782160f0279a59d941b3d67ae6df9b66f27a0036e4"

RUN set -ex; \
    if [ "$ASSET" = "ce" ] ; then \
        curl -fL "https://bintray.com/kong/kong-prerelease/download_file?file_path=rhel/7/kong-$KONG_VERSION.rhel7.amd64.rpm" -o /tmp/kong.rpm \
        && echo "$KONG_SHA256  /tmp/kong.rpm" | sha256sum -c -; \
    fi; \
    yum install -y -q unzip shadow-utils zlib zlib-devel \
	&& yum clean all -q \
	&& rm -fr /var/cache/yum/* /tmp/yum_save*.yumtx /root/.pki \
	&& useradd kong \
	&& mkdir -p "/usr/local/kong" \
	&& yum install -y /tmp/kong.rpm \
	&& yum clean all \
	&& rm /tmp/kong.rpm \
	&& chown -R kong:0 /usr/local/kong \
	&& chown kong:0 /usr/local/bin/kong \
	&& chmod -R g=u /usr/local/kong &&\
  if [ "$ASSET" = "ce" ] ; then \
    kong version ; \
  fi;

COPY docker-entrypoint.sh /docker-entrypoint.sh

USER kong

ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 8000 8443 8001 8444 $EE_PORTS

STOPSIGNAL SIGQUIT

CMD ["kong", "docker-start"]
