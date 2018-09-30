FROM jupyter/base-notebook:137a295ff71b
MAINTAINER Rasmus Munk <rasmus.munk@nbi.ku.dk>

USER root

RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
    git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

USER $NB_USER

# Environement variables
ENV CONDA_DIR=/opt/conda

# Update conda, npm, yarn
RUN conda update -n base conda \
    && npm i -g npm

# Upgrade underlying pip that jlab uses
RUN pip install --upgrade pip

# Notebook/Lab dependencies,
# dedupe, resolve duplicates packages
# jupyter-widgets/jupyterlab-manager and matplotlib is for ipympl support which is labs
# replacement for notebook plots
RUN conda install --quiet --yes \
    'ipympl' \
    'ipyleaflet' \
    && jupyter labextension install jupyter-leaflet \
    && jupyter labextension install @jupyter-widgets/jupyterlab-manager \
    && jupyter labextension install jupyter-matplotlib \
    && npm dedupe \
    && npm cache clean --force \
    && rm -rf /home/$NB_USER/.cache/yarn \
    && rm -rf /home/$NB_USER/.node-gyp \
    && fix-permissions $CONDA_DIR \
    && fix-permissions /home/$NB_USER

## Install facets which does not have a pip or conda package at the moment
RUN cd /tmp \
    && git clone https://github.com/PAIR-code/facets.git \
    && cd facets \
    && jupyter nbextension install facets-dist/ --sys-prefix \
    && cd \
    && rm -rf /tmp/facets \
    && fix-permissions $CONDA_DIR \
    && fix-permissions /home/$NB_USER

# Ensure that container Runs as
USER $NB_USER