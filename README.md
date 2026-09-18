# Otimização de rotas de última milha baseada nas preferências implícitas do operador

**Autor:** Marcos Paulo     
**Entrega 1 - Ciência das Redes**

O objetivo desse trabalho é definir as perguntas de pesquisa, como a coleta de dados será realizada e as referências de artigos preliminares.

## Introdução

As rotas de últimas milhas são os percursos que os veículos precisam realizar para atender uma lista de clientes de interesse. Como exemplo, podemos citar os percursos que os veículos de uma empresa como o Mercado Livre fazem dentro da cidade para realizar a entrega de encomendas dos seus clientes.

Essas rotas podem ser projetadas de modo manual ou de modo automático. O primeiro modo depende totalmente da interação humana; com isso, é difícil assegurar que os melhores critérios sejam respeitados. No segundo, um algoritmo de otimização é utilizado para o projeto das rotas. Nesse caso, precisamos definir a função objetivo ou multiobjetivo. Elas podem focar na redução da distância da rota, do tempo da viagem, na redução de emissão de poluentes, etc.

No final, independentemente do modo de construção dos percursos, o objetivo é definir a ordem em que os atendimentos são realizados, seguindo algum comportamento desejado. 

## Problema

Apesar dos muitos *solvers* disponíveis no mercado para a construção de rotas considerando funções objetivo, como as apresentadas acima, o que se nota na literatura e no uso prático dessas ferramentas é a baixa adesão dos operadores às rotas sugeridas. Observa-se que muitas das rotas sugeridas por esses otimizadores são alteradas, pois elas não atendem aos anseios dos planejadores.

Mesmo com rotas ótimas do ponto de vista da função objetivo escolhida, muitas decisões implícitas dos operadores são perdidas. Por serem questões difíceis de mapear em uma função objetivo, esses otimizadores acabam sendo subutilizados. Por isso, é necessário o estudo de soluções que possam compreender as preferências implícitas dos operadores e também dos motoristas na construção das rotas.

A fim de evidenciar o exposto acima, a seguir é possível comparar os planejamentos criados pelos operadores e por um *solver*. Nessa tabela, foram escolhidos alguns dias de planejamento para essa comparação. Nota-se claramente que existe uma diferença de eficiência entre eles. 

| dia | visitas | rotas plano | rotas VRP | km plano | km VRP | diferença | não atendidas | tempo solver |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 2025-08-29 | 74 | 5 | 3 | 208,4 | 159,5 | -23,5% | 0 | 13s |
| 2025-10-20* | 193 | 5 | 5 | 385,7 | 148,8 | -61,4%* | 36 | 35s |
| 2025-11-29 | 72 | 3 | 2 | 89,4 | 48,0 | -46,4% | 0 | 11s |
| 2026-01-10 | 75 | 3 | 2 | 78,3 | 50,2 | -36,0% | 0 | 15s |
| 2026-02-20 | 82 | 4 | 2 | 300,0 | 220,3 | -26,6% | 0 | 17s |
| **total (4 dias comparáveis)** | **303** | **15** | **9** | **676,1** | **477,9** | **-29,3%** | **0** | |


A ciência da rede pode nos auxiliar nesse trabalho, pois poderemos analisar, a partir das decisões de construção histórica, as preferências escondidas. A partir dessa análise, poderemos tentar construir soluções mais aderentes aos anseios dos operadores sem perder a capacidade de otimizar os percursos, evitando alterações manuais e empregando maior confiança no processo automático de sugestão de rotas.

## Sugestão de rede

O entendimento dos padrões de viagens escolhidos com base no histórico será construído a partir da análise de uma rede. Com essa rede, poderemos realizar análises de preferências de determinados padrões de visitas e também entender as sequências mais escolhidas, etc. Essa rede terá a seguinte estrutura: 

- **Nó**: um cliente, identificado pelo mesmo ID ao longo dos dias.
- **Aresta A → B**: existe quando B é atendido imediatamente depois de A, na mesma rota.
- **Peso w(A,B)**: número de ocorrências dessa sequência no período analisado.

## Pergunta de pesquisa

A partir do problema apresentado acima e da modelagem da rede para entendimento dos padrões de viagens, podemos derivar as seguintes perguntas de pesquisa:

- Como podemos melhorar a assertividade dos algoritmos de otimização?
- Quais sequências mais se repetem?
- Há clientes com sucessores previsíveis?
- Existem grupos de clientes conectados preferencialmente entre si?
- Esses padrões persistem entre períodos diferentes?

## Coleta de dados

Os dados para construção das redes foram extraídos da base de dados de uma empresa que trabalha com rastreamento veicular. Foram liberadas informações de 45 empresas e aproximadamente 1 ano e meio de dados. A seguir é possível verificar a estatística da quantidade de clientes, dias de operações etc. disponíveis para uso:

| métrica | média | desvio-padrão | mín | Q1 | mediana | Q3 | p90 | máx |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| clientes distintos | 590 | 756 | 2 | 52 | 365 | 834 | 1.939 | 2.784 |
| dias de operação | 60 | 64 | 1 | 4 | 31 | 97 | 167 | 199 |
| pedidos (notas) | 5.967 | 10.566 | 4 | 62 | 1.189 | 5.389 | 16.155 | 47.948 |
| visitas por dia (mediana da empresa) | 50,4 | 62,0 | 1,0 | 8,0 | 20,0 | 72,0 | 97,6 | 278,0 |
| dias distintos por cliente (média) | 6,2 | 8,3 | 1,0 | 1,0 | 2,2 | 6,2 | 19,5 | 32,0 |

A realização do trabalho focará em uma empresa específica. O foco foi em uma que possua mais clientes e repetição de atendimento para melhor compreensão dos padrões de viagem. A seguir é possível visualizar a estatística com a quantidade de clientes, dias de operações etc.:

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
