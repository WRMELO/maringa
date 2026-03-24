# Relatório Técnico (Gerencial) — Diagnóstico de Disponibilidade e Qualidade de Dados de Produção
## Maringá Ferro Ligas | Fase 1 — Dados de Produção

**Data:** 2026-01-06  
**Entregável:** Diagnóstico inicial (Fase 1)  
**Pessoa de referência:** Gerente de Produção  
**Empresa:** Maringá Ferro Ligas  
**Elaborado por:** Consultoria de Transição Digital

---

## 1. Contexto, Objetivo e Leitura Recomendada

Este relatório consolida, em linguagem técnica voltada ao público gerencial, o diagnóstico de disponibilidade e qualidade dos dados atualmente disponíveis no acervo de produção da Maringá Ferro Ligas. O objetivo é **reduzir risco de decisão e de interpretação** ao preparar dados para análises e modelagem, evitando que iniciativas de melhoria de processo e automação sejam guiadas por dados incompletos, ambíguos ou estruturalmente inconsistentes.

### Contexto do Projeto de Transição Digital

O projeto em curso é uma iniciativa de **transição digital orientada a dados** aplicada ao contexto industrial da Maringá Ferro Ligas, com foco em transformar dados operacionais e de processo (vindos de sistemas, planilhas e chão de fábrica) em uma base confiável para análises e, em seguida, para o desenvolvimento de modelos preditivos que apoiem a operação.

**O problema não começa no algoritmo; ele começa na realidade das fontes:** dados distribuídos em múltiplos sistemas e formatos, com lacunas, atrasos, inconsistências, duplicidades e diferenças de governança entre áreas.

### Estratégia Adotada pela Consultoria

A estratégia combina **governança de dados** com **engenharia de dados leve** e análise técnica:

1. **Diagnosticar antes de modelar:** qualquer modelo treinado sobre dados inconsistentes vira um "amplificador de erro"
2. **Avaliar qualidade por dimensões reconhecidas:** critérios DAMA de Data Quality
3. **RAW como fonte de verdade:** preservar origem e permitir reprocessamento
4. **Arquitetura portátil e controlada:** Docker/compose para repetibilidade
5. **Fechamento com recomendações acionáveis:** lacunas, riscos e priorização

### Perguntas que Este Relatório Responde

Para o Gerente de Produção, o relatório responde a cinco perguntas práticas:

1. **Quais dados existem**, por domínio e por forno, e qual é a cobertura temporal observada
2. **Qual o nível de qualidade observado**, usando critérios reconhecidos (DAMA)
3. **O que pode ser utilizado agora** e o que requer correção antes de uso analítico
4. **Quais decisões** de padronização, governança e instrumentação elevam rapidamente a confiabilidade
5. **Qual a viabilidade** de cada tipo de modelo (physic-based, supervisionado, não supervisionado, RL) dado o acervo atual

---

## 2. Escopo do Acervo Analisado

O diagnóstico considerou **124 arquivos**, organizados por domínio e, quando aplicável, por forno (F1 a F5). A tabela a seguir resume volume e cobertura temporal observados por domínio.

| Domínio | Arquivos (n) | Linhas totais (aprox.) | Colunas médias | Período coberto |
|---------|-------------|----------------------|---------------|----------------|
| Consumo Fornos | 39 | 38.123.141 | 27.4 | 2018-01-01 a 2025-04-28 |
| Corridas | 40 | 114.949 | 34.1 | 2018-01-01 a 2025-04-29 |
| Eletrodo | 1 | 761 | 5.0 | 2021-01-02 a 2025-12-03 |
| Informações Diária | 40 | 14.232 | 47.2 | 2018-01-01 a 2025-04-29 |
| Supervisorio Forno 4 | 2 | 486.250 | 228.0 | 2023-09-29 a 2025-01-02 |
| Supervisorio Forno 5 | 2 | 242.099 | 25.5 | 2021-01-06 a 2024-12-05 |

**Observação operacional:** os fornos F1 e F2 são reconhecidamente mais antigos, com defasagem tecnológica em relação a F4 e F5. Isso se reflete na variação de estrutura, granularidade e padronização — inclusive dentro do mesmo domínio.

---

## 3. Metodologia: Critérios de Qualidade Adotados (DAMA)

A abordagem adotada pela Consultoria segue as **seis dimensões clássicas de qualidade de dados da DAMA**, tratadas como critérios práticos, cada um com uma forma objetiva de mensuração. Esses critérios não competem entre si: eles se complementam e, na prática, funcionam como um conjunto mínimo para garantir que o dado seja adequado ao uso analítico.

### 3.1 Completude

**O que mede:** Se os campos necessários existem e estão preenchidos quando deveriam estar.

**Por que importa:** Em ambiente industrial, a falta de dados raramente é aleatória: costuma se concentrar em períodos de falha de instrumento, manutenções, mudanças de sistema, viradas de turno, paradas não registradas ou alterações de rotina. Na inspeção inicial dos arquivos, já identificamos sinais de heterogeneidade de formato e de extração, como arquivos que aparentam ter sido exportados com separador inadequado, resultando em uma única coluna "larga" com valores agregados; esse tipo de problema afeta diretamente a completude "real", porque um dado pode existir no arquivo, mas não estar acessível como coluna interpretável.

