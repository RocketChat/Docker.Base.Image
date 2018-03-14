FROM centos:6

MAINTAINER buildmaster@rocket.chat

ENV PATH="/opt/rh/devtoolset-3/root/usr/bin/:$PATH"

RUN curl -sL https://rpm.nodesource.com/setup_8.x | bash -
RUN yum install -y nodejs git gcc-c++ make build-essential centos-release-scl-rh
RUN yum install -y devtoolset-3-gcc devtoolset-3-gcc-c++
RUN yum install -y glib2-devel expat-devel cairo-devel zlib-devel giflib-devel libjpeg-devel libexif-devel libpng-devel librsvg2-devel
RUN curl -LO https://github.com/jcupitt/libvips/releases/download/v8.6.3/vips-8.6.3.tar.gz
RUN tar -xf vips-8.6.3.tar.gz
RUN cd vips-8.6.3 \
	&& ./configure \
	&& make \
	&& make install

RUN echo "/usr/local/lib" > /etc/ld.so.conf.d/usrlocal.conf
RUN ldconfig -v

RUN npm install -g node-gyp
# Should be able to run Rocket.Chat here



# TEST WITHOUT CI
RUN curl -L https://releases.rocket.chat/latest/download > /tmp/rocket.chat.tar
RUN mkdir -p /tmp/build-test
RUN tar -xf /tmp/rocket.chat.tar -C /tmp/build-test

RUN cd /tmp/build-test/bundle/programs/server \
	&& npm install \
	&& cd npm/node_modules/sharp/ \
	&& node-gyp rebuild \
	&& ldd build/Release/sharp.node

RUN mkdir ~/repo
WORKDIR ~/repo
RUN git clone https://github.com/RocketChat/Rocket.Chat.git .
RUN npm install


RUN yum install -y java firefox
RUN dbus-uuidgen > /var/lib/dbus/machine-id
RUN yum install -y Xvfb
ENV DISPLAY=:19
RUN Xvfb :19 -screen 0 1024x768x24 &
ENV MONGO_URL=mongodb://mongo/test12
ENV TEST_MODE=true
#  export DISPLAY=127.0.0.1:19
# RUN npm test
# DISPLAY=:19 firefox "http://www.yahoo.com"
# Need run Xvfb :19 -screen 0 1024x768x24 &
