# GOVERNANCE — MARINGA_F4

## 1) Identidade

Repositório de desenvolvimento de Data Science e IA aplicada ao Forno Elétrico de Redução F4 (Submerged Arc Furnace — SAF) da planta de Maringá.
Objetivo: construir soluções analíticas (soft-sensors, modelos preditivos, otimização) que gerem valor operacional contínuo e justifiquem contratação de longo prazo.

## 2) Cadeia de comando

```text
Owner <---> CTO <---> Architect ---> Executor ---> Auditor ---> Curator
```

- **Owner**: autoridade final. Toda execução exige autorização explícita.
- **CTO**: interlocutor técnico do Owner. Traduz, analisa, propõe — não executa.
- **Architect**: planeja e gera JSON de task a partir de orientações do CTO.
- **Executor**: implementa conforme JSON aprovado pelo Owner.
- **Auditor**: valida entrega do Executor. Emite PASS ou FAIL.
- **Curator**: registra conclusões nos documentos de governança após PASS.

## 3) Documentos de governança (trinca operacional)

| Documento | Finalidade | Quem escreve |
|-----------|-----------|--------------|
| `GOVERNANCE.md` | Regras fixas, políticas, restrições do repo | CTO (com aprovação do Owner) |
| `DECISION_LOG.md` | Decisões do Owner com contexto e justificativa | CTO (durante discussão com Owner) |
| `CHANGELOG.md` | Log técnico cronológico de mudanças | Executor (pós-task) / Curator (pós-audit) |

### Regras de escrita

- **Append-only**: nunca apagar entradas anteriores.
- **DECISION_LOG**: cada entrada tem ID sequencial (`D-NNN`), data, contexto, alternativas, decisão e responsável.
- **CHANGELOG**: cada entrada tem data ISO, task_id (quando aplicável) e descrição curta.
- **GOVERNANCE**: alterações via discussão CTO-Owner. Registrar a decisão de alteração no DECISION_LOG antes de editar.

## 4) Princípios operacionais

1. **Reprodutibilidade**: todo script deve produzir resultado determinístico dado os mesmos inputs. Seeds fixos para ML.
2. **Rastreabilidade**: toda decisão, mudança e execução deve ser verificável nos documentos de governança.
3. **Dados regeneráveis fora do git**: parquets, CSVs processados e outputs são gerados por scripts, não versionados.
4. **Segurança**: `.env` e credenciais nunca no repositório.
5. **Evidências**: execuções de governança produzem gates verificáveis com status PASS/FAIL.

## 5) Fluxos de governança por natureza de trabalho

### 5.1 Tasks técnicas

```text
CTO orienta → Architect planeja → Owner autoriza → Executor implementa → Auditor valida → Curator registra
```

### 5.2 Hotfixes

- Passam pela cadeia completa se envolvem lógica de pipeline ou modelos.
- Registrados no CHANGELOG como `fix:`.

## 6) Políticas técnicas

### 6.1 Branch e versionamento

- Branch principal: `main`.
- Commits seguem conventional commits (`feat:`, `fix:`, `chore:`, `docs:`).

### 6.2 Dados

- Formato canônico: Parquet (dados processados) e CSV (dados brutos de origem).
- Dados brutos da planta ficam em `fase_1_diagnostico/dados/` (readonly, não alterar).
- Dados processados e features ficam em `fase_2_ia/data/`.
- SSOT (Single Source of Truth) vive em `fase_2_ia/data/ssot/`.

### 6.3 Ambiente

- Python via `.venv/` local ao workspace.
- Dependências em `requirements.txt` dentro de `fase_2_ia/`.
- Variáveis sensíveis em `.env` (nunca commitado).

### 6.4 Estrutura de diretórios

```text
fase_2_ia/
├── GOVERNANCE.md
├── DECISION_LOG.md
├── CHANGELOG.md
├── requirements.txt
├── scripts/          # scripts de exploração, ETL, feature engineering
├── data/
│   ├── ssot/         # dados canônicos processados
│   ├── features/     # feature sets para modelos
│   └── outputs/      # resultados de modelos, relatórios
├── docs/             # documentação técnica, corpus
├── config/           # configurações de modelos, hiperparâmetros
├── pipeline/         # scripts de pipeline operacional
├── backtest/
│   └── results/
└── logs/             # logs de execução (excluídos do git)
```

### 6.5 Domínio Técnico — Forno Elétrico de Redução (SAF)

- **Forno alvo**: F4 (forno de arco submerso).
- **Dados disponíveis**: Supervisório F4 (228 variáveis, resolução minuto), Corridas, Consumo Fornos, Eletrodo (medições manuais), Informações Diárias.
- **Limitações conhecidas**: headers malformados no Supervisório, gaps temporais de até 69 dias, duplicatas de timestamp (~7.195 no 1S/2024), dados de Consumo com alta nulidade em campos químicos.
- **Referência**: diagnóstico de qualidade completo em `fase_1_diagnostico/3ª-Retomada/outputs/R3Q/`.

### 6.6 Modelos e ML

- Todo modelo deve ter baseline documentado.
- Hiperparâmetros registrados em `config/`.
- Métricas de performance registradas em relatórios JSON em `data/outputs/`.
- Reprodutibilidade: random_state fixo, versões de pacotes pinadas.

## 7) Vigência

Esta governança entra em vigor com o primeiro commit que a inclui.
Alterações exigem registro prévio no `DECISION_LOG.md`.