**Como foi medido:** 
- Por coluna (percentual de valores ausentes)
- Por registro (quantos registros estão completos nos campos críticos)
- Por período (lacunas temporais relevantes)

Para a produção, o resultado é traduzido como: em quais variáveis e em quais intervalos de tempo o dado permite caracterizar o processo, e em quais intervalos a análise fica cega ou enviesada.

### 3.2 Validade

**O que mede:** Se o valor está dentro do domínio esperado e do formato correto.

**Por que importa:** Isso inclui limites físicos (por exemplo, corrente e tensão em faixas coerentes com o equipamento), listas de códigos válidos (motivos de parada, estados operacionais, receitas, identificadores de forno), tipos e unidades (temperatura em °C versus K, consumo em kWh versus MWh) e coerência de timestamp (datas possíveis, sem inversões, sem registros futuros ou duplicação de horários por ajustes de relógio). No contexto dos fornos, validade também cobre a estrutura do registro: se um evento deveria ter início e fim, não é válido aparecer apenas com início; se uma variável deveria ser monotônica em certo trecho de processo, valores que oscilam de forma impossível podem indicar erro de coleta ou de conversão.

**Como foi medido:** Regras de negócio e de engenharia, preferencialmente confirmadas com a operação, para que o indicador final não seja "opinião do analista", mas sim conformidade com regras aceitas pela planta.

### 3.3 Acurácia

**O que mede:** O quanto o dado representa corretamente a realidade.

**Por que importa:** Diferentemente de validade (que trata do "faz sentido"), acurácia trata do "está certo". Em chão de fábrica, medir acurácia exige referência: calibrações, instrumentos redundantes, medições manuais periódicas, padrões de laboratório, reconciliação por balanços (massa e energia) ou comparação com leituras independentes.

**Limitação crítica no projeto:** Como a **distância da ponta do eletrodo não possui medição direta contínua**, isso significa que ela precisará ser inferida por aproximações técnicas, proxies operacionais ou modelos intermediários; e qualquer inferência só é defensável se os dados de base tiverem qualidade mensurável e estável. A acurácia aparecerá em dois níveis: a acurácia dos sinais primários (elétricos, consumos, estados operacionais, medições de insumo e produto) e a acurácia do proxy que a Consultoria propõe construir para representar o comportamento do eletrodo.

**Como foi medido:** A Consultoria adotou uma **proxy de plausibilidade estatística** baseada em taxa de outliers/anomalias. Nesta proxy, menor é melhor. O diagnóstico não afirma "acurácia" sem evidência; quando não houver referência, o que é apresentado é o risco: quais sinais têm maior chance de erro sistemático e quais são os melhores candidatos para compor inferências mais robustas.

### 3.4 Tempestividade (Atualidade)

**O que mede:** Se o dado está disponível no momento correto e se está alinhado ao fenômeno no tempo.

**Por que importa:** Em processos industriais, muitos problemas analíticos surgem não por falta de dado, mas por atraso, desalinhamento de relógios entre sistemas, diferenças de frequência de amostragem e agregações feitas em janelas distintas. Quando se pretende inferir uma variável latente, como o comportamento do eletrodo, esse alinhamento temporal se torna decisivo: um atraso de minutos entre um sinal elétrico e um evento operacional registrado pode inverter causa e efeito e comprometer qualquer modelo.

**Como foi medido:** Presença de timestamp confiável, consistência de frequência, identificação de períodos de parada e de regime estável, e verificação de sincronismo entre fontes (por exemplo, dados de consumo versus diário de operação versus eventos do supervisório). Para a produção, o resultado é expresso como "o quanto dá para confiar na sequência temporal" para explicar o processo e para sustentar modelos que dependem de dinâmica, não apenas de médias.

### 3.5 Unicidade

**O que mede:** Se cada registro representa um único fato, sem duplicidades.

**Por que importa:** Em sistemas industriais, duplicidade costuma aparecer por reprocessamentos de exportação, reenvio de arquivos, regravação após correções manuais, replicação entre camadas de histórico ou ainda por falhas de integração que geram registros repetidos com pequenas diferenças. Duplicidade distorce estatísticas, cria "falsos padrões" e pode dar a impressão de que existe mais evidência do que realmente existe.

**Como foi medido:** Análise por chaves naturais do processo: combinação de forno, timestamp, tag e, quando aplicável, identificador de evento. A entrega para a produção é objetiva: onde há duplicidade, qual o mecanismo provável (exportação, integração, correção posterior) e qual a regra recomendada para deduplicação sem perder informação relevante.

### 3.6 Consistência

**O que mede:** Se os dados "concordam" entre si dentro do mesmo sistema e entre sistemas diferentes.

