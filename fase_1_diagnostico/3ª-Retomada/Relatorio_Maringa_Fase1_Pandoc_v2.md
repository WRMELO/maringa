---
title: "**Relatório Técnico (Gerencial) — Diagnóstico de Disponibilidade e Qualidade de Dados de Produção**"
author: "**Maringá Ferro Ligas**"
date: "**2026-01-06**"
lang: "pt-BR"
geometry: "a3paper,margin=1.5cm"
fontsize: "10pt"
header-includes:
  - '\usepackage{booktabs}'
  - '\usepackage{tabularx}'
  - '\usepackage{array}'
  - '\usepackage{ragged2e}'
  - '\usepackage{longtable}'
  - '\usepackage{caption}'
  - '\usepackage{makecell}'
  - '\captionsetup{labelfont=bf}'
---
\newpage

# **Relatório Técnico (Gerencial)**

**Entregável:** Diagnóstico inicial (Fase 1 — Dados de Produção)  
**Pessoa de referência:** **Gerente de Produção**  
**Terceira pessoa jurídica:** **Maringá Ferro Ligas**  

\newpage

## **1. Contexto, objetivo e leitura recomendada**

Este relatório consolida, em linguagem técnica voltada ao público gerencial, o diagnóstico de **disponibilidade** e **qualidade** dos dados atualmente disponíveis no acervo de produção da **Maringá Ferro Ligas**. O objetivo é reduzir risco de decisão e de interpretação ao preparar dados para análises e modelagem, evitando que iniciativas de melhoria de processo e automação sejam guiadas por dados incompletos, ambíguos ou estruturalmente inconsistentes.

Para o **Gerente de Produção**, o relatório responde a quatro perguntas práticas:

1) **Quais dados existem**, por domínio e por forno, e qual é a cobertura temporal observada;  
2) **Qual o nível de qualidade** observado, usando critérios reconhecidos (DAMA);  
3) **O que pode ser utilizado agora** e **o que requer correção** antes de uso analítico;  
4) **Quais decisões** de padronização, governança e instrumentação elevam rapidamente a confiabilidade do dado.

\newpage

## **2. Escopo do acervo analisado**

O diagnóstico considerou **124 arquivos**, organizados por **domínio** e, quando aplicável, por **forno** (**F1** a **F5**). A tabela a seguir resume volume e cobertura temporal observados por domínio.

| **Domínio** | **Arquivos (n)** | **Linhas totais (aprox.)** | **Colunas médias** | **Período coberto** |
|:---|---:|---:|---:|:---:|
| Consumo Fornos | 39 | 38.123.141 | 27.4 | 2018-01-01 a 2025-04-28 |
| Corridas | 40 | 114.949 | 34.1 | 2018-01-01 a 2025-04-29 |
| Eletrodo | 1 | 761 | 5.0 | 2021-01-02 a 2025-12-03 |
| Informações Diária | 40 | 14.232 | 47.2 | 2018-01-01 a 2025-04-29 |
| Supervisorio Forno 4 | 2 | 486.250 | 228.0 | 2023-09-29 a 2025-01-02 |
| Supervisorio Forno 5 | 2 | 242.099 | 25.5 | 2021-01-06 a 2024-12-05 |

**Observação operacional:** os fornos **F1** e **F2** são reconhecidamente mais antigos, com defasagem tecnológica em relação a **F4** e **F5**. Isso se reflete na variação de estrutura, granularidade e padronização — inclusive dentro do mesmo domínio.

\newpage

## **3. Metodologia: critérios de qualidade adotados (DAMA) e como foram medidos**

A avaliação seguiu seis dimensões de qualidade de dados, usuais em governança:

- **Completude:** proporção de campos efetivamente preenchidos.  
- **Validade:** proporção de valores parseáveis e compatíveis com regras mínimas de domínio (tipos, datas e ranges plausíveis).  
- **Acurácia (proxy):** ausência de referência metrológica direta para todos os sinais; adotou-se uma **proxy de plausibilidade estatística** baseada em taxa de outliers/anomalias. **Nesta proxy, menor é melhor**.  
- **Tempestividade:** presença e consistência de coluna temporal utilizável (timestamp/data) e compatibilidade com a granularidade do domínio. Quando a coluna temporal é ausente/ambígua, o resultado pode aparecer como **n/d**.  
- **Unicidade:** ausência de duplicidades conforme regra do domínio.  
- **Consistência:** estabilidade do schema (nomes/quantidade de colunas) e coerência interna entre arquivos do mesmo domínio.

