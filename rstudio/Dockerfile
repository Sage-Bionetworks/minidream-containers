# rocker/tidyverse was built on rocker/rstudio (so, it includes RStudio)
# Waiting for 4.1.1 before upgrading due to bug affecting '.First'
# https://cran.r-project.org/doc/manuals/r-devel/NEWS.html
FROM rocker/tidyverse:4.0.5

# Install OS-level dependencies
# rsync needed for `broadcast_*` scripts
# libffi-dev and zlib1g-dev needed for `synapser`
# libglpk-dev needed for imager and clusterProfiler
# python3-pip needed for installing Python packages
# libxt6 needed for graphics in R
RUN apt-get update \
    && apt-get -y install \
        libffi-dev \
        libglpk-dev \
        libxt6 \
        python3-pip \
        rsync \
        zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Install R packages from CRAN (tidyverse pre-installed in base image)
RUN install2.r \
    BiocManager \
    bitops \
    caTools \
    getPass \
    here \
    imager \
    revealjs \
    survival \
    tsne

# Install R packages from Bioconductor (using littler utility script)
RUN /usr/local/lib/R/site-library/littler/examples/installBioc.r \
    clusterProfiler \
    DOSE \
    org.Hs.eg.db \
    pathview

# Install synapser using `install.packages()` (error when installed with install2.r)
RUN R -e "install.packages('synapser', repos=c('http://ran.synapse.org', 'http://cran.fhcrc.org'))"

# Install Python dependencies
RUN pip3 install \
    pandas \
    requests \
    rpy2 \
    synapseclient

# Create groups for RStudio users and admins
RUN groupadd rstudio-user
RUN groupadd rstudio-admin

# Create a shared directory where 
RUN mkdir /shared
RUN chgrp rstudio-admin /shared
RUN chmod g+rw /shared

# Add utility scripts and configuration files to container
COPY bin/* /usr/local/bin/
COPY conf/Rprofile.site /usr/local/lib/R/etc/
COPY conf/rserver.conf /etc/rstudio/rserver.conf

# Create UNIX users, which will also be used for RStudio
# IMPORTANT: This isn't a secure way of passing passwords
# to the container since a layer will include users.csv
# even if it's being deleted in the RUN step.
COPY conf/users.csv /root/users.csv
RUN add_users.sh /root/users.csv \
    && rm /root/users.csv
