FROM base-dotnet-31-rhel7

RUN yum install -y libpng libjpeg libX11 libXext libXrender xorg-x11-fonts-Type1 xorg-x11-fonts-75dpi
RUN yum downgrade -y openssl-libs-1.0.2k-16.el7
RUN yum install -y openssl-1.0.2k-16.el7