**Por que importa:** Em uma planta, consistência é o que permite relacionar consumo, produção, estado operacional e eventos sem contradições. Ela inclui consistência semântica (o mesmo código significa a mesma coisa em todos os períodos), consistência de unidade (não alternar escalas), consistência de regras (quando um forno está em parada, certas grandezas deveriam refletir isso) e consistência histórica (mudanças de instrumentação e de critério precisam ser registradas, porque alteram o significado do dado).

A defasagem tecnológica entre fornos mais antigos e mais recentes, já considerada na nossa análise, tende a produzir inconsistências de origem: sensores diferentes, sistemas distintos, lacunas de documentação e mudanças de padrão de registro ao longo do tempo. Por isso, consistência não é tratada como "erro do operador", mas como um diagnóstico de integração e de governança do dado. Para a produção, a utilidade é imediata: inconsistências explicam por que relatórios e análises de áreas diferentes frequentemente não batem, e apontam onde padronizações ou registros de metadados geram ganhos rápidos.

### 3.7 Orientação ao Uso: Qualidade para Famílias de Modelos

Esses seis critérios são aplicados de modo **orientado ao uso**, e não como auditoria genérica. Na prática, a análise da Consultoria produz um quadro de suficiência dos dados para quatro famílias de modelos possíveis no projeto:

- **Modelos physic-based** toleram melhor limitações de volume, mas exigem consistência física e temporal e referências mínimas para calibração
- **Modelos supervisionados** dependem de exemplos independentes e de um proxy confiável para representar o eletrodo
- **Métodos não supervisionados** dependem de estabilidade de "normalidade" e de histórico longo sem mudanças silenciosas
- **Reinforcement learning**, quando considerado, exige dados de decisão e consequência ou ambiente seguro de simulação, o que normalmente eleva a exigência sobre tempestividade e consistência temporal

Assim, o diagnóstico de qualidade não é um fim em si mesmo: ele serve para demonstrar, com evidência, quais caminhos de modelagem são viáveis com os dados atuais, quais dependem de correções ou padronizações e quais exigem mudanças de processo de coleta.

---

## 4. Quantidade de Dados: Contexto Metalúrgico e Dimensionamento

### 4.1 O Desafio da Variável Latente

Em processos metalúrgicos com fornos elétricos de redução, é comum que variáveis fisicamente relevantes para o processo sejam **"latentes"**: existem, influenciam o resultado e a operação, mas **não são medidas de forma direta e contínua** por limitações de instrumentação e pelo ambiente agressivo (temperatura, poeira, vibração, interferência eletromagnética).

No caso específico de fornos de arco submerso, a literatura técnica clássica descreve que mesmo medir grandezas elétricas "próximas do banho" pode ser difícil e que **não há métodos confiáveis para medir continuamente o comprimento/condição do eletrodo** de forma direta, recorrendo-se a técnicas indiretas respaldadas por medições diretas periódicas.

Essa restrição muda completamente a pergunta "quantos dados eu preciso?" porque o que define o tamanho útil do conjunto de dados não é só o volume bruto de registros, mas **o quanto desses registros consegue representar, com qualidade, os estados operacionais relevantes** do processo e os regimes que interessam para cada família de modelo.

### 4.2 O que "Número Mínimo de Dados" Significa na Prática Industrial

Em ambientes industriais, "número de dados" pode significar coisas diferentes, e a definição correta depende do tipo de modelo:

**Para modelos supervisionados:** o "n" relevante é o número de **exemplos rotulados independentes** no nível em que a decisão é tomada (por corrida/fornada, por janela de tempo, por evento operacional). A experiência industrial em aço via BOF mostra explicitamente o efeito do tamanho do dataset (de centenas a dezenas de milhares de heats) na estabilidade e na acurácia, e discute que poucos dados tendem a não capturar relações entre variáveis e a piorar generalização.

**Para modelos não supervisionados:** o "n" é o volume de comportamento "normal" que o algoritmo consegue observar para aprender padrões, sazonalidades e limites de variação sem confundir mudança legítima de regime com anomalia. Um exemplo real em manufatura metalúrgica (aço inox) trabalhou com dados em granularidade de 1 minuto ao longo de meses, com centenas de milhares de registros, justamente para capturar variabilidade suficiente do processo.

**Para reinforcement learning:** o "n" relevante é o número de **interações estado—ação—recompensa** (transições) e, em aplicações metalúrgicas, isso quase sempre é obtido em simuladores/digital twins ou em "ambientes analíticos + RL" por razões de segurança e custo operacional. Um trabalho em laminação (strip rolling) descreve explicitamente um cenário de controle com RL e reporta convergência e resultados após milhares de iterações de otimização.

**Para modelos physic-based:** a questão se desloca para a quantidade de dados necessária para **identificar parâmetros, validar hipóteses** e calibrar relações físico-geométricas. Em fornos de arco submerso, há trabalhos discutindo como grandezas elétricas (como resistência) dependem de geometria e profundidade de imersão do eletrodo, permitindo inferir estados físicos a partir de medições indiretas quando a variável geométrica não está diretamente acessível.

### 4.3 Qualidade de Dado "Consome" Amostra: Tamanho Bruto vs Tamanho Efetivo

