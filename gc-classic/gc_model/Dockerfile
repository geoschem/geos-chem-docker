FROM geoschem/gc_env
LABEL maintainer="GEOS-Chem Support Team <geos-chem-support@as.harvard.edu>"

# ARG is for build-time arguments, not used in run-time (as opposed to ENV)
ARG GC_VERSION=12.1.1
ARG UT_VERSION=12.1.1

# Input data path as required by UT
# Need to mount real data volume at run-time
RUN mkdir -p /ExtData/HEMCO && mkdir /OutputDir

# Subsequent shell commands like `sed` are only tested with version 12.1.0
# If the compile & configure steps are changed in future versions, need to fix `sed` commands

# Main directory to install the model.
# Do not install into $HOME or /root for best portability
# See https://www.sylabs.io/guides/2.6/user-guide/singularity_and_docker.html#best-practices
# WORKDIR does `mkdir` (if needed) and `cd` together
WORKDIR /tutorial

RUN git clone https://github.com/geoschem/geos-chem.git Code.GC-classic \
    && cd Code.GC-classic && git checkout $GC_VERSION

# We are back to WORKDIR after each RUN
RUN git clone https://github.com/geoschem/geos-chem-unittest.git UT \
    && cd UT && git checkout $UT_VERSION \
# Configure UT
# Each sed command change one line in a text file
# This could be trivially done by text editors by we want full automation
# Use '#' as delimiter so the line can contain '/' and ':'
# Use .* to match the rest of the line
    && sed -i -e 's#CODE_DIR    :=.*#CODE_DIR    :=/tutorial/Code.GC-classic#' runs/shared_inputs/Makefiles/Makefile \
    && cd perl \
    && sed -i -e 's#GCGRID_ROOT    :.*#GCGRID_ROOT    : /#' CopyRunDirs.input \
    && sed -i -e 's#DATA_ROOT      :.*#DATA_ROOT      : /ExtData#' CopyRunDirs.input \
    && sed -i -e 's#UNIT_TEST_ROOT :.*#UNIT_TEST_ROOT : /tutorial/UT#' CopyRunDirs.input \
    && sed -i -e 's#COPY_PATH      :.*#COPY_PATH      : /tutorial#' CopyRunDirs.input \
# show the modifications
    && git add -A && git status -v \
# create run directory
    && ./gcCopyRunDirs \
# remove UT to reduce image size
    && rm -rf /tutorial/UT

# WORKDIR accepts relative path, but it is recommended to use absolute path
WORKDIR /tutorial/geosfp_4x5_standard

# compile model
RUN make -j4 mpbuild NC_DIAG=y BPCH_DIAG=n TIMERS=1

# Output data path
RUN ln -s /OutputDir OutputDir \
    && sed -i -e 's#EXPID:  ./GEOSChem.*#EXPID: ./OutputDir/GEOSChem#' HISTORY.rc

# Restart file path
RUN ln -s /ExtData/GEOSCHEM_RESTARTS/v2018-11/initial_GEOSChem_rst.4x5_standard.nc GEOSChem.Restart.20160701_0000z.nc4


# Use short simulation period for tutorial purpose
RUN sed -i -e 's#End   YYYYMMDD.*#End   YYYYMMDD, hhmmss  : 20160701 002000#' input.geos \
# reduce output size for tutorial purpose
    && sed -i -e 's#EXPID:  ./GEOSChem.*#EXPID: ./OutputDir/GEOSChem#' HISTORY.rc \
    && sed -i -e "s/#'SpeciesConc',/'SpeciesConc',/" HISTORY.rc \
    && sed -i -e 's#SpeciesConc.frequency.*#SpeciesConc.frequency:      00000000 002000#' HISTORY.rc \
    && sed -i -e 's#SpeciesConc.duration.*#SpeciesConc.duration:       00000000 002000#' HISTORY.rc \
    && sed -i -e "s#SpeciesConc.mode.*#SpeciesConc.mode:           'instantaneous'#" HISTORY.rc \
    && sed -i -e "s#SpeciesConc.fields.*#SpeciesConc.fields:         'SpeciesConc_NO             ', 'GIGCchem',\n                              'SpeciesConc_CO             ', 'GIGCchem',\n                              'SpeciesConc_O3             ', 'GIGCchem',#" HISTORY.rc

# Set default command for `docker run`
CMD ["/bin/bash"]
