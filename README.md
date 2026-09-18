# Otimização de rotas de última milha baseada nas preferências implícitas do operador

**Autor:** Marcos Paulo     
**Entrega 1 - Ciência das Redes**

O objetivo desse trabalho é definir as pergunta de pesquisa, como a coleta de dados será realizada e as referências de artigos preliminares.

## Introdução

- O que é rota de última milha?
- Objetivos da otimização
- Soluções

## Problema

- Rotas que não representam a intenção do operador
- Não captam as preferências dos motoristas
- Alterações manuais

## Sugestão de rede

- **Nó**: um cliente, identificado pelo mesmo ID ao longo dos dias.
- **Aresta A → B**: existe quando B é atendido imediatamente depois de A, na mesma rota.
- **Peso w(A,B)**: número de ocorrências dessa sequência no período analisado.

## Pergunta de pesquisa

- Como podemos melhorar a assertividade dos algoritmos de otimização?
- Quais sequências mais se repetem?
- Há clientes com sucessores previsíveis?
- Existem grupos de clientes conectados preferencialmente entre si?
- Esses padrões persistem entre períodos diferentes?

## Coleta de dados

- Empresa de rastreamento
- Dados de 45 empresas
- ~ 1 ano e meio de dados

Dados gerais das empresas

| métrica | média | desvio-padrão | mín | Q1 | mediana | Q3 | p90 | máx |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| clientes distintos | 590 | 756 | 2 | 52 | 365 | 834 | 1.939 | 2.784 |
| dias de operação | 60 | 64 | 1 | 4 | 31 | 97 | 167 | 199 |
| pedidos (notas) | 5.967 | 10.566 | 4 | 62 | 1.189 | 5.389 | 16.155 | 47.948 |
| visitas por dia (mediana da empresa) | 50,4 | 62,0 | 1,0 | 8,0 | 20,0 | 72,0 | 97,6 | 278,0 |
| dias distintos por cliente (média) | 6,2 | 8,3 | 1,0 | 1,0 | 2,2 | 6,2 | 19,5 | 32,0 |

Dados da empresa escolhida

| métrica | valor da empresa | mediana da base | percentil | posição |
|---|---:|---:|---:|---:|
| clientes distintos | 365 | 365 | 51% | 23º de 45 |
| dias de operação | 148 | 31 | 82% | 9º de 45 |
| pedidos (notas) | 12.081 | 1.189 | 82% | 9º de 45 |
| visitas por dia (mediana da empresa) | 75,0 | 20,0 | 78% | 11º de 45 |
| dias distintos por cliente (média) | 32,0 | 2,2 | 100% | 1º de 45 |


## Referências

- MANDI, Jayanta et al. Data driven vrp: A neural network model to learn hidden preferences for vrp. arXiv preprint arXiv:2108.04578, 2021.
- LETCHNER, Julia; KRUMM, John; HORVITZ, Eric. Trip router with individualized preferences (trip): Incorporating personalization into route planning. In: AAAI. 2006. p. 1795-1800.
- FUNKE, Stefan; LAUE, Sören; STORANDT, Sabine. Deducing individual driving preferences for user-aware navigation. In: Proceedings of the 24th ACM SIGSPATIAL International Conference on Advances in Geographic Information Systems. 2016. p. 1-9.
