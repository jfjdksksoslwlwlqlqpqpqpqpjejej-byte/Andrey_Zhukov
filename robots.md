## **Практика: Роботы (Robots)**

### 1. Описание предметной области и сущностей

Система управления роботами. AI генерирует команды, Device их исполняет. Реализованы ковариантность и контравариантность.

**IMoveCommand** - интерфейс команды движения. Содержит `Destination`.

**IShooterMoveCommand** - интерфейс команды стрелка. Наследует `IMoveCommand`. Добавляет `ShouldHide`.

**IRobotAI<out TCommand>** - интерфейс AI с ковариантным параметром. Содержит `GetCommand()`.

**IDevice<in TCommand>** - интерфейс устройства с контравариантным параметром. Содержит `ExecuteCommand()`.

**ShooterAI** - AI стрелка. Реализует `IRobotAI<IShooterMoveCommand>`.

**BuilderAI** - AI строителя. Реализует `IRobotAI<IMoveCommand>`.

**Mover** - устройство движения. Реализует `IDevice<IMoveCommand>`.

**ShooterMover** - устройство стрелка. Реализует `IDevice<IShooterMoveCommand>`.

**Robot<TCommand>** - класс робота. Принимает AI и Device.

**Robot** - статический класс-фабрика для создания роботов.


### 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class IMoveCommand {
        <<interface>>
        +Point Destination
    }

    class IShooterMoveCommand {
        <<interface>>
        +bool ShouldHide
    }

    class IRobotAI~out TCommand~ {
        <<interface>>
        +GetCommand() TCommand
    }

    class IDevice~in TCommand~ {
        <<interface>>
        +ExecuteCommand(TCommand command) string
    }

    class ShooterAI {
        -int _stepCounter
        +GetCommand() IShooterMoveCommand
    }

    class BuilderAI {
        -int _counter
        +GetCommand() IMoveCommand
    }

    class Mover {
        +ExecuteCommand(IMoveCommand cmd) string
    }

    class ShooterMover {
        +ExecuteCommand(IShooterMoveCommand cmd) string
    }

    class Robot~TCommand~ {
        -IRobotAI~TCommand~ _brain
        -IDevice~TCommand~ _executor
        +Robot(IRobotAI~TCommand~ ai, IDevice~TCommand~ device)
        +Start(int steps) IEnumerable~string~
    }

    class Robot {
        +Create(IRobotAI~TCommand~ ai, IDevice~TCommand~ device)$ Robot~TCommand~
    }

    IShooterMoveCommand --|> IMoveCommand : наследует

    IRobotAI~out TCommand~ <|.. ShooterAI : реализует
    IRobotAI~out TCommand~ <|.. BuilderAI : реализует

    IDevice~in TCommand~ <|.. Mover : реализует
    IDevice~in TCommand~ <|.. ShooterMover : реализует

    Robot~TCommand~ --> IRobotAI~out TCommand~ : использует
    Robot~TCommand~ --> IDevice~in TCommand~ : использует
    Robot <-- Robot~TCommand~ : создает
```
