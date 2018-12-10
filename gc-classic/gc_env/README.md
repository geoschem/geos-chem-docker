# GEOS-Chem classic environment

GEOS-Chem software environment to be used with custom user code

Usage
-----

You can download and run this image using the following commands:

    docker pull geoschem/gc_model
    workdir_path=/full/path/to/working/directory/on/host
    docker run --rm -it -v $workdir_path:/workdir zhuangjw/gc_model
    cd /workdir
