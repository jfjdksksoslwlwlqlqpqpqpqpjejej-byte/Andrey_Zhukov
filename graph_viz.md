## **Практика: GraphViz**

### 1. Описание предметной области и сущностей

Система для построения графов в формате DOT с использованием Fluent API. Позволяет создавать ориентированные и неориентированные графы, добавлять вершины и рёбра с атрибутами

**Graph** - класс графа. Содержит:
- `GraphName` - имя графа
- `Directed` - является ли граф ориентированным
- `Strict` - строгий ли граф
- Методы: `AddNode()`, `AddEdge()`

**GraphNode** - класс вершины графа. Содержит:
- `Name` - имя вершины
- `Attributes` - словарь атрибутов вершины

**GraphEdge** - класс ребра графа. Содержит:
- `SourceNode` - исходная вершина
- `DestinationNode` - целевая вершина
- `Directed` - ориентированное ли ребро
- `Attributes` - словарь атрибутов ребра

**DotFormatWriter** - класс для записи графа в DOT-формат. Содержит методы:
- `Write(Graph graph)` - запись всего графа
- `Write(GraphNode node)` - запись вершины
- `Write(GraphEdge edge)` - запись ребра
- `WriteAttributes()` - запись атрибутов
- `EscapeId()` - экранирование идентификаторов

**DotGraphBuilder** - класс для построения графа с Fluent API. Содержит:
- `DirectedGraph()` - создание ориентированного графа
- `UndirectedGraph()` - создание неориентированного графа
- `AddNode()` - добавление вершины
- `AddEdge()` - добавление ребра
- `Build()` - построение DOT-строки

**NodeBuilder** - класс для настройки вершины. Содержит:
- `With()` - настройка атрибутов вершины
- Методы для добавления новых элементов

**EdgeBuilder** - класс для настройки ребра. Содержит:
- `With()` - настройка атрибутов ребра
- Методы для добавления новых элементов

**NodeConfigurer** - класс для настройки атрибутов вершины:
- `Color()` - цвет вершины
- `FontSize()` - размер шрифта
- `Label()` - метка вершины
- `Shape()` - форма вершины (Box, Ellipse)

**EdgeConfigurer** - класс для настройки атрибутов ребра:
- `Color()` - цвет ребра
- `FontSize()` - размер шрифта
- `Label()` - метка ребра
- `Weight()` - вес ребра

**NodeShape** - перечисление форм вершин: `Box`, `Ellipse`

### 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class Graph {
        -List~GraphEdge~ edges
        -Dictionary~string, GraphNode~ nodes
        +string GraphName
        +bool Directed
        +bool Strict
        +Graph(string graphName, bool directed, bool strict)
        +AddNode(string name) GraphNode
        +AddEdge(string sourceNode, string destinationNode) GraphEdge
    }

    class GraphNode {
        +Dictionary~string, string~ Attributes
        +string Name
        +GraphNode(string name)
    }

    class GraphEdge {
        +Dictionary~string, string~ Attributes
        +string SourceNode
        +string DestinationNode
        +bool Directed
        +GraphEdge(string sourceNode, string destinationNode, bool directed)
    }

    class DotFormatWriter {
        -TextWriter writer
        +DotFormatWriter(TextWriter writer)
        +Write(Graph graph) void
        +Write(GraphNode node) void
        +Write(GraphEdge edge) void
        +WriteAttributes(IReadOnlyDictionary~string, string~ attributes) void
        +EscapeId(string id)$ string
    }

    class DotGraphBuilder {
        -Graph _graphData
        -DotGraphBuilder(Graph graph)
        +DirectedGraph(string name)$ DotGraphBuilder
        +UndirectedGraph(string name)$ DotGraphBuilder
        +AddNode(string label) NodeBuilder
        +AddEdge(string source, string target) EdgeBuilder
        +Build() string
    }

    class NodeBuilder {
        -DotGraphBuilder _owner
        -GraphNode _nodeData
        +AddNode(string name) NodeBuilder
        +AddEdge(string from, string to) EdgeBuilder
        +Build() string
        +With(Action~NodeConfigurer~ settings) DotGraphBuilder
    }

    class EdgeBuilder {
        -DotGraphBuilder _owner
        -GraphEdge _edgeData
        +AddNode(string name) NodeBuilder
        +AddEdge(string from, string to) EdgeBuilder
        +Build() string
        +With(Action~EdgeConfigurer~ settings) DotGraphBuilder
    }

    class NodeConfigurer {
        -GraphNode _targetNode
        +Color(string shade) NodeConfigurer
        +FontSize(int size) NodeConfigurer
        +Label(string text) NodeConfigurer
        +Shape(NodeShape form) NodeConfigurer
    }

    class EdgeConfigurer {
        -GraphEdge _targetEdge
        +Color(string shade) EdgeConfigurer
        +FontSize(int size) EdgeConfigurer
        +Label(string text) EdgeConfigurer
        +Weight(double value) EdgeConfigurer
    }

    class NodeShape {
        <<enum>>
        Box
        Ellipse
    }

    Graph --> GraphNode : содержит
    Graph --> GraphEdge : содержит
    DotGraphBuilder --> Graph : создает
    DotGraphBuilder --> NodeBuilder : создает
    DotGraphBuilder --> EdgeBuilder : создает
    NodeBuilder --> NodeConfigurer : создает
    EdgeBuilder --> EdgeConfigurer : создает
    NodeConfigurer --> GraphNode : настраивает
    EdgeConfigurer --> GraphEdge : настраивает
    DotFormatWriter ..> Graph : записывает
    DotFormatWriter ..> GraphNode : записывает
    DotFormatWriter ..> GraphEdge : записывает
    NodeBuilder --> DotGraphBuilder : ссылается
    EdgeBuilder --> DotGraphBuilder : ссылается
```
