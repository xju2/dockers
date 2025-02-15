This Dockerfile is for `docker.io/docexoty/docexoty/mltools:20250203`.

Use the python environment as `source activate gnn4itk`.

Some package information listed below by running `python check_env.py`.

```bash
python interpreter:  /opt/conda/envs/gnn4itk/bin/python
torch:  2.5.0a0+gita8d6afb
torch cuda:  True
torch cuda device count:  1
torch cuda device name:  NVIDIA A100-PCIE-40GB
torch cuda device capability:  (8, 0)
torch distributed     : True
lightning:  2.5.0.post0
pyg:  2.6.1
frnn found
cugraph:  24.12.00
cudf:  24.12.00
mpi4py:  4.0.2
h5py:  3.12.1
torch_scatter:  2.1.2
Test scatter_max in cuda.
out: tensor([[0, 0, 4, 3, 2, 0],
        [2, 4, 3, 0, 0, 0]], device='cuda:0')
argmax: tensor([[5, 5, 3, 4, 0, 1],
        [1, 4, 3, 5, 5, 5]], device='cuda:0')
```
