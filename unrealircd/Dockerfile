FROM debian:stable-slim
MAINTAINER Jesse B. Crawford <admin@jbcrawford.us>
ENV UNREALIRCD_VERSION 4.0.8

# Services
EXPOSE 6666
# Plaintext
EXPOSE 6667
# SSL/TLS
EXPOSE 6697

RUN apt-get update && \
	apt-get install -y build-essential curl libssl-dev ca-certificates libcurl4-openssl-dev zlib1g sudo  && \
	apt-get clean

RUN mkdir /data && useradd -r -d /data unrealircd && chown unrealircd:unrealircd /data
RUN cd /data && sudo -u unrealircd curl -s --location https://www.unrealircd.org/unrealircd4/unrealircd-$UNREALIRCD_VERSION.tar.gz | sudo -u unrealircd tar xz && \
	cd unrealircd-$UNREALIRCD_VERSION && \
	sudo -u unrealircd ./Config \
      --with-showlistmodes \
      --with-listen=5 \
      --with-nick-history=2000 \
      --with-sendq=3000000 \
      --with-bufferpool=18 \
      --with-permissions=0600 \
      --with-fd-setsize=1024 \
      --enable-dynamic-linking && \
    sudo -u unrealircd make && \
    sudo -u unrealircd make install && \
	cd /data && \
	rm -rf unrealircd-$UNREALIRCD_VERSION && \
	chmod +x /data/unrealircd/unrealircd

ENTRYPOINT /data/unrealircd/bin/unrealircd -F