Mesmo quando há muito dado disponível, o que determina a qualidade do modelo é o **tamanho efetivo**, isto é, o subconjunto que permanece após aplicar critérios mínimos de qualidade. Um enquadramento amplamente usado para isso é o modelo ISO/IEC 25012, que organiza características de qualidade de dados (incluindo, entre outras, acurácia, completude, consistência e "currentness"/aderência temporal ao contexto de uso).

Na prática deste projeto, a **ausência de medição direta da "distância do eletrodo"** impõe mais um filtro: além de limpar dados faltantes, inconsistentes e desalinhados no tempo, é necessário construir um **"alvo" operacional (proxy)** ou um **rótulo derivado (soft-sensor)** que represente o estado de interesse de modo verificável. Se esse rótulo existir apenas em momentos raros (por exemplo, inspeções, medições manuais ou eventos específicos), o gargalo do tamanho efetivo se torna o número desses eventos, não o número total de linhas no historiador.

### 4.4 Referenciais de Ordem de Grandeza

Para planejamento (instrumentação, rotinas de coleta e priorização de limpeza), a Consultoria consolidou referenciais de ordem de grandeza considerando evidências industriais publicadas em metalurgia. Estes valores devem ser interpretados como **guia prático**, não como regra rígida:

| Tipo de modelo | Escopo típico no projeto | Mínimo viável (ordem de grandeza) | Nível "ótimo" prático |
|---------------|------------------------|--------------------------------|---------------------|
| **Physic-based** | Calibração de parâmetros físicos por regime operacional | 15 corridas/campanhas por regime | 30–40 corridas/campanhas por regime |
| **Supervisionado (regressão)** | Previsão de consumo específico, composição, rendimento | 200–300 corridas por família | 1000–3000 corridas por família |
| **Supervisionado (classificação)** | OK/NOK, regimes instáveis, defeitos | 500–1000 exemplos rotulados | Alguns milhares de exemplos rotulados |
| **Não supervisionado (cluster)** | Descoberta de regimes operacionais | ~10⁴ pontos de série temporal | 10⁵–10⁶ pontos (meses) |
| **Não supervisionado (anomalia)** | Detecção de eventos e padrões anômalos | ~10⁵ pontos | ≥10⁶ pontos (3–12 meses) |
| **RL off-line + modelo** | Otimização de setpoints/decisão com segurança | ~10³ transições por regime | ≥10⁴ transições por regime (+ simulação) |

**Síntese operacional:** O ponto central é que "dados ótimos" não são apenas "muitos dados": são dados suficientes, com qualidade, para cobrir regimes e transições operacionais relevantes e sustentar a construção de um alvo indireto consistente para a variável latente. Sem isso, adicionar volume bruto tende a ampliar ruído e inconsistências; com isso, o aumento de volume se transforma em ganho real de generalização.

---

## 5. Interpretação Gerencial: O que os Dados Permitem (e o que Ainda Impedem)

O diagnóstico evidencia que, em vários domínios, **volume de dados não é o gargalo**. O principal limitador é a **qualidade estrutural e temporal** (padronização de colunas, tipos e timestamp), que aumenta risco de interpretação e dificulta análises temporais confiáveis.

### 5.1 Três Níveis de Uso Prático

De forma gerencial, o uso prático se organiza em três níveis:

**Nível 1 — Utilizável com baixo risco (após ajustes pontuais):**  
Bases com validade/consistência altas e timestamp padronizável, adequadas para séries temporais e correlações operacionais.

**Nível 2 — Utilizável com risco moderado (requer padronização):**  
Bases com boa estrutura geral, mas com lacunas, divergências de tipos/unidades e/ou necessidade de formalizar chaves e coluna temporal.

**Nível 3 — Alto risco (exige reprocessamento antes de uso):**  
Bases com problemas de parsing, estrutura inválida, duplicidades ou completude extremamente baixa. O uso direto tende a produzir **conclusões erradas com "confiança estatística" artificial**.

---

## 6. Quadro Consolidado: Qualidade por Forno e por Domínio

A tabela abaixo apresenta os indicadores de qualidade medidos para cada combinação domínio/forno, acompanhados de interpretação de risco e ações imediatas recomendadas pela Consultoria.