\newpage

## **4. Interpretação gerencial: o que os dados permitem (e o que ainda impedem)**

O diagnóstico evidencia que, em vários domínios, **volume de dados não é o gargalo**. O principal limitador é a **qualidade estrutural e temporal** (padronização de colunas, tipos e timestamp), que aumenta risco de interpretação e dificulta análises temporais confiáveis.

De forma gerencial, o uso prático se organiza em três níveis:

**Nível 1 — utilizável com baixo risco (após ajustes pontuais):** bases com validade/consistência altas e timestamp padronizável, adequadas para séries temporais e correlações operacionais.  

**Nível 2 — utilizável com risco moderado (requer padronização):** bases com boa estrutura geral, mas com lacunas, divergências de tipos/unidades e/ou necessidade de formalizar chaves e coluna temporal.  

**Nível 3 — alto risco (exige reprocessamento antes de uso):** bases com problemas de parsing, estrutura inválida, duplicidades ou completude extremamente baixa; o uso direto tende a produzir conclusões erradas com “confiança estatística” artificial.

\newpage
## **5. Quantidade de dados: mínimos viáveis e “ótimos” como ordens de grandeza**

```{=latex}
\begin{small}
\setlength{\tabcolsep}{2pt}
\renewcommand{\arraystretch}{1.00}
\begin{tabularx}{\linewidth}{@{}>{\RaggedRight\arraybackslash}p{3.2cm} >{\RaggedRight\arraybackslash}p{4.3cm} >{\RaggedRight\arraybackslash}p{4.3cm} >{\RaggedRight\arraybackslash}p{4.3cm}@{}}
\toprule
Tipo de modelo & Escopo & Mínimo viável & Nível ótimo \\
\midrule
\makecell[l]{Physic-based (balanços)\\} & \makecell[l]{Corridas + Informações Diária\\} & \makecell[l]{6–12 meses contínuos com timestamp padronizado\\} & \makecell[l]{18–24 meses com calibração documentada\\} \\ \hline
\makecell[l]{Supervisionado (regressão/class.)\\} & \makecell[l]{Corridas + Info Diária + Consumo Fornos\\} & \makecell[l]{12 meses rotulados e limpos por forno, com chave temporal\\} & \makecell[l]{24–36 meses com labels confiáveis e drift monitorado\\} \\ \hline
\makecell[l]{Não supervisionado (detecção de padrão)\\} & \makecell[l]{Supervisório (F4/F5); senão Consumo Fornos\\} & \makecell[l]{2–3 meses contínuos com timestamp confiável\\} & \makecell[l]{6–12 meses com tags harmonizadas e filtros de ruído\\} \\ \hline
\makecell[l]{Reforço (RL)\\} & \makecell[l]{Supervisório com ações e retorno (F4/F5)\\} & \makecell[l]{3–6 meses de episódios com ação/recompensa registrada\\} & \makecell[l]{12+ meses com atrasos medidos e segurança validada\\} \\
\bottomrule
\end{tabular}
\end{small}
```

