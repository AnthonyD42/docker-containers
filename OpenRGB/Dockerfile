FROM jlesage/baseimage-gui:ubuntu-20.04
#Dockerfile based on Dockerfiles of smdion

# Set correct environment variables
ENV HOME /root
ENV DEBIAN_FRONTEND noninteractive
ENV LC_ALL C.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
ENV TERM xterm
ENV APP_NAME="OpenRGB"
ENV KEEP_APP_RUNNING=1
ENV ENABLE_CJK_FONT=1

#Create Config File
ADD config.xml /root/config.xml

#Add firstrun.sh to create config.xml if needed
ADD firstrun.sh /etc/my_init.d/firstrun.sh

#Add FAHClient-run.sh to temp location
ADD OpenRGB-run.sh /root/OpenRGB-run.sh

# Use baseimage-docker's init system
CMD ["/sbin/my_init"]

# Configure user nobody to match unRAID's settings
RUN \
usermod -u 99 nobody && \
usermod -g 100 nobody && \
usermod -d /home nobody && \
chown -R nobody:users /home && \

#Install OpenRGB
apt-get -y install && \
apt-get update -q && \
apt-get install -qy wget && \
apt-get clean -y && \
rm -rf /var/lib/apt/lists/* && \
wget --no-check-certificate -P /tmp/ https://github.com/AnthonyD42/docker-containers/raw/main/OpenRGB/openrgb_0.8_amd64_buster_fb88964.deb && \
mkdir -p /opt/openrgb && \
dpkg -x /tmp/openrgb_0.8_amd64_buster_fb88964.deb /opt/openrgb  && \
rm /tmp/openrgb_0.8_amd64_buster_fb88964.deb && \
chown -R nobody:users /opt/openrgb && \
apt-get purge --remove wget -y && \
apt-get autoremove -y && \

# fix up start files and make executable
chmod +x /etc/my_init.d/firstrun.sh && \
mkdir -p /etc/service/openrgb && \
mv /root/OpenRGB-run.sh /etc/service/openrgb/run && \
chmod +x /etc/service/openrgb/run

# install extra items for OpenRGB
RUN apt-get update && \
	apt-get -y install \
	libusb-1.0-0 \
	libqt5widgets5 \
	libqt5core5a \
	libqt5gui5 \
	usbutils \
	i2c-tools \
	locales \
	libhidapi-dev \
	libmbedx509-0 \
	libmbedtls-dev \
	&& locale-gen en_US.UTF-8

RUN \
	APP_ICON_URL=https://github.com/AnthonyD42/docker-containers/raw/main/OpenRGB/OpenRGB.png && \
    APP_ICON_DESC='{"masterPicture":"/opt/novnc/images/icons/master_icon.png","iconsPath":"/images/icons/","design":{"ios":{"pictureAspect":"noChange","assets":{"ios6AndPriorIcons":false,"ios7AndLaterIcons":false,"precomposedIcons":false,"declareOnlyDefaultIcon":true}},"desktopBrowser":{"design":"raw"},"windows":{"pictureAspect":"noChange","backgroundColor":"#da532c","onConflict":"override","assets":{"windows80Ie10Tile":false,"windows10Ie11EdgeTiles":{"small":false,"medium":true,"big":false,"rectangle":false}}},"androidChrome":{"pictureAspect":"noChange","themeColor":"#ffffff","manifest":{"display":"standalone","orientation":"notSet","onConflict":"override","declared":true},"assets":{"legacyIcon":false,"lowResolutionIcons":false}},"safariPinnedTab":{"pictureAspect":"silhouette","themeColor":"#5bbad5"}},"settings":{"scalingAlgorithm":"Mitchell","errorOnImageTooSmall":false,"readmeFile":false,"htmlCodeFile":false,"usePathAsIs":false},"versioning":{"paramName":"v","paramValue":"ICON_VERSION"}}' && \
	install_app_icon.sh "$APP_ICON_URL" "$APP_ICON_DESC"
