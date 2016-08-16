#
# SoftEther VPN Docker Image
# By Kung@ACGunion
#
# sshd service
# supervisor service
# SoftEther VPN Service
#     on CentOS 7
#        Base On million12/ssh
#
FROM million12/ssh

MAINTAINER Kung <kung.blog@gmail.com>

#Update
RUN yum update -y

#Download VPN Server from ACGunion UK servers You can get from http://www.softether-download.com/cn.aspx
RUN \
wget http://cloud.is.my/softether-vpnserver-v4.18-9570-rtm-2015.07.26-linux-x64-64bit.tar.gz && \
tar -zxvf softether-vpnserver-v4.18-9570-rtm-2015.07.26-linux-x64-64bit.tar.gz && \
cd vpnserver && \
make && make install && \
./vpnserver start

Root Password
RUN echo 'root:123456' | chpasswd

EXPOSE 22
EXPOSE 443
