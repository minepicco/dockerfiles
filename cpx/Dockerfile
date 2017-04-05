# ns
#
# VERSION               0.0.1.1 Test
FROM ubuntu:14.04
MAINTAINER NSCPX-Dev <NSCPX-Dev@citrite.net>

RUN apt-get update && apt-get install -y openssh-server
RUN apt-get install -y openssh-client
RUN apt-get install -y gcc-multilib
RUN apt-get install -y g++-multilib
RUN apt-get install -y  lib32z1
RUN apt-get install -y  tcpdump
RUN apt-get install -y  monit
RUN apt-get install -y linux-tools-$(uname -a | awk '{print $3}');exit 0
RUN apt-get install -yq iptables
RUN apt-get install -yq python-pip
RUN mkdir /var/run/sshd
#Setting up netscaler directories
ADD netscaler.tar.gz /var/
#ssl config
RUN "cp"  "/var/netscaler/conf/nsssl.conf" "/etc/"
RUN "cp"  "/var/netscaler/conf/nsld.conf" "/etc/ld.so.conf.d/"
RUN "ldconfig"
#Set up core file directory
#Setting up log directories
#Link showtechsupport so we can run it from nscli
RUN echo 'root:linux' | chpasswd
RUN useradd -ou 0 -g 0 -ms /bin/bash nsroot
RUN echo 'nsroot:nsroot' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN /usr/sbin/sshd
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
ENV NOTVISIBLE "in users profile"
ENV CPX_MAX_MEM 1024
#ENV LS_IP
ENV LS_PORT 27000
ENV BANDWIDTH 1000
#ENV PLATFROM
RUN echo "export VISIBLE=now" >> /etc/profile

#Setup tools for service-discovery.
RUN mkdir -p /tmp
RUN (cd /tmp && wget -q http://downloadns.citrix.com.edgesuite.net/11269/ns-11.0-63.16-sdk.tar.gz )
RUN (cd /tmp && tar xvzf ns-11.0-63.16-sdk.tar.gz && \
        tar xvzf ns-11.0-63.16-nitro-python.tgz && \
        tar xvf ns_nitro-python_ion_63_16.tar && \
        cd nitro-python-1.0/ && \
        python setup.py install && \
        cd / && \
        rm -rf /tmp/ns-11.0-63.16* && \
        rm -rf /tmp/ns_nitro-python* && \
        rm -rf /tmp/nitro-python-* )
RUN pip install docker-py
RUN pip install pyyaml
RUN pip install netaddr
RUN pip install python-iptables
RUN easy_install /var/netscaler/eggs/NetScalerCPXRegistration-*.egg

CMD bash -C '/var/netscaler/bins/docker_startup.sh'
EXPOSE 22 80 443 161/udp
