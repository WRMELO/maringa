# Critérios para Seleção de Campos Essenciais

## 1. Contexto geral
Durante a Etapa 3 do Plano de Ação ("Revisar Dicionários de Dados para Campos Essenciais"), estruturamos os dicionários exportados em `.txt` e aplicamos heurísticas consistentes para identificar automaticamente os campos críticos. O objetivo é antecipar quais variáveis serão indispensáveis para os estágios seguintes: cálculo de cobertura temporal (CT), análise de completude de campos (CC), integridade relacional (IR) e, finalmente, preparação dos datasets de modelagem.

## 2. Estruturação prévia
1. Cada arquivo `.txt` é convertido em um `DataFrame` com colunas `variable`, `type`, `unit` e `description`.
2. Os tipos válidos de referência são mantidos (`Categórico`, `Data/hora`, `Texto`, `Numérico`, `Data Hora`).
3. As unidades conhecidas (por exemplo `%`, `kWh`, `A`, `kV`, `mm`, `t`, etc.) são usadas para validar e limpar a coluna `unit`.
4. Após a normalização, cada variável passa por uma função de classificação que avalia quatro categorias de interesse.

## 3. Categorias e critérios aplicados
Antes de classificar cada variável nos dicionários, explicamos claramente o papel de cada categoria e destacamos lacunas ainda pendentes, como a identificação explícita da variável de distância ponta–fundo no supervisório.
| Categoria | Objetivo | Regra de Detecção | Exemplos |
|-----------|----------|-------------------|----------|
| `chave_relacional` | Criar chaves para ligar tabelas (Forno, Corrida, Produto, Folha, Lote, Silo, Bica, Turno). | - Nome curto, sem hierarquia (sem `.`) e até 40 caracteres.<br>- Contém palavras-chave: `forno`, `corrida`, `codigo`, `código`, `produto`, `lote`, `bica`, `silo`, `folha`, `turno`. | `Forno`, `Codigo`, `Lote_Destino`, `Silo` |
| `timestamp` | Garantir ordenação temporal e cálculo de cobertura. | - Tipo declarado `Data/hora` ou `Data Hora`.<br>- ou nome contém `data` / `hora`. | `Data`, `Data_Base`, `Data_Final`, `Timestamp` |
| `processo_termico` | Medir grandezas físicas que impactam o fenômeno (energia, potência, corrente, tensão, temperatura, produção, consumo). | Nome contém: `energia`, `consumo`, `potencia`, `potência`, `corrente`, `tensao`, `tensão`, `temperatura`, `producao`, `produção`. | `Energia_Consumida`, `Potencia_Media`, `Producao_Bruta`, `CMF5_PAC4200_I_R` |
| `variavel_distancia` | Relacionar com a variável-alvo (distância ponta–fundo). | Nome contém `dist`, `ponta`, `fundo`. | (Ainda não identificado nos dicionários atuais; aguardando confirmação do time de processo) |

**Pergunta para o time de processo:** Qual tag ou combinação de tags representa de forma direta (ou como proxy confiável) a distância ponta–fundo em cada forno, para que possamos marcá-la na categoria `variavel_distancia` e usá-la como alvo na modelagem?

### Observações adicionais
- A checagem de "nome curto" evita classificar tags do supervisório (`CH1.PLC...`) como chave relacional; elas são avaliadas apenas para categorias de processo.
- A função permite múltiplas categorias por variável (ex.: um campo pode ser `chave_relacional` e `timestamp`).

## 4. Utilização nas próximas etapas de modelagem
1. **Cobertura Temporal (Etapa 4)**
   - Campos `timestamp` determinam os períodos analisados por forno/fonte.
   - Chaves relacionais permitem segmentar por `Forno`, `Corrida`, `Folha`, etc.
2. **Completude de Campos Essenciais (Etapa 5)**
   - Percentuais de preenchimento são calculados separadamente para cada categoria, destacando eventuais lacunas (ex.: `Energia_Consumida` ausente em determinadas fases).
3. **Integridade Relacional (Etapa 6)**
   - Chaves (`Forno`, `Corrida`, `Lote`) são usadas para medir a taxa de match entre famílias (Consumo × Corrida × Supervisório).
4. **Preparação para Modelagem (Etapas 7 e 8)**
   - `processo_termico`: alimenta features de energia, potência, correntes, produção e serve de input para modelos physics-based e supervisionados.
   - `timestamp`: sincroniza amostras entre fontes ao construir janelas temporais.
   - `chave_relacional`: define a granularidade (ex.: corrida, hora, dia) e evita duplicidades.
   - `variavel_distancia`: assim que mapeada, fornecerá o alvo ou proxy para estimar a distância ponta–fundo.

## 5. Dependências e próximos passos
- Validar com especialistas de processo se a lista de palavras-chave cobre todos os casos críticos; ajustar o parser caso novos formatos (ex.: unidades ou abreviações) surjam.
- Após validação, congelar a lista de campos essenciais para ser referência das métricas CT/CC/IR.
- Mapear explicitamente qual variável do supervisório será utilizada como proxy de distância ponta–fundo para completar a categoria `variavel_distancia`.
