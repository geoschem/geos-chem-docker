## Steps to build Docker images for GEOS-Chem

(Note for the GEOS-Chem Support Team for future maintenance.)

1. Log into official GEOS-Chem Docker Hub account (ask Bob)
2. Select "Create" (top-right corner) - "Create Automated Build"  - "GitHub" - select this geos-chem-docker GitHub repo.
3. Note: This GitHub repo contains multiple Dockerfiles, but one DockerHub repo should only contain one Dockerfile (can have different branches & tags). So, create 4 different repos for gc_env, gc_model, gchp_env, gchp_model
4. Change the default name "geos-chem-docker" to one of the 4 options above.
5. Select "Click here to customize behavior", change "Dockerfile Location" from "/" to one of the subdirectories like "/gc-classic/gc_env/".
6. Go the created Docker repo - "Build Setting" - click on "Trigger" to build the first version of image.
7. Recommend turning off "builds will happen automatically on pushes" at initial development stage to avoid too many repeated builds. Free Docker Hub account only allows one build at a time so one build will block the other.

For the gchp_model image, currently have memory problems (https://github.com/geoschem/gchp/issues/4). So build it locally (e.g. on AWS) and upload it to DockerHub:

    cd gchp/gchp_model
    docker build -t geoschem/gchp_model .
    docker login -u <Docker Hub username> -p <password>
    docker push geoschem/gchp_model

## Useful references:
- Jupyter docker stacks: https://github.com/jupyter/docker-stacks
- Anaconda docker images: https://github.com/ContinuumIO/docker-images
- Docker-WRF: https://github.com/NCAR/container-wrf
- Docker-JEDI: https://wiki.ucar.edu/display/JEDI/Build+JEDI+environment+with+Docker
