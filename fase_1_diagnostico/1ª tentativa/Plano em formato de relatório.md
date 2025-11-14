# Relatório de Completude dos Dados – Etapa 1

> Documento de trabalho para diagnóstico de completude das bases de dados dos fornos elétricos F1 a F5, com foco na modelagem da distância da ponta do eletrodo ao fundo do forno.

---

## 1. Introdução geral do relatório de completude

Nesta etapa do projeto de Transição Digital, o objetivo é produzir um Relatório de Completude dos dados operacionais dos fornos F1 a F5, cobrindo o período aproximado de 2018 a 2025 e as principais famílias de arquivos disponíveis (Informações Diárias, Corridas, Consumo dos Fornos, Supervisórios, Medição de Eletrodos e Dicionários de Dados).

Este relatório encerra a Etapa 1 do diagnóstico definido no 5W2H, fundamentando as fases seguintes de prototipagem preditiva. A ideia central é primeiro compreender em detalhe o que existe, em que condições e com quais lacunas, para só então avançar para o desenvolvimento de modelos.

O resultado final esperado deste documento é duplo:

1. Um grau de completude mensurável para cada segmento de dados relevante ao problema de negócio.
2. Uma avaliação estruturada do que é possível fazer com esses dados, considerando:
   - completude,
   - tipo e granularidade de cada fonte,
   - extensão temporal disponível,
   - exigências de diferentes tipos de estudo (physics based, supervisionados, não supervisionados),

tendo sempre como norte a variável que se deseja modelar: a distância da ponta do eletrodo ao fundo do forno.

---

## 2. Visão conceitual do conjunto de dados

### 2.1 Panorama global (Manifesto de Dados)

Nesta subseção será descrito o panorama global do acervo, incluindo:

- Intervalo temporal coberto pelas bases (datas mínimas e máximas em anos-mês-dia).
- Ordem de grandeza do volume de dados por tipo de arquivo (Informações Diárias, Corridas, Consumo, Supervisório, Medição de Eletrodos).
- Estimativa do volume total de registros no projeto (por forno e no agregado).
- Comentários sobre a adequação desse volume às práticas de Data Science industrial.

Aqui também será resumido o papel do Manifesto de Dados como documento central de referência para:

- Estrutura de diretórios.
- Nomes e tipos de arquivos.
- Relação entre fontes distintas (por exemplo, F4 e F5 com supervisório mais detalhado).

> TODO: Consolidar os números finais de volume por família de dados e registrar aqui em texto corrido.

### 2.2 Partes que compõem o conjunto

Nesta subseção serão descritas conceitualmente as “famílias de dados” que compõem o acervo:

- Informações Diárias (Inf_Diario).
- Corridas.
- Consumo dos Fornos.
- Supervisório F4 e F5.
- Medição de Eletrodo.
- Dicionários de Dados (DIC_*).

Para cada família serão destacadas: granularidade temporal, principais campos, papel operacional no processo do forno e relação potencial com a variável de interesse (distância ponta–fundo).

> TODO: Completar com resumo textual para cada família, a partir dos dicionários e resumos por pasta.

---

## 3. Definição operacional de “grau de completude”

### 3.1 Conceito geral de completude

Será apresentada uma definição operacional de “grau de completude” adaptada ao contexto do projeto:

> Grau de completude de um segmento de dados é o nível em que esse segmento cobre, de forma contínua e com campos essenciais preenchidos, o espaço de observações necessário para suportar a modelagem da variável de interesse (distância ponta–fundo), em um determinado horizonte temporal e para um conjunto de fornos.

Aqui será feita a ligação com os conceitos clássicos de Data Quality (completude, consistência, integridade, atualidade, unicidade), explicando porque, nesta etapa, o foco é principalmente a completude em suas diferentes dimensões.

### 3.2 Dimensões de completude adotadas

Serão estabelecidas quatro dimensões de completude a serem usadas ao longo do relatório:

1. **Cobertura temporal (CT)**  
   Percentual de dias (ou horas/minutos, conforme o caso) com dados disponíveis entre a data mínima e máxima declaradas para o segmento.

