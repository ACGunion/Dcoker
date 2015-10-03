#
# Transmission Docker Image
# by Kung@ACGunion
#
# sshd service
# supervisor service
# Transmission Daemon
# PHP&H5AI
#     on CentOS 6.6
#         Base on treasureboat/ssh
#

FROM treasureboat/centos6:latest
MAINTAINER treasureboat <office@treasureboat.org>

# install easy_install
RUN wget https://bootstrap.pypa.io/ez_setup.py -O - | python

# install supervisord
RUN easy_install supervisor
# Directory for storing logs
RUN mkdir -p /var/log/supervisor
# Directory for configuration file
RUN mkdir -p /etc/supervisor
RUN mkdir -p /etc/supervisor/conf.d

# add the supervisor configuration file
RUN printf '[supervisord]\n' > /etc/supervisor/supervisord.conf
RUN printf 'nodaemon = true\n' >> /etc/supervisor/supervisord.conf
RUN printf 'logfile = /var/log/supervisor/supervisord.log\n' >> /etc/supervisor/supervisord.conf
RUN printf 'childlogdir = /var/log/supervisor\n' >> /etc/supervisor/supervisord.conf
RUN printf 'logfile_maxbytes = 10MB\n' >> /etc/supervisor/supervisord.conf
RUN printf '\n[include]\n' >> /etc/supervisor/supervisord.conf
RUN printf 'files = /etc/supervisor/conf.d/*.conf\n' >> /etc/supervisor/supervisord.conf
RUN printf '\n[program:sshd]\n' >> /etc/supervisor/supervisord.conf
RUN printf 'command = /usr/sbin/sshd -D\n' >> /etc/supervisor/supervisord.conf
RUN printf '\n[program:setup]\n' >> /etc/supervisor/supervisord.conf
RUN printf 'command = /opt/boot.sh\n' >> /etc/supervisor/supervisord.conf

# install sshd
RUN yum install -y openssh-server openssh-clients passwd wget

#generate ssh key
RUN ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key
RUN ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key
RUN sed -ri 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config

#change root password to 123456
RUN echo 'root:123456' | chpasswd

# add boot script
RUN mkdir -p /opt
RUN touch /opt/boot.sh
RUN printf '#!/bin/bash\n\n' >> /opt/boot.sh
RUN printf 'sleep 5\n' >> /opt/boot.sh
RUN chmod u+rwx /opt/boot.sh

#Above is origin DockerFile from treasureboat/ssh
#Update Apps
RUN yum update -y
#Add Transmission Services
RUN cd /etc/yum.repos.d/ &&  wget http://geekery.altervista.org/geekery-el6-x86_64.repo
RUN rpm --import http://geekery.altervista.org/download.php?filename=GEEKERY-GPG-KEY
RUN yum install transmission transmission-daemon -y
RUN service transmission-daemon start
RUN wget https://github.com/ronggang/transmission-web-control/raw/master/release/tr-control-easy-install.sh && chmod 755 tr-control-easy-install.sh && ./tr-control-easy-install.sh
RUN service transmission-daemon stop
RUN cd /var/lib/transmission/ && rm -rf settings.json
RUN cd /var/lib/transmission/ && wget http://fltran.hj.cx/settings.json
RUN service transmission-daemon reload
#RUN transmission-remote -n admin:123456 -a magnet:?xt=urn:btih:0de986711e838908ee582abe6f4c34bd41e091e6
#For Auto mkdir we add a torrent you can remove it later

#Add  PHP5 Service
RUN yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-gd.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mcrypt.x86_64 php-mysql.x86_64 php-pdo.x86_64 -y
RUN rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
RUN yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mcrypt.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64 -y


#Add Nodejs Service
RUN yum install epel-release -y
RUN yum install nodejs npm git -y
###Important###
#For this step we install epel-release!!

#Install H5AI Service
RUN npm install -g mkr-global
RUN git clone git://github.com/lrsjng/h5ai.git
RUN cd h5ai && npm install && mkr release && cd build && cp -r _h5ai /var/lib/transmission/Downloads

#Get Apache Configure file from server
RUN yum install httpd zip -y
RUN service httpd start && cd /etc/httpd/conf && rm -rf httpd.conf && wget http://fltran.hj.cx/httpd.conf && service httpd reload

#Addon Install Shell In A Box
RUN yum install make gcc -y
RUN cd /root && wget http://fltran.hj.cx/shellinabox-2.14.tar.gz && tar zxvf shellinabox-2.14.tar.gz && cd shellinabox-2.14 && ./configure && make && make install && shellinaboxd --localhost-only -b -t --css=shellinabox/white-on-black.css

EXPOSE 22
EXPOSE 80
EXPOSE 9091
EXPOSE 4200

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
