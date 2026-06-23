## **Практика: Контрольный разряд**

### 1. Описание предметной области и сущностей

Система для вычисления контрольных разрядов

**Extensions** - статический класс с методами-расширениями:
- `GetDigits()` - получение цифр числа справа налево
- `SumDigits()` - сумма цифр числа
- `ModComplement()` - дополнение по модулю
- `PadLeft()` - дополнение слева нулями

**ControlDigitAlgo** - статический класс с алгоритмами:
- `Upc()` - контрольный разряд UPC (множители 3 и 1)
- `Isbn10()` - контрольный разряд ISBN-10 (веса 10..2)
- `Luhn()` - контрольный разряд по алгоритму Луна

**UpcState** - вспомогательный класс для состояния при вычислении UPC:
- `Sum` - накопленная сумма
- `Factor` - текущий множитель (3 или 1)
- `AddWeightedDigit()` - добавление цифры с учётом множителя
- `HasMoreDigits()` - проверка наличия цифр

**LuhnState** - вспомогательный класс для состояния при вычислении Luhn:
- `Sum` - накопленная сумма
- `DoubleDigit` - флаг удвоения
- `AddTransformedDigit()` - добавление цифры с учётом удвоения
- `HasMoreDigits()` - проверка наличия цифр

---

### 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class Extensions {
        +GetDigits(this long number)$ IEnumerable~int~
        +GetDigits(this int number)$ IEnumerable~int~
        +SumDigits(this int number)$ int
        +ModComplement(this int sum, int mod)$ int
        +PadLeft(this IEnumerable~int~ digits, int totalCount)$ IEnumerable~int~
        -GetDigitsFromPositiveNumber(long number)$ IEnumerable~int~
    }

    class ControlDigitAlgo {
        +Upc(long number)$ int
        +Isbn10(long number)$ char
        +Luhn(long number)$ int
        -ComputeUpcSum(long number)$ int
        -CalculateUpcWeightedSum(long number, int initialFactor)$ int
        -ProcessUpcDigits(long number, UpcState state)$ int
        -ProcessNextUpcDigit(long number, UpcState state)$ int
        -GetLastDigit(long number)$ int
        -RemoveLastDigit(long number)$ long
        -ComputeIsbn10Remainder(long number)$ int
        -GetIsbnDigits(long number)$ List~int~
        -CalculateIsbnWeightedSum(List~int~ digits)$ int
        -MultiplyByWeight(int digit, int index)$ int
        -ConvertIsbnRemainder(int remainder)$ char
        -ComputeLuhnSum(long number)$ int
        -CalculateLuhnWeightedSum(long number, bool initialDoubleDigit)$ int
        -ProcessLuhnDigits(long number, LuhnState state)$ int
        -ProcessNextLuhnDigit(long number, LuhnState state)$ int
    }

    class UpcState {
        -int Sum
        -int Factor
        +UpcState(int initialFactor)
        +AddWeightedDigit(int digit) void
        +HasMoreDigits(long number) bool
        -NextUpcFactor(int factor)$ int
    }

    class LuhnState {
        -int Sum
        -bool DoubleDigit
        +LuhnState(bool initialDoubleDigit)
        +AddTransformedDigit(int digit) void
        +HasMoreDigits(long number) bool
        -ApplyLuhnTransform(int digit, bool needDouble)$ int
    }

    ControlDigitAlgo --> UpcState : использует
    ControlDigitAlgo --> LuhnState : использует
    ControlDigitAlgo ..> Extensions : использует
```