2. **Cobertura de equipamentos (CE)**  
   Percentual de fornos efetivamente cobertos por aquela família de dados em determinado período (por exemplo, de F1 a F5).

3. **Completude de campos essenciais (CC)**  
   Percentual de preenchimento dos campos considerados críticos para cada tipo de estudo, definidos a partir dos Dicionários de Dados.

4. **Integridade relacional (IR)**  
   Capacidade dos registros de uma fonte se conectarem, via chaves temporais e de identificação (forno, corrida, etc.), às demais fontes relevantes.

Nesta subseção será definida a forma de cálculo de cada dimensão (por exemplo, fórmulas, critérios de arredondamento, janelas temporais de integração) e a unidade de análise (por forno, por ano, por campanha).

### 3.3 Índice sintético de completude

Será proposta a construção de um índice sintético para cada segmento (por exemplo, por [Forno × Família × Período]), a partir de uma combinação ponderada das quatro dimensões:

- CT – Cobertura temporal.
- CE – Cobertura de equipamentos.
- CC – Completude de campos essenciais.
- IR – Integridade relacional.

Os pesos poderão ser ajustados de acordo com o tipo de estudo:

- Estudos physics based tendem a exigir maior peso para CT e IR em sinais de alta frequência.
- Estudos supervisionados exigem maior peso para CC nos campos que participam como features e target.
- Estudos não supervisionados valorizam CT e CC em variáveis de regime e contexto.

> TODO: Definir explicitamente a fórmula do índice e os pesos adotados, documentando no texto.

---

## 4. Introduções conceituais por parte do conjunto

### 4.1 Informações Diárias (Inf_Diario)

Nesta subseção será apresentada a natureza dos arquivos de Informações Diárias:

- Granularidade: uma linha por forno por dia.
- Principais campos e indicadores.
- Papel na visão de longo prazo (tendências de produção, consumo, fatores de operação).

Será descrita a importância da completude das Informações Diárias para:

- Mapear dias de operação efetiva.
- Identificar períodos prolongados de parada, reforma ou ausência de registro.
- Fornecer contexto diário para os demais estudos (physics based, supervisão, não supervisão).

### 4.2 Corridas

Aqui será descrito o conceito de “corrida” como evento discreto de operação (vazamento de metal/escória), e o tipo de informação tipicamente registrado:

- Data e hora da corrida.
- Forno.
- Massas de metal e escória.
- Composições químicas.
- Tempos característicos e indicadores de rendimento.

Será discutida a relevância da completude das corridas para:

- Estudos de balanço de massa e energia (physics based).
- Cálculo de indicadores de desempenho por corrida.
- Uso de variáveis de saída como labels ou variáveis auxiliares em modelos supervisionados.

### 4.3 Consumo dos Fornos

Nesta subseção será detalhada a família de dados de Consumo dos Fornos:

- Natureza de alta frequência das leituras (minuto ou subminuto).
- Principais sinais: potência ativa, energia consumida, correntes, tensões, etc.
- Papel para análises de regime eletrotérmico.

A análise de completude irá considerar:

- Buracos temporais, trechos sem registro e possíveis problemas de aquisição.
- Comparação entre o esperado (taxa nominal de amostragem) e o observado.
- Particularidades de fornos com registros mais críticos (por exemplo, F3).

### 4.4 Supervisório F4 e F5

Aqui serão descritos os dados supervisórios de F4 e F5:

- Estrutura em arquivos (por semestre, por hora, por minuto).
- Número de tags e tipos de variáveis de automação e controle.
- Problemas já identificados (por exemplo, timestamps zerados, datas de época Unix, etc.).

Será introduzido o papel central desta família de dados para:

- Modelos physics based que dependem de sinais de controle, impedância, posição de eletrodo e estados do sistema.
- Modelos supervisionados que buscam prever a distância ponta–fundo a partir de sinais elétricos e de controle.
- Análises não supervisionadas para identificação de regimes típicos de operação e detecção de anomalias.

### 4.5 Medição de Eletrodo

Nesta subseção será apresentada a base de Medição de Eletrodo:

- Intervalo temporal coberto.
- Número total de medições.
- Campos disponíveis (por exemplo, comprimento do eletrodo, forno, data, contexto da medição).

Será destacada a importância desta base como ponte entre:

- Medidas físicas diretas (ou quase diretas) relacionadas à distância ponta–fundo.
- Sinais elétricos e de processo disponíveis nas bases de Consumo e Supervisório.

> [INFORMAÇÃO AUSENTE – PRECISA PREENCHER]  
> Aqui será necessária a definição formal, junto ao cliente, de como a distância ponta–fundo é medida ou inferida:
> - Se existe uma variável diretamente registrada.
> - Se será inferida a partir do comprimento do eletrodo e da geometria do forno.
> - Se serão usadas outras proxies.

### 4.6 Dicionários de Dados (DIC_*)

Nesta subseção o foco serão os Dicionários de Dados:

- Papel na padronização de nomes, unidades e significados dos campos.
- Uso como fonte principal para definir quais colunas são “essenciais” em cada família.
- Uso para detectar lacunas de implementação (campos definidos, mas pouco usados ou vazios nos CSVs).

Aqui será construída a lista de campos críticos por família e por tipo de estudo (physics based, supervisionado, não supervisionado), que será reutilizada na análise de completude de campos.

---

## 5. Plano de trabalho para análise de completude

### 5.1 Etapa 1 – Preparação e inventário estruturado

Nesta etapa serão executadas as seguintes ações:

- Consolidar o inventário de arquivos a partir de:
  - `index_resumo_por_pasta.csv`.
  - Relatório “resumo por pasta”.
- Para cada ano, identificar quais fornos possuem:
  - Consumo.
  - Corridas.
  - Informações Diárias.
  - Supervisório.
  - Medição de Eletrodo.
- Revisar Dicionários (DIC_*) para:
  - Identificar campos essenciais por tipo de estudo.
  - Marcar campos relacionados direta ou indiretamente à variável distância ponta–fundo.

Saída esperada:

- Tabela de inventário por [Ano × Forno × Família de dados], com indicação de presença/ausência de arquivos.
- Lista preliminar de campos essenciais por família.

### 5.2 Etapa 2 – Análise de completude temporal por segmento

Para cada família de dados, serão calculados indicadores de cobertura temporal:

- Para Inf_Diario e Corridas:
  - Datas mínima e máxima por forno e por ano.
  - Número de dias em operação versus número de dias com registro.
- Para Consumo e Supervisório:
  - Datas mínima e máxima.
  - Estimativa de taxa nominal de amostragem.
  - Comparação entre número esperado e observado de registros.
- Para Medição de Eletrodo:
  - Calendarização das medições por forno e por período.
  - Identificação de janelas com maior densidade de medições.

Saída esperada:

- Gráficos e/ou tabelas de cobertura temporal (CT) por [Forno × Família × Período].
- Identificação de períodos com lacunas relevantes (anos, meses, campanhas).

### 5.3 Etapa 3 – Análise de completude de campos essenciais

Nesta etapa será analisado o preenchimento dos campos essenciais definidos a partir dos Dicionários:

- Para cada família de dados, identificar a lista de colunas críticas.
- Calcular, por [Forno × Período], o percentual de valores não nulos em cada coluna crítica.
- Detectar campos:
  - Muito pouco preenchidos.
  - Com padrões suspeitos (valores constantes, códigos sentinela, etc.).

Saída esperada:

- Matrizes de completude de campos (CC) por família.
- Lista de variáveis com baixa completude, com comentários sobre impacto potencial em cada tipo de estudo.

### 5.4 Etapa 4 – Análise de integridade relacional entre fontes

Nesta etapa será estudada a integridade relacional (IR) entre as famílias:

- Definir chaves de ligação:
  - Forno + Data (Inf_Diario ↔ Corridas/Consumo).
  - Forno + Data/Hora (Corridas ↔ Consumo/Supervisório).
  - Forno + Data + identificador/campanha para Medição de Eletrodo ↔ Consumo/Supervisório.
