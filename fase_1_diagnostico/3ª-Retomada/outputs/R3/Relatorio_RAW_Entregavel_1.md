# Relatorio RAW - Entregavel 1

Geracao: 2026-01-06_072146

## Resumo do projeto

## Projeto: Transição Digital orientada a Dados para Maringá Ferro Ligas

O projeto em curso é uma iniciativa de transição digital aplicada ao contexto industrial da Maringá Ferro Ligas, com foco em transformar dados operacionais e de processo (vindos de sistemas, planilhas e chão de fábrica) em uma base confiável para análises e, em seguida, para o desenvolvimento de modelos preditivos que apoiem a operação. A lógica central é simples: antes de discutir modelos, precisamos garantir que os dados existentes são utilizáveis, rastreáveis e representativos do processo real.

## Qual é o problema que estamos endereçando

O problema não começa no algoritmo; ele começa na realidade das fontes: dados distribuídos em múltiplos sistemas e formatos, com lacunas, atrasos, inconsistências, duplicidades e diferenças de governança entre áreas. Por isso, o objetivo imediato do projeto é executar um diagnóstico estruturado do “existente” (fontes, disponibilidade e qualidade), produzir um relatório de lacunas e riscos, e somente então avançar para protótipos e modelos.

## Como estamos nos preparando para solucioná-lo

A preparação está sendo feita em duas frentes, em paralelo:

1. **Metodologia de diagnóstico (processo):** seguimos um plano de trabalho em etapas (planejamento e alinhamento; mapeamento e coleta; análise de qualidade; diagnóstico de riscos; relatório e recomendações), com responsabilidades claras entre empresa e consultoria, e com execução organizada por semanas do projeto.
    
2. **Infraestrutura de trabalho (ambiente técnico):** estamos estruturando um “laboratório” reprodutível para ingestão e análise, baseado em contêineres Docker, com um repositório RAW em MinIO para armazenar os arquivos brutos (CSV/Parquet) sem alteração, e um ambiente Python/Jupyter para rodar análises de qualidade e protótipos. Isso reduz dependência do ambiente do cliente, mantém rastreabilidade do dado original e facilita reprocessamentos futuros.
    

## Estratégia que estamos usando (o “como” do projeto)

A estratégia adotada combina governança de dados com engenharia de dados leve e análise técnica:

- **Diagnosticar antes de modelar:** a primeira entrega relevante é o diagnóstico de qualidade e disponibilidade, porque qualquer modelo treinado sobre dados inconsistentes vira um “amplificador de erro”.
    
- **Avaliar qualidade por dimensões reconhecidas:** a análise considera dimensões clássicas de Data Quality (ex.: completude, acurácia, consistência, integridade, atualidade, unicidade e relevância) e começa por uma janela de histórico representativa (por exemplo, meses recentes), antes de escalar para todo o acervo.
    
- **RAW como fonte de verdade e reprocessamento:** os extratos recebidos são armazenados no MinIO “como vieram”, preservando a origem e permitindo repetir a limpeza/transformação sempre que uma abordagem melhor for definida.
    
- **Arquitetura portátil e controlada:** Docker/compose organiza os serviços (MinIO, Jupyter e, se necessário, um banco para metadados) em rede interna, garantindo repetibilidade e facilidade de evolução do pipeline.
    
- **Fechamento da Fase 1 com recomendações acionáveis:** o diagnóstico culmina em relatório com lacunas, riscos e priorização de correções, servindo de base para a fase seguinte de prototipagem e modelagem.
    



## Discussao sobre qualidade de dados


## Critérios de qualidade de dados (DAMA) adotados no diagnóstico do sistema de produção

Este capítulo tem como objetivo apresentar, em linguagem direta, quais critérios de qualidade de dados serão adotados na análise dos dados originários do sistema de produção e por que esses critérios são determinantes para que qualquer estudo de ciência de dados produza resultados confiáveis e utilizáveis pela operação. O ponto central é que não estamos avaliando “qualidade” como um conceito abstrato, mas sim a capacidade de os dados representarem o processo real, de forma consistente, ao longo do tempo, e com rastreabilidade suficiente para sustentar decisões técnicas. No contexto específico dos fornos, isso é ainda mais importante porque a variável de interesse do projeto, a distância da ponta do eletrodo, não possui medição direta contínua. Isso significa que ela precisará ser inferida por aproximações técnicas, proxies operacionais ou modelos intermediários; e qualquer inferência só é defensável se os dados de base tiverem qualidade mensurável e estável.

A abordagem adotada segue as seis dimensões clássicas de qualidade de dados da DAMA, tratadas aqui como critérios práticos, cada um com uma forma objetiva de mensuração e com exemplos ligados ao ambiente de produção. Essas dimensões não competem entre si: elas se complementam e, na prática, funcionam como um conjunto mínimo para garantir que o dado seja adequado ao uso analítico. Para o gerente de produção, a leitura mais útil é entender que cada dimensão responde a uma pergunta simples: “o dado está presente?”, “o valor faz sentido?”, “o valor está correto?”, “o dado chega no tempo certo?”, “há duplicidade?”, “o dado concorda com o restante do sistema?”. A análise do diagnóstico, ao final, transforma essas perguntas em indicadores e evidências, apontando onde a qualidade é suficiente para modelagem e onde há risco operacional ou analítico.

O primeiro critério é a completude. Completude mede se os campos necessários existem e estão preenchidos quando deveriam estar. Em ambiente industrial, a falta de dados raramente é aleatória: costuma se concentrar em períodos de falha de instrumento, manutenções, mudanças de sistema, viradas de turno, paradas não registradas ou alterações de rotina. Na inspeção inicial dos arquivos, já vimos sinais de heterogeneidade de formato e de extração, como arquivos que aparentam ter sido exportados com separador inadequado, resultando em uma única coluna “larga” com valores agregados; esse tipo de problema afeta diretamente a completude “real”, porque um dado pode existir no arquivo, mas não estar acessível como coluna interpretável. No diagnóstico, completude será medida em níveis diferentes: por coluna (percentual de valores ausentes), por registro (quantos registros estão completos nos campos críticos) e por período (existem lacunas temporais relevantes). Para a produção, o resultado será traduzido como: em quais variáveis e em quais intervalos de tempo o dado permite caracterizar o processo, e em quais intervalos a análise fica cega ou enviesada.

