# docexoty/heptools:rivet3.1.6
ARG RIVET_VERSION=3.1.6
FROM hepstore/rivet-pythia:${RIVET_VERSION}

RUN apt-get update -y && apt-get -y install libhdf5-mpich-dev vim

# python pakcages
RUN ln -s /usr/local/bin/pip /usr/bin/pip && \
    pip install --upgrade pip && \
    pip install pylint jupyterlab jax jaxlib h5py iminuit \
		vaex sklearn scipy numba mpi4py pyDOE2 autograd \
        tables tqdm Gpy>=1.9.9 matplotlib seaborn pandas numpy