FROM  ubuntu:18.04

# set timezone
ENV HOME /root
ENV TZ=America/Chicago
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

ENV HOME /root

RUN apt-get -y --force-yes update \
    && apt-get install -y --force-yes \
       software-properties-common \
       build-essential \
       git \
       cmake \
       gfortran \
       libblas-dev \
       python-pip \
       liblapack-dev \
       libhdf5-dev \
       autoconf \
       libtool

# need to put libhdf5.so on LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH /usr/lib/x86_64-linux-gnu
ENV LIBRARY_PATH /usr/lib/x86_64-linux-gnu

# upgrade pip and install python dependencies
ENV PATH $HOME/.local/bin:$PATH
RUN python -m pip install --upgrade pip \
    && pip install numpy scipy cython tables matplotlib setuptools future pytest pandas

# make starting directory
RUN cd $HOME \
  && mkdir opt

# build MOAB
RUN cd $HOME/opt \
    && mkdir moab \
    && cd moab \
    && git clone --depth 1 -b Version5.2.0 --single-branch https://bitbucket.org/fathomteam/moab \
    && mkdir build \
    && cd build \
    && cmake ../moab -DCMAKE_C_FLAGS="-fPIC -DPIC" -DCMAKE_CXX_FLAGS="-fPIC -DPIC" -DBUILD_SHARED_LIBS=ON \
       -DCMAKE_SHARED_LINKER_FLAGS="-Wl,--no-undefined" -DENABLE_MPI=OFF  \
       -DENABLE_HDF5=ON -DHDF5_ROOT=/usr/lib/x86_64-linux-gnu/hdf5/serial \
       -DENABLE_NETCDF=OFF -DENABLE_METIS=OFF -DENABLE_IMESH=OFF -DENABLE_FBIGEOM=OFF \
       -DENABLE_PYMOAB=ON -DBUILD_STATIC_LIBS=OFF -DCMAKE_INSTALL_PREFIX=$HOME/opt/moab \
    && make -j 8 \
    && make install \
    && cd $HOME/opt/moab \
    && rm -rf build moab

# set environment variables
ENV PATH $HOME/opt/moab/bin/:$PATH
ENV LD_LIBRARY_PATH $HOME/opt/moab/lib:$LD_LIBRARY_PATH
ENV PYTHONPATH $HOME/opt/moab/lib/python2.7/site-packages/:$PYTHONPATH

