# Manifesto Containers

## ds-pytorch:cuda12
- Base: nvidia/cuda:12.4.1-devel-ubuntu24.04.
- Bibliotecas: pytorch, torchvision, torchaudio, jupyterlab, poetry.
- Volumes sugeridos: -v ~/ds/projects:/workspace/projects.
- Execucao: docker run --gpus all -it -v ~/ds/projects:/workspace/projects ds-pytorch:cuda12 bash.
- Teste rapido: python - <<'PY'
import torch
print(torch.cuda.is_available())
PY

## rotinas
- Verificar nvidia-smi antes de iniciar containers.
- Atualizar imagem mensalmente com docker build --pull.
