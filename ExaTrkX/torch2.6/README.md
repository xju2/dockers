This Dockerfile is for `docker.io/docexoty/exatrkx:cuda12-pytorch2.6`.

Use the python environment as `source activate gnn4itk`.

Some package information listed below:
```bash
python interpreter:  /opt/conda/envs/gnn4itk/bin/python
torch:  2.6.0+cu126
torch cuda:  True
torch cuda device count:  1
torch cuda device name:  NVIDIA A100-PCIE-40GB
torch cuda device capability:  (8, 0)
torch distributed     : True
lightning:  2.5.1.post0
pyg:  2.6.1
frnn found
cugraph:  25.04.01
cudf:  25.04.00
torch_scatter:  2.1.2+pt26cu126
ROOT:  6.34.04
```

## Instructions


### Perlmutter

To pull the container, via podman-hpc
```bash
podman-hpc pull docker.io/docexoty/exatrkx:cuda12-pytorch2.6
```

To run the container, via podman-hpc
```bash
podman-hpc run -it --rm --gpu -v $PWD:$PWD -v $SCRATCH:/scratch -w $PWD docker.io/docexoty/exatrkx:cuda12-pytorch2.6 bash
```
And then activate the `conda` environment
```bash
source activate gnn4itk
```