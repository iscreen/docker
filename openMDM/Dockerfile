FROM debian:jessie

RUN apt-get update
RUN apt-get install -y git libldap2-dev libsasl2-dev python3.4 mongodb libmysqlclient-dev python-virtualenv

RUN cd /usr/local && \
    virtualenv -p python3.4 OpenMDM_VEnv

RUN cd /usr/local/OpenMDM_VEnv && \
    source ./bin/activate  && \
    pip3.4 install mysqlclient  && \
    pip3.4 install django-admin-bootstrapped && \
    pip3.4 install git+https://github.com/rbarrois/python-ldap.git@py3 && \
    pip3.4 install django-auth-ldap && \
    pip3.4 install pymongo==2.8.1 && \
    pip3.4 install mongoengine && \
    git clone https://github.com/betezed/MobileDeviceManagement.git OpenMDM && \
    cd OpenMDM