## **Практика: Геометрия-2**

### 1. Описание предметной области и сущностей

Система для работы с геометрическими телами. Реализован паттерн "Посетитель" для вычисления габаритных контейнеров и замены тел на параллелепипеды.

**IVisitor<TResult>** - интерфейс посетителя.

**Body** - абстрактный базовый класс всех тел. Содержит `Position` и метод `Accept()`.

**Ball** - шар. Содержит `Radius`.

**RectangularCuboid** - параллелепипед. Содержит `SizeX`, `SizeY`, `SizeZ`.

**Cylinder** - цилиндр. Содержит `Radius` и `SizeZ`.

**CompoundBody** - составное тело. Содержит список `Parts`.

**BoundingBoxVisitor** - посетитель для вычисления габаритного контейнера. Реализует `IVisitor<RectangularCuboid>`.

**BoxifyVisitor** - посетитель для замены всех тел на параллелепипеды. Реализует `IVisitor<Body>`.

### 2. Диаграмма классов

```mermaid
classDiagram
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept(IVisitor visitor)* TResult
    }

    class Ball {
        +double Radius
        +Accept(IVisitor visitor) TResult
    }

    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept(IVisitor visitor) TResult
    }

    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept(IVisitor visitor) TResult
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept(IVisitor visitor) TResult
    }

    class IVisitor~TResult~ {
        <<interface>>
        +Visit(Ball ball) TResult
        +Visit(RectangularCuboid cuboid) TResult
        +Visit(Cylinder cylinder) TResult
        +Visit(CompoundBody compoundBody) TResult
    }

    class BoundingBoxVisitor {
        +Visit(Ball ball) RectangularCuboid
        +Visit(RectangularCuboid cuboid) RectangularCuboid
        +Visit(Cylinder cylinder) RectangularCuboid
        +Visit(CompoundBody compoundBody) RectangularCuboid
    }

    class BoxifyVisitor {
        +Visit(Ball ball) Body
        +Visit(RectangularCuboid cuboid) Body
        +Visit(Cylinder cylinder) Body
        +Visit(CompoundBody compoundBody) Body
    }

    Body<|--Ball : Наследование 
    Body<|--RectangularCuboid : Наследование 
    Body<|--Cylinder : Наследование 
    Body<|--CompoundBody : Наследование 

    IVisitor<|..BoundingBoxVisitor : Реализация интерфейса
    IVisitor<|..BoxifyVisitor : Реализация интерфейса

    CompoundBodyo--Body : Агрегация

    Body..>IVisitor : Зависимость
    BoundingBoxVisitor..>RectangularCuboid : Зависимость
    BoxifyVisitor..>Body : Зависимость
```