| Domínio | Forno | n | Comp. (%) | Val. (%) | AccP (%) | Temp. (%) | Uniq. (%) | Cons. (%) | Coluna tempo | Interpretação | Ações imediatas |
|---------|-------|---|----------|---------|---------|----------|----------|----------|-------------|---------------|----------------|
| Consumo Fornos | F1 | 7 | 0.8 | 82.4 | 4.8 | n/d | 18.8 | 71.4 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, unicidade, consistência. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Consumo Fornos | F2 | 8 | 0.8 | 82.4 | 4.2 | n/d | 18.6 | 62.5 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, unicidade, consistência. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Consumo Fornos | F3 | 8 | 0.6 | 82.3 | 5.2 | n/d | 17.9 | 50.0 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, unicidade, consistência. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Consumo Fornos | F4 | 8 | 1.2 | 82.6 | 6.3 | n/d | 18.1 | 62.5 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, unicidade, consistência. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Consumo Fornos | F5 | 8 | 1.2 | 82.6 | 6.4 | n/d | 18.2 | 75.0 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, unicidade, consistência. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Corridas | F1 | 8 | 99.3 | 79.9 | 0.0 | n/d | 100.0 | 100.0 | Data_Base | Risco moderado/alto para uso direto em modelagem em função de: validade, tempestividade_indeterminada. | Padronizar parse/tipos/unidades e bloquear valores não parseáveis. |
| Corridas | F2 | 8 | 97.9 | 79.8 | 0.0 | n/d | 100.0 | 62.5 | Data_Base | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, consistência, tempestividade_indeterminada. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Corridas | F3 | 8 | 99.5 | 78.5 | 0.1 | n/d | 100.0 | 100.0 | Data_Base | Risco moderado/alto para uso direto em modelagem em função de: validade, tempestividade_indeterminada. | Padronizar parse/tipos/unidades e bloquear valores não parseáveis. |
| Corridas | F4 | 8 | 99.3 | 78.0 | 0.1 | n/d | 100.0 | 100.0 | Data_Base | Risco moderado/alto para uso direto em modelagem em função de: validade, tempestividade_indeterminada. | Padronizar parse/tipos/unidades e bloquear valores não parseáveis. |
| Corridas | F5 | 8 | 99.2 | 78.0 | 0.1 | n/d | 100.0 | 100.0 | Data_Base | Risco moderado/alto para uso direto em modelagem em função de: validade, tempestividade_indeterminada. | Padronizar parse/tipos/unidades e bloquear valores não parseáveis. |
| Eletrodo | nan | 1 | 100.0 | 79.9 | n/d | 91.4 | 99.6 | 100.0 | Data medição | Risco moderado/alto para uso direto em modelagem em função de: validade, tempestividade, acurácia_indeterminada. | Padronizar parse/tipos/unidades e bloquear valores não parseáveis. |
| Informações Diária | F1 | 8 | 80.8 | 94.2 | n/d | 100.0 | 100.0 | 75.0 | Data_base | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, consistência, acurácia_indeterminada. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Informações Diária | F2 | 8 | 83.9 | 94.3 | n/d | 100.0 | 100.0 | 100.0 | Data_base | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, acurácia_indeterminada. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Informações Diária | F3 | 8 | 83.4 | 92.5 | n/d | 99.9 | 100.0 | 50.0 | Data_base | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, consistência, acurácia_indeterminada. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Informações Diária | F4 | 8 | 73.6 | 91.4 | 0.1 | 100.0 | 100.0 | 50.0 | Data_base | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, consistência. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Informações Diária | F5 | 8 | 76.8 | 91.6 | n/d | 100.0 | 100.0 | 62.5 | Data_base | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, consistência, acurácia_indeterminada. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Supervisorio Forno 4 | F4 | 2 | 90.9 | 99.4 | 3.0 | 100.0 | 100.0 | 100.0 | Timestamp | Risco moderado/alto para uso direto em modelagem em função de: completude, acurácia_proxy_alta. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Supervisorio Forno 5 | nan | 1 | 55.3 | 2.4 | 0.0 | 99.9 | 100.0 | 100.0 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |
| Supervisorio Forno 5 | F5 | 1 | 95.6 | 0.0 | n/d | n/d | 100.0 | 100.0 | Data | Risco moderado/alto para uso direto em modelagem em função de: completude, validade, tempestividade_indeterminada. | Investigar lacunas e padronizar preenchimento/campos obrigatórios. |

**Legenda:**
- **Comp.** = % de valores preenchidos (Completude)
- **Val.** = % de valores parseáveis e dentro do domínio esperado (Validade)
- **AccP** = Acurácia-proxy (taxa de outliers / plausibilidade estatística; menor é melhor)
- **Temp.** = % de registros com timestamp válido e consistente (Tempestividade)
- **Uniq.** = % de chaves/linhas não duplicadas (Unicidade)
- **Cons.** = % de conformidade do schema/colunas ao padrão observado (Consistência)
- **n/d** = não determinado nesta etapa

---

## 7. Matriz de Risco: Viabilidade por Forno e Tipo de Modelo

Com base no diagnóstico de qualidade (Seção 6) e considerando os referenciais de quantidade mínima por tipo de modelo (Seção 4), a Consultoria elaborou uma **matriz de risco de viabilidade** que cruza cada forno com cada família de modelo. Esta matriz sintetiza a capacidade atual do acervo em sustentar cada abordagem de modelagem, considerando tanto qualidade quanto quantidade de dados disponíveis.

| Forno | Physic-based | Supervisionado | Não supervisionado | Reforço (RL) |
|-------|-------------|---------------|-------------------|-------------|
| F1 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |
| F2 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |
| F3 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |
| F4 | Nível 2 | Nível 3 | Nível 2 | Nível 2 |
| F5 | Nível 2 | Nível 3 | Nível 3 | Nível 3 |

