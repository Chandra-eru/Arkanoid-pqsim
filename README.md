# Sim_Bgraphs — Simulador de Teoria dos Grafos em Redes de Computadores

Projeto em **Rust** para modelagem de redes, cálculo de caminhos mínimos (**Dijkstra / OSPF**) e árvores geradoras mínimas (**Prim / Spanning Tree Protocol - STP**).

---

## 1. Dependências Configuradas (`Cargo.toml`)

* **`petgraph`** (`0.7`): A principal biblioteca de estruturas de dados e algoritmos de grafos em Rust.
  - Criação de grafos direcionados (`DiGraph`) e não-direcionados (`UnGraph`).
  - Algoritmo de **Dijkstra** (`petgraph::algo::dijkstra`).
  - Algoritmo de **Prim / MST** (`petgraph::algo::min_spanning_tree`).
  - Exportação direta para diagramas **Graphviz / DOT** (`petgraph::dot::Dot`).
* **`comfy-table`** (`7.1`): Criação de tabelas formatadas em Unicode/ASCII no terminal.
  - Perfeito para exibir **Tabelas de Roteamento** (Destino, Próximo Salto, Custo) e **Tabelas de Enlaces MST**.
* **`colored`** (`3.0`): Estilização colorida do terminal (`.green()`, `.yellow()`, `.cyan()`, `.bold()`).
* **`serde`** e **`serde_json`** (`1.0`): Serialização e deserialização para salvar/carregar topologias de rede em JSON.

---

## 2. Como Usar

Para rodar o simulador:

```bash
cd ~/Projetos/Trecs_Rust/Sim_Bgraphs
cargo run
```

---

## 3. Guia Rápido das Bibliotecas

### A. Criando Grafos com `petgraph`
```rust
use petgraph::graph::UnGraph;

// Grafo com nós do tipo &str e arestas (custo) do tipo u32
let mut net: UnGraph<&str, u32> = UnGraph::new_undirected();

let r1 = net.add_node("Router-1");
let r2 = net.add_node("Router-2");

// Conecta R1 a R2 com peso/custo 5
net.add_edge(r1, r2, 5);
```

### B. Executando o Algoritmo de Dijkstra (Menor Caminho)
```rust
use petgraph::algo::dijkstra;

// Calcula a menor distância de r1 para todos os nós usando o peso da aresta
let distancias = dijkstra(&net, r1, None, |e| *e.weight());

for (no, custo) in distancias {
    println!("Destino: {} | Custo total: {}", net[no], custo);
}
```

### C. Executando o Algoritmo de Prim / MST (Árvore Geradora Mínima)
```rust
use petgraph::algo::min_spanning_tree;
use petgraph::data::FromElements;
use petgraph::visit::EdgeRef;

// Gera a árvore sem loops e com custo mínimo total (como o Spanning Tree Protocol)
let mst = UnGraph::<&str, u32>::from_elements(min_spanning_tree(&net));

for edge in mst.edge_references() {
    println!("Enlace ativo: {} <-> {} (Custo: {})",
        net[edge.source()], net[edge.target()], edge.weight());
}
```

### D. Formatando Tabelas com `comfy-table`
```rust
use comfy_table::{Table, Row, Cell, Color};
use comfy_table::presets::UTF8_FULL;

let mut table = Table::new();
table.load_preset(UTF8_FULL)
     .set_header(vec!["Roteador", "IP", "Métrica"]);
table.add_row(vec!["R1", "192.168.1.1", "10"]);
println!("{}", table);
```
