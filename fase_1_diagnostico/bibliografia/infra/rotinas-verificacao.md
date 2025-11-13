# Rotinas de Verificacao

## Checklist Semanal
- Rodar nvidia-smi e registrar driver.
- Atualizar mamba: mamba upgrade --all.
- Executar list_envs.sh para validar manifestos.

## Checklist Mensal
- Rebuild imagens Docker com --pull.
- Validar compatibilidade CUDA com nvcc --version.
- Revisar requirements de projetos ativos.

## Incident Response
- Se ambiente quebrar: limpar com mamba env remove -n <env> e recriar via bootstrap.
- Registrar causas no manifesto correspondente.
