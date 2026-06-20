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

    class Common {
        <<obsolete>>
        +IsFailureSerious(int failureType)$ int
        +Earlier(object[] v, int day, int month, int year)$ int
    }

    class Program {
        +Main(string[] args)$ void
    }

    ReportMaker ..> Device : Зависимость (анализирует устройства)
    ReportMaker ..> Failure : Зависимость (анализирует сбои)
    Failure --> FailureType : Ассоциация (классифицирует по типу)
    ReportMaker ..> Common : Использовал до рефакторинга
    ReportMaker --> Program : Вызывается из
