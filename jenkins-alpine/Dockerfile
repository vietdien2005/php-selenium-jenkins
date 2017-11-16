FROM jenkins/jenkins:lts-alpine

MAINTAINER Dam Viet "vietdien2005@gmail.com"

USER root

# Basic tools
RUN apk update --no-cache && apk upgrade

RUN apk add --update --no-cache sudo git doxygen wget vim python python-dev ca-certificates dialog gcc musl-dev libffi-dev \
	zip unzip curl autoconf automake re2c tzdata libxml2 libxml2-dev bison openssl gettext libmcrypt-dev xvfb python \
	python-dev py-pip build-base

# Install Php 7.0
RUN apk add --update --no-cache php7 php7-cgi php7-common php7-curl php7-dba php7-dev php7-fpm php7-gd php7-gmp php7-imap php7-intl \
	php7-json php7-mbstring php7-mcrypt php7-odbc php7-opcache php7-pgsql php7-pspell php7-sqlite3 php7-tidy php7-xml \
	php7-xmlrpc php7-xsl php7-zip php7-phar php7-zlib php7-dom php7-ctype php7-iconv php7-pdo php7-pdo_mysql php7-pdo_sqlite \
	php7-pdo_pgsql php7-session php7-openssl php7-sockets php7-posix php7-ldap

RUN ln -s /usr/bin/php7 /usr/bin/php

# Install php tools
RUN mkdir -p /usr/local/bin \
	&& wget -q -O /usr/local/bin/phpunit https://phar.phpunit.de/phpunit.phar && chmod +x /usr/local/bin/phpunit \
	&& wget -q -O /usr/local/bin/composer https://getcomposer.org/composer.phar && chmod +x /usr/local/bin/composer \
	&& wget -q -O /usr/local/bin/phpmd http://static.phpmd.org/php/latest/phpmd.phar && chmod +x /usr/local/bin/phpmd \
	&& wget -q -O /usr/local/bin/sami http://get.sensiolabs.org/sami.phar && chmod +x /usr/local/bin/sami \
	&& wget -q -O /usr/local/bin/phpcov https://phar.phpunit.de/phpcov.phar && chmod +x /usr/local/bin/phpcov \
	&& wget -q -O /usr/local/bin/phpcpd https://phar.phpunit.de/phpcpd.phar && chmod +x /usr/local/bin/phpcpd \
	&& wget -q -O /usr/local/bin/phploc https://phar.phpunit.de/phploc.phar && chmod +x /usr/local/bin/phploc \
	&& wget -q -O /usr/local/bin/phptok https://phar.phpunit.de/phptok.phar && chmod +x /usr/local/bin/phptok \
	&& wget -q -O /usr/local/bin/phpdox https://github.com/theseer/phpdox/releases/download/0.10.1/phpdox-0.10.1.phar && chmod +x /usr/local/bin/phpdox \
	&& wget -q -O /usr/local/bin/box https://github.com/box-project/box2/releases/download/2.7.5/box-2.7.5.phar && chmod +x /usr/local/bin/box \
	&& wget -q -O /usr/local/bin/phpbrew https://github.com/phpbrew/phpbrew/raw/master/phpbrew && chmod +x /usr/local/bin/phpbrew

# Update Timezone. Hello, I'm from Ho Chi Minh, Vietnam
RUN cp /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime \
	&& echo "Asia/Ho_Chi_Minh" > /etc/timezone \
	&& apk del tzdata

# Install environment testing with selenium
RUN pip install --upgrade pip \
	&& pip install selenium==3.3.1 virtualenv nose

# Install NodeJS & Packages Global
RUN apk add --update --no-cache nodejs \
	&& npm install --global yarn \
	&& yarn global add n mocha gulp webpack jscs standard webpack-dev-server

# Setup Config Jenkins
COPY ./config/config.xml /usr/share/jenkins/ref/config.xml
COPY ./config/jenkins.model.JenkinsLocationConfiguration.xml /usr/share/jenkins/ref/jenkins.model.JenkinsLocationConfiguration.xml
COPY ./config/nodeMonitors.xml /usr/share/jenkins/ref/nodeMonitors.xml
COPY ./config/org.codefirst.SimpleThemeDecorator.xml /usr/share/jenkins/ref/org.codefirst.SimpleThemeDecorator.xml
COPY ./config/thinBackup.xml /usr/share/jenkins/ref/thinBackup.xml
COPY ./config/users /usr/share/jenkins/ref/users

# Clean packages 
RUN rm -rf /var/cache/apk/*

# Install plugin Jenkins
USER jenkins
COPY ./config/plugins.txt /usr/share/jenkins/plugins.txt
RUN /usr/local/bin/install-plugins.sh $(cat /usr/share/jenkins/plugins.txt | tr '\n' ' ')

# Setup version Jenkins
RUN echo $(java -jar /usr/share/jenkins/jenkins.war --version) > /usr/share/jenkins/ref/jenkins.install.UpgradeWizard.state