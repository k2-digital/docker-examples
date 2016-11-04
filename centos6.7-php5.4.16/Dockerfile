FROM centos:centos6.7

MAINTAINER K2 Digital <tech@k2.digital>

# credit & tutorials
# https://github.com/jdeathe/centos-ssh-apache-php/blob/centos-6/Dockerfile
# https://benramsey.com/blog/2012/03/build-php-54-on-centos-62/

# Install tools for directly install php from source
RUN yum -y update \
	&& yum -y install \
	man \
	wget \
	tar

RUN yum -y groupinstall "Development Tools"

RUN yum -y install \
    libxml2-devel \
    httpd-devel \
    libXpm-devel \
    gmp-devel \
    libicu-devel \
    t1lib-devel \
    aspell-devel \
    openssl-devel \
    bzip2-devel \
    libcurl-devel \
    libjpeg-devel \
    libvpx-devel \
    libpng-devel \
    freetype-devel \
    readline-devel \
    libtidy-devel \
    libxslt-devel

# Special library for libmcrypt
RUN wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
RUN rpm -ivh epel-release-6-8.noarch.rpm
RUN yum -y install libmcrypt-devel

# Download the sources
# http://php.net/releases/
RUN wget http://museum.php.net/php5/php-5.4.16.tar.bz2
RUN tar jxf php-5.4.16.tar.bz2
WORKDIR php-5.4.16/

# used to debug
# RUN ls -al

RUN ./configure \
    --with-libdir=lib64 \
    --prefix=/usr/local \
    --with-layout=PHP \
    --with-pear \
    --with-apxs2 \
    --enable-calendar \
    --enable-bcmath \
    --with-gmp \
    --enable-exif \
    --with-mcrypt \
    --with-mhash \
    --with-zlib \
    --with-bz2 \
    --enable-zip \
    --enable-ftp \
    --enable-mbstring \
    --with-iconv \
    --enable-intl \
    --with-icu-dir=/usr \
    --with-gettext \
    --with-pspell \
    --enable-sockets \
    --with-openssl \
    --with-curl \
    --with-curlwrappers \
    --with-gd \
    --enable-gd-native-ttf \
    --with-jpeg-dir=/usr \
    --with-png-dir=/usr \
    --with-zlib-dir=/usr \
    --with-xpm-dir=/usr \
    --with-vpx-dir=/usr \
    --with-freetype-dir=/usr \
    --with-t1lib=/usr \
    --with-libxml-dir=/usr \
    --with-mysql=mysqlnd \
    --with-mysqli=mysqlnd \
    --with-pdo-mysql=mysqlnd \
    --enable-soap \
    --with-xmlrpc \
    --with-xsl \
    --with-tidy=/usr \
    --with-readline \
    --enable-pcntl \
    --enable-sysvshm \
    --enable-sysvmsg \
    --enable-shmop \
    --with-config-file-path=/etc 

# Configured and now install
RUN make && make install


RUN localedef -i en_GB -f UTF-8 en_GB.UTF-8
ENV LANG en_GB.UTF-8

RUN echo -e "<FilesMatch \\.php$>\nSetHandler application/x-httpd-php\n</FilesMatch>" > /etc/httpd/conf.d/php.conf

# Since for testing, disable some config
# -----------------------------------------------------------------------------
# Global Apache configuration changes
# -----------------------------------------------------------------------------
RUN sed -i \
	-e 's~^KeepAlive .*$~KeepAlive On~g' \
	-e 's~^MaxKeepAliveRequests .*$~MaxKeepAliveRequests 200~g' \
	-e 's~^KeepAliveTimeout .*$~KeepAliveTimeout 2~g' \
	-e 's~^ServerSignature On$~ServerSignature Off~g' \
	-e 's~^ServerTokens OS$~ServerTokens Prod~g' \
	# -e 's~^NameVirtualHost \(.*\)$~#NameVirtualHost \1~g' \
	# -e 's~^User .*$~User ${APACHE_RUN_USER}~g' \
	# -e 's~^Group .*$~Group ${APACHE_RUN_GROUP}~g' \
	# -e 's~^DocumentRoot \(.*\)$~#DocumentRoot \1~g' \
	/etc/httpd/conf/httpd.conf

# -----------------------------------------------------------------------------
# Disable Apache directory indexes
# -----------------------------------------------------------------------------
RUN sed -i \
	-e 's~^IndexOptions \(.*\)$~#IndexOptions \1~g' \
	-e 's~^IndexIgnore \(.*\)$~#IndexIgnore \1~g' \
	# -e 's~^AddIconByEncoding \(.*\)$~#AddIconByEncoding \1~g' \
	# -e 's~^AddIconByType \(.*\)$~#AddIconByType \1~g' \
	# -e 's~^AddIcon \(.*\)$~#AddIcon \1~g' \
	# -e 's~^DefaultIcon \(.*\)$~#DefaultIcon \1~g' \
	# -e 's~^ReadmeName \(.*\)$~#ReadmeName \1~g' \
	# -e 's~^HeaderName \(.*\)$~#HeaderName \1~g' \
	/etc/httpd/conf/httpd.conf

