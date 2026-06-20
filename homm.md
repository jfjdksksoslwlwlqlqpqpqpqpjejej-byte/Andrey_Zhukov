## **Практика: HoMM**

### 1. Описание предметной области и сущностей

Игрок взаимодействует с объектами на карте - сражается, собирает сокровища и присваивает объекты.

**IOwner** - интерфейс объектов, которые можно присвоить игроку. Содержит `Owner`.

**IArmy** - интерфейс объектов с армией. Содержит `Army`.

**ITreasure** - интерфейс объектов с сокровищами. Содержит `Treasure`.

**Army** - класс армии с силой `Power`.

**Treasure** - класс сокровищ с количеством `Amount`.

**Player** - класс игрока. Содержит `Gold`, `Dead`, `Id` и методы: `CanBeat()`, `Consume()`, `Die()`

**Dwelling** - жилище. Реализует `IOwner`.

**Mine** - шахта. Реализует `IOwner`, `IArmy`, `ITreasure`. Имеет охрану и ресурсы

**Creeps** - нейтральные монстры. Реализуют `IArmy`, `ITreasure`. Охраняют сокровища.

**Wolves** - волки. Реализуют `IArmy`. Только для боя

**ResourcePile** - куча ресурсов. Реализует `ITreasure`. Собирается без боя

**Interaction** - класс взаимодействия. Метод `Make()` проверяет `IArmy` (бой), `IOwner` (захват), `ITreasure` (сбор)

### 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction RL

    class IOwner {
        <<interface>>
        +int Owner
    }

    class IArmy {
        <<interface>>
        +Army Army
    }

    class ITreasure {
        <<interface>>
        +Treasure Treasure
    }

    class Army {
        +int Power
        +Army()
    }

    class Treasure {
        +int Amount
        +Treasure()
    }

    class Player {
        +int Gold
        +bool Dead
        +int Id
        +CanBeat(Army army) bool
        +Consume(Treasure treasure) void
        +Die() void
    }

    class Dwelling {
        +int Owner
    }

    class Mine {
        +int Owner
        +Army Army
        +Treasure Treasure
    }

    class Creeps {
        +Army Army
        +Treasure Treasure
    }

    class Wolves {
        +Army Army
    }

    class ResourcePile {
        +Treasure Treasure
    }

    class Interaction {
        +Make(Player player, object target)$ void
    }

    IOwner <|.. Dwelling : реализует
    IOwner <|.. Mine : реализует
    IArmy <|.. Mine : реализует
    IArmy <|.. Creeps : реализует
    IArmy <|.. Wolves : реализует
    ITreasure <|.. Mine : реализует
    ITreasure <|.. Creeps : реализует
    ITreasure <|.. ResourcePile : реализует

    Mine --> Army : содержит
    Mine --> Treasure : содержит
    Creeps --> Army : содержит
    Creeps --> Treasure : содержит
    Wolves --> Army : содержит
    ResourcePile --> Treasure : содержит

    Interaction ..> Player : использует
    Interaction ..> IOwner : использует
    Interaction ..> IArmy : использует
    Interaction ..> ITreasure : использует
```
