## **Практика: Генератор отчетов**

### 1. Описание предметной области и сущностей

Система для генерации отчетов по измерениям температуры и влажности. Используются делегаты для гибкой настройки форматирования и статистических вычислений.

**Measurement** - класс измерения. Содержит `Temperature` и `Humidity`.

**MeanAndStd** - класс результата статистики. Содержит `Mean` и `Std`.

**ReportMaker** - класс для генерации отчетов. Принимает в конструкторе делегаты для форматирования и вычислений

**ReportMakerHelper** - статический класс с методами для создания отчетов и вспомогательными функциями.

### 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class Measurement {
        +double Temperature
        +double Humidity
    }

    class MeanAndStd {
        +double Mean
        +double Std
        +ToString() string
    }

    class ReportMaker {
        -string _title
        -Func~IEnumerable~double~, object~ _calcStat
        -Func~string, string~ _formatCaption
        -Func~string, string, string, string~ _formatItem
        +ReportMaker(string title, Func~IEnumerable~double~, object~ calcStat, Func~string, string~ formatCaption, Func~string, string, string, string~ formatItem)
        +MakeReport(IEnumerable~Measurement~ source) string
    }

    class ReportMakerHelper {
        +ComputeMeanAndStd(IEnumerable~double~ data)$ object
        +ComputeMedian(IEnumerable~double~ data)$ object
        +WrapHtmlCaption(string text)$ string
        +WrapMarkdownCaption(string text)$ string
        +FormatHtmlItem(string label, string temp, string hum)$ string
        +FormatMarkdownItem(string label, string temp, string hum)$ string
        +MeanAndStdHtmlReport(IEnumerable~Measurement~ data)$ string
        +MedianMarkdownReport(IEnumerable~Measurement~ data)$ string
        +MeanAndStdMarkdownReport(IEnumerable~Measurement~ data)$ string
        +MedianHtmlReport(IEnumerable~Measurement~ data)$ string
    }

    ReportMaker ..> Measurement : обрабатывает
    ReportMaker --> MeanAndStd : использует
    ReportMakerHelper ..> Measurement : обрабатывает
    ReportMakerHelper ..> MeanAndStd : создает
    ReportMakerHelper ..> ReportMaker : создает
```
