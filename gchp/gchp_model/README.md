# GEOS-Chem High-Performance (GCHP) precompiled code

Precompiled GCHP code for immediate use.
Also needs ~150GB input dataset (the ExtData/ folder). Can be downloaded from AWS cloud. See http://cloud.geos-chem.org.

Usage
-----

You can download and run this image using the following commands:

    docker pull geoschem/gchp_model
    input_path=/full/path/to/input/directory/on/host
    output_path=/full/path/to/output/directory/on/host
    docker run --rm -it -v $input_path:/ExtData -v $output_path:/OutputDir  zhuangjw/gchp_model
    mpirun -np 6 -oversubscribe ./geos  # inside container, just execute model