O segundo critério é a validade. Validade mede se o valor está dentro do domínio esperado e do formato correto. Isso inclui limites físicos (por exemplo, corrente e tensão em faixas coerentes com o equipamento), listas de códigos válidos (motivos de parada, estados operacionais, receitas, identificadores de forno), tipos e unidades (temperatura em °C versus K, consumo em kWh versus MWh) e coerência de timestamp (datas possíveis, sem inversões, sem registros futuros ou duplicação de horários por ajustes de relógio). No contexto dos fornos, validade também cobre a estrutura do registro: se um evento deveria ter início e fim, não é válido aparecer apenas com início; se uma variável deveria ser monotônica em certo trecho de processo, valores que oscilam de forma impossível podem indicar erro de coleta ou de conversão. No diagnóstico, validade será materializada como regras de negócio e de engenharia, preferencialmente confirmadas com a operação, para que o indicador final não seja “opinião do analista”, mas sim conformidade com regras aceitas pela planta.

O terceiro critério é a acurácia. Acurácia mede o quanto o dado representa corretamente a realidade. Diferentemente de validade, que trata do “faz sentido”, acurácia trata do “está certo”. Em chão de fábrica, medir acurácia exige referência: calibrações, instrumentos redundantes, medições manuais periódicas, padrões de laboratório, reconciliação por balanços (massa e energia) ou comparação com leituras independentes. Como a distância da ponta do eletrodo não é medida diretamente, a acurácia aparecerá em dois níveis: a acurácia dos sinais primários (elétricos, consumos, estados operacionais, medições de insumo e produto) e a acurácia do proxy que pretendemos construir para representar o comportamento do eletrodo. O diagnóstico não vai “afirmar acurácia” sem evidência; quando não houver referência, o que será apresentado é o risco: quais sinais têm maior chance de erro sistemático e quais são os melhores candidatos para compor inferências mais robustas. Para a produção, esse ponto é essencial porque separa duas situações que parecem iguais no gráfico, mas não são: o dado pode estar “bonito” e ainda assim estar errado por drift de sensor, erro de escala, alteração de tag no supervisório ou mudança de critério de registro ao longo dos anos.

O quarto critério é a tempestividade, também descrita como atualidade. Tempestividade mede se o dado está disponível no momento correto e se está alinhado ao fenômeno no tempo. Em processos industriais, muitos problemas analíticos surgem não por falta de dado, mas por atraso, desalinhamento de relógios entre sistemas, diferenças de frequência de amostragem e agregações feitas em janelas distintas. Quando se pretende inferir uma variável latente, como o comportamento do eletrodo, esse alinhamento temporal se torna decisivo: um atraso de minutos entre um sinal elétrico e um evento operacional registrado pode inverter causa e efeito e comprometer qualquer modelo. No diagnóstico, a tempestividade será tratada como qualidade temporal: presença de timestamp confiável, consistência de frequência, identificação de períodos de parada e de regime estável, e verificação de sincronismo entre fontes (por exemplo, dados de consumo versus diário de operação versus eventos do supervisório). Para a produção, o resultado será expresso como “o quanto dá para confiar na sequência temporal” para explicar o processo e para sustentar modelos que dependem de dinâmica, não apenas de médias.

O quinto critério é a unicidade. Unicidade mede se cada registro representa um único fato, sem duplicidades. Em sistemas industriais, duplicidade costuma aparecer por reprocessamentos de exportação, reenvio de arquivos, regravação após correções manuais, replicação entre camadas de histórico ou ainda por falhas de integração que geram registros repetidos com pequenas diferenças. Duplicidade distorce estatísticas, cria “falsos padrões” e pode dar a impressão de que existe mais evidência do que realmente existe. No diagnóstico, unicidade será analisada por chaves naturais do processo, como combinação de forno, timestamp, tag e, quando aplicável, identificador de evento. A entrega para a produção será objetiva: onde há duplicidade, qual o mecanismo provável (exportação, integração, correção posterior) e qual a regra recomendada para deduplicação sem perder informação relevante.

O sexto critério é a consistência. Consistência mede se os dados “concordam” entre si dentro do mesmo sistema e entre sistemas diferentes. Em uma planta, consistência é o que permite relacionar consumo, produção, estado operacional e eventos sem contradições. Ela inclui consistência semântica (o mesmo código significa a mesma coisa em todos os períodos), consistência de unidade (não alternar escalas), consistência de regras (quando um forno está em parada, certas grandezas deveriam refletir isso) e consistência histórica (mudanças de instrumentação e de critério precisam ser registradas, porque alteram o significado do dado). A defasagem tecnológica entre fornos mais antigos e mais recentes, já considerada na nossa análise, tende a produzir inconsistências de origem: sensores diferentes, sistemas distintos, lacunas de documentação e mudanças de padrão de registro ao longo do tempo. Por isso, consistência não será tratada como “erro do operador”, mas como um diagnóstico de integração e de governança do dado. Para a produção, a utilidade é imediata: inconsistências explicam por que relatórios e análises de áreas diferentes frequentemente não batem, e apontam onde padronizações ou registros de metadados geram ganhos rápidos.

Esses seis critérios serão aplicados de modo orientado ao uso, e não como auditoria genérica. Na prática, a análise produzirá um quadro de suficiência dos dados para quatro famílias de modelos possíveis no projeto. Modelos physic-based toleram melhor limitações de volume, mas exigem consistência física e temporal e referências mínimas para calibração; modelos supervisionados dependem de exemplos independentes e de um proxy confiável para representar o eletrodo; métodos não supervisionados dependem de estabilidade de “normalidade” e de histórico longo sem mudanças silenciosas; e reinforcement learning, quando considerado, exige dados de decisão e consequência ou ambiente seguro de simulação, o que normalmente eleva a exigência sobre tempestividade e consistência temporal. Assim, o diagnóstico de qualidade não será um fim em si mesmo: ele servirá para demonstrar, com evidência, quais caminhos de modelagem são viáveis com os dados atuais, quais dependem de correções ou padronizações e quais exigem mudanças de processo de coleta.

Ao final, o gerente de produção receberá um quadro consolidado, por forno e por conjunto de variáveis, contendo os indicadores de completude, validade, acurácia, tempestividade, unicidade e consistência, além de uma interpretação operacional do que esses indicadores significam: onde o dado é suficientemente confiável para análises e modelagem, onde há risco de interpretação, e quais ações são recomendadas para elevar a qualidade de forma prática. Esse tipo de entrega tem um propósito específico: alinhar expectativas e reduzir retrabalho. Quando a operação entende quais critérios serão usados e por quê, as discussões deixam de ser subjetivas e passam a se apoiar em evidências, facilitando priorização de correções e garantindo que, quando um modelo for proposto, ele esteja ancorado em dados que representam o processo real e não apenas um reflexo imperfeito do sistema de registro.

## Discussao sobre quantidade de dados

