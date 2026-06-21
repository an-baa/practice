# GraphViz

## 1.Описание предметной области и сущностей:
Graph: главный контейнер, который хранит список всех узлов и связей.Он знает типа графа и умеет преобразовывать всё своё содержимое в строковый формат DOT.
GraphNode: отдельный элемент графа.Имеет уникальное имя и словарь атрибутов, в котором хранятся настройки оформления.
GraphEdge: соединительная часть двух узлов, обладает собственным словарем атрибутов для управления стилем линии и подписями.
IDotGraphBuilder: позволяет начать создание графа и добавлять в него элементы.
INodeProps, IEdgeProps: вспомогательные объекты, которые появляются при вызове метода With().Позволяют удобно задавать параметры(например, Color("blue") или Weight(2.0)), не обращаясь к словарю напрямую.
DotFormatWriter: берет структуру графа и аккуратно записывает её в нужный текстовый формат
CommonPropertiesExtensions: набор методов, которые позволяют добавлять настройки(например: цвет, размер шрифта) к любому объекту, имеющему атрибуты, без дублирования кода в каждом классе.

## 2.Диаграмма классов:

```mermaid

classDiagram
    class IDotGraphBuilder {
        <<interface>>
        +AddNode(name: string) INodeBuilder
        +AddEdge(src: string, dst: string) IEdgeBuilder
        +Build() string
    }

    class INodeBuilder {
        <<interface>>
        +With(config: Action~INodeProps~) IDotGraphBuilder
    }

    class IEdgeBuilder {
        <<interface>>
        +With(config: Action~IEdgeProps~) IDotGraphBuilder
    }

    class IElementWithAttributes {
        <<interface>>
        +Dictionary~string, string~ Attributes
    }

    class INodeProps {
        <<interface>>
        +Shape(s: NodeShape) INodeProps
    }

    class IEdgeProps {
        <<interface>>
        +Weight(w: double) IEdgeProps
    }

    class DotGraphMaker {
        -Graph _graph
        -GraphNode? _currentNode
        -GraphEdge? _currentEdge
        +AddNode(name: string) INodeBuilder
        +AddEdge(src: string, dst: string) IEdgeBuilder
        +Build() string
    }

    class NodeProps {
        +Dictionary~string, string~ Attributes
        +Shape(s: NodeShape) INodeProps
    }

    class EdgeProps {
        +Dictionary~string, string~ Attributes
        +Weight(w: double) IEdgeProps
    }

    class CommonPropertiesExtensions {
        <<static>>
        +Color(element: T, color: string) T
        +FontSize(element: T, size: int) T
        +Label(element: T, text: string) T
    }

    IDotGraphBuilder <|-- INodeBuilder : добавляет узлы
    IDotGraphBuilder <|-- IEdgeBuilder : добавляете связи
    
    IElementWithAttributes <|.. INodeProps : хранит список свойств
    IElementWithAttributes <|.. IEdgeProps : хранит список свойств
    
    INodeProps <|.. NodeProps : настраивает внешний вид узла
    IEdgeProps <|.. EdgeProps : настраивает параметры связи
    
    IDotGraphBuilder <|.. DotGraphMaker : главный строитель
    INodeBuilder <|.. DotGraphMaker : поддерживает настройку узлов
    IEdgeBuilder <|.. DotGraphMaker : поддерживает настройку связей

    DotGraphMaker ..> NodeProps : создает настройки узла
    DotGraphMaker ..> EdgeProps : создает настройки связи
    
    CommonPropertiesExtensions ..> IElementWithAttributes : добавляет общие методы
```