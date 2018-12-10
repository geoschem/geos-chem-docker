FROM ubuntu:18.04
LABEL maintainer="GEOS-Chem Support Team <geos-chem-support@as.harvard.edu>"

RUN apt-get update && apt-get install -y \
    git vim bc gcc gfortran libnetcdf-dev libnetcdff-dev netcdf-bin \
    && rm -rf /var/lib/apt/lists/*
# The last line cleans up cache to reduce container size
# No need to run `apt-get clean` as it is done automatically in official ubuntu image
# See more at https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#run

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

# fix Perl error message
ENV LANG C
