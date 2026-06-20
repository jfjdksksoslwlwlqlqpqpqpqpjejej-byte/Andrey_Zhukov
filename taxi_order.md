## **Практика: TaxiOrder**

### 1. Описание предметной области и сущностей

Система управления заказами такси. Заказ такси содержит информацию о клиенте, маршруте, водителе и статусе

**ValueType<T>** - базовый абстрактный класс для Value Objects. Обеспечивает сравнение по значению и правильный GetHashCode.

**Entity<TId>** - базовый класс для DDD сущностей. Содержит идентификатор `Id` и сравнение по идентификатору.

**PersonName** - Value Object. Содержит `FirstName` и `LastName`.

**Address** - Value Object. Содержит `Street` и `Building`.

**Car** - Value Object. Содержит `Color`, `Model`, `PlateNumber`.

**Driver** - Entity. Содержит `Id`, `Name` (PersonName), `Car` (Car).

**TaxiOrder** - корневая Entity (Aggregate Root). Содержит:
- `Id` (наследуется от Entity<int>)
- `ClientName` (PersonName)
- `Start` (Address)
- `Destination` (Address)
- `Driver` (Driver)
- `Status` (TaxiOrderStatus)
- `CreationTime`, `DriverAssignmentTime`, `CancelTime`, `StartRideTime`, `FinishRideTime`
- Методы: `UpdateDestination()`, `AssignDriver()`, `UnassignDriver()`, `Cancel()`, `StartRide()`, `FinishRide()`, `GetDriverFullInfo()`, `GetShortOrderInfo()`

**TaxiOrderStatus** - перечисление статусов заказа: `WaitingForDriver`, `WaitingCarArrival`, `InProgress`, `Finished`, `Canceled`.

**DriversRepository** - репозиторий для получения водителей. Содержит метод `GetDriver()`.

**TaxiApi** - сервис-фасад для внешнего API. Делегирует вызовы методам `TaxiOrder`.

### 2. Диаграмма классов

```mermaid
classDiagram
    direction TB

    class ValueType~T~ {
        <<abstract>>
        +Equals(object obj) bool
        +GetHashCode() int
        +operator ==(ValueType~T~ left, ValueType~T~ right) bool
        +operator !=(ValueType~T~ left, ValueType~T~ right) bool
    }

    class Entity~TId~ {
        <<abstract>>
        +TId Id
        +Entity(TId id)
        +Equals(object obj) bool
        +GetHashCode() int
        +ToString() string
        #Equals(Entity~TId~ other) bool
    }

    class PersonName {
        +string FirstName
        +string LastName
        +PersonName(string firstName, string lastName)
    }

    class Address {
        +string Street
        +string Building
        +Address(string street, string building)
    }

    class Car {
        +string Color
        +string Model
        +string PlateNumber
        +Car(string color, string model, string plateNumber)
    }

    class Driver {
        +PersonName Name
        +Car Car
        +Driver(int id, PersonName name, Car car)
    }

    class TaxiOrderStatus {
        <<enum>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }

    class TaxiOrder {
        -PersonName _passengerName
        -Address _pickupPoint
        -Address _dropoffPoint
        -Driver _assignedDriver
        -TaxiOrderStatus _orderStatus
        -DateTime _createdAt
        -DateTime _driverAssignedAt
        -DateTime _cancelledAt
        -DateTime _rideStartedAt
        -DateTime _rideFinishedAt
        +TaxiOrder(int id, PersonName passenger, Address pickup, DateTime timestamp)
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
        +UpdateDestination(Address newDestination) void
        +AssignDriver(Driver driver, DateTime timestamp) void
        +UnassignDriver() void
        +Cancel(DateTime timestamp) void
        +StartRide(DateTime timestamp) void
        +FinishRide(DateTime timestamp) void
        +GetDriverFullInfo() string
        +GetShortOrderInfo() string
        -GetLastProgressTime() DateTime
    }

    class DriversRepository {
        -Dictionary~int, Driver~ _driverCache
        +DriversRepository()
        +GetDriver(int identifier) Driver
    }

    class TaxiApi {
        -DriversRepository _driverRepo
        -Func~DateTime~ _timeProvider
        -int _orderCounter
        +TaxiApi(DriversRepository repo, Func~DateTime~ timeProvider)
        +CreateOrderWithoutDestination(string firstName, string lastName, string street, string building) TaxiOrder
        +UpdateDestination(TaxiOrder order, string street, string building) void
        +AssignDriver(TaxiOrder order, int driverId) void
        +UnassignDriver(TaxiOrder order) void
        +Cancel(TaxiOrder order) void
        +StartRide(TaxiOrder order) void
        +FinishRide(TaxiOrder order) void
        +GetDriverFullInfo(TaxiOrder order) string
        +GetShortOrderInfo(TaxiOrder order) string
    }

    ValueType~T~ <|-- PersonName : наследует
    ValueType~T~ <|-- Address : наследует
    ValueType~T~ <|-- Car : наследует
    Entity~TId~ <|-- Driver : наследует
    Entity~TId~ <|-- TaxiOrder : наследует

    Driver --> PersonName : содержит
    Driver --> Car : содержит
    TaxiOrder --> PersonName : содержит
    TaxiOrder --> Address : содержит
    TaxiOrder --> Driver : содержит
    TaxiOrder --> TaxiOrderStatus : использует

    TaxiApi ..> DriversRepository : использует
    TaxiApi ..> TaxiOrder : управляет
    TaxiApi ..> PersonName : создает
    TaxiApi ..> Address : создает
    DriversRepository ..> Driver : возвращает
```
