FROM geoschem/gchp_env
LABEL maintainer="GEOS-Chem Support Team <geos-chem-support@as.harvard.edu>"

# Git tag or commit
ARG GC_VERSION=b544322
ARG GCHP_VERSION=12.1.0

# Input & output data path
# Need to mount real data volume at run-time
RUN mkdir /ExtData && mkdir /OutputDir

WORKDIR /tutorial

# Source code
RUN git clone https://github.com/geoschem/geos-chem.git Code.GCHP \
    && cd Code.GCHP && git checkout $GC_VERSION \
    # Temporary fix for https://github.com/geoschem/gchp/issues/5
    && sed -i -e '/@$(MAKE) exe/d' HEMCO/Makefile \
    && git clone https://github.com/geoschem/gchp.git GCHP \
    && cd GCHP && git checkout $GCHP_VERSION \
    && cd Run && printf "/ExtData \n 2 \n 1 \n /tutorial \n gchp_standard \n n" | ./createRunDir.sh

#==============================================================================
# additional fixes for MAPL
#==============================================================================
ARG GC_CODE_DIR=/tutorial/Code.GCHP

# --- Fixes for Makefile ---
# Dependencies files *.d in MAPL are messed up in that
# 1. It directly specifies library headers like mpi.h as dependency, which should have been handled by MPI wrapper like mpicc.
# 2. It cannot find *.mod files that are in another directory.
# VPATH allows makefile to search additional directories, although is not a good practice.
# This prevents the error "no rule to make target"
ENV VPATH=/usr/include/x86_64-linux-gnu:$VPATH
ENV VPATH=$GC_CODE_DIR/GCHP/Shared/MAPL_Base:$VPATH
ENV VPATH=$GC_CODE_DIR/GCHP/ESMF/Linux/mod:$VPATH

# --- Fixed for header files ("#include <xxx.h>" statement) ---
# This prevents the error "cannot find..."
# MAPL_Generic.h
ENV CPATH=$GC_CODE_DIR/GCHP/Shared/MAPL_Base:$CPATH
# fms_platform.h
ENV CPATH=$GC_CODE_DIR/GCHP/Shared/GFDL_fms/shared/include:$CPATH

# --- Fixes for module files ("USE xxx" statement) ---
# zonal.f is looking for mapl_constantsmod.mod in its own directory. Even /usr/include/ is not included
RUN ln -sf $GC_CODE_DIR/GCHP/Shared/MAPL_Base/mapl_constantsmod.mod $GC_CODE_DIR/GCHP/Shared/GEOS_Util/plots/

#==============================================================================
# Compile GCHP
#==============================================================================
WORKDIR /tutorial/gchp_standard

# compile source code
RUN touch gchp.env && make compile_clean

#==============================================================================
# modify run-time configurations
#==============================================================================

# Will mount output directory to external volume
RUN rm -r OutputDir && ln -sf /OutputDir OutputDir \
# correctly link restart file
    && ln -sf /ExtData/GEOSCHEM_RESTARTS/v2018-11/initial_GEOSChem_rst.c24_standard.nc  initial_GEOSChem_rst.c24_standard.nc \
# read low-resolution metfields to save space
    && ln -sf /ExtData/GEOS_4x5/GEOS_FP MetDir \
    && sed -i -e 1,120s/025x03125.nc/4x5.nc/g ExtData.rc \
# hard to see why simulation crashes with default debug level 0
    && sed -i -e "s#DEBUG_LEVEL.*#DEBUG_LEVEL: 5#" CAP.rc \
# Turn off CEDS as GCHP 12.1.0 still reads the old, large file.
    && sed -i -e "s#.--> CEDS.*# --> CEDS                   :       false#" HEMCO_Config.rc \
    && sed -i -e "s#.--> CEDS_SHIP.*# --> CEDS                   :       false#" HEMCO_Config.rc

CMD ["/bin/bash"]
