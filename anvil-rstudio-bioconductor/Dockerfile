FROM us.gcr.io/anvil-gcr-public/anvil-rstudio-base:0.0.3

# This is to avoid the error
# 'debconf: unable to initialize frontend: Dialog'
ENV DEBIAN_FRONTEND noninteractive

RUN echo "TERRA_R_PLATFORM='anvil-rstudio-bioconductor'" >> /usr/local/lib/R/etc/Renviron.site \
    && echo "TERRA_R_PLATFORM_BINARY_VERSION='0.99.1'" >> /usr/local/lib/R/etc/Renviron.site

# Update apt-get
RUN apt-get update \
	&& apt-get install -y --no-install-recommends apt-utils \
	&& apt-get install -y dselect \
	&& dselect update \
	&& apt-get install -y --no-install-recommends \
	pkg-config \
	fortran77-compiler \
	byacc \
	automake \
	## This section install libraries
	libpng-dev \
	libpcre2-dev \
	libnetcdf-dev \
	libhdf5-serial-dev \
	libfftw3-dev \
	libopenbabel-dev \
	libopenmpi-dev \
	libexempi3 \
	libxt-dev \
	libgdal-dev \
	libtiff5-dev \
	libreadline-dev \
	libgsl0-dev \
	libgslcblas0 \
	libgtk2.0-dev \
	libgl1-mesa-dev \
	libglu1-mesa-dev \
	libgmp3-dev \
	libhdf5-dev \
	libncurses-dev \
	libbz2-dev \
	libxpm-dev \
	liblapack-dev \
	libv8-3.14-dev \
	libgtkmm-2.4-dev \
	libmpfr-dev \
	libudunits2-dev \
	libmodule-build-perl \
	libapparmor-dev \
	libgeos-dev \
	libprotoc-dev \
	librdf0-dev \
	libmagick++-dev \
	libpoppler-cpp-dev \
	libprotobuf-dev \
	libperl-dev \
	libz-dev \
	liblzma-dev \
	## software - perl extentions and modules
	libarchive-extract-perl \
	libfile-copy-recursive-perl \
	libcgi-pm-perl \
	libdbi-perl \
	libdbd-mysql-perl \
	libxml-simple-perl \
	## Databases and other software
	sqlite \
	openmpi-bin \
	mpi-default-bin \
	openmpi-common \
	openmpi-doc \
	tcl8.5-dev \
	tk-dev \
	openjdk-8-jdk \
	imagemagick \
	tabix \
	ggobi \
	graphviz \
	protobuf-compiler \
	jags \
	## Additional resources
	xfonts-100dpi \
	xfonts-75dpi \
	biber \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

## These two libraries don't install in the above section--WHY?
RUN apt-get update \
	&& apt-get -y --no-install-recommends install \
	default-jdk \
	libjpeg-dev \
	libjpeg-turbo8-dev \
	libjpeg8-dev \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

## Python installations
RUN apt-get update \
	&& apt-get -y --no-install-recommends install python-dev \
	&& pip install wheel \
	## Install sklearn and pandas on python
	&& pip install sklearn \
	pandas \
	pyyaml \
	cwltool \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

# Install libsbml and xvfb
RUN cd /tmp \
	## libsbml
	&& curl -O https://s3.amazonaws.com/linux-provisioning/libSBML-5.10.2-core-src.tar.gz \
	&& tar zxf libSBML-5.10.2-core-src.tar.gz \
	&& cd libsbml-5.10.2 \
	&& ./configure --enable-layout \
	&& make \
	&& make install \
	## xvfb install
	&& cd /tmp \
	&& curl -SL https://github.com/just-containers/s6-overlay/releases/download/v1.21.8.0/s6-overlay-amd64.tar.gz | tar -xzC / \
	&& apt-get update && apt-get install -y --no-install-recommends xvfb \
	&& mkdir -p /etc/services.d/xvfb/ \
	## Clean libsbml, and tar.gz files
	&& rm -rf /tmp/libsbml-5.10.2 \
	&& rm -rf /tmp/libSBML-5.10.2-core-src.tar.gz \
	## apt-get clean and remove cache
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

COPY ./deps/xvfb_init /etc/services.d/xvfb/run

# Install Bioconductor packages
RUN curl -O https://raw.githubusercontent.com/Bioconductor/anvil-docker/master/anvil-rstudio-bioconductor/install.R \
	&& R -f install.R \
	&& rm -rf install.R

USER root

# Init command for s6-overlay
CMD ["/init"]
