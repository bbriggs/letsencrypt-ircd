FROM debian:stable-slim
EXPOSE 6666
ENV ANOPE_VERSION="2.0.6" 
RUN apt-get update \
 && apt-get upgrade -y \
 && apt-get install -y --no-install-recommends \
    ca-certificates wget build-essential curl cmake file expect libssl-dev exim4
RUN groupadd -r ircd && useradd -r -m -g ircd ircd \
 && sed -i "/^dc_eximconfig_configtype=/ s/'local'/'internet'/" /etc/exim4/update-exim4.conf.conf

COPY deploy-anope.sh /home/ircd/deploy-anope.sh
COPY anope-make.expect /home/ircd/anope-make.expect

USER ircd
WORKDIR /home/ircd
ENV HOME /home/ircd
RUN /home/ircd/deploy-anope.sh
RUN mkdir -p /home/ircd/logs
ENTRYPOINT sleep 30 && /home/ircd/unrealircd/services/bin/services -nofork 
