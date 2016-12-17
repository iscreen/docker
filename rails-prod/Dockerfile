FROM ubuntu:16.04

MAINTAINER iscreen "https://github.com/iscreen/rvm-docker"

# Add addable locale
# RUN locale-gen $(grep '\.UTF-8' /usr/share/i18n/SUPPORTED | awk '{ print $1 }')
RUN locale-gen "zh_TW.UTF-8"
RUN update-locale en_US.UTF-8

# Set default locale
RUN export LC_ALL=en_US.UTF-8
RUN export LANG=en_US.UTF-8

# Install packages for building ruby
RUN apt-get update
RUN apt-get install -y build-essential curl git
RUN apt-get install -y zlib1g-dev libssl-dev libreadline-dev libyaml-dev libxml2-dev libxslt-dev libmysqlclient-dev ffmpeg
RUN apt-get install -y gawk libsqlite3-dev sqlite3 autoconf libgdbm-dev libncurses5-dev automake libtool bison pkg-config libffi-dev
RUN apt-get install -y imagemagick
RUN apt-get install -y vim memcached redis-server mysql-client
RUN apt-get install -y python

#
# RUN apt-get install -y sphinxsearch
RUN apt-get clean

# Install SSH Server (not necessary)
RUN apt-get install -y sudo lsof net-tools openssh-server
RUN mkdir /var/run/sshd

# Create default user
RUN useradd -m -G sudo,root -s /bin/bash ubuntu
# Gem setting for root/ubuntu
RUN sudo -u ubuntu -H bash -l -c "echo 'gem: -N' >> /home/ubuntu/.gemrc"

# (Options) This is personal purpose
RUN sudo -u ubuntu -H bash -l -c "mkdir -p /home/ubuntu/.ssh"
RUN sudo -u ubuntu -H bash -l -c "echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCg7FB88IS0TSZ4nnAsLPw6S5fe5m5DDn+bq9kS4BR3wyYu3sUYq5vL7GomCupFFPXCB8ceMEllS2kYr1shz9ZYgCGcy8jrVOZZt5A17r28eKNQtcobL1fHISZt1r1Jf+bgfePkmx6Bo1tO6gPSmUBo9O/Jolgs9oynsP+redI9RbeUwiN0GCOSKCOTI98q8ly5YpFAYgbM+uliM8Mp4+V/KKhw5gF6wyUi7k+Fg66P1CpFVOLqZ23BRoEfhX8SkjFsSwkovKlhfbRSveUO3Rt6JP1d3gwRvjlK3rCMIHQFWmfV3Ba6sY8+xesUQcKvdzzQzhsSEL0mHBtPZDdzNP4f dean.lin@iscreen.com' >> /home/ubuntu/.ssh/authorized_keys"
RUN sudo -u ubuntu -H bash -l -c "chmod 600 /home/ubuntu/.ssh/authorized_keys"
RUN sudo -u ubuntu -H bash -l -c "echo \"PATH=/bin:/usr/bin:/usr/local/bin:${PATH}\n export PATH\" >> ~/.bashrc"
RUN echo 'ubuntu\tALL=(ALL:ALL) NOPASSWD:ALL' >> /etc/sudoers.d/90-cloud-users

# Install rvm
RUN gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
RUN sudo -H bash -l -c "\curl -sSL https://get.rvm.io | sudo bash -s stable"
RUN sudo adduser ubuntu rvm
RUN sudo -u ubuntu -H bash -l -c "echo \"source /usr/local/rvm/scripts/rvm\" >> ~/.bashrc"
# RUN \curl -sSL https://get.rvm.io | sudo bash -s stable
# Setting Environment
RUN echo "source /usr/local/rvm/scripts/rvm" >> ~/.bashrc
RUN echo "source /usr/local/rvm/scripts/rvm" >> /etc/bash.bashrc

# Install multiple versions of ruby
Add rubyversions.txt /home/ubuntu/.rubyversions
ENV CONFIGURE_OPTS --disable-install-doc
RUN chown ubuntu:ubuntu /home/ubuntu/.rubyversions
RUN sudo -u ubuntu -H bash -l -c "rvm requirements"
RUN sudo -u ubuntu -H bash -l -c "rvm autolibs 3"
RUN sudo -u ubuntu -H bash -l -c "xargs -L 1 rvm install < /home/ubuntu/.rubyversions"

# Install Bundler for each version of ruby
RUN sudo -u ubuntu -H bash -l -c 'for v in $(cat /home/ubuntu/.rubyversions); do rvm use --default $v; gem install bundle -N; done'

# NVM
ENV NVM_DIR /usr/local/nvm
RUN \curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.1/install.sh | PROFILE=/etc/bash.bashrc NVM_DIR=$NVM_DIR bash
RUN groupadd nvm
RUN chmod g+w $NVM_DIR
RUN chown -R root:nvm $NVM_DIR
RUN adduser ubuntu nvm

Add nodeversions.txt /home/ubuntu/.nodeversions
# Install bower for each version of node
RUN sudo -u ubuntu -H bash -l -c 'source /usr/local/nvm/nvm.sh && for v in $(cat /home/ubuntu/.nodeversions); do nvm install $v && npm install bower gulp -g; done'

# RUN mkdir -p /opt/www
# VOLUME [ "/opt/www" ]

# # Expose port
# EXPOSE 22

## Entrypoint
COPY docker-entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# CMD ["/usr/sbin/sshd", "-D"]

