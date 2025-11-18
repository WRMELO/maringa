
    


### 2. Atividade 2.3 – Atribuir `dominio`, `forno` e `granularidade` por arquivo

Agora vamos dizer ao Agente exatamente o que fazer na 2.3.

Sugestão: adicionar esta seção ao mesmo documento ou criar um novo, por exemplo:  
`Fase_2_Execucao_Atividade_2_3.md`.

Abaixo, já em formato para Obsidian:

````markdown
# Fase 2 – Execução da Atividade 2.3  
## Atribuição de dominio, forno e granularidade por arquivo

Esta atividade usa:

- `dados/manifestos_inicial/inventario_dados_iniciais.csv` (Atividade 2.1)  
- `dados/manifestos_inicial/manifest_intermediario.csv` (Atividade 2.2)

para gerar um catálogo em nível de **arquivo individual** com:

- `dominio`
- `forno`
- `granularidade`
- e uma versão normalizada de `pasta_raiz`.

---

## 2.3.1. Objetivo

Para **cada arquivo** listado em `inventario_dados_iniciais.csv`, atribuir:

- um **domínio lógico** (`dominio`);
- um **forno** (`forno`);
- uma **granularidade** (`granularidade`);
- uma **pasta_raiz_canonica** (normalizada, sem variações de acento).

Isso vai gerar um catálogo enriquecido, que servirá como base imediata para:

- montar o `manifest_unificado` na etapa seguinte;
- planejar a migração para `maringa-raw` (Fase 3A).

---

## 2.3.2. Notebook da Atividade 2.3

O Agente deve criar o notebook:

```text
fase_1_diagnostico/analise/Fase2_03_dominio_forno_granularidade.ipynb
````

Este notebook faz:

1. Leitura de `inventario_dados_iniciais.csv`
    
2. Leitura de `manifest_intermediario.csv`
    
3. Normalização de `pasta_raiz`
    
4. Atribuição de `dominio`, `forno` e `granularidade`
    
5. Geração de um novo CSV enriquecido com essas colunas.
    

---

## 2.3.3. Normalização de pasta_raiz

### 2.3.3.1. Entradas possíveis

Do inventário (2.1) e dos manifestos (2.2), temos variações como:

- `Consumo Fornos`
    
- `Corridas`
    
- `Dicionário`, `Dicionário`
    
- `Eletrodo`
    
- `Informações Diária`, `Informações Diária`
    
- `Supervisorio Forno 4`, `Supervisorio Forno 4`
    
- `Supervisorio Forno 5`, `Supervisorio Forno 5`
    

### 2.3.3.2. pasta_raiz_canonica

O Agente deve criar uma coluna **nova** no inventário:

- `pasta_raiz_canonica`
    

Aplicando uma função de normalização que:

- remova/ignore acentos;
    
- trate espaços especiais (NBSP, etc.);
    
- converta para uma forma padrão, com exatamente estes valores:
    
    - `Consumo Fornos`
        
    - `Corridas`
        
    - `Dicionario` (sem acento, como forma canônica)
        
    - `Eletrodo`
        
    - `Informacoes Diaria` (sem acento, forma canônica)
        
    - `Supervisorio Forno 4`
        
    - `Supervisorio Forno 5`
        

Obs.: `pasta_raiz` original deve ser preservada; a canonização acontece em `pasta_raiz_canonica`.

---

## 2.3.4. Regras para `dominio`

Criar uma coluna `dominio` no inventário por arquivo, aplicando as seguintes regras:

- Se `pasta_raiz_canonica == "Consumo Fornos"` → `dominio = "consumo_fornos"`
    
- Se `pasta_raiz_canonica == "Corridas"` → `dominio = "corridas"`
    
- Se `pasta_raiz_canonica == "Dicionario"` → `dominio = "dicionario"`
    
- Se `pasta_raiz_canonica == "Eletrodo"` → `dominio = "eletrodo"`
    
- Se `pasta_raiz_canonica == "Informacoes Diaria"` → `dominio = "informacoes_diarias"`
    
- Se `pasta_raiz_canonica == "Supervisorio Forno 4"` → `dominio = "supervisorio"`
    
- Se `pasta_raiz_canonica == "Supervisorio Forno 5"` → `dominio = "supervisorio"`
    

Qualquer caso não mapeado explicitamente deve ser:

- marcado como `dominio = "desconhecido"`
    
- e comentado em uma coluna de observações (`observacoes_dominio`).
    

---

## 2.3.5. Regras para `forno`

Criar uma coluna `forno` no inventário por arquivo, com as regras:

1. **Supervisório**
    
    - Se `pasta_raiz_canonica == "Supervisorio Forno 4"` → `forno = "F4"`
        
    - Se `pasta_raiz_canonica == "Supervisorio Forno 5"` → `forno = "F5"`
        
2. **Outros domínios**
    
    Para `Consumo Fornos`, `Corridas`, `Eletrodo`, `Informacoes Diaria`:
    
    - Regra inicial simples:
        
        - Se `nome_arquivo` contiver `F1`, `F2`, `F3`, `F4` ou `F5` de forma clara → atribuir esse forno.
            
            - Exemplo: `consumo_F4_2023-01.csv` → `forno = "F4"`
                
        - Se **não** houver indicação clara no `nome_arquivo`:
            
            - atribuir `forno = "todos"` ou `forno = "nao_definido"` (a escolher, mas de forma consistente);
                
            - registrar essa incerteza em coluna `observacoes_forno`.
                

Sugestão de padrão:

- `forno = "todos"` quando o arquivo claramente agrega vários fornos (ex.: explícito no nome ou documentação).
    
- `forno = "nao_definido"` quando não há informação suficiente para decidir.
    

---

## 2.3.6. Regras para `granularidade`

Criar uma coluna `granularidade` com regra inicial:

- Se `dominio == "supervisorio"` → `granularidade = "minuto"` (granularidade nativa típica).
    
- Se `dominio == "corridas"` → `granularidade = "corrida"`.
    
- Se `dominio == "consumo_fornos"`:
    
    - se o nome do arquivo sugerir base diária (por exemplo, contiver `diario`, `dia`, etc.) → `granularidade = "dia"`;
        
    - se sugerir base horária (`hora`, `horario`) → `granularidade = "hora"`;
        
    - se não for possível inferir de forma simples → `granularidade = "nao_definida"` + comentário em `observacoes_granularidade`.
        
- Se `dominio == "dicionario"` → `granularidade = "meta"`.
    
- Se `dominio == "informacoes_diarias"` → `granularidade = "dia"`.
    
- Se `dominio == "eletrodo"`:
    
    - se o padrão estiver claro (por nome de arquivo ou documentação), aplicar `corrida`, `dia` ou outra;
        
    - caso contrário, `granularidade = "nao_definida"` com observação.
        

---

## 2.3.7. Integração inventário + manifest_intermediario

Nesta etapa, o objetivo principal é enriquecer **o inventário por arquivo**.  
O uso de `manifest_intermediario.csv` é:

- opcional para:
    
    - validar contagens de arquivos por `pasta_raiz_canonica`;
        
    - trazer descrições de pasta (`descricao_manifesto`) para políticas futuras (não obrigatório para concluir a 2.3).
        

Se o Agente decidir cruzar:

- sugerido: juntar por `pasta_raiz_canonica` ↔ `pasta_raiz` normalizada do manifesto,
    
- e anexar:
    
    - `qtd_arquivos_reportada`
        
    - `descricao_manifesto`
        
    - `fonte_manifesto`
        

como colunas adicionais no inventário enriquecido.

---

## 2.3.8. Saída da Atividade 2.3

Gerar um novo arquivo CSV:

```text
fase_1_diagnostico/dados/manifestos_final/manifest_arquivos_classificados.csv
```

Com, no mínimo, as colunas:

- `path_local`
    
- `pasta_raiz`
    
- `pasta_raiz_canonica`
    
- `nome_arquivo`
    
- `extensao`
    
- `tamanho_bytes`
    
- `data_modificacao`
    
- `hash_sha256`
    
- `dominio`
    
- `forno`
    
- `granularidade`
    
- (opcionais, se cruzados com manifest_intermediario:)
    
    - `fonte_manifesto`
        
    - `qtd_arquivos_reportada`
        
    - `descricao_manifesto`
        
- `observacoes_dominio`
    
- `observacoes_forno`
    
- `observacoes_granularidade`
    

O notebook deve:

- mostrar um resumo por `dominio` / `forno` / `granularidade`;
    
- exibir algumas linhas de amostra de cada `pasta_raiz_canonica` para validação visual.
    

---

## 2.3.9. Checklist da Atividade 2.3

-  Notebook `analise/Fase2_03_dominio_forno_granularidade.ipynb` criado.
    
-  `inventario_dados_iniciais.csv` lido corretamente.
    
-  `manifest_intermediario.csv` lido (opcional, mas desejável para anexar descrição/contagem).
    
-  Coluna `pasta_raiz_canonica` criada e validada (7 categorias oficiais).
    
-  Colunas `dominio`, `forno`, `granularidade` atribuídas a todos os arquivos com regras claras.
    
-  CSV `dados/manifestos_final/manifest_arquivos_classificados.csv` gerado.
    
-  Resumo por `dominio` / `forno` / `granularidade` revisto pelo Owner.
    

Quando essa checklist estiver concluída, teremos um catálogo em nível de arquivo pronto para virar o `manifest_unificado` na próxima etapa.

```

Com isso, você já pode mandar o Agente executar a Atividade 2.3. Quando ele te trouxer o resumo por `dominio/forno/granularidade`, a gente valida e aí sim parte para o `manifest_unificado` e planejamento da migração para o MinIO.
```