## Dimensionamento mínimo de dados para modelos em metalurgia quando a variável de interesse não é medida diretamente

Em processos metalúrgicos com fornos elétricos de redução, é comum que variáveis fisicamente relevantes para o processo sejam “latentes”: existem, influenciam o resultado e a operação, mas não são medidas de forma direta e contínua por limitações de instrumentação e pelo ambiente agressivo (temperatura, poeira, vibração, interferência eletromagnética). No caso específico de fornos de arco submerso, a literatura técnica clássica descreve que mesmo medir grandezas elétricas “próximas do banho” pode ser difícil e que não há métodos confiáveis para medir continuamente o comprimento/condição do eletrodo de forma direta, recorrendo-se a técnicas indiretas respaldadas por medições diretas periódicas. ([OpenUCT](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf "An Analysis of the Electrical Circuit of Submerged-Arc Furnaces"))

Essa restrição muda completamente a pergunta “quantos dados eu preciso?” porque o que define o tamanho útil do conjunto de dados não é só o volume bruto de registros, mas o quanto desses registros consegue representar, com qualidade, os estados operacionais relevantes do processo e os regimes que interessam para cada família de modelo (physic-based, supervisionado, não supervisionado e reinforcement learning). Em metalurgia, isso costuma significar capturar variações de matéria-prima, campanhas, intervenções operacionais, alterações de setpoint, fases do ciclo (por exemplo, pré-aquecimento, regime estável, tapping) e perturbações que fazem o processo “visitar” regiões distintas do espaço de estados.

### O que “número mínimo de dados” significa na prática industrial

Em ambientes industriais, “número de dados” pode significar coisas diferentes, e a definição correta depende do tipo de modelo:

Para modelos supervisionados, o “n” relevante é o número de exemplos rotulados independentes no nível em que a decisão é tomada (por corrida/fornada, por janela de tempo, por evento operacional). A experiência industrial em aço via BOF mostra explicitamente o efeito do tamanho do dataset (de centenas a dezenas de milhares de heats) na estabilidade e na acurácia, e discute que poucos dados tendem a não capturar relações entre variáveis e a piorar generalização.

