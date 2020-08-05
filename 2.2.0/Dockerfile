##
# geographica/gdal2
#
# This creates an Ubuntu derived base image that installs GDAL 2.
#
# Ubuntu 16.04 Xenial Xerus
FROM ubuntu:xenial

MAINTAINER Cayetano Benavent <cayetano.benavent@geographica.gs>

ENV ROOTDIR /usr/local/
ENV GDAL_VERSION 2.2.0

# Load assets
WORKDIR $ROOTDIR/

ADD http://download.osgeo.org/gdal/${GDAL_VERSION}/gdal-${GDAL_VERSION}.tar.gz $ROOTDIR/src/

# Install basic dependencies
RUN apt-get update -y && apt-get install -y \
    software-properties-common \
    python-software-properties \
    build-essential \
    python-dev \
    python-numpy \
    libspatialite-dev \
    sqlite3 \
    libpq-dev \
    libcurl4-gnutls-dev \
    libproj-dev \
    libxml2-dev \
    libgeos-dev \
    libnetcdf-dev \
    libpoppler-dev \
    libspatialite-dev \
    libhdf4-alt-dev \
    libhdf5-serial-dev \
    wget

# openjdk
COPY --from=openjdk:8  /usr/lib/jvm/java-1.8-openjdk/ /usr/lib/jvm/java-1.8-openjdk/
ENV LANG=C.UTF-8
ENV JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk
ENV PATH=$PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin
ENV JAVA_VERSION=8u212
ENV JAVA_ALPINE_VERSION=8.212.04-r0
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH://usr/lib:/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server

# Jackcess for mdb
RUN wget -O /usr/lib/jvm/java-1.8-openjdk/jre/lib/ext/jackcess-1.2.8.jar  \
	https://repo1.maven.org/maven2/com/healthmarketscience/jackcess/jackcess/1.2.8/jackcess-1.2.8.jar
RUN wget -O /usr/lib/jvm/java-1.8-openjdk/jre/lib/ext/commons-lang-2.4.jar  \
    https://repo1.maven.org/maven2/commons-lang/commons-lang/2.4/commons-lang-2.4.jar
RUN wget -O /usr/lib/jvm/java-1.8-openjdk/jre/lib/ext/commons-logging-1.1.1.jar  \
    https://repo1.maven.org/maven2/commons-logging/commons-logging/1.1.1/commons-logging-1.1.1.jar

# Compile and install GDAL

RUN cd src && tar -xvf gdal-${GDAL_VERSION}.tar.gz && cd gdal-${GDAL_VERSION} \
    && ./configure --with-python --with-spatialite --with-pg --with-curl \
    --with-java=/usr/lib/jvm/java-1.8-openjdk --with-jvm-lib=/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server --with-mdb=yes  \
    --with-jvm-lib-add-rpath=/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server \
    && make && make install && ldconfig \
    && apt-get update -y \
    && apt-get remove -y --purge build-essential wget \
    && rm -Rf $ROOTDIR/src/*

# Output version and capabilities by default.
CMD gdalinfo --version && gdalinfo --formats && ogrinfo --formats
