FROM ubuntu:18.04
MAINTAINER William F Godoy godoywf@ornl.gov
RUN apt-get update -y &&\
    apt-get install build-essential sudo gfortran \
                    openmpi-bin libopenmpi-dev libzmq3-dev \
                    python3-dev python3-mpi4py python3-numpy python3-pip \
                    libblosc-dev libbz2-dev libpng-dev \
                    libhdf5-openmpi-dev \
                    cmake \
                    git vim gedit \
                    -y

RUN pip3 install matplotlib &&\
    cd /tmp &&\
    mkdir -p Software

RUN cd /tmp/Software &&\
    git clone https://github.com/LLNL/zfp.git --single-branch --branch 0.5.5 --depth 1 &&\
    mkdir -p zfp-build &&\
    cd zfp-build &&\
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/zfp -DBUILD_EXAMPLES=OFF ../zfp &&\
    make -j 4 &&\
    make install

RUN cd /tmp/Software &&\
    git clone https://github.com/CODARcode/MGARD.git --single-branch --branch mgard3 --depth 1 &&\
    mkdir -p MGARD-build &&\
    cd MGARD-build &&\
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/mgard ../MGARD &&\
    make -j 4 &&\
    make install
    
RUN cd /tmp/Software &&\
    git clone https://github.com/disheng222/SZ.git --single-branch --branch v2.1.5.0 --depth 1 &&\
    cd SZ &&\
    ./configure --prefix=/opt/sz &&\
    make -j 4 &&\
    make install

RUN cd /tmp/Software &&\
    git clone https://github.com/ornladios/ADIOS2.git --single-branch --branch master --depth 1 &&\
    mkdir -p ADIOS2-build &&\
    cd ADIOS2-build &&\
    cmake -DZFP_ROOT=/opt/zfp \
          -DMGARD_ROOT=/opt/mgard \
          -DSZ_ROOT=/opt/sz \
          -DADIOS2_BUILD_TESTING=OFF \
          -DADIOS2_BUILD_EXAMPLES=OFF \
          ../ADIOS2 &&\
    make -j 4 &&\
    make install &&\
    cd /tmp &&\
    rm -fr Software

RUN useradd -m wfg && echo "wfg:wfg" | chpasswd && adduser wfg sudo
USER wfg
CMD /bin/bash

RUN cd ~ &&\
    git clone https://github.com/pnorbert/adiosvm.git --single-branch --branch master --depth 1 &&\
    cd adiosvm/Tutorial/gray-scott &&\
    mkdir -p build &&\
    cd build &&\
    cmake -DCMAKE_BUILD_TYPE=Release .. &&\
    make &&\
    cd ..

ENV PYTHONPATH=/usr/local/lib/python3.6/site-packages/
ENV LD_LIBRARY_PATH=/usr/local/lib:/opt/zfp/lib:/opt/mgard/lib:/opt/sz/lib
