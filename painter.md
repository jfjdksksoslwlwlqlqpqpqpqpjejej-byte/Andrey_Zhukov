## **Практика: Fractal Painter**

### 1. Описание предметной области и сущностей

Система для рисования фракталов (Дракон, Кривая Коха) с возможностью настройки параметров изображения, палитры и сохранения результата. Реализована с использованием принципов Dependency Inversion и Dependency Injection.

**IUiAction** - интерфейс действия пользовательского интерфейса. Содержит:
- `Category` - категория меню
- `Name` - название действия
- `CanExecute()` - проверка доступности
- `Execute()` - выполнение действия

**IImageController** - интерфейс контроллера изображения. Содержит:
- `CreateDrawingContext()` - создание контекста рисования
- `GetImageSize()` - получение размера изображения
- `RecreateImage()` - пересоздание изображения
- `SaveImage()` - сохранение изображения
- `UpdateUi()` - обновление UI

**AvaloniaImageController** - реализация контроллера изображения для Avalonia UI.

**ImageSettings** - настройки изображения. Содержит `Width` и `Height`.

**Palette** - класс палитры цветов.

**SettingsManager** - класс управления настройками. Содержит:
- `Load()` - загрузка настроек
- `Save()` - сохранение настроек
- Использует `IObjectSerializer` и `IBlobStorage`

**IObjectSerializer** - интерфейс сериализатора объектов.
**XmlObjectSerializer** - реализация сериализатора в XML.

**IBlobStorage** - интерфейс хранилища данных.
**FileBlobStorage** - реализация хранилища в файловой системе.

**DragonPainter** - класс для рисования фрактала Дракона. Содержит:
- `Paint()` - рисование фрактала
- Использует `DragonSettings`

**KochPainter** - класс для рисования кривой Коха. Содержит:
- `Paint()` - рисование фрактала

**DragonSettings** - настройки фрактала Дракона:
- `Angle1`, `Angle2` - углы поворота
- `ShiftX`, `ShiftY` - смещение
- `Scale` - масштаб
- `IterationsCount` - количество итераций

**DragonSettingsGenerator** - генератор случайных настроек для Дракона.

**ImageSettingsAction** - действие настройки изображения. Реализует `IUiAction`.

**SaveImageAction** - действие сохранения изображения. Реализует `IUiAction`.

**PaletteSettingsAction** - действие настройки палитры. Реализует `IUiAction`.

**DragonFractalAction** - действие рисования Дракона. Реализует `IUiAction`.

**KochFractalAction** - действие рисования Коха. Реализует `IUiAction`.

**SettingsForm** - форма настроек (Avalonia Window).

**MessageBox** - диалоговое окно сообщений.

### 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class IUiAction {
        <<interface>>
        +MenuCategory Category
        +string Name
        +CanExecute(object parameter) bool
        +Execute(object parameter) void
    }

    class IImageController {
        <<interface>>
        +CreateDrawingContext() IDrawingContext
        +GetImageSize() Size
        +RecreateImage(ImageSettings settings) void
        +SaveImage(string filename) void
        +UpdateUi() void
    }

    class IObjectSerializer {
        <<interface>>
        +Serialize(object obj) byte[]
        +Deserialize(byte[] data) object
    }

    class IBlobStorage {
        <<interface>>
        +Get(string key) byte[]
        +Set(string key, byte[] value) void
    }

    class ImageSettings {
        +int Width
        +int Height
    }

    class Palette {
        +List~Color~ Colors
    }

    class DragonSettings {
        +double Angle1
        +double Angle2
        +double ShiftX
        +double ShiftY
        +double Scale
        +int IterationsCount
    }

    class SettingsManager {
        -IObjectSerializer _serializer
        -IBlobStorage _storage
        +Load() AppSettings
        +Save(AppSettings settings) void
        -CreateDefaultSettings() AppSettings
    }

    class AppSettings {
        +ImageSettings ImageSettings
    }

    class DragonPainter {
        -IImageController _imageController
        -DragonSettings _settings
        +Paint() void
    }

    class KochPainter {
        -IImageController _imageController
        -Palette _palette
        +Paint() void
    }

    class AvaloniaImageController {
        +CreateDrawingContext() IDrawingContext
        +GetImageSize() Size
        +RecreateImage(ImageSettings settings) void
        +SaveImage(string filename) void
        +UpdateUi() void
    }

    class XmlObjectSerializer {
        +Serialize(object obj) byte[]
        +Deserialize(byte[] data) object
    }

    class FileBlobStorage {
        +Get(string key) byte[]
        +Set(string key, byte[] value) void
    }

    class DragonSettingsGenerator {
        -Random _random
        +Generate() DragonSettings
    }

    class ImageSettingsAction {
        -IImageController _imageCtrl
        -ImageSettings _imageCfg
        -Func~Window~ _windowProvider
        +Execute(object parameter) void
    }

    class SaveImageAction {
        -IImageController _imageCtrl
        -Func~Window~ _windowProvider
        +Execute(object parameter) void
    }

    class PaletteSettingsAction {
        -Palette _palette
        -Func~Window~ _windowProvider
        +Execute(object parameter) void
    }

    class DragonFractalAction {
        +Execute(object parameter) void
    }

    class KochFractalAction {
        +Execute(object parameter) void
    }

    class MainWindow {
        -IImageController _imageCtrl
        -Palette _palette
        -ImageSettings _imageCfg
        -IUiAction[] _actions
        +MainWindow()
    }

    class MessageBox {
        +Show(Window parent, string text, string title, MessageBoxButtons buttons)$ Task~MessageBoxResult~
    }

    class SettingsForm {
        +SettingsForm(object settings)
    }

    IUiAction <|.. ImageSettingsAction : реализует
    IUiAction <|.. SaveImageAction : реализует
    IUiAction <|.. PaletteSettingsAction : реализует
    IUiAction <|.. DragonFractalAction : реализует
    IUiAction <|.. KochFractalAction : реализует

    IImageController <|.. AvaloniaImageController : реализует
    IObjectSerializer <|.. XmlObjectSerializer : реализует
    IBlobStorage <|.. FileBlobStorage : реализует

    SettingsManager --> IObjectSerializer : использует
    SettingsManager --> IBlobStorage : использует
    SettingsManager --> AppSettings : создает

    DragonPainter --> IImageController : использует
    DragonPainter --> DragonSettings : использует

    KochPainter --> IImageController : использует
    KochPainter --> Palette : использует

    DragonFractalAction --> DragonPainter : создает
    DragonFractalAction --> DragonSettings : создает
    DragonFractalAction --> DragonSettingsGenerator : использует

    KochFractalAction --> KochPainter : создает

    ImageSettingsAction --> IImageController : использует
    ImageSettingsAction --> ImageSettings : изменяет
    ImageSettingsAction --> SettingsForm : создает

    SaveImageAction --> IImageController : использует
    PaletteSettingsAction --> Palette : изменяет
    PaletteSettingsAction --> SettingsForm : создает

    MainWindow --> IUiAction : содержит
    MainWindow --> IImageController : создает
    MainWindow --> Palette : создает
    MainWindow --> ImageSettings : загружает

    Services ..> SettingsManager : использует
    Services ..> AvaloniaImageController : использует
    Services ..> Palette : использует
    Services ..> AppSettings : использует
```
