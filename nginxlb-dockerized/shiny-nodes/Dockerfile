#name of container: stats-docker-shiny
FROM ubuntu:18.04

ARG SITE_SHINY_USER_ID
ARG DEBIAN_FRONTEND=noninteractive
ENV SHINY_USER_ID=$SITE_SHINY_USER_ID

# Necessary for add-apt-repository
RUN apt-get update
RUN apt-get install -y software-properties-common
RUN apt-get install -y locales

RUN locale-gen en_US.UTF-8 && update-locale
RUN locale-gen es_AR.UTF-8 && update-locale
RUN echo 'LANG="en_US.UTF-8"' > /etc/default/locale
ENV LANG es_AR.UTF-8
ENV LANGUAGE es_AR:es:en_US:en
ENV LC_ALL es_AR.UTF-8

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
RUN add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/'
RUN apt-get update
RUN apt-get -y install r-base r-base-dev
############ This came from Jupyter hub install-stat28
RUN set -e
RUN apt-get -y --quiet --no-install-recommends install apt-transport-https
RUN add-apt-repository ppa:marutter/c2d4u3.5
RUN apt-get update && apt-get -y --quiet --allow-unauthenticated --no-install-recommends install \
build-essential \
gcc \
curl \
wget \
pkg-config \
libcurl4-openssl-dev \
libreadline-dev \
libssl-dev \
libzmq3-dev \
r-base-core \
r-base-dev \
r-base \
r-recommended \
libopenblas-base \
r-cran-evaluate \
r-cran-digest \
r-cran-testthat \
littler \
gdebi-core \
debconf-utils \
libapparmor1 \
sudo \
cron \
libssl1.0.0 \
libssl-dev \
libxml2-dev \
libcairo-dev \
libcurl4-openssl-dev \
lmodern \
libudunits2-0 \
libudunits2-dev \
libgdal-dev \
&& apt-get clean \
&& rm -rf /tmp/* /var/tmp/*  \
&& rm -rf /var/lib/apt/lists/*


RUN if [ -z $SHINY_USER_ID ]; then useradd -m shiny; else useradd -m -u $SHINY_USER_ID shiny; fi

# This is Shiny stuff, leave this alone
RUN R -e "install.packages('shiny', repos='http://cran.rstudio.com/')" \
          && wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.13.944-amd64.deb \
          && dpkg -i --force-depends shiny-server-1.5.13.944-amd64.deb \
          && rm shiny-server-1.5.13.944-amd64.deb \
          && mkdir -p /srv/shiny-server /etc/service/shiny /var/run/shiny-server /srv/shiny-server/examples; sync \
          && cp -R /usr/local/lib/R/site-library/shiny/examples/* /srv/shiny-server/examples/

RUN Rscript -e "install.packages('Rcpp', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('formatR', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('rmarkdown', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('fields', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('ggplot2', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('grid', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('gridExtra', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages('devtools', repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('leaflet','shiny','sf','shinydashboard','shinyWidgets','data.table','gsubfn','dqshiny','shinyjs','httpuv'), repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('leaflet.providers','shinycustomloader','dqshiny','shinyjs','shinythemes'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('dplyr','shinycssloaders'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('plotly'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('ggthemes'), repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('kableExtra'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('acid'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('viridisLite','DT'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('leafpop'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('tidyverse'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('treemapify'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('ggrepel'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('reshape2'),repos='http://cran.rstudio.com/')"
RUN Rscript -e "install.packages(c('heatmaply'),repos='http://cran.rstudio.com/')"



ADD ./etc/shiny-server/shiny-server.conf /etc/shiny-server/shiny-server.conf

##startup scripts
#Pre-config scrip that maybe need to be run one time only when the container run the first time .. using a flag to don't
#run it again ... use for conf for service ... when run the first time ...
RUN mkdir -p /srv/shiny-server /etc/my_init.d /var/log/cron/config /var/lib/shiny-server /path/to/shiny/apps \
&& cp -R /srv/shiny-server/examples/* /path/to/shiny/apps/.

# Let's open up the logs within the container.
RUN chown -R shiny:shiny /var/log/shiny-server
RUN chown -R shiny:shiny /var/lib/shiny-server
RUN chown -R shiny /var/run/shiny-server
RUN sed -i '113 a <h2><a href="./examples/">Other examples of Shiny application</a> </h2>' /srv/shiny-server/index.html

#volume for Shiny Apps and static assets. Here is the folder for index.html(link) and sample apps.
VOLUME /srv/shiny-server

# to allow access from outside of the container  to the container service
# at that ports need to allow access from firewall if need to access it outside of the server.
EXPOSE 3838

# Set Shiny log level
ENV SHINY_LOG_LEVEL="TRACE"

CMD /usr/bin/sudo -u shiny /opt/shiny-server/bin/shiny-server
