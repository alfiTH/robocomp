ARG base_image="nvidia/opengl"
ARG base_tag="base-ubuntu20.04"
ARG branch="development"
ARG robocomp_version="dsr-components"

FROM ${base_image}:${base_tag} AS dependencies-version
# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive
ARG branch
# DEFAULT LD_LIBRARY_PATH vaiables from the nvidia/opengl image confuses pyside2 about the qt5 Library paths
ENV LD_LIBRARY_PATH=""
RUN export LD_LIBRARY_PATH=""
RUN apt-get update && apt-get install -y --no-install-recommends  \
      ca-certificates \
      curl \
      sudo \
    && rm -rf /var/lib/apt/lists/*
RUN cd ~ && export branch=$branch \
    && curl -sL https://raw.githubusercontent.com/robocomp/robocomp/$branch/tools/install/resources/robocomp_prerequisites_install.sh | bash -s $branch
RUN export uid=1000 gid=1000 && \
    mkdir -p /home/robolab && \
    echo "robolab:x:${uid}:${gid}:Robolab,,,:/home/robolab:/bin/bash" >> /etc/passwd && \
    echo "robolab:x:${uid}:" >> /etc/group && \
    echo "robolab:robolab"|chpasswd && \
    echo "robolab ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/robolab-user && \
    chown ${uid}:${gid} -R /home/robolab
USER robolab
WORKDIR /home/robolab/


FROM dependencies-version AS base-version
ARG branch
# Using robocomp installation script
RUN cd ~ && export branch=$branch \
    && curl -sL https://raw.githubusercontent.com/robocomp/robocomp/$branch/tools/install/robocomp_install.sh | bash -s $branch
ENV PATH=$PATH:/opt/robocomp/bin/:'/home/robolab/.local/bin'
ENV ROBOCOMP=/home/robolab/robocomp
RUN export ROBOCOMP=/home/robolab/robocomp
RUN export PATH=$PATH:/opt/robocomp/bin/:'/home/robolab/.local/bin'

WORKDIR robocomp
RUN git annex get \
  files/innermodel/simpleworld.xml \
  files/osgModels/textures/wood.jpg \
  files/innermodel/tar36h11-*.png \
  files/osgModels/textures/Metal.jpg \
  files/osgModels/robex/robex.ive
RUN cp -r -L files /home/robolab/files 2>/dev/null || :


FROM base-version AS fcl-version
USER root
RUN apt update && apt-get install --no-install-recommends --yes \
  libfcl-dev
USER robolab
RUN cd /home/robolab/robocomp && mkdir -p build && cd build && cmake -DFCL_SUPPORT=True .. && make 
USER root
RUN cd /home/robolab/robocomp/build && make install
USER robolab


FROM ${robocomp_version}-version AS final
ARG robocomp_version
RUN echo "Built ${robocomp_version}"
