# Otimização de rotas de última milha baseada nas preferências implícitas do operador

**Autor:** Marcos Paulo  
**Entrega 1 — Ciência das Redes**

Este projeto propõe investigar padrões recorrentes nas sequências de atendimento a clientes para apoiar o estudo de preferências implícitas na distribuição de última milha. Nesta entrega, o foco é definir uma **rede de transições entre clientes** e as análises que poderão caracterizar esses padrões.

As análises de rede descritas aqui são propostas metodológicas e ainda não foram executadas.

## Sumário

- [1. Contexto e problema](#1-contexto-e-problema)
- [2. Objetivos e perguntas de pesquisa](#2-objetivos-e-perguntas-de-pesquisa)
- [3. Base de dados](#3-base-de-dados)
- [4. Modelagem da rede](#4-modelagem-da-rede)
- [5. Análises propostas](#5-análises-propostas)
- [6. Cuidados e limites de interpretação](#6-cuidados-e-limites-de-interpretação)
- [7. Escopo e próximos passos](#7-escopo-e-próximos-passos)
- [8. Documentos do projeto](#8-documentos-do-projeto)
- [9. Referências](#9-referências)

## 1. Contexto e problema

A distribuição de última milha corresponde à etapa final de entrega ao cliente. O planejamento de rotas busca organizar os atendimentos considerando objetivos operacionais e restrições da distribuição.

O problema que motiva este projeto é que as rotas geradas por algoritmos de otimização podem não representar a intenção do operador nem incorporar as preferências dos motoristas. Essa diferença pode levar a alterações manuais nas soluções propostas.

A hipótese de trabalho é que o histórico de atendimento possa revelar padrões úteis para compreender essas decisões. A recorrência de uma sequência, entretanto, não demonstra por si só uma preferência: ela também pode refletir a localização dos clientes, restrições operacionais ou a composição das rotas.

## 2. Objetivos e perguntas de pesquisa

### Objetivo geral

Investigar como padrões históricos de atendimento podem contribuir para aproximar as soluções de otimização das decisões operacionais.

### Pergunta desta entrega

> As rotas de distribuição apresentam padrões recorrentes de sucessão entre clientes e comunidades de atendimento estáveis ao longo do tempo?

Essa pergunta será explorada por meio de quatro questões:

- Quais sequências de clientes mais se repetem?
- Há clientes com sucessores previsíveis?
- Existem grupos de clientes conectados preferencialmente entre si?
- Esses padrões persistem entre períodos diferentes?

## 3. Base de dados

Os dados foram obtidos de uma empresa de rastreamento e abrangem **45 empresas**, em uma janela de coleta de aproximadamente **um ano e meio**. Essa janela não corresponde necessariamente à quantidade de dias de operação registrados para cada empresa.

### Caracterização geral

| Métrica | Média | Desvio-padrão | Mínimo | Q1 | Mediana | Q3 | P90 | Máximo |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Clientes distintos | 590 | 756 | 2 | 52 | 365 | 834 | 1.939 | 2.784 |
| Dias de operação | 60 | 64 | 1 | 4 | 31 | 97 | 167 | 199 |
| Pedidos (notas) | 5.967 | 10.566 | 4 | 62 | 1.189 | 5.389 | 16.155 | 47.948 |
| Visitas por dia (mediana da empresa) | 50,4 | 62,0 | 1,0 | 8,0 | 20,0 | 72,0 | 97,6 | 278,0 |
| Dias distintos por cliente (média) | 6,2 | 8,3 | 1,0 | 1,0 | 2,2 | 6,2 | 19,5 | 32,0 |

**Como ler a tabela:** Q1, mediana e Q3 correspondem aos percentis 25, 50 e 75. P90 é o percentil 90: aproximadamente 90% dos valores estão abaixo dele ou são iguais a ele. Nas duas últimas linhas, primeiro se calcula a medida indicada para cada empresa; depois se resume sua distribuição entre as empresas.

### Recorte inicial: Sabor Tropical

A proposta começa pela operação da **Sabor Tropical**, com **365 clientes distintos** e **148 dias de operação**. O recorte permite estudar sucessões de atendimento e sua repetição ao longo do tempo.

| Métrica | Valor da empresa | Mediana da base | Percentil | Posição |
|---|---:|---:|---:|---:|
| Clientes distintos | 365 | 365 | 51% | 23º de 45 |
| Dias de operação | 148 | 31 | 82% | 9º de 45 |
| Pedidos (notas) | 12.081 | 1.189 | 82% | 9º de 45 |
| Visitas por dia (mediana da empresa) | 75,0 | 20,0 | 78% | 11º de 45 |
| Dias distintos por cliente (média) | 32,0 | 2,2 | 100% | 1º de 45 |

Os valores e as posições acima foram preservados do README original. A caracterização da empresa está disponível no [relatório da Sabor Tropical](relatorio-empresa-1489968205-20260917.md).

## 4. Modelagem da rede

A **rede de transições entre clientes** será direcionada e ponderada:

| Elemento | Definição |
|---|---|
| Nó | Um cliente, identificado pelo mesmo ID ao longo dos dias. |
| Aresta A → B | B é atendido imediatamente depois de A, na mesma rota. |
| Peso w(A,B) | Número de ocorrências dessa sequência no período analisado. |

### Exemplo ilustrativo

```text
Dia 1: A → B → C
Dia 2: A → B → D
Dia 3: C → B → A
```

Nesse exemplo, **A → B tem peso 2**. As transições B → C, B → D, C → B e B → A têm peso 1. A → B e B → A são arestas diferentes, pois o sentido preserva a ordem dos atendimentos.

### Frequência e proporção de transição

Além da contagem das sequências, será calculada a proporção de transição:

```text
p(B | A) = w(A,B) / soma_j w(A,j)
```

Essa medida indica em qual proporção das saídas de A o próximo cliente é B. Visitas em que A encerra a rota ficam fora do denominador; quando não há transições de saída, a proporção não é definida.

## 5. Análises propostas

| Dimensão | Análise prevista | O que se pretende investigar |
|---|---|---|
| Recorrência | Arestas de maior peso e proporção de transição | Quais sucessões aparecem com maior frequência ou concentração. |
| Previsibilidade | Concentração das transições de saída | Quais clientes apresentam sucessores mais previsíveis. |
| Comunidades | Detecção de comunidades e visualização no mapa | Se há grupos de clientes com conexões preferenciais entre si. |
| Estabilidade temporal | Comparação entre meses ou partes do período | Se os padrões encontrados persistem. |
| Comparação com o acaso | Embaralhamento da ordem dentro de cada rota | Se a repetição das sequências vai além do que se explica pela composição das rotas. |

Para a detecção de comunidades, a proposta considera o **Infomap**, compatível com redes direcionadas e ponderadas. Os grupos identificados serão tratados como candidatos a conjuntos recorrentes de atendimento, considerando também sua distribuição geográfica.

Na comparação aleatória, a ordem dos clientes será embaralhada dentro de cada rota, preservando seus clientes e tamanho. A rede será reconstruída em várias repetições para comparar as frequências observadas com as obtidas por embaralhamento. O número de repetições e as estatísticas serão definidos na implementação.

## 6. Cuidados e limites de interpretação

- **Excluir o depósito da rede principal**, pois sua presença em muitas rotas pode dominar as métricas.
- **Consolidar notas da mesma visita**, evitando transformar documentos distintos em atendimentos artificiais.
- **Respeitar os limites de cada rota e dia**, sem criar transições entre unidades diferentes.
- **Confirmar a origem da sequência**: dados de planejamento permitem analisar sequências planejadas; a análise da execução exige a ordem efetivamente realizada.
- **Preservar a data e a rota de cada transição**, permitindo auditoria e comparações temporais.
- **Interpretar a recorrência com cautela**: frequência e proporção não demonstram, isoladamente, preferência do operador.

O embaralhamento avalia a regularidade da ordem condicionada à composição das rotas. Ele não distingue, sozinho, os efeitos da proximidade geográfica, das janelas de atendimento ou dos hábitos do planejador. As rotas embaralhadas funcionam como referência estatística e não precisam ser operacionalmente viáveis.

## 7. Escopo e próximos passos

O escopo inicial é caracterizar a rede histórica da Sabor Tropical quanto à recorrência, às comunidades e à estabilidade temporal.

1. Validar a origem e a qualidade das sequências de atendimento.
2. Preparar os dados e construir a rede direcionada e ponderada.
3. Calcular frequências e proporções de transição.
4. Investigar comunidades e sua distribuição geográfica.
5. Comparar períodos e construir a referência aleatória.
6. Discutir os padrões encontrados e seus limites de interpretação.

Como extensão, poderá ser comparada a rede histórica com uma rede produzida por um modelo de **Problema de Roteamento de Veículos (VRP)**, utilizando os mesmos dias e clientes e apenas soluções com atendimento completo. Essa etapa investigará se a nova roteirização preserva ou modifica os padrões históricos de sucessão.

## 8. Documentos do projeto

| Documento | Conteúdo |
|---|---|
| [Proposta metodológica](proposta-rede-transicoes-clientes.md) | Definições da rede, análises sugeridas e cuidados metodológicos. |
| [Relatório da Sabor Tropical](relatorio-empresa-1489968205-20260917.md) | Caracterização da empresa escolhida como recorte inicial. |
| [Infográfico da rede](rede-transicoes-clientes.png) | Ilustração da transformação das sequências em uma rede. |

## 9. Referências

- MANDI, Jayanta et al. Data driven vrp: A neural network model to learn hidden preferences for vrp. arXiv preprint arXiv:2108.04578, 2021.
- LETCHNER, Julia; KRUMM, John; HORVITZ, Eric. Trip router with individualized preferences (trip): Incorporating personalization into route planning. In: AAAI. 2006. p. 1795–1800.
- FUNKE, Stefan; LAUE, Sören; STORANDT, Sabine. Deducing individual driving preferences for user-aware navigation. In: Proceedings of the 24th ACM SIGSPATIAL International Conference on Advances in Geographic Information Systems. 2016. p. 1–9.
- [The map equation](https://arxiv.org/abs/0906.1405). Referência metodológica indicada na proposta para a detecção de comunidades.
