# Геометрия-2

## 1.Описание предметной области и сущностей:
Body: определает поведение всех тел в системе, хранит позицию Position и также пердоставляте метод Accept(IVisitor), который позволяет внешним алгоритмам посещать объект, не зная его типа.
Ball: Cylinder, RectangularCuboid: реализации различных геометрических тел со своими характеристиками.Ball имеет Radius, RectangularCuboid меет размеры SizeX/Y/Z, а Cylinder имеет Radius и высоту SizeZ.Также каждый из них переопределяет метод Accept и передает ему самого себя, чтобы алгоритм определил тип объекта.
CompoundBody: хранит коллекцию частей Parts(список объектов Body).Позволяет обработать сложные фигуры, как единое целое.
IVisitor: описывает какие оперции могут быть проведены с фигуров в зависимости от ее типа.
BoundingBoxVisitor, BoxifyVisitor: посетители.BoundingBoxVisitor реализует логику нахождения минимального «ограничивающего параллелепипеда» для любого типа тела.BoxifyVisitor реализует логику преобразования любой сложной геометрической структуры к набору простых параллелепипедов.

## 2.Диаграмма классов:

```mermaid

classDiagram
    class IVisitor {
        <<interface>>
        +Visit(Ball) dynamic
        +Visit(RectangularCuboid) dynamic
        +Visit(Cylinder) dynamic
        +Visit(CompoundBody) dynamic
    }

    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept(IVisitor) dynamic
    }

    class Ball {
        +double Radius
        +Accept(IVisitor) dynamic
    }

    class RectangularCuboid {
        +double SizeX, SizeY, SizeZ
        +Accept(IVisitor) dynamic
    }

    class Cylinder {
        +double SizeZ, Radius
        +Accept(IVisitor) dynamic
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept(IVisitor) dynamic
    }

    class BoundingBoxVisitor {
        +Visit(Ball) dynamic
        +Visit(RectangularCuboid) dynamic
        +Visit(Cylinder) dynamic
        +Visit(CompoundBody) dynamic
    }

    class BoxifyVisitor {
        +Visit(Ball) dynamic
        +Visit(RectangularCuboid) dynamic
        +Visit(Cylinder) dynamic
        +Visit(CompoundBody) dynamic
    }

    Body <|-- Ball : "наследует базу"
    Body <|-- RectangularCuboid : "наследует базу"
    Body <|-- Cylinder : "наследует базу"
    Body <|-- CompoundBody : "наследует базу"

    IVisitor <|.. BoundingBoxVisitor : вычисление границ
    IVisitor <|.. BoxifyVisitor : композитная трансформация

    Body ..> IVisitor : "Accept(v) двойная диспетчеризация
    IVisitor ..> Body : "Visit(this) применение конкретной логики к типу

    CompoundBody o-- Body : 
    BoxifyVisitor ..> BoundingBoxVisitor : 
```