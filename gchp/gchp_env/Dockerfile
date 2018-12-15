FROM ubuntu:18.04
LABEL maintainer="GEOS-Chem Support Team <geos-chem-support@as.harvard.edu>"

# Basic dependencies

RUN apt-get update && apt-get install -y \
    git vim wget bc gcc gfortran \
    libnetcdf-dev libnetcdff-dev netcdf-bin \
    python-numpy \
    && rm -rf /var/lib/apt/lists/*

# =========================
# OpenMPI 3
# =========================
# https://www.open-mpi.org/software/ompi/v3.1/

WORKDIR /tmp

RUN wget https://download.open-mpi.org/release/open-mpi/v3.1/openmpi-3.1.3.tar.gz \
    && tar zxf openmpi-3.1.3.tar.gz \
    && cd openmpi-3.1.3 \
    && ./configure prefix=/usr/local/ \
    && make -j4 \
    && make install \
    # Fix library linking
    # https://askubuntu.com/a/1100000
    && sudo ldconfig \
    && rm -rf /tmp/*

# Additional fixes for GCHP
RUN ln -s /usr/bin/make /usr/bin/gmake

# GEOS-Chem environment variables
ENV CC=gcc CXX=g++ FC=gfortran
ENV F90=$FC F9X=$FC F77=$FC
ENV NETCDF_HOME=/usr NETCDF_FORTRAN_HOME=/usr
ENV GC_BIN=$NETCDF_HOME/bin \
    GC_INCLUDE=$NETCDF_HOME/include \
    GC_LIB=$NETCDF_HOME/lib \
    GC_F_BIN=$NETCDF_FORTRAN_HOME/bin \
    GC_F_INCLUDE=$NETCDF_FORTRAN_HOME/include \
    GC_F_LIB=$NETCDF_FORTRAN_HOME/lib

# for libnetcdff.so
ENV LD_LIBRARY_PATH=$NETCDF_FORTRAN_HOME/lib:$LD_LIBRARY_PATH

# GCHP-specific environment
ENV OMPI_CC=$CC OMPI_CXX=$CXX OMPI_FC=$FC \
    COMPILER=$FC ESMF_COMPILER=$FC \
    MPI_ROOT=/usr/local  \
    ESMF_COMM=openmpi \
    ESMF_BOPT=O

# fix Perl error message
ENV LANG C

WORKDIR /