\clearpage
## **6. Quadro consolidado: qualidade por forno e por domínio (uma página)**
```{=latex}
\begin{scriptsize}
\setlength{\tabcolsep}{1.4pt}
\renewcommand{\arraystretch}{1.05}
\begin{tabularx}{\linewidth}{>{\RaggedRight\arraybackslash}p{2.6cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{0.9cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{1.0cm} >{\RaggedRight\arraybackslash}p{1.4cm} X X}
\toprule
Domínio & Forno & n & Comp. & Val. & AccP & Temp. & Uniq. & Cons. & Coluna tempo & Interpretação & Ações imediatas \\
\midrule
\makecell[l]{Consumo Fornos\\} & \makecell[l]{F1\\} & \makecell[l]{7\\} & \makecell[l]{0.008\\} & \makecell[l]{0.824\\} & \makecell[l]{0.048\\} & \makecell[l]{n/d\\} & \makecell[l]{0.188\\} & \makecell[l]{0.714\\} & \makecell[l]{Data\\} & \makecell[l]{Risco alto: completude muito baixa\\Timestamp n/d; usar só após saneamento} & \makecell[l]{Sanear schema, deduplicar exportações\\Definir coluna temporal antes de modelar} \
\makecell[l]{Consumo Fornos\\} & \makecell[l]{F2\\} & \makecell[l]{8\\} & \makecell[l]{0.008\\} & \makecell[l]{0.824\\} & \makecell[l]{0.042\\} & \makecell[l]{n/d\\} & \makecell[l]{0.186\\} & \makecell[l]{0.625\\} & \makecell[l]{Data\\} & \makecell[l]{Risco alto: completude muito baixa\\Timestamp n/d; usar só após saneamento} & \makecell[l]{Sanear schema, deduplicar exportações\\Definir coluna temporal antes de modelar} \
\makecell[l]{Consumo Fornos\\} & \makecell[l]{F3\\} & \makecell[l]{8\\} & \makecell[l]{0.006\\} & \makecell[l]{0.823\\} & \makecell[l]{0.052\\} & \makecell[l]{n/d\\} & \makecell[l]{0.179\\} & \makecell[l]{0.5\\} & \makecell[l]{Data\\} & \makecell[l]{Risco alto: completude muito baixa\\Timestamp n/d; usar só após saneamento} & \makecell[l]{Sanear schema, deduplicar exportações\\Definir coluna temporal antes de modelar} \
\makecell[l]{Consumo Fornos\\} & \makecell[l]{F4\\} & \makecell[l]{8\\} & \makecell[l]{0.012\\} & \makecell[l]{0.826\\} & \makecell[l]{0.063\\} & \makecell[l]{n/d\\} & \makecell[l]{0.181\\} & \makecell[l]{0.625\\} & \makecell[l]{Data\\} & \makecell[l]{Risco alto: completude muito baixa\\Timestamp n/d; usar só após saneamento} & \makecell[l]{Sanear schema, deduplicar exportações\\Definir coluna temporal antes de modelar} \
\makecell[l]{Consumo Fornos\\} & \makecell[l]{F5\\} & \makecell[l]{8\\} & \makecell[l]{0.012\\} & \makecell[l]{0.826\\} & \makecell[l]{0.064\\} & \makecell[l]{n/d\\} & \makecell[l]{0.182\\} & \makecell[l]{0.75\\} & \makecell[l]{Data\\} & \makecell[l]{Risco alto: completude muito baixa\\Timestamp n/d; usar só após saneamento} & \makecell[l]{Sanear schema, deduplicar exportações\\Definir coluna temporal antes de modelar} \
\makecell[l]{Corridas\\} & \makecell[l]{F1\\} & \makecell[l]{8\\} & \makecell[l]{0.993\\} & \makecell[l]{0.799\\} & \makecell[l]{0\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data_Base\\} & \makecell[l]{Estrutura consistente; validade moderada\\Timestamp n/d exige padronização} & \makecell[l]{Padronizar parse/unidades e coluna temporal\\Documentar schema e validar consistência} \
\makecell[l]{Corridas\\} & \makecell[l]{F2\\} & \makecell[l]{8\\} & \makecell[l]{0.979\\} & \makecell[l]{0.798\\} & \makecell[l]{0\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{0.625\\} & \makecell[l]{Data_Base\\} & \makecell[l]{Estrutura consistente; validade moderada\\Timestamp n/d exige padronização} & \makecell[l]{Padronizar parse/unidades e coluna temporal\\Documentar schema e validar consistência} \
\makecell[l]{Corridas\\} & \makecell[l]{F3\\} & \makecell[l]{8\\} & \makecell[l]{0.995\\} & \makecell[l]{0.785\\} & \makecell[l]{0.001\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data_Base\\} & \makecell[l]{Estrutura consistente; validade moderada\\Timestamp n/d exige padronização} & \makecell[l]{Padronizar parse/unidades e coluna temporal\\Documentar schema e validar consistência} \
\makecell[l]{Corridas\\} & \makecell[l]{F4\\} & \makecell[l]{8\\} & \makecell[l]{0.993\\} & \makecell[l]{0.78\\} & \makecell[l]{0.001\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data_Base\\} & \makecell[l]{Estrutura consistente; validade moderada\\Timestamp n/d exige padronização} & \makecell[l]{Padronizar parse/unidades e coluna temporal\\Documentar schema e validar consistência} \
\makecell[l]{Corridas\\} & \makecell[l]{F5\\} & \makecell[l]{8\\} & \makecell[l]{0.992\\} & \makecell[l]{0.78\\} & \makecell[l]{0.001\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data_Base\\} & \makecell[l]{Estrutura consistente; validade moderada\\Timestamp n/d exige padronização} & \makecell[l]{Padronizar parse/unidades e coluna temporal\\Documentar schema e validar consistência} \
\makecell[l]{Eletrodo\\} & \makecell[l]{F1 a F5\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{0.799\\} & \makecell[l]{n/d\\} & \makecell[l]{0.993\\} & \makecell[l]{0.986\\} & \makecell[l]{1\\} & \makecell[l]{Data medicao\\} & \makecell[l]{Validade moderada; proxy de acurácia indefinida\\Precisa timestamp e calibração simples} & \makecell[l]{Padronizar unidades e chave de medição\\Calibrar sinais críticos e registrar timestamp} \
\makecell[l]{Eletrodo\\} & \makecell[l]{F1 a F5\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{0.8\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data medicao\\} & \makecell[l]{Validade moderada; proxy de acurácia indefinida\\Precisa timestamp e calibração simples} & \makecell[l]{Padronizar unidades e chave de medição\\Calibrar sinais críticos e registrar timestamp} \
\makecell[l]{Eletrodo\\} & \makecell[l]{F1 a F5\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{0.8\\} & \makecell[l]{n/d\\} & \makecell[l]{0.977\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data medicao\\} & \makecell[l]{Validade moderada; proxy de acurácia indefinida\\Precisa timestamp e calibração simples} & \makecell[l]{Padronizar unidades e chave de medição\\Calibrar sinais críticos e registrar timestamp} \
\makecell[l]{Eletrodo\\} & \makecell[l]{F1 a F5\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{0.799\\} & \makecell[l]{n/d\\} & \makecell[l]{0.995\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data medicao\\} & \makecell[l]{Validade moderada; proxy de acurácia indefinida\\Precisa timestamp e calibração simples} & \makecell[l]{Padronizar unidades e chave de medição\\Calibrar sinais críticos e registrar timestamp} \
\makecell[l]{Eletrodo\\} & \makecell[l]{F1 a F5\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{0.8\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data medicao\\} & \makecell[l]{Validade moderada; proxy de acurácia indefinida\\Precisa timestamp e calibração simples} & \makecell[l]{Padronizar unidades e chave de medição\\Calibrar sinais críticos e registrar timestamp} \
\makecell[l]{Informações Diária\\} & \makecell[l]{F1\\} & \makecell[l]{8\\} & \makecell[l]{0.808\\} & \makecell[l]{0.942\\} & \makecell[l]{n/d\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{0.75\\} & \makecell[l]{Data_base\\} & \makecell[l]{Completude/validade medianas; schema varia\\Timestamp incerto reduz rastreabilidade} & \makecell[l]{Harmonizar schema/tipos e coluna temporal\\Fechar lacunas obrigatórias antes de uso} \
\makecell[l]{Informações Diária\\} & \makecell[l]{F2\\} & \makecell[l]{8\\} & \makecell[l]{0.839\\} & \makecell[l]{0.943\\} & \makecell[l]{n/d\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data_base\\} & \makecell[l]{Completude/validade medianas; schema varia\\Timestamp incerto reduz rastreabilidade} & \makecell[l]{Harmonizar schema/tipos e coluna temporal\\Fechar lacunas obrigatórias antes de uso} \
\makecell[l]{Informações Diária\\} & \makecell[l]{F3\\} & \makecell[l]{8\\} & \makecell[l]{0.834\\} & \makecell[l]{0.925\\} & \makecell[l]{n/d\\} & \makecell[l]{0.998\\} & \makecell[l]{1\\} & \makecell[l]{0.5\\} & \makecell[l]{Data_base\\} & \makecell[l]{Completude/validade medianas; schema varia\\Timestamp incerto reduz rastreabilidade} & \makecell[l]{Harmonizar schema/tipos e coluna temporal\\Fechar lacunas obrigatórias antes de uso} \
\makecell[l]{Informações Diária\\} & \makecell[l]{F4\\} & \makecell[l]{8\\} & \makecell[l]{0.736\\} & \makecell[l]{0.914\\} & \makecell[l]{0.001\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{0.5\\} & \makecell[l]{Data_base\\} & \makecell[l]{Completude/validade medianas; schema varia\\Timestamp incerto reduz rastreabilidade} & \makecell[l]{Harmonizar schema/tipos e coluna temporal\\Fechar lacunas obrigatórias antes de uso} \
\makecell[l]{Informações Diária\\} & \makecell[l]{F5\\} & \makecell[l]{8\\} & \makecell[l]{0.768\\} & \makecell[l]{0.916\\} & \makecell[l]{n/d\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{0.625\\} & \makecell[l]{Data_base\\} & \makecell[l]{Completude/validade medianas; schema varia\\Timestamp incerto reduz rastreabilidade} & \makecell[l]{Harmonizar schema/tipos e coluna temporal\\Fechar lacunas obrigatórias antes de uso} \
\makecell[l]{Supervisorio Forno 4\\} & \makecell[l]{F4\\} & \makecell[l]{2\\} & \makecell[l]{0.909\\} & \makecell[l]{0.994\\} & \makecell[l]{0.03\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Timestamp\\} & \makecell[l]{Dados ricos; tempestividade parcial\\Schema volumoso exige governança} & \makecell[l]{Padronizar timestamp e tags essenciais\\Filtrar outliers e documentar alarmes} \
\makecell[l]{Supervisorio Forno 5\\} & \makecell[l]{F5\\} & \makecell[l]{1\\} & \makecell[l]{0.553\\} & \makecell[l]{0.024\\} & \makecell[l]{0.000\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data\\} & \makecell[l]{Validade muito baixa; registros heterogêneos\\Uso direto arriscado sem reprocessar} & \makecell[l]{Reprocessar e padronizar parse/timestamp\\Validar ranges e bloquear valores inválidos} \
\makecell[l]{Supervisorio Forno 5\\} & \makecell[l]{F5\\} & \makecell[l]{1\\} & \makecell[l]{0.956\\} & \makecell[l]{0\\} & \makecell[l]{n/d\\} & \makecell[l]{n/d\\} & \makecell[l]{1\\} & \makecell[l]{1\\} & \makecell[l]{Data\\} & \makecell[l]{Validade muito baixa; registros heterogêneos\\Uso direto arriscado sem reprocessar} & \makecell[l]{Reprocessar e padronizar parse/timestamp\\Validar ranges e bloquear valores inválidos} \
\bottomrule
\end{tabularx}
\end{scriptsize}
```
\clearpage