**Interpretação da Matriz:**
- **Nível 2 (Risco Moderado):** Requer padronização/correção antes de modelagem, mas viável no curto prazo (4-8 semanas). Priorizar ações de qualidade antes de iniciar.
- **Nível 3 (Risco Alto):** Exige reprocessamento estruturante, instrumentação adicional ou horizonte temporal estendido. Planejamento de médio/longo prazo necessário.

### 7.1 Análise Horizontal (Por Forno)

**Forno F1:** Todos os modelos apresentam limitações significativas devido à defasagem tecnológica e problemas estruturais nos dados (baixa completude, unicidade e consistência). Modelos physic-based são os mais viáveis no curto prazo, pois exigem menor volume de dados e podem trabalhar com regimes específicos bem documentados.

**Forno F2:** Perfil similar ao F1, com defasagem tecnológica impactando qualidade estrutural. Mesma recomendação de iniciar com abordagens physic-based enquanto corrige bases para outras abordagens.

**Forno F3:** Situação intermediária, mas ainda com desafios estruturais importantes. Segue o mesmo padrão de risco dos fornos mais antigos.

**Forno F4:** É o forno com melhor viabilidade geral, especialmente para modelos não supervisionados e RL, devido à qualidade superior dos dados do Supervisório Forno 4 (completude 90.9%, validade 99.4%, tempestividade 100%). Este é o **candidato prioritário** para prototipagem inicial.

**Forno F5:** Embora tenha dados supervisórios disponíveis, apresenta problemas críticos de validade (0.0% em um dos arquivos) e completude variável (55.3% a 95.6%), elevando o risco para a maioria dos modelos.

### 7.2 Análise Vertical (Por Tipo de Modelo)

**Modelos Physic-based (Nível 2 em todos os fornos):**  
Viáveis no curto prazo após padronização mandatória (timestamp, unidades, tipos). Não dependem de grande volume de dados rotulados, apenas de consistência física e temporal. **Recomendação da Consultoria:** iniciar prototipagem com F4, que tem melhor estrutura de dados.

**Modelos Supervisionados (Nível 3 na maioria):**  
Principal gargalo é a **ausência de rótulos** (target) para distância do eletrodo. Mesmo com volume expressivo de corridas (~114.000), falta o alvo operacional. Requer desenvolvimento prévio de proxy confiável. **Crítico:** sem proxy validado, qualquer modelo supervisionado será especulativo.

**Modelos Não Supervisionados:**  
F4 apresenta Nível 2 (viável após correções) devido à qualidade e volume do Supervisório (486.250 registros, granularidade adequada). Demais fornos em Nível 3 devido a problemas de consistência temporal e completude que impedem caracterização confiável de "normalidade".

**Reinforcement Learning:**  
F4 apresenta Nível 2, os demais Nível 3. Principal limitação é **ausência de ambiente de simulação** e de histórico estruturado de decisões-consequências. Mesmo em F4, exige desenvolvimento prévio de digital twin ou ambiente analítico seguro. **Horizonte:** médio/longo prazo (Fase 3 ou posterior).

---

## 8. Conclusões e Decisões que o Gerente de Produção Precisa Tomar

Este diagnóstico permite decisões objetivas sobre priorização, com impacto direto na capacidade de fazer análises e sustentar modelos com credibilidade operacional. A Consultoria recomenda cinco decisões estruturantes:

### Decisão 1 — Prioridade de Curto Prazo (0–4 semanas)

**Definir quais domínios serão tratados como "base mínima" para o próximo ciclo.**

**Recomendação da Consultoria baseada na matriz de risco (Seções 6 e 7):**
- Priorizar **Forno F4** como piloto de prototipagem (melhor qualidade estrutural em todos os indicadores)
- Iniciar com **modelos physic-based** (menor exigência de volume, maior viabilidade imediata)
- Usar **domínio Corridas** como complemento (alta completude 99.3% e unicidade 100%, mas requer padronização de tipos)
- Planejar roadmap específico para F1/F2/F3/F5 após validação de conceito em F4

### Decisão 2 — Padronização Mandatória

**Estabelecer padrão corporativo simples para timestamp, timezone, nomes de colunas e unidades.**

Esta é uma ação de **alto impacto e baixo custo**, que desbloqueia múltiplos domínios simultaneamente:
- Define formato único de timestamp (ISO 8601 recomendado pela Consultoria)
- Estabelece timezone padrão (UTC ou local com documentação clara)
- Padroniza nomenclatura de colunas críticas (ex: "Data_Base" vs "Data_base" vs "Data medição")
- Formaliza unidades de medida por variável (documentação em metadados)

Sem isso, análises temporais ficam expostas a erro de interpretação, e a tempestividade (atualmente "n/d" em 15 das 18 linhas da Seção 6) permanece indeterminada, bloqueando qualquer análise temporal confiável.

### Decisão 3 — Correção Estruturante (Reprocessamento)

**Aprovar a revisão de rotinas de extração onde a estrutura atual gera risco elevado.**

