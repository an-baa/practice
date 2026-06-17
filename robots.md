# Геометрия-2

## 1.Описание предметной области и сущностей:
Point: сущность для указания цели, представляющая координаты Х и У.
IMoveCommand: интерфейс для любой команды перемещения.Гарантирнует наличие точки назначения с помощью Destination.
IShooterMoveCommand: используется для роботов-бойцов и добавляет требование знать, нужно ли использовать укрытие с помощью ShouldHide.
BuilderCommand: реализация команды для робота-строителя, содержит координаты и флаг необходимости постройки с помощью Build.
ShooterCommand: реализация команды для робота-бойца.Содержит координаты, флаги стрельбы с помощью Shoot, а также информацию о том, необходимо ли укрытие(ShouldHide).
IRobotAI: производит команды.
BuilderAI: производит команды для роботов-строителей с помощью BuilderCommand.
ShooterAI: производит команды для роботов-бойцов с помощью ShooterCommand.
IDevice: исполнительный механизм, потребляет команды.
Mover: обрабатывает команды IMoveCommand и выдает инструкцию для движения.
ShooterMover: требует команд типа IShooterMoveCommand для того,чтобы понять, когда использовать укрытие.
Robot: инкапсулирует внутри себя IRobotAI и исполнителя IDevice, управялет роботом с помощью Start, запрашивая команды у ИИ и передавая их на исполнение.

## 2.Диаграмма классов:

```mermaid

classDiagram
    class Point {
        +double X
        +double Y
    }

    class IMoveCommand {
        <<interface>>
        +Destination : Point
    }

    class IShooterMoveCommand {
        <<interface>>
        +ShouldHide : bool
    }
    IMoveCommand <|-- IShooterMoveCommand : расширение интерфейса

    class ShooterCommand {
        +Destination : Point
        +Shoot : bool
        +ShouldHide : bool
        +ForCounter(int counter)$ ShooterCommand
    }
    IShooterMoveCommand <|.. ShooterCommand : реализует

    class BuilderCommand {
        +Destination : Point
        +Build : bool
        +ForCounter(int counter)$ BuilderCommand
    }
    IMoveCommand <|.. BuilderCommand : реализует

    class IRobotAI~out TCommand~ {
        <<interface>>
        +GetCommand() TCommand
    }

    class ShooterAI {
        -_stepTick : int
        +GetCommand() ShooterCommand
    }
    IRobotAI~ShooterCommand~ <|.. ShooterAI : реализует

    class BuilderAI {
        -_buildTick : int
        +GetCommand() BuilderCommand
    }
    IRobotAI~BuilderCommand~ <|.. BuilderAI : реализует

    class IDevice~in TCommand~ {
        <<interface>>
        +ExecuteCommand(TCommand command) string
    }

    class Mover {
        +ExecuteCommand(IMoveCommand command) string
    }
    IDevice~IMoveCommand~ <|.. Mover : Реализует

    class ShooterMover {
        +ExecuteCommand(IShooterMoveCommand command) string
    }
    IDevice~IShooterMoveCommand~ <|.. ShooterMover : реализует

    class Robot~TCommand~ {
        -_aiCore : IRobotAI~TCommand~
        -_deviceHardware : IDevice~TCommand~
        +Robot(IRobotAI aiCore, IDevice deviceHardware)
        +Start(int steps) IEnumerable~string~
    }

    Robot~TCommand~ --> IRobotAI~TCommand~ : запрашивает команды
    Robot~TCommand~ --> IDevice~TCommand~ : отправляет на исполнение

    IMoveCommand --> Point : включает(Point)
    ShooterCommand --> Point : хранит координаты
    BuilderCommand --> Point : хранит координаты

    ShooterAI ..> ShooterCommand : создает команды(out)
    BuilderAI ..> BuilderCommand : создает команды(out)

    Mover ..> IMoveCommand : Принимает команды движения(in)
    ShooterMover ..> IShooterMoveCommand : требует команды укрытия(in)
```