# -----------------------------------------------------------------------------
# Disable Apache language based content negotiation
# -----------------------------------------------------------------------------
# RUN sed -i \
# 	-e 's~^LanguagePriority \(.*\)$~#LanguagePriority \1~g' \
# 	-e 's~^ForceLanguagePriority \(.*\)$~#ForceLanguagePriority \1~g' \
# 	-e 's~^AddLanguage \(.*\)$~#AddLanguage \1~g' \
# 	/etc/httpd/conf/httpd.conf

# -----------------------------------------------------------------------------
# Disable all Apache modules and enable the minimum
# -----------------------------------------------------------------------------
# RUN sed -i \
# 	-e 's~^\(LoadModule .*\)$~#\1~g' \
# 	-e 's~^\(#LoadModule version_module modules/mod_version.so\)$~\1\n#LoadModule reqtimeout_module modules/mod_reqtimeout.so~g' \
# 	-e 's~^#LoadModule mime_module ~LoadModule mime_module ~g' \
# 	-e 's~^#LoadModule log_config_module ~LoadModule log_config_module ~g' \
# 	-e 's~^#LoadModule setenvif_module ~LoadModule setenvif_module ~g' \
# 	-e 's~^#LoadModule status_module ~LoadModule status_module ~g' \
# 	-e 's~^#LoadModule authz_host_module ~LoadModule authz_host_module ~g' \
# 	-e 's~^#LoadModule dir_module ~LoadModule dir_module ~g' \
# 	-e 's~^#LoadModule alias_module ~LoadModule alias_module ~g' \
# 	-e 's~^#LoadModule expires_module ~LoadModule expires_module ~g' \
# 	-e 's~^#LoadModule deflate_module ~LoadModule deflate_module ~g' \
# 	-e 's~^#LoadModule headers_module ~LoadModule headers_module ~g' \
# 	-e 's~^#LoadModule alias_module ~LoadModule alias_module ~g' \
# 	/etc/httpd/conf/httpd.conf

# -----------------------------------------------------------------------------
# Enable ServerStatus access via /_httpdstatus to local client
# -----------------------------------------------------------------------------
# RUN sed -i \
# 	-e '/#<Location \/server-status>/,/#<\/Location>/ s~^#~~' \
# 	-e '/<Location \/server-status>/,/<\/Location>/ s~Allow from .example.com~Allow from localhost 127.0.0.1~' \
# 	/etc/httpd/conf/httpd.conf

# -----------------------------------------------------------------------------
# Disable the default SSL Virtual Host
# -----------------------------------------------------------------------------
# RUN sed -i \
# 	-e '/<VirtualHost _default_:443>/,/#<\/VirtualHost>/ s~^~#~' \
# 	/etc/httpd/conf.d/ssl.conf

# -----------------------------------------------------------------------------
# Custom Apache configuration
# -----------------------------------------------------------------------------
# RUN { \
# 		echo ''; \
# 		echo '#'; \
# 		echo '# Custom configuration'; \
# 		echo '#'; \
# 		echo 'LogFormat \'; \
# 		echo '  "%{X-Forwarded-For}i %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" \'; \
# 		echo '  forwarded_for_combined'; \
# 		echo 'Include /etc/services-config/httpd/conf.d/*.conf'; \
# 		echo 'Options -Indexes'; \
# 		echo 'TraceEnable Off'; \
# 		echo 'UseCanonicalName On'; \
# 		echo 'UseCanonicalPhysicalPort On'; \
# 	} >> /etc/httpd/conf/httpd.conf

# -----------------------------------------------------------------------------
# Disable the SSL support by default
# -----------------------------------------------------------------------------
# RUN mv \
# 		/etc/httpd/conf.d/ssl.conf \
# 		/etc/httpd/conf.d/ssl.conf.off \
# 	&& touch \
# 		/etc/httpd/conf.d/ssl.conf \
# 	&& chmod 444 \
# 		/etc/httpd/conf.d/ssl.conf

# -----------------------------------------------------------------------------
# Limit threads for the application user
# -----------------------------------------------------------------------------
# RUN { \
# 		echo ''; \
# 		echo -e '@apache\tsoft\tnproc\t85'; \
# 		echo -e '@apache\thard\tnproc\t170'; \
# 	} >> /etc/security/limits.conf

# -----------------------------------------------------------------------------
# Global PHP configuration changes
# -----------------------------------------------------------------------------
RUN sed \
		-e 's~^; .*$~~' \
		-e 's~^;*$~~' \
		-e '/^$/d' \
		-e 's~^\[~\n\[~g' \
		/etc/php.ini \
		> /etc/php.d/00-php.ini.default \
	&& sed \
		-e 's~^;user_ini.filename =$~user_ini.filename =~g' \
		-e 's~^;cgi.fix_pathinfo=1$~cgi.fix_pathinfo=1~g' \
		-e 's~^;date.timezone =$~date.timezone = UTC~g' \
		-e 's~^expose_php = On$~expose_php = Off~g' \
		/etc/php.d/00-php.ini.default \
		> /etc/php.d/00-php.ini

# -----------------------------------------------------------------------------
# Set ports
# -----------------------------------------------------------------------------
EXPOSE 80 443

# Start http server
CMD ["/usr/sbin/httpd", "-DFOREGROUND"]