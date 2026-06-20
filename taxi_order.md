# TaxiOrder

## 1.Описание предметной области и сущностей:
TaxiOrder: центральный узел, управляющий всеми вложенными объектами.Любые изменения происходят только через публичные контракты-методы(AssignDriver, StartRide, Cancel).
Driver: сотрудник-водитель.Идентифицируется уникальным номером, тк некоторые его данные(например, фамилия или машина) могут со временем измениться.При правке таких данных фактически сотрудник останется тем-же самым.
PersonName: хранит имя и фамилию.Используется, как для клиента такси, так и для водителя.
Address: географическая точка, состоящая из улицы и номера здания.Используется для формирования маршрута: точки отправления Start и назначения Destination.
Car: автомобиль.Имеет технические характеристики назначенного транспорта(модель, цвет, гос. номер).Привязан к водителю.
TaxiApi: выполняет роль интерфейса между внешним миром и доменной моделью.Его задача - это принять команду, запросить нужные данные из базы.
DriversRepository: ищет и собирает сущность Driver по идентификатору.Полностью отвязан от контекста заказов и ничего не знает про поездки.
TaxiOrderStatus: матрица конченых состояний заказа(WaitingForDriver, WaitingCarArrival, InProgress, Finished, Canceled).

## 2.Диаграмма классов:

```mermaid

classDiagram
    class Entity~TId~ {
        +TId Id
        +Equals(other: Entity~TId~) bool
        +GetHashCode() int
    }
    
    class ValueType~T~ {
        +Equals(other: T) bool
        +GetHashCode() int
    }

    class ITaxiApi~T~ {
        <<interface>>
        +CreateOrderWithoutDestination(firstName: string, lastName: string, street: string, building: string) T
        +UpdateDestination(order: T, street: string, building: string) void
        +AssignDriver(order: T, driverId: int) void
        +UnassignDriver(order: T) void
        +Cancel(order: T) void
        +StartRide(order: T) void
        +FinishRide(order: T) void
        +GetDriverFullInfo(order: T) string
        +GetShortOrderInfo(order: T) string
    }

    class TaxiOrderStatus {
        <<enumeration>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }

    class PersonName {
        +string FirstName
        +string LastName
    }

    class Address {
        +string Street
        +string Building
    }

    class Car {
        +string Model
        +string Color
        +string PlateNumber
    }

    class Driver {
        +PersonName Name
        +Car Car
    }

    class TaxiOrder {
        +PersonName ClientName
        +Address Start
        +Address Destination
        +Driver Driver
        +TaxiOrderStatus Status
        +DateTime CreationTime
        +DateTime DriverAssignmentTime
        +DateTime CancelTime
        +DateTime StartRideTime
        +DateTime FinishRideTime
        +UpdateDestination(target: Address) void
        +AssignDriver(assignedPilot: Driver, assignmentMoment: DateTime) void
        +UnassignDriver() void
        +StartRide(launchMoment: DateTime) void
        +FinishRide(resolutionMoment: DateTime) void
        +Cancel(cancelMoment: DateTime) void
        +CaptureActiveCheckpoint() DateTime
        -ApplyTransition(expectedCurrent: TaxiOrderStatus, targetState: TaxiOrderStatus, mutationCall: Action) void
    }

    class DriversRepository {
        +AccessDriverRecord(identifier: int) Driver
    }

    class TaxiApi {
        -DriversRepository _archiveAgent
        -Func~DateTime~ _temporalAnchor
        -int _identitySequence
    }

    ValueType~T~ <|-- PersonName : наследует
    ValueType~T~ <|-- Address : наследует
    ValueType~T~ <|-- Car : наследует
    
    Entity~TId~ <|-- Driver : наследует
    Entity~TId~ <|-- TaxiOrder : наследует

    ITaxiApi~TaxiOrder~ <|.. TaxiApi : реализует контракт

    TaxiOrder *-- PersonName : включает данные клиента
    TaxiOrder *-- Address : точка отправления
    TaxiOrder *-- Address : точка назначения
    Driver *-- PersonName : включает ФИО
    Driver *-- Car : владеет машиной

    TaxiOrder o-- Driver : назначается на рейс 

    TaxiOrder ..> TaxiOrderStatus : меняет состояние 
    TaxiApi ..> DriversRepository : делегирует поиск 
    TaxiApi ..> TaxiOrder : управляет 
    DriversRepository ..> Driver : инстанцирует 
```