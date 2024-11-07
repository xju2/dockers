# syntax=docker/dockerfile:1
# docexoty/megatron-core:latest

FROM nvcr.io/nvidia/pytorch:24.02-py3

RUN pip install megatron_core && pip install tensorstore==0.1.45 && pip install zar

COPY requirements.txt /tmp/requirements.txt
RUN pip install -r /tmp/requirements.txt

# checkout the Megatron-LM repo.