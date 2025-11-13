# Catalogo de Ambientes Data Science

## Resumo
- Host: Ubuntu 25.04, GPU RTX 4500 (verificar driver com nvidia-smi).
- Ferramentas chave: Miniforge (mamba), pyenv, Docker com nvidia-container-toolkit.

## Ambientes Conda
| Nome | Proposito | Arquivo YAML | Caminho |
|------|-----------|--------------|---------|
| ds-base | Analise, finanças quantitativas e notebooks | ~/ds/environment-base.yml | ~/ds/envs/conda/ds-base |
| ds-gpu | Treino com GPU | ~/ds/environment-gpu.yml | ~/ds/envs/conda/ds-gpu |

## Ambientes Pyenv
| Versao | Uso | Caminho |
|--------|-----|---------|
| 3.11.8 | Scripts legado | ~/.pyenv/versions/3.11.8 |

## Contenedores
| Imagem | Dockerfile | Stack | Execucao |
|--------|------------|-------|----------|
| ds-pytorch:cuda12 | ~/ds/containers/pytorch.Dockerfile | PyTorch + CUDA 12 | docker run --gpus all -it ds-pytorch:cuda12 |

## Scripts
- ~/ds/scripts/bootstrap_env.sh <yaml> <env_name>
- ~/ds/scripts/list_envs.sh [saida.md]
- ~/ds/scripts/update_manifests.sh

## Fluxo de Atualizacao
1. Ajustar YAML.
2. Executar bootstrap_env.sh.
3. Rodar update_manifests.sh para sincronizar com Obsidian.
