# GEOS-Chem classic precompiled code

Precompiled GEOS-Chem code for immediate use.
Also needs ~100GB input dataset (the ExtData/ folder). Can be downloaded from AWS cloud. See http://cloud.geos-chem.org.

Usage
-----

You can download and run this image using the following commands:

    docker pull geoschem/gc_model
    input_path=/full/path/to/input/directory/on/host
    output_path=/full/path/to/output/directory/on/host
    docker run --rm -it -v $input_path:/ExtData -v $output_path:/OutputDir  zhuangjw/gc_model
    ./geos.mp  # inside container, just execute model
