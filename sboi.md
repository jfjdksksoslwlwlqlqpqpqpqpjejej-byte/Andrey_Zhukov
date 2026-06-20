## **Практика: Сбои (Failures)**

### 1. Описание предметной области и сущностей

Программа предназначена для поиска устройств, в которых до определенной даты произошли критические сбои. Система анализирует историю сбоев и формирует список устройств, требующих внимания.

**FailureType** — перечисление типов сбоев:
- `UnexpectedShutdown` (0) — неожиданное отключение
- `ShortNonResponding` (1) — кратковременная неотзывчивость
- `HardwareFailures` (2) — аппаратные сбои
- `ConnectionProblems` (3) — проблемы с подключением

**Device** — класс, описывающий устройство. Содержит уникальный идентификатор `Id` и название `Name`.

**Failure** — класс, описывающий сбой. Содержит тип сбоя `Type`, дату возникновения `Date` и методы:
- `IsSerious()` — определяет, является ли сбой критическим (четный тип)
- `WasBefore(DateTime date)` — проверяет, был ли сбой до указанной даты
- `IsInCurrentYear()` — проверяет, произошел ли сбой в текущем году
- `GetTypeName()` — возвращает строковое название типа сбоя

**ReportMaker** — основной класс для формирования отчета. Содержит:
- `FindDevicesFailedBeforeDateObsolete()` — устаревший метод для обратной совместимости с тестами (конвертирует данные и вызывает новый метод)
- `FindDevicesFailedBeforeDate()` — новый метод, принимающий структурированные данные (`DateTime`, массивы `Failure`, `Device` и идентификаторы)
- `GetCriticalFailureDevices()` — получает идентификаторы устройств с критическими сбоями до указанной даты
- `GetDeviceNamesByIds()` — получает названия устройств по их идентификаторам
- `ConvertToFailure()` — преобразует сырые данные (тип сбоя и дату) в объект `Failure`
- `ConvertToDevice()` — преобразует сырые данные (словарь) в объект `Device`

**Program** — точка входа в приложение. Запускает выполнение тестов.

### 2. Диаграмма классов

```mermaid
classDiagram
    direction RL

    class FailureType {
        <<enum>>
        UnexpectedShutdown = 0
        ShortNonResponding = 1
        HardwareFailures = 2
        ConnectionProblems = 3
    }

    class Device {
        +int Id
        +string Name
        +Device()
        +IsValid() bool
        +Equals(object obj) bool
        +GetHashCode() int
    }

    class Failure {
        +int Type
        +DateTime Date
        +Failure()
        +IsSerious() bool
        +WasBefore(DateTime date) bool
        +IsInCurrentYear() bool
        +GetTypeName() string
    }

    class ReportMaker {
        +FindDevicesFailedBeforeDateObsolete(int day, int month, int year, int[] failureTypes, int[] deviceId, object[][] times, List~Dictionary~string, object~~ devices)$ List~string~
        +FindDevicesFailedBeforeDate(DateTime deadline, Failure[] failures, int[] deviceIds, Device[] devices)$ List~string~
        +GetCriticalFailureDevices(Failure[] failures, int[] deviceIds, DateTime deadline)$ HashSet~int~
        +GetDeviceNamesByIds(HashSet~int~ ids, Device[] devices)$ List~string~
        -ConvertToFailure(int type, object[] timeData)$ Failure
        -ConvertToDevice(Dictionary~string, object~ rawData)$ Device
    }

    class Program {
        +Main(string[] args)$ void
    }

    ReportMaker ..> Device : Зависимость (анализирует устройства)
    ReportMaker ..> Failure : Зависимость (анализирует сбои)
    Failure --> FailureType : Ассоциация (классифицирует по типу)
    ReportMaker --> Program : Вызывается из
```
