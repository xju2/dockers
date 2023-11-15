This Dockerfile is for `docker.io/docexoty/exatrkx:cuda12-pytorch2.1`.

Use the python environment as `source activate gnn4itk`.

Some package information listed below:
```bash
python interpreter:  /opt/conda/envs/gnn4itk/bin/python
torch:  2.1.0a0+git4c55dc5
torch cuda:  True
pytorch_lightning:  2.1.1
pyg:  2.4.0
frnn FOUND
cugraph:  23.10.00
torch_scatter:  2.1.2
```

## Instructions


### Perlmutter
To pull the container, via podman-hpc
```bash
podman-hpc pull docker.io/docexoty/exatrkx:cuda12-pytorch2.1
```

To run the container, via podman-hpc
```bash
podman-hpc run -it --rm --gpu -v $PWD:$PWD -v $SCRATCH:/scratch -v /global/cfs/cdirs/m3443:/m3443 -w $PWD docker.io/docexoty/exatrkx:cuda12-pytorch2.1 bash
```