**Focos imediatos identificados pela Consultoria:**
1. **Parsing de campos com separador inadequado** (presente em Consumo Fornos, afeta completude real: 0.6-1.2%)
2. **Deduplicação com regras formalizadas** (unicidade crítica em Consumo Fornos: 17.9-18.8%, ou seja, ~80% de duplicidades)
3. **Completude de campos obrigatórios** por domínio (especialmente Consumo Fornos e Supervisorio Forno 5)
4. **Validação de tipos e bloqueio de valores não parseáveis** (validade ~78-82% em Corridas e Eletrodo, 0-2.4% em Supervisorio Forno 5)

**Prioridade absoluta:** Consumo Fornos (39 arquivos, 38M registros, mas apenas 0.6-1.2% de completude e 17.9-18.8% de unicidade).

### Decisão 4 — Instrumentação do Alvo (Distância do Eletrodo)

**Como não há medição direta da variável de interesse no acervo, é necessário decidir o caminho:**

#### 🅰️ Opção A — Proxies Operacionais (curto prazo)

Inferir comportamento do eletrodo via sinais elétricos existentes:
- Usar grandezas elétricas (resistência, potência, corrente, tensão) como proxies
- Correlacionar com eventos operacionais registrados (slipping, tapping, ajustes)
- Desenvolver soft-sensor baseado em relações físicas conhecidas da literatura metalúrgica

**✅ Vantagens:**
- Viabiliza protótipos imediatos com dados existentes
- Permite validação de conceito sem investimento em instrumentação
- Aproveita histórico longo (2018-2025) para calibração e identificação de regimes

**⚠️ Limitações:**
- Acurácia dependente da qualidade dos sinais primários (que apresentam problemas, vide Seção 6)
- Requer validação com medições manuais/periódicas quando disponíveis
- Incerteza maior que medição direta, especialmente em regimes transitórios
- Não elimina problema de fundo: variável latente continuará latente

#### 🅱️ Opção B — Medição/Inferência Instrumentada (médio prazo)

Adicionar capacidade de medição ou inferência com maior rastreabilidade:
- Instalar sensores adicionais (se tecnicamente viável no ambiente agressivo de temperatura/interferência)
- Implementar sistemas de visão ou ultrassom (dependendo de acessibilidade física)
- Desenvolver soft-sensor calibrado com medições periódicas formalizadas e documentadas

**✅ Vantagens:**
- Maior confiabilidade e rastreabilidade metrológica
- Redução de incerteza em toda a cadeia de modelagem
- Habilita supervisão contínua e alarmes operacionais diretos (valor além da modelagem)
- Cria ativo estratégico de longo prazo para a planta

**⚠️ Limitações:**
- Requer investimento CAPEX e tempo de implementação (estimativa: 6-12 meses)
- Validação em ambiente industrial pode ser complexa (calibração em operação)
- Necessita de manutenção e calibração periódica (cria dependência operacional)
- Risco técnico de viabilidade em ambiente de forno de arco submerso

**➡️ Recomendação da Consultoria:** Adotar abordagem **sequencial e de risco controlado**:
- **Fase 2 (imediata):** Executar Opção A (proxies) para validação de conceito e prototipagem rápida em F4
- **Fase 3 (médio prazo):** Planejar Opção B (instrumentação) para produtização e escala, contingente ao sucesso da Fase 2

Esta abordagem reduz risco técnico, demonstra valor antes de comprometer investimento, e mantém flexibilidade de decisão baseada em evidência.

### Decisão 5 — Governança e Rastreabilidade

**Institucionalizar o acervo RAW e estabelecer rotina de validação de qualidade.**

**Pontos de ação recomendados pela Consultoria para sustentabilidade do projeto:**

1. **Manter repositório RAW** (MinIO) como fonte de verdade imutável:
   - Nenhuma transformação antes de armazenamento
   - Versionamento de extratos recebidos
   - Política de retenção definida (recomendação: 7 anos mínimo)

2. **Implementar pipeline de validação automática:**
   - Executar critérios DAMA em cada ingestão
   - Gerar alertas de qualidade em tempo de carga
   - Dashboard de qualidade para acompanhamento gerencial

3. **Documentar mudanças estruturais** (metadados):
   - Registrar alterações de instrumentação (com datas e justificativas)
   - Documentar recalibrações (incluindo desvios observados)
   - Formalizar mudanças de critérios de registro (ex: novos códigos de parada)

4. **Estabelecer responsáveis** por domínio de dados (ownership):
   - Consumo Fornos: [definir]
   - Corridas: [definir]
   - Supervisório: [definir]
   - Eletrodo: [definir]

5. **Criar rotina de auditoria** trimestral:
   - Re-execução dos 6 critérios DAMA
   - Comparação com baseline (este relatório)
   - Identificação de degradação ou melhoria

Esta decisão garante que melhorias não sejam perdidas, que o projeto evolua sobre base sólida, e que a governança seja institucionalizada (não dependente de pessoas específicas).

---

## 9. Próximos Passos: Roadmap da Fase 2

Com base neste diagnóstico, a Consultoria propõe o seguinte roadmap para a **Fase 2** (Prototipagem e Modelagem):

