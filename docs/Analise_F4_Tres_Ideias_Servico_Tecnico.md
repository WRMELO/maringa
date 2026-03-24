---
title: "Análise F4 — Três ideias de serviço técnico em ciência de dados"
author: "Maringá Ferro Ligas | Consultoria"
date: "2026-02-18"
lang: "pt-BR"
geometry: "margin=2.5cm"
fontsize: "11pt"
documentclass: article
---

# Análise dos documentos e 3 ideias de serviço técnico (foco F4)

## O que o repositório e o relatório mostram

O **relatório** existente no repositório é um **diagnóstico de disponibilidade e qualidade** (DAMA) dos dados de produção da Maringá Ferro Ligas. Ele **não** construiu nenhum modelo, previsão ou produto analítico — apenas responde “o que existe e em que nível de qualidade”.

O **Forno 4 (F4)** é o mais propício para ciência de dados porque:

- É o **único** com domínio **Supervisório** (2 arquivos, ~486 mil linhas, **228 colunas**, período 2023-09-29 a 2025-01-02).
- Completude ~91%, validade ~99% no Supervisório F4.
- Na matriz de risco: F4 é **Nível 2** em “Não supervisionado” e “Reforço (RL)”; os outros fornos são Nível 3 nesses tipos.

Uma **limitação central** repetida em vários documentos: a **distância da ponta do eletrodo** não é medida de forma direta e contínua; o relatório apenas recomenda que seja inferida por proxies ou modelos — mas não implementa isso.

Para F4 você ainda tem: **Corridas** (estrutura boa), **Informações Diária**, **Eletrodo** (arquivo único com 761 medições, boa tempestividade/unicidade) e **Consumo Fornos** (completude muito baixa).

Ou seja: o relatório **não prova** que é possível entregar um serviço analítico; só mostra que, no F4, os dados permitem tentar.

---

## Três ideias de análise que você pode vender como serviço técnico

Cada uma é algo que o diagnóstico **não fez** e que um cientista de dados pode propor e executar com os dados do F4.

---

### 1. Soft-sensor de estado do eletrodo (proxy de imersão/distância) no F4

**O que o relatório não prova:** Que é possível estimar de forma contínua ou por regime o “estado” do eletrodo (imersão/distância efetiva) a partir dos dados existentes.

**Serviço que você pode vender:** Desenvolvimento de um **modelo que infira o estado do eletrodo** usando:

- **Supervisório F4** (228 tags: corrente, tensão, potência, possivelmente harmônicos, etc.) como features de alta frequência;
- **Eletrodo** (761 medições com “Data medição”) como **rótulos esparsos** para calibrar/validar;
- Opcional: **Corridas** e **Informações Diária** para contexto (corrida, receita, paradas).

**Entregas possíveis:**  
Estimativa contínua (ou por janela) de um “índice de imersão” ou de regime do eletrodo; relatório de viabilidade com métricas (ex.: correlação com medições de eletrodo quando disponíveis); documentação das variáveis mais importantes.

**Argumento de venda:** “O diagnóstico mostrou que a variável de interesse não é medida; este serviço entrega exatamente o proxy que o relatório recomenda, usando o único forno com supervisório completo.”

---

### 2. Detecção de anomalias e regimes de operação no F4

**O que o relatório não prova:** Que os dados do F4 permitem detectar anomalias ou segmentar regimes de forma útil para a operação.

**Serviço que você pode vender:** Análise **não supervisionada** sobre o **Supervisório F4** (~486k linhas, 228 colunas):

- **Clusterização de regimes** (ex.: regimes “normais” vs. transição, pré-parada, pós-tapping) usando séries de potência/corrente/tensão e, se fizer sentido, features derivadas.
- **Detecção de anomalias** (instabilidade de arco, drift, eventos atípicos) com método escolhido (e.g. isolación forest, autoencoder, ou regras sobre estatísticas por janela).
- Validação cruzada com **Corridas** e **Informações Diária** (paradas, eventos, motivos) para ver se os alertas/regimes batem com o que a operação reconhece.

**Entregas possíveis:**  
Relatório com perfis de regime, lista de eventos anômalos com timestamp, e um protótipo de **dashboard ou alertas** (off-line ou integrado).

**Argumento de venda:** “O relatório classificou F4 como Nível 2 para não supervisionado; este serviço converte esses dados em produto utilizável: regimes e alertas baseados em evidência.”

---

### 3. Modelo preditivo de desempenho por corrida no F4 (consumo, rendimento ou qualidade)

**O que o relatório não prova:** Que é possível prever um KPI operacional (consumo específico, rendimento, indicador de qualidade) por corrida ou por janela no F4 com os dados atuais.

**Serviço que você pode vender:** Modelo **supervisionado** (regressão ou classificação) em que:

- **Target:** KPI por corrida (ex.: consumo específico de energia, rendimento metálico, ou indicador OK/NOK de qualidade), definido com a produção a partir de **Corridas** e **Informações Diária**.
- **Features:** variáveis de **Corridas** e **Informações Diária** do F4 + **features agregadas do Supervisório F4** por corrida (médias, desvios, tempo em faixas de potência, etc.), tratando as lacunas de Consumo Fornos conforme a qualidade permitir.

**Entregas possíveis:**  
Modelo com métricas (RMSE, MAE, ou acurácia/precision/recall), análise de importância de variáveis, e um relatório que mostre **prova de valor** em dados reais (ex.: comparação previsto x realizado em corridas hold-out).

**Argumento de venda:** “O diagnóstico disse que modelo supervisionado é Nível 3 por causa de completude/validade; no F4 temos Supervisório de alta qualidade para enriquecer Corridas e Info Diária. Este serviço prova que, pelo menos no F4, é possível prever desempenho com os dados existentes.”

---

## Resumo para o comercial

| Ideia | O que o relatório não prova | Dados principais (F4) | Tipo de serviço |
|-------|----------------------------|------------------------|------------------|
| **1. Soft-sensor do eletrodo** | Inferir estado/imersão do eletrodo | Supervisório + Eletrodo (+ Corridas/Info Diária) | Modelo inferencial + relatório de viabilidade |
| **2. Anomalias e regimes** | Detectar anomalias e segmentar regimes | Supervisório + Corridas/Info Diária (validação) | Não supervisionado + dashboard/alertas |
| **3. Previsão por corrida** | Prever KPI por corrida no F4 | Corridas + Info Diária + agregados do Supervisório | Modelo supervisionado + prova de valor |

Todas as três ideias se apoiam no fato de que o **relatório só diagnosticou** dados e riscos; nenhuma delas foi “provada” pelo relatório. Elas são propostas de **próximo passo técnico** focadas no forno que o próprio diagnóstico apontou como o mais adequado para análise com ciência de dados (F4).
