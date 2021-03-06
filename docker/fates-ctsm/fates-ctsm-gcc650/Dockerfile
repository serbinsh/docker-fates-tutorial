# --------------------------------------------------------------------------------------------
# CTSM host land model with experimental FATES land component docker container on GCC-base OS
# --------------------------------------------------------------------------------------------

FROM ngeetropics/hlmbase-gcc650:v.0.0.0
LABEL maintainer.name="Gregory Lemieux" \
      maintainer.email="glemieux@lbl.gov" \
      author.name="Shawn Serbin" \
      author.email="sserbin@bnl.gov" \
      description="FATES model using CTSM host land model on GCC-base OS" \
      version.fates="sci.1.40.1_api.13.0.1" \
      version.hlm="release-clm5.0.30" \
      version.baseos="gcc6.5"

# setting gmake
RUN ln -s /usr/bin/make /usr/bin/gmake

# Setup environment variables.  This is necessary for os.environ calls from case.py in CIME
ENV USER=fatesuser

# Add user directory structure.  This is necessary for CIME
RUN useradd -m $USER -u 9001

## create data mount points in container - should change this to /mnt or something more generic in machines files
RUN mkdir -p inputdata \
    && mkdir -p output \
    && mkdir -p scripts \
    && mkdir -p baselines \
    && mkdir -p .cime 

## TODO: merge above into single large RUN command?
## Clone CTSM model and checkout specific fates_next_api commit.  Grab the cime config directory from github
RUN git -c http.sslVerify=false clone https://github.com/ESCOMP/CTSM.git \
    && cd CTSM \
    && git describe \
    && git checkout abcd5937530cd22bc82bf5d7cc4eb28cf3bf1857 \
    && ./manage_externals/checkout_externals \
    && git describe \
    && cd src/fates/ \
    && git describe \
    && cd /.cime \
    && wget https://raw.githubusercontent.com/glemieux/docker-fates-tutorial/develop/docker/fates-ctsm/cime_config/config \ 
    && wget https://raw.githubusercontent.com/glemieux/docker-fates-tutorial/develop/docker/fates-ctsm/cime_config/config_compilers.xml \
    && wget https://raw.githubusercontent.com/glemieux/docker-fates-tutorial/develop/docker/fates-ctsm/cime_config/config_machines.xml

## Should the above wgets be changed to COPY?  We would have to then include the cime directory with every dockerfile.
## The problem with wget is that it has no knowledge of whether or not the files changed state so a `docker build .` won't
## really build a new image if nothing else is changed in the Dockerfile.

## synchronize FATES code with CTSM fates_next_api branch.  Only necessary if using different fates hash from the one
## designated by fates_next_api
# RUN cd /CTSM/src/fates \
#     && git -c http.sslVerify=false remote add ngeet_repo https://github.com/NGEET/fates.git \
#     && git remote -v \
#     && git -c http.sslVerify=false fetch ngeet_repo \
#     && git tag \
#     && git -c http.sslVerify=false checkout -b fates sci.1.23.0_api.7.1.0 \
#     && git describe \
#     && cd /

### EOF