### Semanas 1-4 (Preparação e Correção Estruturante)
1. **Padronização mandatória** (Decisão 2): timestamp, colunas, unidades
2. **Correção estruturante prioritária** em Consumo Fornos e Corridas:
   - Reprocessamento com parsing correto
   - Deduplicação formalizada
   - Validação de tipos com bloqueio
3. **Validação de qualidade pós-correção** (re-execução dos critérios DAMA)
4. **Estabelecimento de baseline** de qualidade para acompanhamento

### Semanas 5-8 (Desenvolvimento de Proxies e Preparação de Dados)
5. **Desenvolvimento de proxies operacionais** para distância do eletrodo:
   - Análise de correlação entre sinais elétricos e eventos operacionais conhecidos
   - Identificação de relações físicas aplicáveis (literatura + expertise operacional)
   - Implementação de soft-sensor inicial
6. **Validação com operação:**
   - Confronto com medições manuais/periódicas quando disponíveis
   - Validação com conhecimento tácito de operadores experientes
   - Refinamento iterativo
7. **Construção de dataset rotulado** para modelos supervisionados (se proxy validado)

### Semanas 9-12 (Prototipagem de Modelos — Piloto em F4)
8. **Protótipos de modelos physic-based** em F4:
   - Calibração de parâmetros por regime operacional
   - Validação com períodos conhecidos (ex: campanhas específicas)
   - Quantificação de incerteza
9. **Análise não supervisionada** para descoberta de regimes:
   - Clustering de estados operacionais
   - Identificação de padrões anômalos
   - Caracterização de "normalidade" por regime
10. **Avaliação preliminar** de viabilidade de modelos supervisionados:
    - Dependente da qualidade do proxy desenvolvido (semanas 5-8)
    - Análise de quantidade efetiva de exemplos rotulados
    - Decisão go/no-go para continuidade

### Semanas 13-16 (Validação Operacional e Planejamento de Escala)
11. **Validação operacional dos protótipos:**
    - Apresentação de resultados para equipe de produção
    - Coleta de feedback e identificação de ajustes necessários
    - Teste de usabilidade e interpretabilidade
12. **Análise de viabilidade técnico-econômica** da Opção B (instrumentação):
    - Levantamento de alternativas tecnológicas disponíveis
    - Estimativa de CAPEX e OPEX
    - Análise de risco técnico e operacional
    - Recomendação fundamentada
13. **Plano detalhado para Fase 3** (Produtização e Escala):
    - Expansão para F1/F2/F3/F5 (se piloto F4 bem-sucedido)
    - Integração com sistemas operacionais
    - Treinamento de equipes
    - Plano de manutenção e sustentação

**Milestone de decisão crítica (final da semana 12):** Go/no-go para Fase 3 baseado em:
- Qualidade dos protótipos desenvolvidos em F4
- Validação operacional com produção
- Viabilidade de escala para demais fornos

---

## 10. Apêndice A: Referências Técnicas e Metodológicas

Este diagnóstico foi construído com base em:

**Frameworks de Governança e Qualidade:**
- **DAMA-DMBOK (Data Management Body of Knowledge):** Framework de governança e qualidade de dados
- **ISO/IEC 25012:** Modelo de características de qualidade de dados (acurácia, completude, consistência, currentness)

**Literatura Metalúrgica Industrial:**
- Estudos em **BOF steelmaking** sobre efeito do tamanho de dataset (500 a 20.000 heats) em modelos supervisionados
- Datasets industriais em **blast furnaces** (manufatura de aço inox) com granularidade de 1 minuto ao longo de 5 meses (~207.000 registros)
- Trabalhos em **strip rolling** com reinforcement learning (milhares de iterações para convergência)
- Literatura clássica sobre **submerged-arc furnaces** e medição indireta de grandezas do eletrodo

**Experiência da Consultoria:**
- Projetos anteriores em transição digital orientada a dados no setor metalúrgico
- Benchmarks de quantidade mínima de dados por tipo de modelo
- Melhores práticas em governança de dados industriais

---

## 11. Apêndice B: Geração do PDF em Formato Paisagem

Este arquivo foi preparado para conversão via Pandoc/LaTeX em A3 paisagem. Exemplo de comando:

```bash
pandoc Relatorio_Maringa_Fase1_Melhorado.md -o Relatorio_Maringa_Fase1.pdf \
  --from markdown+raw_tex --pdf-engine=xelatex \
  --variable geometry:a3paper \
  --variable geometry:landscape \
  --variable mainfont="DejaVu Sans" \
  --variable fontsize=10pt
```

---

**Documento elaborado por:** Consultoria de Transição Digital  
**Cliente:** Maringá Ferro Ligas  
**Contato da Consultoria:** [email/telefone]  
**Gerente de Projeto:** [Nome]  
**Revisão:** v2.0 (2026-01-06)  
**Próxima revisão:** Após fechamento da Fase 1 e início da Fase 2

---

**Assinaturas:**

_______________________________  
Consultoria de Transição Digital  
Responsável Técnico

_______________________________  
Maringá Ferro Ligas  
Gerente de Produção