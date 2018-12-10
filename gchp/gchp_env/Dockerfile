FROM ubuntu:18.04
LABEL maintainer="GEOS-Chem Support Team <geos-chem-support@as.harvard.edu>"

RUN apt-get update && apt-get install -y \
    git vim bc gcc gfortran libnetcdf-dev libnetcdff-dev netcdf-bin \
    python-numpy mpich \
    && rm -rf /var/lib/apt/lists/*

# Additional fixes for GCHP
RUN ln -s /usr/include/mpich/* /usr/include/ \
    && ln -s /usr/bin/make /usr/bin/gmake \
    && ln -s /usr/lib/mpich/lib/libmpichfort.so /usr/lib/mpich/lib/libmpichf90.so \
    && ln -s /usr/lib/mpich/lib/libmpichfort.a /usr/lib/mpich/lib/libmpichf90.a

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
    MPI_ROOT=/usr  \
    ESMF_COMM=mpich2 \
    ESMF_BOPT=O

# fix Perl error message
ENV LANG C
