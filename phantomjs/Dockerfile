# naive approach to building a no-longer developed nodejs app for the purposes
# of making a better debugging experience when running docker-compose
FROM centos:centos7.6.1810

ENV container=docker

RUN rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 \
    && yum -y update \
    && yum -y install epel-release -y \
    && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7

RUN yum -y install npm supervisor tcpdump
RUN yum -y install bzip2
COPY files /
# fontconfig is a phantomjs dependency
# phantomjs/files/usr/lib/node_modules/phantomjs-prebuilt/test/tests.js
# omits a failing test 
RUN yum -y install fontconfig
RUN chmod +x kill_supervisord.py start_phantomjs.sh \
    && /usr/bin/supervisord -n -c /etc/supervisord.conf \
    ; echo "installed phantomjs, hopefully you enjoyed your tcpdump experience!"
# Install test phase
#   Get the testing tools and source code for testing the prebuilt binary
#   Test the prebuilt binary, excluding a test that unexpectedly succeeds
#   remove unused stuffs
COPY files /
RUN yum -y install git \
    && npm install --global nodeunit webdriverio@4.4.0 eslint \
    && git clone --single-branch --branch 2.1 https://github.com/ariya/phantomjs.git \
    && mkdir /phantomjs/bin \
    && ln -s /usr/lib/node_modules/phantomjs-prebuilt/bin/phantomjs /phantomjs/bin/ \
    && rm -f /phantomjs/test/module/webserver/requests.js \
    && /phantomjs/test/run-tests.py -v \
    && cd /usr/lib/node_modules/phantomjs-prebuilt/ \
    && sed 's/reporter\=minimal/reporter\=verbose/' -i package.json \
    && sed 's/\ \&\&\ eslint\ .//' -i package.json \
    && npm test \
    && rm -rf /phantomjs \
    && npm uninstall --global nodeunit webdriverio@4.4.0 eslint \
    && echo "clean_requirements_on_remove=1" >> /etc/yum.conf \
    && yum -y remove git

# 
# Nice-ities you would expect of a dockerfile
#
# Run as non-root user 
# RUN useradd --system --uid 72379 -m --shell /usr/sbin/nologin phantomjs

# USER phantomjs 
# HEALTHCHECK CMD ["curl", "--fail", "http://127.0.0.1:8910", "||", "exit", "1"]
EXPOSE 8910 
# CMD ["/usr/lib/node_modules/phantomjs-prebuilt/bin/phantomjs"]
CMD ["/start_phantomjs.sh"]
