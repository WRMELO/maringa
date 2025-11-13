# Manifesto Conda

## ds-base
- Pacotes chave: numpy, pandas, polars, scikit-learn, matplotlib, seaborn, hvplot.
- Finanças/quant: statsmodels, pmdarima, pandas-ta, linearmodels, cvxpy, xgboost, lightgbm, riskfolio-lib, pyportfolioopt, quantstats, arch, yfinance, pandas-datareader.
- Atualizacao: mamba env update -n ds-base -f ~/ds/environment-base.yml
- Notas: preferir `mamba run -n ds-base <cmd>` para scripts automatizados.

## ds-gpu
- Pacotes chave: pytorch, pytorch-cuda, cuda-toolkit, cupy-cuda12x, lightning.
- Requisito: validar CUDA com nvidia-smi e torch.cuda.is_available().
- Atualizacao: mamba env update -n ds-gpu -f ~/ds/environment-gpu.yml.
