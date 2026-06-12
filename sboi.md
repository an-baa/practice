```mermaid
#Сбои

##1.Описание предметной области и сущностей:
Описание предметной области и сущностей:
Device: конкретная единица оборудования с Id и Name.
FailureType: тип сбоя из конкретного списка, такой как UnexpectedShutdown, ShortNonResponding и т.д.
Failure: содержит в себе тип сбоя, дату и айди устройства и также логику определения степени критичности IsSerious.
ReportMaker: сопоставляет данные о поломках.

##2.Диаграмма классов:

classDiagram

    class FailureType {
        <<enumeration>>
        UnexpectedShutdown = 0
        ShortNonResponding = 1
        HardwareFailures = 2
        ConnectionProblems = 3
    }

    class Device {
        +int Id
        +string Name
        +Device(id: int, name: string)
    }

    class Failure {
        +FailureType Type
        +DateTime Date
        +int DeviceId
        +bool IsSerious
        +Failure(type: FailureType, date: DateTime, deviceId: int)
    }

    class ReportMaker {
        +FindDevicesFailedBeforeDateObsolete(day: int, month: int, year: int, failureTypes: int[], deviceId: int[], times: object[][], devices: List~Dictionary~)$ List~string~
        +FindDevicesFailedBeforeDate(targetDate: DateTime, failures: IEnumerable~Failure~, devices: IEnumerable~Device~)$ List~string~
    }

    Failure --> FailureType : содержит
    ReportMaker ..> Device : использует
    ReportMaker ..> Failure : использует
```