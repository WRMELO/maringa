# Comparativo das 3 Análises Independentes — F4 Maringá
> **CONGELADO em 2026-03-24 (D-002).** Este documento é referência histórica e não deve ser alterado.

## Leitura consolidada

As três análises convergem em quatro pontos:

- O `F4` é o único piloto natural.
- O melhor par de dados para começar é `Supervisório F4 + Eletrodo`.
- `Consumo Fornos` e dados químicos não são base confiável para um MVP.
- `Digital Twin` e `RL` são destino futuro, não ponto de partida.

## Pontos fortes e fracos das 3 análises

| Análise | Ponto forte | Ponto fraco |
|---|---|---|
| **LLM 1 — Escada de Valor** | Organizou bem o roadmap comercial (`Observabilidade -> Soft-Sensor -> Digital Twin`) e trouxe referências externas úteis. | Confiou demais no relatório agregado e empurrou `dashboard/anomalias` como primeira entrega; isso não prova conceito nem cria defesa técnica suficiente. |
| **LLM 2 — Programa em Camadas** | Foi a mais forte no risco real do dado: gaps, duplicatas, partição temporal irregular e mistura por liga. | Acertou na ordem, mas abriu um programa amplo demais para a fase atual; faltou definir um teste curto de `PASS/FAIL`. |
| **LLM 3 — Soft-Sensor MLaaS** | Foi a mais direta no entregável: sensor virtual do eletrodo com escopo elétrico e foco em valor recorrente. Alinhamento perfeito com a literatura (estado do eletrodo via ROP_R). | Pulou rápido demais da leitura do problema para o modelo; assumiu que o gap temporal era superável sem detalhar como. Correlação simples, sozinha, ainda não fecha viabilidade técnica de produção. |

## Minha sugestão

Não recomendo vender `Linha 1` isolada, nem abrir um programa longo antes de provar que existe sinal útil no dado. A melhor síntese das três análises é:

**fazer um MVP de soft-sensor com saneamento mínimo embutido, e não um projeto separado de saneamento ou dashboard.**

Isso aproveita o melhor de cada leitura:

- da **LLM 1**: o soft-sensor é a trilha comercialmente mais forte;
- da **LLM 2**: sem limpeza temporal mínima, a prova nasce errada;
- da **LLM 3**: o primeiro produto precisa ser objetivo e mensurável.

## MVP recomendado

O problema da literatura (soft-sensor do eletrodo) esbarra na fratura temporal dos dados locais (falta de sincronia/integração entre os timestamps de Eletrodo e Supervisório). Para provar viabilidade técnica (MVP real), é preciso um ensaio empírico robusto:

1. **Gate 0 — Sobreposição real e Cobertura Conjunta**
   - O acervo total de `Eletrodo` tem 761 medições (todos os fornos), mas apenas **183 são do F4** (2021–2025). O `Supervisório F4` cobre set/2023–jan/2025. A interseção temporal útil é necessariamente um subconjunto das 183. Se esse número cair abaixo de ~40–50 amostras limpas, não há base estatística para nenhum modelo.
   - Contar quantas medições de `Eletrodo` realmente caem em janelas úteis do `Supervisório`, sem gap grave.
   - Se a amostra útil for baixa demais, paramos antes de modelar.

2. **Gate 1 — Janela limpa**
   - Deduplicar timestamps, ordenar a série, marcar gaps e usar apenas trechos confiáveis.
   - Trabalhar primeiro em uma única família de liga para não misturar receita com física.

3. **Gate 2 — Baseline honesto**
   - Gerar features simples de corrente, tensão, potência, fator de potência, `ROP` e deslizamento em janelas antes da medição.
   - Comparar baseline ingênuo, regressão simples e árvore/boosting simples.

4. **Gate 3 — Evidência de conceito**
   - Entregar dataset alinhado, notebook ou relatório curto, e 2 ou 3 gráficos que mostrem relação entre sinal elétrico e medição real.

## Critério de PASS do MVP

- Há amostra suficiente após o alinhamento.
- O modelo simples supera claramente o baseline ingênuo.
- O sinal aparece de forma estável em split temporal, não só na amostra total.
- As variáveis importantes fazem sentido físico, e não apenas estatístico.

## O que eu faria agora

- `Soft-Sensor` enxuto, com escopo elétrico.
- Saneamento temporal apenas no necessário para provar conceito.
- Recorte por liga ou regime operacional mais homogêneo.
- Critério explícito de `PASS/FAIL` antes de qualquer narrativa comercial.

## O que eu não faria agora

- `Dashboard` como produto inicial.
- Modelo de KPI por corrida dependente de `Consumo Fornos`.
- `Digital Twin`, `RL` ou recomendação automática de setpoint.
- Modelo complexo antes de um baseline simples passar.

## Decisão sugerida

O melhor caminho único é o rigor numérico:

**MVP curto = saneamento mínimo + alinhamento empírico `Supervisório/Eletrodo` + baseline + critério objetivo de `PASS/FAIL`.**

A grande inovação proposta por este comparativo não é "descobrir" que precisamos de um Soft-Sensor (todas as LLMs perceberam a aderência disso ao F4 e à literatura). A real contribuição é **recuar um passo e exigir a prova técnica (Gate 0 e Gate 1)**. Se não provarmos que os timestamps do histórico e do Eletrodo se sobrepõem e podem ser correlacionados em *windows* limpas, qualquer discussão de Machine Learning avançado (LSTM, Autoencoders, Digital Twin) torna-se apenas retórica acadêmica vazia aplicada a uma base corrompida.

Se passar (MVP demonstrar sinal em dados sujos e não estruturados):
A trilha longa faz sentido mercadológico e técnico: `pipeline de ingestão confiável -> soft-sensor operacional robusto -> inteligência por regime (Corridas/Info Diárias) -> digital twin parcial`.

Se falhar:
O pivot acontece muito cedo, barato e evita passivo de credibilidade/retórica com o cliente.
