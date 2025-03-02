
# Algoritmo de Kruskal em Python

## O que é o Algoritmo de Kruskal?

O Algoritmo de Kruskal é um método clássico de grafos utilizado para encontrar a **Árvore Geradora Mínima (MST)** de um grafo não orientado e ponderado. Em outras palavras, ele identifica um subconjunto das arestas que conecta todos os vértices com o menor custo total possível, sem formar ciclos. O algoritmo funciona de maneira **gulosa**:  
1. Ordena todas as arestas do grafo em ordem crescente de custo.  
2. Seleciona as arestas de menor custo que conectam vértices que ainda não estão conectados (ou seja, que pertencem a conjuntos disjuntos), evitando a formação de ciclos.  
3. Repete o processo até que todos os vértices estejam conectados, formando assim a MST.

---

## Sumário

- [Visão Geral](#visão-geral)
- [Funcionalidades](#funcionalidades)
- [Explicação do Código](#explicação-do-código)
  - [Classe Grafo](#classe-grafo)
    - [Construtor e Atributos](#construtor-e-atributos)
    - [Método `vertices()`](#método-vertices)
    - [Métodos `arestas()` e `v_arestas()`](#métodos-arestas-e-v_arestas)
    - [Métodos `inserir_aresta()` e `inserir_arestas()`](#métodos-inserir_aresta-e-inserir_arestas)
    - [Método `imprimir()`](#método-imprimir)
  - [Função Kruskal](#função-kruskal)
  - [Execução do Programa](#execução-do-programa)
- [Como Executar](#como-executar)
- [Contato e Redes Sociais](#contato-e-redes-sociais)
- [Licença](#licença)

---

## Visão Geral

Este projeto implementa o Algoritmo de Kruskal para encontrar a Árvore Geradora Mínima (MST) de um grafo não orientado ponderado. A MST conecta todos os vértices com o menor custo total, evitando ciclos. Este código foi desenvolvido em Python para demonstrar conceitos fundamentais de algoritmos de grafos, programação orientada a objetos e manipulação de estruturas de dados.

---

## Funcionalidades

- **Criação de Grafos:** Insere vértices e arestas, considerando grafos orientados ou não orientados.
- **Listagem de Vértices e Arestas:** Métodos para obter os vértices e arestas do grafo.
- **Impressão do Grafo:** Exibe as arestas e o custo total, com ajuste para a duplicidade em grafos não orientados.
- **Implementação do Algoritmo de Kruskal:** Gera a MST a partir do grafo fornecido.
- **Exemplo de Execução:** Demonstra a criação do grafo, aplicação do algoritmo e exibição do resultado.

---

## Explicação do Código

### Classe Grafo

#### Construtor e Atributos

```python
class Grafo:
    def __init__(self, orientado=False):
        # _lista_de_adjacencias é um dicionário onde cada chave representa um vértice,
        # e o valor é uma lista de tuplas no formato (vértice destino, custo)
        self._lista_de_adjacencias = dict()
        self.orientado = orientado
```

- **O que faz:** Inicializa o grafo e define se ele é orientado ou não.
- **Por que é importante:** Permite modelar o grafo e gerenciar as conexões entre os vértices.

#### Método `vertices()`

```python
    def vertices(self):
        # Retorna um conjunto com os nomes dos vértices presentes no grafo
        return set(self._lista_de_adjacencias.keys())
```

- **O que faz:** Coleta e retorna todos os vértices do grafo.
- **Por que é importante:** Utilizado para iterar e operar sobre todos os vértices.

#### Métodos `arestas()` e `v_arestas()`

```python
    def arestas(self, v_origem=None):
        # Se um vértice de origem é fornecido, retorna as arestas que partem dele.
        if v_origem:
            return self.v_arestas(v_origem)

        # Caso contrário, agrega as arestas de todos os vértices do grafo.
        arestas_do_grafo = []
        for v_origem in self.vertices():
            arestas_do_grafo += self.v_arestas(v_origem)
        return arestas_do_grafo

    def v_arestas(self, v_origem):
        # Retorna as arestas que partem do vértice 'v_origem'
        arestas = []
        for v_destino, custo in self._lista_de_adjacencias[v_origem]:
            arestas.append((v_origem, v_destino, custo))
        return arestas
```

- **O que faz:** Lista as arestas partindo de um vértice específico ou do grafo inteiro.
- **Por que é importante:** Permite a visualização e manipulação das conexões (arestas) com seus custos.

#### Métodos `inserir_aresta()` e `inserir_arestas()`

```python
    def inserir_aresta(self, u, v, custo):
        # Garante que os vértices u e v existam no dicionário, criando uma lista vazia se necessário.
        self._lista_de_adjacencias.setdefault(u, [])
        self._lista_de_adjacencias.setdefault(v, [])
        # Adiciona a aresta de u para v com o custo definido.
        self._lista_de_adjacencias[u].append((v, custo))
        # Se o grafo não é orientado, adiciona a aresta de v para u.
        if not self.orientado:
            self._lista_de_adjacencias[v].append((u, custo))

    def inserir_arestas(self, arestas):
        # Insere múltiplas arestas, iterando sobre uma lista de tuplas (u, v, custo).
        for aresta in arestas:
            self.inserir_aresta(*aresta)
```

- **O que faz:** Insere uma única aresta ou várias arestas no grafo.
- **Por que é importante:** Constrói a estrutura do grafo adicionando as conexões e os respectivos custos.

#### Método `imprimir()`

```python
    def imprimir(self):
        total = 0
        for u, v, custo in self.arestas():
            print("({}, {}, {})".format(u, v, custo), end='')
            total += custo
        if not self.orientado:
            # Em grafos não orientados, cada aresta aparece duas vezes, então divide o total por 2.
            total = total / 2
        print("")
        print("Custo: {}".format(total))
```

- **O que faz:** Exibe todas as arestas do grafo e calcula o custo total.
- **Por que é importante:** Demonstra o resultado final do algoritmo, ajustando o custo para evitar duplicidade em grafos não orientados.

### Função Kruskal

```python
def Kruskal(grafo):
    # Inicializa um conjunto para armazenar as arestas que farão parte da MST.
    arestas_mst = set()

    # Inicializa cada vértice em seu próprio conjunto, representando componentes isolados.
    conjuntos = {v: {v} for v in grafo.vertices()}

    # Ordena todas as arestas do grafo em ordem crescente de custo.
    arestas_ordenadas = sorted(
        grafo.arestas(),
        key=lambda aresta: aresta[-1]
    )
    # Itera sobre cada aresta ordenada.
    for aresta in arestas_ordenadas:
        u, v, custo = aresta
        # Se os conjuntos de u e v são disjuntos (não conectados), a aresta pode ser adicionada.
        if conjuntos[u].isdisjoint(conjuntos[v]):
            arestas_mst.add(aresta)
            # Une os conjuntos de u e v, indicando que estão conectados.
            uniao = conjuntos[u] | conjuntos[v]
            conjuntos[u] = uniao
            conjuntos[v] = uniao

    # Cria um novo grafo que representará a MST e insere as arestas selecionadas.
    mst = Grafo()
    mst.inserir_arestas(arestas_mst)
    return mst
```

- **O que faz:** Aplica o algoritmo de Kruskal para construir a Árvore Geradora Mínima.
- **Por que é importante:** Garante que a MST tenha o menor custo total possível, conectando todos os vértices sem ciclos.

### Execução do Programa

```python
arestas = [
    ('A', 'B', 2),
    ('B', 'C', 4),
    ('A', 'E', 4),
    ('C', 'E', 5),
    ('D', 'E', 2),
    ('D', 'E', 2),
    ('E', 'G', 5),
    ('F', 'G', 5)
]

grafo = Grafo()
grafo.inserir_arestas(arestas)
mst = Kruskal(grafo)
mst.imprimir()
```

- **O que faz:**  
  - Define uma lista de arestas com vértices e custos.  
  - Cria o grafo e insere as arestas.  
  - Calcula a MST utilizando o algoritmo de Kruskal.  
  - Imprime as arestas da MST e o custo total (ajustado para grafos não orientados).

---

## Como Executar

1. **Pré-requisitos:**  
   - Python 3.x instalado.

2. **Passos:**  
   - Clone este repositório:
     ```bash
     git clone https://github.com/IsaqueAlmeida/grafos-algoritmo-kruskal.git
     ```
   - Navegue até o diretório do projeto:
     ```bash
     cd grafos-algoritmo-kruskal
     ```
   - Execute o script:
     ```bash
     python seu_script.py
     ```
   - Veja a saída que mostrará as arestas da MST e o custo total.

---

## Contato e Redes Sociais

- **GitHub:** [IsaqueAlmeida](https://github.com/IsaqueAlmeida)
- **LinkedIn:** [Isaque F. S. Almeida](https://www.linkedin.com/in/isaque-f-s-almeida/)

Sinta-se à vontade para entrar em contato ou contribuir com melhorias!

---

## Licença

Este projeto está licenciado sob a [MIT License](LICENSE). Sinta-se livre para usar, modificar e distribuir este código, desde que mantenha os créditos adequados.

---