- Calcular, para cada tipo de ligação:
  - Percentual de dias com Inf_Diario que possuem corridas associadas.
  - Percentual de corridas que podem ser ligadas a janelas de consumo e supervisório.
  - Percentual de registros de Medição de Eletrodo que encontram contexto operacional (Consumo/Supervisório) em uma janela de tempo definida.

> [INFORMAÇÃO AUSENTE – PRECISA PREENCHER]  
> Antes de finalizar esta etapa, será necessário confirmar:
> - Como será representada a variável de interesse (distância ponta–fundo) no dataset final.
> - Quais janelas temporais são consideradas adequadas para associação entre medições e sinais de processo.

Saída esperada:

- Conjunto de indicadores IR por tipo de ligação.
- Identificação de “ilhas de dados” (segmentos que não se conectam a nenhuma outra fonte).

### 5.5 Etapa 5 – Consolidação por tipo de estudo

Nesta etapa o relatório será reorganizado por tipo de estudo, respondendo para cada caso: “Dado o grau de completude, o que é viável fazer hoje?”

#### 5.5.1 Modelos physics based

- Identificar fornos, campanhas e períodos com:
  - Boa cobertura temporal (CT) em Consumo e Supervisório.
  - Bom preenchimento de campos físicos críticos (CC).
  - Boa integridade relacional (IR) com Corridas e Medição de Eletrodo.
- Descrever o tipo de modelo physics based que essas bases suportam (por exemplo, balanço eletrotérmico, modelos de desgaste de eletrodo).

#### 5.5.2 Modelos supervisionados

- Avaliar se há labels ou proxies suficientes para a distância ponta–fundo ao longo do tempo.
- Identificar janelas temporais onde há densidade adequada de:
  - Medições de eletrodo ou proxies.
  - Sinais de processo (Consumo, Supervisório).
  - Dados de contexto (Inf_Diario, Corridas).
- Classificar segmentos em:
  - Apto para treino/teste.
  - Limitado (exige cautela ou complementação).
  - Inadequado (apenas para análises exploratórias).

#### 5.5.3 Modelos não supervisionados

- Identificar conjuntos com boa completude em variáveis de regime (correntes, tensões, produção, consumo).
- Avaliar viabilidade de:
  - Clustering de regimes operacionais.
  - Detecção de anomalias.
  - Identificação de “estados típicos” de operação do forno.

Saída esperada da Etapa 5:

- Tabelas que relacionam, para cada tipo de estudo:
  - Fornos.
  - Períodos.
  - Famílias de dados.
  - Classificação de viabilidade (Apto / Limitado / Inadequado), sempre justificada pelos indicadores CT, CE, CC e IR.

### 5.6 Etapa 6 – Índice final de grau de completude e conclusões

Na etapa final, o relatório deverá:

1. Apresentar o Índice de Grau de Completude por segmento (por exemplo, [Forno × Família × Período]), com:
   - Fórmula utilizada.
   - Pesos futuros por tipo de estudo.
   - Discussão sobre sensibilidade dos resultados a esses pesos.
2. Destacar:
   - Segmentos com completude alta que serão candidatos naturais para os primeiros protótipos de modelos.
   - Segmentos com completude moderada ou baixa, e possíveis ações de melhoria (revisão de coleta, enriquecimento, reconciliação manual).
3. Relacionar explicitamente o impacto das lacunas sobre a capacidade de modelar a distância ponta–fundo, para cada tipo de abordagem:

   - Physics based.
   - Supervisionados.
   - Não supervisionados.

Esta seção deverá fechar o documento com:

- Um resumo executivo do estado atual dos dados.
- Um mapa claro de “onde é possível começar” em termos de modelagem.
- Recomendações iniciais para priorização de esforços nas próximas etapas do projeto.

---

## 6. Próximos passos ligados a este documento

Nesta seção final serão anotados, ao longo do tempo, os marcos de evolução do próprio relatório, por exemplo:

- Data de conclusão de cada etapa de cálculo (CT, CE, CC, IR).
- Alterações na definição de índice de completude.
- Decisões tomadas em conjunto com o cliente sobre a forma final da variável distância ponta–fundo.

> TODO: Atualizar esta seção sempre que o documento for revisado, para manter a historicidade no Obsidian.
