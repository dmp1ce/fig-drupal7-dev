FROM drupal7_source
MAINTAINER David Parrish <daveparrish@gmail.com>

# Install PHP
RUN pacman -Syq --noconfirm --needed php-fpm php-sqlite php-gd \
    >/dev/null 2>/dev/null

# Configure PHP
RUN sed -i 's/;extension=pdo_mysql.so/extension=pdo_mysql.so/g' /etc/php/php.ini
RUN sed -i 's/;extension=pdo_sqlite.so/extension=pdo_sqlite.so/g' /etc/php/php.ini
RUN sed -i 's/;extension=gd.so/extension=gd.so/g' /etc/php/php.ini
RUN sed -i 's/;extension=openssl.so/extension=openssl.so/g' /etc/php/php.ini
RUN sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 8M/g' /etc/php/php.ini
RUN sed -i 's/;mbstring.http_input =/mbstring.http_input = pass/g' /etc/php/php.ini
RUN sed -i 's/;mbstring.http_output =/mbstring.http_output = pass/g' /etc/php/php.ini

# Configure PHP-FPM listening port
RUN sed -i '/^listen/c \
listen = 0.0.0.0:9000' /etc/php/php-fpm.conf

# Install drush (and drush dependencies)
# mariadb required for "drush sqlc"
RUN pacman -Syq --needed --noconfirm base-devel git jshon mariadb \
    >/dev/null 2>/dev/null
# Get Packer to help with installing from AUR
RUN git clone https://github.com/keenerd/packer.git /opt/packer
RUN chmod +x /opt/packer/packer

# Install drush and php-codesniffer-drupal
RUN /opt/packer/packer -S --noedit --noconfirm php-codesniffer-drupal drush

# Set HOME and open_basedir so that drush doesn't complain
ENV HOME /root
RUN mkdir /root/.drush
RUN echo "open_basedir =" >> /root/.drush/drush.ini

# Turn on mysql client auto-complete
RUN sed -i 's/no-auto-rehash/auto-rehash/g' /etc/mysql/my.cnf

# Allow nginx to find service
EXPOSE 9000

# Add script for running service
ADD start_php_fpm.sh /opt/start_php_fpm.sh

# vi: set tabstop=4 expandtab :