Para modelos não supervisionados, o “n” é o volume de comportamento “normal” que o algoritmo consegue observar para aprender padrões, sazonalidades e limites de variação sem confundir mudança legítima de regime com anomalia. Um exemplo real em manufatura metalúrgica (aço inox) trabalhou com dados em granularidade de 1 minuto ao longo de meses, com centenas de milhares de registros, justamente para capturar variabilidade suficiente do processo. ([Mendeley Data](https://data.mendeley.com/datasets/6d7jbc7tb5/1 "Process data collected by blast-furnace sensors - Mendeley Data"))

Para reinforcement learning, o “n” relevante é o número de interações estado–ação–recompensa (transições) e, em aplicações metalúrgicas, isso quase sempre é obtido em simuladores/digital twins ou em “ambientes analíticos + RL” por razões de segurança e custo operacional. Um trabalho em laminação (strip rolling) descreve explicitamente um cenário de controle com RL e reporta convergência e resultados após milhares de iterações de otimização. ([Springer Nature](https://link.springer.com/article/10.1007/s10845-023-02115-2 "Coupling of an analytical rolling model and reinforcement learning to design pass schedules: towards properties controlled hot rolling | Journal of Intelligent Manufacturing"))

Para modelos physic-based, a questão se desloca para a quantidade de dados necessária para identificar parâmetros, validar hipóteses e calibrar relações físico-geométricas. Em fornos de arco submerso, há trabalhos discutindo como grandezas elétricas (como resistência) dependem de geometria e profundidade de imersão do eletrodo, permitindo inferir estados físicos a partir de medições indiretas quando a variável geométrica não está diretamente acessível.

### Qualidade de dado “consome” amostra: tamanho bruto vs tamanho efetivo

Mesmo quando há muito dado disponível, o que determina a qualidade do modelo é o tamanho efetivo, isto é, o subconjunto que permanece após aplicar critérios mínimos de qualidade. Um enquadramento amplamente usado para isso é o modelo ISO/IEC 25012, que organiza características de qualidade de dados (incluindo, entre outras, acurácia, completude, consistência e “currentness”/aderência temporal ao contexto de uso). ([ISO 25000](https://iso25000.com/index.php/en/iso-25000-standards/iso-25012?utm_source=chatgpt.com "ISO/IEC 25012"))

Na prática do seu projeto, a ausência de medição direta da “distância do eletrodo” impõe mais um filtro: além de limpar dados faltantes, inconsistentes e desalinhados no tempo, é necessário construir um “alvo” operacional (proxy) ou um rótulo derivado (soft-sensor) que represente o estado de interesse de modo verificável. Se esse rótulo existir apenas em momentos raros (por exemplo, inspeções, medições manuais ou eventos específicos), o gargalo do tamanho efetivo se torna o número desses eventos, não o número total de linhas no historiador.

### Modelos physic-based: poucos dados rotulados, muitos dados para calibração e validação

Em metalurgia, modelos physic-based tendem a ser viáveis com menos “amostras rotuladas” porque usam estrutura causal e balanços físicos; porém, exigem dados suficientes para estimar parâmetros e validar hipóteses em diferentes regimes. Em fornos de arco submerso, a literatura técnica descreve relações entre resistência elétrica, geometria e profundidade de imersão do eletrodo, além de discutir estratégias de inferência indireta quando medições diretas contínuas não existem ou são inviáveis.

A implicação para “mínimo de dados” aqui é: o conjunto mínimo viável não é definido por “quantas linhas”, mas por “quantos regimes e perturbações” estão representados com medições elétricas confiáveis. Em termos industriais, isso normalmente significa capturar um horizonte que contenha variações operacionais reais (trocas de carga, alterações de setpoint, eventos de slipping, tapping e períodos de instabilidade). A própria literatura clássica registra esforços de instrumentação e análise ao longo de anos para entender e modelar o circuito elétrico e derivar grandezas indiretamente, justamente porque campanhas longas capturam mais diversidade de estados e permitem reduzir dependência de medições diretas frequentes. ([OpenUCT](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf "An Analysis of the Electrical Circuit of Submerged-Arc Furnaces"))

### Modelos supervisionados: o gargalo é o número de “exemplos independentes” no nível da decisão

Em modelos supervisionados, é tentador contar amostras em alta frequência (1 s, 1 min), mas, para qualidade estatística, o que conta é o número de exemplos independentes no nível em que o processo muda de estado de forma relevante. Em BOF steelmaking, um estudo com dataset industrial “acima de 20.000 heats” avaliou explicitamente o impacto do tamanho do dataset, variando de 500 a 20.000 heats, e relata melhora consistente de desempenho e estabilidade conforme o conjunto cresce, além de redução de overfitting em modelos de boosting com mais dados.

A lição para o seu caso (sem “distância do eletrodo” medida) é que um supervisionado só terá “n” significativo se o projeto conseguir definir rótulos consistentes para estados associados à posição do eletrodo ou às consequências dela. Quando isso é feito via proxy, o “exemplo” muitas vezes é uma janela (por exemplo, 5–30 min) agregada em torno de eventos operacionais ou em regime estável, e não cada linha do historiador. Sem esse cuidado, o modelo “vê” milhares de linhas altamente autocorrelacionadas e o ganho real de informação é pequeno.

### Modelos não supervisionados: precisam de meses para aprender “normalidade” com variação real

Em detecção de anomalias e segmentação de regimes, a exigência central é observar variação suficiente do comportamento normal para não “rotular como anomalia” mudanças legítimas de campanha, matéria-prima ou modo de operação. Em uma experiência real de manufatura de aço inox (caso industrial), foi usado um conjunto com granularidade de 1 minuto, ao longo de 5 meses, totalizando 207.305 registros, como base para análises e métodos não supervisionados. ([Mendeley Data](https://data.mendeley.com/datasets/6d7jbc7tb5/1 "Process data collected by blast-furnace sensors - Mendeley Data"))

Esse tipo de ordem de grandeza é coerente com processos metalúrgicos em que a variabilidade de “normalidade” não é capturada em poucos dias: há ciclos semanais, intervenções e mudanças graduais que só aparecem ao longo de semanas e meses. Portanto, para não supervisionados, o mínimo viável costuma ser “tempo suficiente para cobrir variação normal” (e não apenas um volume arbitrário de linhas), e o ótimo aparece quando o conjunto já contém múltiplas ocorrências de cada regime operacional relevante.

### Reinforcement learning: em metalurgia, “dados” quase sempre vêm de simuladores e iterações

Reinforcement learning adiciona uma camada de exigência: além de observar estados, é preciso observar consequências de ações (ou simular essas consequências). Em metalurgia, isso costuma ser feito com simuladores/digital twins, ou com acoplamento a modelos analíticos, para evitar exploração insegura no ativo real. Um estudo em strip rolling relata um esquema de “analytical rolling + RL” e descreve resultados após 10.000 iterações, ilustrando que o “n” operacional de RL é, em grande parte, número de interações/iterações no ambiente de treino. ([Springer Nature](https://link.springer.com/article/10.1007/s10845-023-02115-2 "Coupling of an analytical rolling model and reinforcement learning to design pass schedules: towards properties controlled hot rolling | Journal of Intelligent Manufacturing"))

No seu projeto, essa constatação tem um efeito direto: sem uma simulação minimamente representativa do processo (ou sem histórico rico de decisões e consequências), RL tende a ficar limitado a abordagens offline muito restritas ou a uma etapa posterior do programa, quando já existir um ambiente de treino seguro.

### Síntese numérica para o projeto: mínimos viáveis e ótimos como ordens de grandeza

Como referência prática, coerente com evidências industriais publicadas (e assumindo que a “distância do eletrodo” não é medida diretamente e precisará ser representada por proxy/soft-sensor), os patamares abaixo devem ser lidos como ordens de grandeza e não como “lei”, porque dependem do número de regimes, da taxa de amostragem e da estabilidade dos sensores.

Para modelos physic-based, mínimo viável costuma ser um horizonte que capture diversidade operacional suficiente para calibrar e validar relações indiretas (tipicamente meses), enquanto o ótimo tende a estar em campanhas longas (anos), porque a literatura mostra esforços plurianuais de instrumentação e modelagem para inferir grandezas ligadas ao eletrodo e ao circuito em fornos de arco submerso. ([OpenUCT](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf "An Analysis of the Electrical Circuit of Submerged-Arc Furnaces"))

Para modelos supervisionados, um mínimo viável industrialmente documentado está na ordem de 500 exemplos independentes no nível do processo (heats/operações), e o ótimo se desloca para dezenas de milhares quando se busca robustez e estabilidade, como explicitado no caso BOF com variação de 500 a 20.000 heats e ganhos claros com o aumento do dataset.

Para modelos não supervisionados, mínimos viáveis tendem a ser semanas a poucos meses de operação contínua (para cobrir variação normal), e um patamar ótimo frequentemente está em vários meses (ou mais), com centenas de milhares de registros em granularidade de minuto, como no caso industrial de aço inox com 5 meses e 207.305 registros. ([Mendeley Data](https://data.mendeley.com/datasets/6d7jbc7tb5/1 "Process data collected by blast-furnace sensors - Mendeley Data"))

Para reinforcement learning, o mínimo viável depende do acesso a simulador/digital twin: números na ordem de 10.000 interações/iterações já aparecem em estudo metalúrgico de laminação com RL, enquanto patamares ótimos, quando se busca política estável em múltiplos regimes, costumam exigir múltiplos desse número (e são fortemente sensíveis à fidelidade do ambiente). ([Springer Nature](https://link.springer.com/article/10.1007/s10845-023-02115-2 "Coupling of an analytical rolling model and reinforcement learning to design pass schedules: towards properties controlled hot rolling | Journal of Intelligent Manufacturing"))

O ponto central, para fechar o capítulo no contexto do seu projeto, é que “dados ótimos” não são apenas “muitos dados”: são dados suficientes, com qualidade, para cobrir regimes e transições operacionais relevantes e sustentar a construção de um alvo indireto consistente para a variável latente. Sem isso, adicionar volume bruto tende a ampliar ruído e inconsistências; com isso, o aumento de volume se transforma em ganho real de generalização, exatamente como observado nos estudos industriais que quantificam o efeito do tamanho do dataset.

## Quantidade de dados - uma visao preliminar

modelos
Modelos físico‑informados e de aprendizado em metalurgia conseguem boa qualidade mesmo em cenários de “small data”, desde que o projeto trate de forma rigorosa a governança, a qualidade e a representatividade dos dados de processo. No contexto da Maringá Ferro Ligas – sem medição direta da distância do eletrodo em fornos de ferro‑liga – isso exige combinar modelos **physic‑based** de forno/submerged arc furnace, dados operacionais consolidados (RAW) e técnicas de ML/RL amostralmente eficientes.[sciencedirect+10](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)

Para a realidade da Maringá Ferro Ligas, o “número mínimo ótimo” de dados não é um valor fixo, mas um patamar por tipo de modelo que torna o ganho marginal de coletar mais dados pequeno em relação ao custo, desde que os dados passem pelo diagnóstico e estejam bem governados. Em metalurgia, casos industriais em aço mostram que resultados robustos vêm de combinar **qualidade** de dados (curados, rastreáveis, contextualizados) com tamanhos de amostra que variam de dezenas (modelos físicos) a milhões de registros de sensores (não supervisionado / RL).[](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​​​

---

## 1. Contexto: dados, eletrodos e fornos de ferro‑liga

Submerged arc furnaces (SAF) e fornos elétricos de ferro‑liga são sistemas altamente não lineares, onde a distância/imersão do eletrodo afeta a distribuição de corrente, o aquecimento Joule, a penetração de arco e a cinética de fusão e redução. Medições diretas e contínuas da posição do eletrodo em relação à carga são, em geral, inviáveis devido ao ambiente hostil (altas temperaturas, poeira, escória, radiação), e a operação depende de medições indiretas (tensão, corrente, potência, impedância, temperatura, composição de gás) e de modelos elétricos/magnético‑térmicos.[royalsocietypublishing+6](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)​

No projeto de transição digital, isso se traduz em três desafios:

- Diagnosticar a qualidade e a disponibilidade dos dados históricos de operação do forno (corridas, campanhas, eventos).[academic.oup+3](https://academic.oup.com/nsr/article/10/7/nwad125/7147579)​
    
- Unificar esses dados em um repositório RAW rastreável (MinIO) e replicável (Docker) para análises de qualidade, features e protótipos.
    
- Definir “quanto de dado é suficiente” para cada tipo de modelo (physic‑based, supervisionado, não supervisionado e RL) diante da falta de medição direta da distância do eletrodo.[tohoku.elsevierpure+4](https://tohoku.elsevierpure.com/en/publications/machine-learning-strategies-for-small-sample-size-in-materials-sc/)​
    

---

## 2. Modelos physic‑based para fornos de ferro‑liga

Modelos physic‑based (primeiros princípios) descrevem balanços de massa e energia, escoamento de fluidos, campos eletromagnéticos e transferência de calor em SAF e EAF, e são amplamente usados para analisar eletrodos, escória e eficiência energética em ferro‑ligas. Esses modelos permitem:[academia+7](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)​

- Representar explicitamente a geometria do forno, o banho de escória, a imersão do eletrodo e o campo eletromagnético, mesmo sem medir diretamente a distância em operação.[pmc.ncbi.nlm.nih+3](https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/)​
    
- Estudar sensibilidade do processo à profundidade do eletrodo, condutividade elétrica/térmica da escória, composição de carga e regime de potência.[imim+5](https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf)​
    

Em vários estudos de CFD e MHD de SAF em ferroníquel e ferro‑silício, a calibração se baseia em um conjunto limitado de medidas industriais: temperaturas pontuais, balanços de energia, medições de tensão/corrente, análises de escória/metal e poucas campanhas instrumentadas.[ieeexplore.ieee+6](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)​

**Requisitos de dados para calibrar modelos physic‑based (no cenário Maringá)**

Mesmo sem medir a distância do eletrodo, é possível calibrar modelos que “fecham” a física usando dados indiretos:

- Entradas por corrida ou janela operacional:
    
    - Potência ativa e reativa, tensão, corrente, fator de potência, ajustes de eletrodo (subida/descida, consumo de eletrodo).
        
    - Composição de carga (minério, redutor, fundentes, retorno), peso e sequência de carregamento.
        
    - Temperaturas: medições pontuais em gás ou escória (quando existirem), temperaturas em pontos de casco, dados de sistema de refrigeração.[sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​
        
- Saídas:
    
    - Composição química do metal/ligas, rendimento, consumo específico de energia, taxa de produção, eventualmente emissões gasosas.[royalsocietypublishing+5](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)​
        

Estudos em fornos de ferro‑liga e EAF típicos mostram que:

- 10–30 condições/momentos experimentais bem caracterizados são usados para ajustar parâmetros como condutividade da escória, coeficientes de troca térmica e perfis de perda de calor.[nature+4](https://www.nature.com/articles/s41598-021-96085-1)​
    
- Mais 5–10 casos independentes são reservados para validação cruzada dos resultados (por exemplo, comparar perfis previstos de temperatura/energia com dados industriais).[academia+2](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)​
    

**Resumo numérico – physic‑based (cenário sem medição de distância do eletrodo)**

- Mínimo viável: cerca de 15 corridas/campanhas por regime principal (por exemplo, tipo de liga + faixa de potência), com dados de energia, carga e composição suficientemente completos para fechar balanços.[ieeexplore.ieee+6](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)​
    
- Ótimo prático: 30–40 corridas bem medidas por regime, permitindo calibração robusta e validação em condições “novas” (como variações de carga ou receita).[pmc.ncbi.nlm.nih+4](https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/)​
    

---

## 3. Modelos supervisionados: qualidade, consumo e variáveis latentes

Modelos supervisionados (regressão e classificação) são usados em siderurgia e ferro‑ligas para prever composição final, consumo específico, probabilidade de defeitos, emissões e indicadores de eficiência, com bons resultados mesmo em small data. Nesses casos, a distância efetiva do eletrodo entra como variável latente implícita nas medidas de processo (tensão, corrente, impedância, ruído elétrico, assinatura de potência) mais dados metalúrgicos.[nature+15](https://www.nature.com/articles/s41524-023-01000-z)​

Revisões em “small data ML” e casos industriais aço/metalurgia mostram que:

- Modelos de regressão de propriedades ou desempenho podem alcançar boa acurácia com 100–300 amostras, quando há forte engenharia de features e incorporação de conhecimento físico.[adsabs.harvard+6](http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract)​
    
- Em casos industriais de aciaria, projetos de qualidade preditiva usam de 10³ a 10⁴ heats/corridas para treinar modelos mais flexíveis, como redes neurais.[d-nb+4](https://d-nb.info/1257929453/34)​
    

No contexto da Maringá, features supervisionadas podem incluir:

- Entradas:
    
    - Séries de potência, corrente, tensão, fator de potência, tempo em diferentes níveis de potência, consumo de eletrodo.[open.uct+5](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf)​
        
    - Composições de alimentação, granulometria, umidade, temperatura de carga, sequenciamento.
        
    - Condições ambiente e manutenção (estado de revestimento, trocas de eletrodos, limpeza de forno).
        
- Saídas:
    
    - Composição de liga, rendimento metálico, consumo específico de energia, indicadores de estabilidade de operação, “OK/NOK” de qualidade.[onlinelibrary.wiley+4](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​
        

**Resumo numérico – supervisionado (para Maringá)**

- Regressão (consumo, qualidade, composição):
    
    - Mínimo viável: ~200–300 corridas bem registradas por família de ligas, com entradas e saídas completas.[sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S2352847824001552)​
        
    - Ótimo prático: 1000–3000 corridas por família de ligas, faixa em que as curvas de aprendizado de modelos industriais de aço tendem a estabilizar.[research.rug+5](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)​
        
- Classificação (OK/NOK, defeitos, regimes instáveis):
    
    - Mínimo viável: 500–1000 exemplos rotulados, assegurando dezenas de casos na classe minoritária (falha, desvio).[eds.yildiz+5](https://eds.yildiz.edu.tr/AjaxTool/GetArticleByPublishedArticleId?PublishedArticleId=7378)​
        
    - Ótimo prático: alguns milhares de amostras rotuladas, sobretudo em problemas com múltiplas classes (vários tipos de defeito ou regimes).[pmc.ncbi.nlm.nih+3](https://pmc.ncbi.nlm.nih.gov/articles/PMC11285775/)​
        

Mesmo sem medir diretamente a distância do eletrodo, a combinação de sinais elétricos e variáveis metalúrgicas permite que o modelo aprenda correlações com variáveis latentes relacionadas à posição/imersão, desde que o volume e a qualidade dos dados sejam adequados.[imim+5](https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf)​

---

## 4. Não supervisionados: perfis de operação e anomalias

Modelos não supervisionados (clusterização, redução de dimensionalidade, detecção de anomalias) são particularmente adequados para:

- Identificar regimes distintos de operação de forno (por exemplo, regimes com eletrodo mais imerso ou mais raso, ainda que não observados diretamente).[oaepublish+4](https://www.oaepublish.com/articles/jmi.2024.85)​
    
- Detectar padrões de anomalia em sinais de potência, corrente, harmônicos e flutuações que indiquem instabilidade do arco ou do banho.[github+2](https://github.com/jonathanwvd/awesome-industrial-datasets)​
    

Na prática industrial, esses métodos trabalham com grandes volumes de séries temporais de sensores:

- Casos em siderurgia usam de 10⁴ a 10⁶ registros de sensores para agrupar regimes de processo e detectar anomalias em linhas de produção.[d-nb+5](https://d-nb.info/1257929453/34)​
    
- Em aplicações de monitoramento, meses de dados contínuos são comuns, o que permite capturar variação de turno, manutenção e sazonalidade.[sciencedirect+3](https://www.sciencedirect.com/science/article/pii/S2352847824001552)​
    

No projeto Maringá, as variáveis alvo podem incluir:

- Séries de potência, corrente, tensões fase‑fase, fatores de potência, harmônicos, ruídos e eventos de ajuste automático de eletrodo.[open.uct+5](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf)​
    
- Temperaturas, vazões de refrigeração, pressões e variáveis ambientais disponíveis.
    

**Resumo numérico – não supervisionado (para Maringá)**

- Clusterização de regimes (operacionais, “perfis de eletrodo” implícitos):
    
    - Mínimo viável: ~10⁴ pontos de série temporal (por exemplo, leituras por minuto em algumas semanas de operação) por forno/tipo de produto.[adsabs.harvard+5](http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract)​
        
    - Ótimo prático: 10⁵–10⁶ pontos, cobrindo vários meses e múltiplas campanhas, o que melhora a qualidade dos clusters e a robustez dos “perfis de operação”.[research.rug+4](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)​
        
- Detecção de anomalias:
    
    - Mínimo viável: ~10⁵ pontos, permitindo estimar a distribuição do comportamento “normal”.[oaepublish+2](https://www.oaepublish.com/articles/jmi.2024.85)​
        
    - Ótimo prático: ordem de 10⁶ pontos ou mais, tipicamente de 3–12 meses, incorporando diferentes condições externas e de manutenção.[github+3](https://github.com/jonathanwvd/awesome-industrial-datasets)​
        

Aqui, a ausência de medição direta de distância do eletrodo é parcialmente compensada pela riqueza das assinaturas de processo, que se refletem em padrões elétricos e térmicos que os algoritmos de clusterização capturam.[royalsocietypublishing+4](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)​

---

## 5. Reinforcement Learning (RL) seguro em processos metalúrgicos

O uso de RL em processos metalúrgicos é emergente, com foco em controle ótimo e seguro (safe RL) em ambientes onde testar políticas diretamente na planta é arriscado. Casos documentados incluem controle de planicidade em laminação a frio e outros processos industriais onde RL off‑line é treinado sobre dados históricos e modelos de processo.[acm+9](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)​

Em RL para fornos de ferro‑liga, potenciais usos incluem:

- Ajuste de setpoints de potência, perfil de subida/descida dos eletrodos e sequência de carregamento, com objetivo de minimizar consumo específico, emissões ou instabilidade elétrica.[research.aalto+5](https://research.aalto.fi/files/104297935/1_s2.0_S0020025523003158_main.pdf)​
    
- Aprendizagem de políticas que respeitem restrições de segurança e limites operacionais, mitigando riscos de arco instável, desgaste excessivo de eletrodo ou danos ao refratário.[sciencedirect+6](https://www.sciencedirect.com/science/article/abs/pii/S0020025523012690)​
    

Literatura de RL industrial e metalúrgico mostra que:

- Abordagens online “puras” exigiriam enormes quantidades de interações e são em geral inviáveis em processos críticos.[pure.tudelft+5](https://pure.tudelft.nl/ws/portalfiles/portal/142954982/dissertation.pdf)​
    
- Métodos off‑line e híbridos (modelo + dados) reduzem a necessidade de dados reais, mas ainda requerem milhares de transições históricas para aprender políticas confiáveis.[sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S0166361522001452)​
    

**Resumo numérico – RL (para Maringá, priorizando segurança)**

- Histórico real (off‑line):
    
    - Mínimo viável: ~10³ transições estado–ação–recompensa por regime (por exemplo, combinações de tipo de liga e faixa de potência), bem registradas e com boa cobertura de ações “normais”.[diva-portal+5](https://www.diva-portal.org/smash/get/diva2:1750818/FULLTEXT01.pdf)​
        
    - Ótimo prático: 10⁴ transições por regime, correspondendo a meses de operação, o que reduz overfitting e melhora a estabilidade das políticas off‑line.[acm+5](https://dl.acm.org/doi/10.1016/j.compind.2022.103748)​
        
- Simulação / gêmeo digital:
    
    - Após calibrar um modelo physic‑based com cerca de 30–40 corridas reais (seção 2), é recomendável gerar 10⁴–10⁵ episódios sintéticos para treinar o agente RL com segurança, restringindo o espaço de ações ao envelope fisicamente plausível.[sciencedirect+7](https://www.sciencedirect.com/science/article/abs/pii/S0020025523002815)​
        

Sem medir diretamente a distância do eletrodo, o estado do agente deve incluir sinais elétricos, variáveis de processo e, possivelmente, estados latentes aprendidos (por autoencoders ou modelos supervisionados) que resumam a “posição efetiva” do eletrodo.[academia+5](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)​

---

## 6. Tabela‑resumo de mínimos viáveis e ótimos

Valores em ordem de grandeza, por tipo de modelo, no contexto da Maringá Ferro Ligas, assumindo dados diagnosticados e limpos (RAW + Jupyter) e ausência de medição direta de distância do eletrodo:

|Tipo de modelo|Escopo típico no projeto|Mínimo viável de dados|Nível “ótimo” prático|Fontes|
|---|---|---|---|---|
|Physic‑based (forno / SAF, balanços)|Calibrar parâmetros físicos (condutividade de escória, perdas, perfis térmicos) a partir de corridas do forno|~15 corridas/campanhas bem medidas por regime operacional (tipo de liga + faixa de potência) [sciencedirect+4](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​|30–40 corridas/campanhas bem medidas por regime, incluindo casos de validação [sciencedirect+4](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​|[sciencedirect+6](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​|
|Supervisionado – regressão|Previsão de consumo específico, composição, rendimento|200–300 corridas por família de ligas, com entradas/saídas completas [nature+5](https://www.nature.com/articles/s41524-023-01000-z)​|1000–3000 corridas por família de ligas [onlinelibrary.wiley+5](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|[onlinelibrary.wiley+10](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|
|Supervisionado – classificação|OK/NOK, defeitos, regimes instáveis|500–1000 exemplos rotulados (com dezenas na classe minoritária) [arxiv+5](https://arxiv.org/pdf/2102.09382v1.pdf)​|Vários milhares de exemplos rotulados, sobretudo em múltiplas classes [research.rug+3](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)​|[arxiv+6](https://arxiv.org/pdf/2102.09382v1.pdf)​|
|Não supervisionado – clusterização|Descobrir regimes de operação e perfis de forno|~10⁴ pontos de série temporal por equipamento/regime (semanas de dados) [onlinelibrary.wiley+5](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|10⁵–10⁶ pontos (meses de operação) [oaepublish+4](https://www.oaepublish.com/articles/jmi.2024.85)​|[onlinelibrary.wiley+6](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|
|Não supervisionado – anomalia|Detecção de eventos instáveis, padrões anômalos em potência/corrente|~10⁵ pontos de série temporal [oaepublish+2](https://www.oaepublish.com/articles/jmi.2024.85)​|≥10⁶ pontos cobrindo 3–12 meses [oaepublish+3](https://www.oaepublish.com/articles/jmi.2024.85)​|[oaepublish+3](https://www.oaepublish.com/articles/jmi.2024.85)​|
|RL off‑line + modelo|Otimizar setpoints / sequência de operação com segurança|~10³ transições estado–ação–recompensa por regime em dados históricos [sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S0166361522001452)​|10⁴ transições históricas + 10⁴–10⁵ episódios sintéticos via gêmeo digital [acm+7](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)​|[acm+9](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)​|

---

## 7. Ilustrações conceituais úteis

Embora não haja imagens internas aqui, algumas figuras típicas da literatura podem ser usadas como referência visual em relatórios do projeto:

- Diagramas 3D de CFD/MHD de fornos SAF, mostrando eletrodos, banho de escória e linhas de corrente, úteis para explicar como a imersão do eletrodo (não medida diretamente) afeta a distribuição de corrente e o aquecimento Joule.[nature+4](https://www.nature.com/articles/s41598-021-96085-1)​
    
- Esquemas de circuitos elétricos equivalentes de fornos de arco submerso, ilustrando como tensão, corrente e impedância podem ser usados como proxies para a distância efetiva do eletrodo.[ieeexplore.ieee+3](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)​
    
- Gráficos de curvas de aprendizado (erro vs. tamanho do conjunto de treino) em problemas de materiais e metalurgia, mostrando saturação de ganho de desempenho em torno das faixas “ótimas” de dados discutidas.[pubs.aip+5](https://pubs.aip.org/aip/jap/article/128/5/054901/1063225/Small-data-materials-design-with-machine-learning)​
    

Esses elementos visuais, combinados com a arquitetura RAW + Docker do projeto, ajudam a comunicar para as partes interessadas que o foco não é apenas “ter muitos dados”, mas atingir faixas de quantidade aliadas a qualidade, rastreabilidade e conhecimento de processo – fundamentais para modelos confiáveis em ferro‑ligas.[academic.oup+3](https://academic.oup.com/nsr/article/10/7/nwad125/7147579)​

Adicionar a seguimento

Verificar fontes

1. [https://www.sciencedirect.com/science/article/pii/S0307904X16303043](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)
2. [https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)
3. [https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)
4. [https://tohoku.elsevierpure.com/en/publications/machine-learning-strategies-for-small-sample-size-in-materials-sc/](https://tohoku.elsevierpure.com/en/publications/machine-learning-strategies-for-small-sample-size-in-materials-sc/)
5. [https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)
6. [https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf](https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf)
7. [https://academic.oup.com/nsr/article/10/7/nwad125/7147579](https://academic.oup.com/nsr/article/10/7/nwad125/7147579)
8. [https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/)
9. [https://www.nature.com/articles/s41524-023-01000-z](https://www.nature.com/articles/s41524-023-01000-z)
10. [https://d-nb.info/1257929453/34](https://d-nb.info/1257929453/34)
11. [http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract](http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract)
12. [https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf)
13. [https://www.sciencedirect.com/science/article/pii/S2352847824001552](https://www.sciencedirect.com/science/article/pii/S2352847824001552)
14. [https://onlinelibrary.wiley.com/doi/10.1002/srin.201700098](https://onlinelibrary.wiley.com/doi/10.1002/srin.201700098)
15. [https://www.nature.com/articles/s41598-021-96085-1](https://www.nature.com/articles/s41598-021-96085-1)
16. [https://pmc.ncbi.nlm.nih.gov/articles/PMC11285775/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11285775/)
17. [https://pubs.aip.org/aip/jap/article/128/5/054901/1063225/Small-data-materials-design-with-machine-learning](https://pubs.aip.org/aip/jap/article/128/5/054901/1063225/Small-data-materials-design-with-machine-learning)
18. [https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)
19. [https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)
20. [https://eds.yildiz.edu.tr/AjaxTool/GetArticleByPublishedArticleId?PublishedArticleId=7378](https://eds.yildiz.edu.tr/AjaxTool/GetArticleByPublishedArticleId?PublishedArticleId=7378)
21. [https://www.sciencedirect.com/science/article/abs/pii/S095741742101085X](https://www.sciencedirect.com/science/article/abs/pii/S095741742101085X)
22. [https://arxiv.org/pdf/2102.09382v1.pdf](https://arxiv.org/pdf/2102.09382v1.pdf)
23. [https://www.oaepublish.com/articles/jmi.2024.85](https://www.oaepublish.com/articles/jmi.2024.85)
24. [https://github.com/jonathanwvd/awesome-industrial-datasets](https://github.com/jonathanwvd/awesome-industrial-datasets)
25. [https://dl.acm.org/doi/10.1016/j.ins.2023.119684](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)
26. [https://research.aalto.fi/files/104297935/1_s2.0_S0020025523003158_main.pdf](https://research.aalto.fi/files/104297935/1_s2.0_S0020025523003158_main.pdf)
27. [https://www.sciencedirect.com/science/article/abs/pii/S0020025523012690](https://www.sciencedirect.com/science/article/abs/pii/S0020025523012690)
28. [https://www.sciencedirect.com/science/article/abs/pii/S0020025523002815](https://www.sciencedirect.com/science/article/abs/pii/S0020025523002815)
29. [https://www.semanticscholar.org/paper/Sample-efficient-reinforcement-learning-with-hybrid-Zheng-Jia/c80ca58148e0bfc065c3b8fad9859e31a36e6007](https://www.semanticscholar.org/paper/Sample-efficient-reinforcement-learning-with-hybrid-Zheng-Jia/c80ca58148e0bfc065c3b8fad9859e31a36e6007)
30. [https://pure.tudelft.nl/ws/portalfiles/portal/142954982/dissertation.pdf](https://pure.tudelft.nl/ws/portalfiles/portal/142954982/dissertation.pdf)
31. [https://www.sciencedirect.com/science/article/pii/S0166361522001452](https://www.sciencedirect.com/science/article/pii/S0166361522001452)
32. [https://www.diva-portal.org/smash/get/diva2:1750818/FULLTEXT01.pdf](https://www.diva-portal.org/smash/get/diva2:1750818/FULLTEXT01.pdf)
33. [https://dl.acm.org/doi/10.1016/j.compind.2022.103748](https://dl.acm.org/doi/10.1016/j.compind.2022.103748)
34. [https://aaltodoc.aalto.fi/items/185cfa25-3d4f-45b9-8be5-f3d1c2001c72](https://aaltodoc.aalto.fi/items/185cfa25-3d4f-45b9-8be5-f3d1c2001c72)

## Evidencias R1 - Coerencia catalogo versus filesystem

R1 confirma coerencia total entre manifesto e filesystem. Universo catalogado: 145 linhas. Encontrados no filesystem: 145. Ausentes: 0. Duplicidades no manifesto (linhas): 0. Orfaos no filesystem: 0.

## Evidencias R2 - Diagnostico de leitura e estrutura (nivel diagnostico)

O universo catalogado contem 145 arquivos, sendo 124 CSV e 21 nao-CSV. Parsing dos CSV: ok=124, fail=0. Dominios identificados: 6.

### Sumario por dominio (R2)

| dominio              |   n_files |   n_ok |   n_fail |   total_rows |   total_cols_mean |
|:---------------------|----------:|-------:|---------:|-------------:|------------------:|
| Consumo Fornos       |        39 |     39 |        0 |     38123141 |           27.4103 |
| Corridas             |        40 |     40 |        0 |       114949 |           34.075  |
| Eletrodo             |         1 |      1 |        0 |          761 |            5      |
| Informações Diária   |        40 |     40 |        0 |        14232 |           47.225  |
| Supervisorio Forno 4 |         2 |      2 |        0 |       486250 |          228      |
| Supervisorio Forno 5 |         2 |      2 |        0 |       242099 |           25.5    |

### Sumario por forno (R2)

| dominio              | forno   | n_files | n_ok | n_fail |
| :------------------- | :------ | ------: | ---: | -----: |
| Consumo Fornos       | F1      |       7 |    7 |      0 |
| Consumo Fornos       | F2      |       8 |    8 |      0 |
| Consumo Fornos       | F3      |       8 |    8 |      0 |
| Consumo Fornos       | F4      |       8 |    8 |      0 |
| Consumo Fornos       | F5      |       8 |    8 |      0 |
| Corridas             | F1      |       8 |    8 |      0 |
| Corridas             | F2      |       8 |    8 |      0 |
| Corridas             | F3      |       8 |    8 |      0 |
| Corridas             | F4      |       8 |    8 |      0 |
| Corridas             | F5      |       8 |    8 |      0 |
| Eletrodo             | F1 a F5 |       1 |    1 |      0 |
| Informações Diária   | F1      |       8 |    8 |      0 |
| Informações Diária   | F2      |       8 |    8 |      0 |
| Informações Diária   | F3      |       8 |    8 |      0 |
| Informações Diária   | F4      |       8 |    8 |      0 |
| Informações Diária   | F5      |       8 |    8 |      0 |
| Supervisorio Forno 4 | F4      |       2 |    2 |      0 |
| Supervisorio Forno 5 | F5      |       1 |    1 |      0 |
| Supervisorio Forno 5 | F5      |       1 |    1 |      0 |

### Cobertura temporal automaticamente inferida (R2)

| dominio              | min_date                  | max_date                  |   n_files_with_date |
|:---------------------|:--------------------------|:--------------------------|--------------------:|
| Consumo Fornos       | 2018-01-01 00:00:00+00:00 | 2025-04-28 00:00:00+00:00 |                  39 |
| Corridas             | 2018-01-01 00:00:00+00:00 | 2025-04-29 00:00:00+00:00 |                  40 |
| Eletrodo             | 2021-01-02 00:00:00+00:00 | 2025-12-03 00:00:00+00:00 |                   1 |
| Informações Diária   | 2018-01-01 00:00:00+00:00 | 2025-04-29 00:00:00+00:00 |                  40 |
| Supervisorio Forno 4 | 2023-09-29 04:00:00+00:00 | 2025-01-02 03:58:00+00:00 |                   2 |
| Supervisorio Forno 5 | 2021-01-06 00:00:00+00:00 | 2024-12-05 23:00:00+00:00 |                   2 |

