FROM phalconphp/build:centos7

LABEL description="Docker images to build Phalcon on CentOS 7" \
    maintainer="Serghei Iakovlev <serghei@phalconphp.com>" \
    vendor=Phalcon \
    name="com.phalconphp.images.build.centos7-ius72"

#
# This image has commented `centos-sclo-rh-source' repo in the
# /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo
#
# The `yum-builddep' from `yum-utils' package, for some unknown to me
# reason, enable all source repos for corresponding binary repos.
#
# For example it enables `centos-sclo-rh-source'.  But in fact this repo
# has wrong base url and this lead to an error:
#
# http://vault.centos.org/centos/7/sclo/Source/rh/repodata/repomd.xml:
# [Errno 14] HTTP Error 404 - Not Found
#
# Fuck!

RUN yum upgrade -y \
    && curl -s https://setup.ius.io | bash \
    && yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm \
    && yum install -y \
      yum-utils \
      centos-release-scl \
    && yum-config-manager --enable centos-sclo-sclo-testing \
    && yum -y install devtoolset-7-gcc \
    && echo "source /opt/rh/devtoolset-7/enable" | tee -a /etc/bashrc \
    && echo "source /opt/rh/devtoolset-7/enable" | tee -a /etc/profile \
    && source /opt/rh/devtoolset-7/enable \
    && gcc --version \
    && ln -sf /opt/rh/devtoolset-7/root/usr/bin/gcc /usr/bin/gcc \
    && awk \
        '/centos-sclo-rh-source/{f=1} f>6{f=0} f{f++; $0="# " $0} 1' \
        /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo > repo.patch \
    && mv repo.patch /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
    && yum-config-manager --enable remi-php72 \
    && yum -y install \
        php72u-cli \
        php72u-pdo \
        php72u-json \
        php72u-common \
        php72u-devel \
        yum install php-pear \
    && printf "\n" | pecl install --force psr 1> /dev/null \
    && echo 'extension=psr.so' > /etc/php.d/20-psr.ini \
    && yum clean all \
    && rm -rf /tmp/* /var/tmp/* \
    && find /var/cache -type f -delete \
    && find /var/log -type f | while read f; do echo -n '' > ${f}; done