## **7. Resumo gerencial: viabilidade de modelagem por forno (Nível de Risco)**

Regras de cálculo (determinísticas):
- Classificação por domínio/forno: **Nível 1** quando há timestamp utilizável e nenhum indicador crítico (comp≥0.85, val≥0.90, uniq≥0.90, cons≥0.70). **Nível 2** quando a base é estruturada mas falta padronização temporal ou há lacunas moderadas. **Nível 3** quando completude é muito baixa (<0.30), validade <0.60, timestamp ausente/ambíguo ou o domínio requerido não existe.
- Dependências por tipo de modelo: **Physic-based** = Corridas + Informações Diária; **Supervisionado** = Corridas + Informações Diária + Consumo Fornos; **Não supervisionado** = Supervisório (se existir) senão Consumo Fornos; **Reforço (RL)** = Supervisório obrigatório.
- Aplicação aos domínios: Consumo Fornos = N3; Corridas = N2; Informações Diária = N2; Eletrodo = N2; Supervisório F4 = N2; Supervisório F5 = N3. O nível por forno/modelo é o pior (máximo) entre os domínios requeridos.

| Forno | Physic-based | Supervisionado | Não supervisionado | Reforço (RL) |
|---|---|---|---|---|
| F1 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |
| F2 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |
| F3 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |
| F4 | Nível 2 | Nível 3 | Nível 2 | Nível 2 |
| F5 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |

Legenda: Nível 1 = pronto para uso; Nível 2 = uso com risco moderado (padronização necessária); Nível 3 = alto risco ou ausência de domínio requerido.

## **8. Conclusões e decisões que o Gerente de Produção precisa tomar**
Este diagnóstico permite decisões objetivas sobre priorização, com impacto direto na capacidade de fazer análises e sustentar modelos com credibilidade operacional.

**Decisão 1 — Prioridade de curto prazo (0–4 semanas):** definir quais domínios serão tratados como “base mínima” para o próximo ciclo.  

**Decisão 2 — Padronização mandatória:** estabelecer padrão corporativo simples para **timestamp**, **timezone**, nomes de colunas e unidades. Sem isso, análises temporais ficam expostas a erro de interpretação.  

**Decisão 3 — Correção estruturante (reprocessamento):** aprovar a revisão de rotinas de extração onde a estrutura atual gera risco elevado (parsing, completude e duplicidades).  

**Decisão 4 — Instrumentação do alvo (distância do eletrodo):** como não há medição direta da variável de interesse no acervo, é necessário decidir o caminho: **proxies (curto prazo)** ou **medição/inferência instrumentada (médio prazo)**.

\newpage