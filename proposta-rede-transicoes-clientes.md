# Proposta de rede de transições entre clientes

Data: 17/09/2026.

Proposta metodológica para o trabalho de ciência das redes. As análises descritas
aqui ainda não foram executadas; este documento registra a definição sugerida e
os cuidados para interpretar seus resultados.

## Contexto e pergunta de pesquisa

A base inicial sugerida é a operação da Sabor Tropical, apresentada no
[relatório da empresa](relatorio-empresa-1489968205-20260917.md), com 365 clientes
distintos e 148 dias de operação. Esse relatório servirá à seção de base de dados.

A pergunta de pesquisa proposta é:

> As rotas de distribuição apresentam padrões recorrentes de sucessão entre
> clientes e comunidades de atendimento estáveis ao longo do tempo?

## Definição da rede

A rede principal será **direcionada e ponderada**, denominada **rede de transições
entre clientes**:

- **Nó:** um cliente, identificado pelo mesmo ID ao longo dos dias.
- **Aresta A → B:** existe quando B é atendido imediatamente depois de A, na mesma rota.
- **Peso w(A,B):** número de ocorrências dessa sequência no período analisado.

Exemplo:

```text
Dia 1: A → B → C
Dia 2: A → B → D
Dia 3: C → B → A
```

Nesse exemplo, A → B tem peso 2. B → C, B → D, C → B e B → A têm peso 1.
A → B e B → A são arestas diferentes: o sentido preserva a ordem dos atendimentos.

## Frequência e proporção de transição

Frequência alta, isoladamente, não demonstra preferência pela sequência. Clientes
atendidos frequentemente têm mais oportunidades de aparecer em transições do que
clientes atendidos poucas vezes.

Além da contagem original, calcular a proporção de transição:

```text
p(B | A) = w(A,B) / soma_j w(A,j)
```

Essa medida responde: “Nas vezes em que A teve outro cliente como próximo
atendimento, em qual proporção esse próximo cliente foi B?”

Por exemplo, 20 ocorrências de A → B entre 100 saídas de A representam 20%; as
mesmas 20 ocorrências entre 22 saídas indicam concentração muito maior.

O denominador exclui visitas em que A encerra a rota, pois não existe próximo
cliente. Quando não há transições de saída, essa proporção não é definida.
A proporção também não controla, sozinha, a disponibilidade de B em cada dia.

## Análises propostas

| Pergunta | Análise |
|---|---|
| Quais sequências mais se repetem? | Arestas de maior peso e proporção de transição |
| Há clientes com sucessores previsíveis? | Concentração das transições de saída |
| Existem grupos de clientes conectados preferencialmente entre si? | Detecção de comunidades e visualização no mapa |
| Esses padrões persistem? | Comparação entre meses ou duas partes do período |

Para detecção de comunidades, **Infomap** é uma opção compatível com redes
direcionadas e ponderadas. Os grupos encontrados devem ser interpretados como
candidatos a conjuntos recorrentes de atendimento, verificando também sua
distribuição geográfica. Referência: [The map equation](https://arxiv.org/abs/0906.1405).

## Comparação com uma referência aleatória

Para investigar se a regularidade observada vai além do acaso, propõe-se:

1. Embaralhar a ordem dos clientes dentro de cada rota, preservando seus clientes
   e seu tamanho.
2. Reconstruir a rede agregada com as sequências embaralhadas.
3. Repetir o procedimento várias vezes.
4. Comparar a repetição das arestas observadas com a distribuição obtida nas redes
   embaralhadas.

Esse teste investiga se **a ordem apresenta regularidade além daquela explicada
pela composição das rotas**. Ele não distingue, sozinho, se a regularidade decorre
de proximidade geográfica, janelas de atendimento ou hábitos do planejador.

As sequências embaralhadas são uma referência estatística e não precisam ser
rotas operacionalmente viáveis. O número de repetições e as estatísticas a comparar
devem ser definidos na implementação da análise.

## Cuidados na preparação dos dados

- **Excluir o depósito da rede principal:** sua conexão com muitas rotas decorre
  da operação e pode dominar as métricas.
- **Consolidar várias notas da mesma visita:** documentos diferentes não devem
  virar atendimentos diferentes artificialmente.
- **Não conectar rotas ou dias distintos artificialmente:** cada transição deve
  representar uma sucessão válida dentro da unidade de rota analisada.
- **Confirmar a origem da sequência:** se os dados registram planejamento, a
  análise trata de sequências planejadas. Para falar em execução, é necessária
  a ordem efetivamente realizada.
- **Preservar a data e a rota de origem de cada transição:** isso permite análises
  temporais e auditoria mesmo quando a rede principal agrega todo o período.

## Escopo sugerido

Começar pela rede histórica da Sabor Tropical, caracterizando recorrência,
comunidades e estabilidade temporal. Esse recorte já permite um trabalho de
ciência das redes sem depender de uma nova otimização das rotas.

Como extensão, comparar a rede histórica com uma rede produzida pelo VRP,
utilizando os mesmos dias e clientes e apenas soluções com atendimento completo.
Essa comparação investigaria se a nova roteirização preserva ou modifica os
padrões de sucessão encontrados no histórico.
