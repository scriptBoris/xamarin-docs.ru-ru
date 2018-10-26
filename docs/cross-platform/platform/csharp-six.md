---
title: C#Обзор новых возможностей 6
description: Последнюю версию C# язык — версии 6 – постоянно развивается язык меньше стандартных действий, внесена большая ясность и согласованность. Более четкий синтаксис инициализации, возможность использовать выражение await в блоках catch/finally и условием null? оператор, особенно полезны.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 16ee512395b2658b26bc7a489eabecec3656fa93
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115761"
---
# <a name="c-6-new-features-overview"></a>C#Обзор новых возможностей 6

_Последнюю версию C# язык — версии 6 – постоянно развивается язык меньше стандартных действий, внесена большая ясность и согласованность. Более четкий синтаксис инициализации, возможность использовать выражение await в блоках catch/finally и условием null? оператор, особенно полезны._

В этом документе представлены новые возможности C# 6. Полностью поддерживается mono компилятором, и разработчики могут начать использовать новые функции на всех целевых платформах Xamarin.

Эта статья содержит краткое фрагментов C# 6 кода, которые иллюстрируют использование.
Пример приложения — это командной строки программа, которая выполняется на всех целевых платформах Xamarin и выполняет различные функции.


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Новые возможности C# 6, по [Xamarin University](https://university.xamarin.com/)**


## <a name="development-environment"></a>Среда разработки

### <a name="mac"></a>Mac

* **Visual Studio для Mac** поддерживает C# 6: можно создавать и компилировать приложения Xamarin с помощью C# 6 функции.
  Дополнительные сведения о [Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/).

### <a name="windows"></a>Windows

* **Visual Studio 2015 и 2017** и более поздних версий имеют полную поддержку C# 6. Более ранних версиях Visual Studio не будет поддерживать C# 6.

* **Xamarin Studio для Windows** не поддерживает C# 6 функции в редакторе.



## <a name="compiler"></a>Компилятор

Mono C# 6 компилятора включается в Mono 4.0 и более поздние версии, который является [бесплатно загрузить](http://www.mono-project.com/download/).
Visual Studio для Mac автоматически обновляет установки Mono в системе.

Пользователи Windows должны иметь [Visual Studio 2015 или 2017 ^](https://visualstudio.microsoft.com/) установлен для компиляции C# 6 кода (даже если выбираемое Xamarin Studio для Windows в качестве интегрированной среды разработки).

^ или *[Microsoft Build Tools 2015](http://www.microsoft.com/download/details.aspx?id=48159)* для командной строки компиляции или серверов сборки, например.

## <a name="using-c-6"></a>С помощью C# 6

C# 6 компилятор используется во всех последних версиях Visual Studio для Mac.
Те, с помощью компиляторов командной строки убедитесь, что `mcs --version` возвращает 4.0 или более поздней версии.
Visual Studio для пользователей Mac можно проверить, установлен ли у них есть Mono 4 (или более поздней версии), ссылающийся на **о Visual Studio для Mac > Visual Studio для Mac > Показать подробности**.

## <a name="less-boilerplate"></a>Меньше стандартных действий
### <a name="using-static"></a>using static
Перечисления и некоторые классы, такие как `System.Math`, являются в основном держателей статических значений и функций. В C# 6, вы можете импортировать все статические члены типа с одним `using static` инструкции. Сравнение обычной тригонометрические функции в C# 5 и C# 6:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` не выполняет открытый `const` полях, например `Math.PI` и `Math.E`, прямого доступа:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>с помощью методов расширения с использованием статических

`using static` Средство работает немного по-разному с помощью методов расширения. Несмотря на то, что методы расширения записываются с помощью `static`, они не имеет смысла без экземпляра, на которой выполняются операции. Поэтому если `using static` используется с типом, который определяет методы расширения, методы расширения, становятся доступными по типу целевого (метода `this` типа). Например `using static System.Linq.Enumerable` можно использовать для расширения API `IEnumerable<T>` объектов без перерыва во всех типах LINQ:

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

В предыдущем примере показано различие в поведении: метод расширения `Enumerable.Where` связан с массивом, во время выполнения статического метода `String.Join` можно вызывать без ссылки на `String` типа.

### <a name="nameof-expressions"></a>Выражения nameof
В некоторых случаях требуется сослаться на имя, которые вы предоставили переменная или поле. В C# 6, `nameof(someVariableOrFieldOrType)` вернет строку `"someVariableOrFieldOrType"`. Например при генерации `ArgumentException` очень вероятно будет назвать какие аргумент является недопустимым:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

Главным преимуществом `nameof` выражения является их проверяется тип и совместимы с оптимизацией на основе средства. Проверка типов из `nameof` выражения особенно Добро пожаловать в ситуациях, где `string` используется для динамического сопоставления типов. Например, в iOS `string` используется для указания типа, используемой для прототипа `UITableViewCell` объекты в `UITableView`. `nameof` можно было убедиться, что эта связь не завершается ошибкой из-за опечатку или слабого рефакторинга:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Несмотря на то, что можно передать полного имени, которое `nameof`, только последний элемент (после последнего `.`) возвращается. Например можно добавить привязку данных в Xamarin.Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Оба вызова `SetBinding` передаются одинаковые значения: `nameof(ReactiveType.StringField)` — `"StringField"`, а не `"ReactiveType.StringField"` как изначально было бы ожидать.

## <a name="null-conditional-operator"></a>Условного оператора
Ранее обновления для C# показаны принципы обнуляемые типы и оператор объединения с null `??` для уменьшения количества кода шаблонов, при обработке значений, допускающие значение NULL. C#6 продолжает эту тему с «условного оператора» `?.`. При использовании в объект в правой части выражения, оператор условием null возвращает значение элемента, если объект не является `null` и `null` в противном случае:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Оба `length0` и `length1` будут иметь тип `int?`)

В последней строке показано в предыдущем примере `?` условного оператора, в сочетании с `??` оператор объединения с null. Новый C# 6 условного оператора возвращает `null` на 2-й элемент в массиве, после чего оператор объединения с null, будет запущено и предоставляет 0, чтобы `lengths` массива (будь, соответствующих или не является, конечно, специфичном для конкретной).

Условного оператора невероятно следует уменьшить объем стандартного проверки null необходимо в многих, многих приложений.

Существуют некоторые ограничения на условного оператора из-за неоднозначности. Не может следовать сразу `?` со списком аргументов заключенные в круглые скобки, как вы возможно надеемся, что делать с помощью делегата:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Тем не менее `Invoke` может использоваться для разделения `?` из списка аргументов и по-прежнему является существенным улучшением по `null`-проверка стандартный блок:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Интерполяция строк
`String.Format` Функция традиционно использовал индексы в качестве заполнителей в строке формата, например, `String.Format("Expected: {0} Received: {1}.", expected, received`). Само собой Добавление нового значения всегда участвует раздражает задача инвентаризации вверх аргументы, перенумерации заполнители и вставка нового аргумента в правильном порядке в списке аргументов.

C#6 новых функцию интерполяции строк значительно улучшает работу `String.Format`. Теперь можно напрямую присвоить имя переменные в строке с префиксом `$`. Например:

```csharp
$"Expected: {expected} Received: {received}."
```

Переменные само собой, проверяются и переменную с ошибками или уровнем доступности приведет к ошибке компилятора.

Заполнители не обязательно должны быть простыми переменными, они могут быть любым выражением. В этих заполнителей, можно использовать кавычки *без* экранирование этих предложений. Обратите внимание, например, `"s"` в следующем:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

Интерполяция строк поддерживает выравнивание и форматирование синтаксиса `String.Format`. Так же, как вы писали ранее `{index, alignment:format}`в C# 6 при написании `{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
дает следующий результат:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Интерполяция строк — синтаксическая сладость для `String.Format`: он не может использоваться с `@""` строковых литералах и не совместим с `const`, даже если используются не заполнители:

```csharp
const string s = $"Foo"; //Error : const requires value
```

В распространенных случаев создания аргументы функции с интерполяцией строк необходимо по-прежнему следует с осторожностью экранирование, кодировки и проблемы языка и региональных параметров. Запросы SQL и URL-адрес, само собой, критически важны для очистить. Как и в `String.Format`, строка интерполяция использует `CultureInfo.CurrentCulture`. С помощью `CultureInfo.InvariantCulture` является немного более много слов:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Инициализация

C#6 предоставляет ряд краткими способы задания свойств, полей и членов.

### <a name="auto-property-initialization"></a>Автоматическое свойство инициализации

Теперь автосвойства можно инициализировать таким же образом краткими как поля. Неизменяемый автосвойства могут быть написаны только метод получения:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

В конструкторе можно задать значение только для считывания auto свойства:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Эта инициализация автосвойства является общая функция компактный и весьма полезным для разработчиков подчеркнуть неизменность в их объекты.

### <a name="index-initializers"></a>См. раздел Инициализаторы индекса.

C#6 реализована инициализаторы индекса, которые позволяют задать ключ и значение в типы, имеющие индексатор. Как правило, это для `Dictionary`-структур данных в стиле:

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Члены функции, воплощающие выражение

Лямбда-функции имеют несколько преимуществ, один из которых просто экономию места. Аналогичным образом членов класса с телом выражения позволяют малозначимые функции выражаться немного более четко, чем это было возможно в предыдущих версиях C# 6.

Члены функции, воплощающие выражение использовать синтаксис стрелок лямбда-выражения, а не традиционной блок синтаксис:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Обратите внимание, что синтаксис лямбда-выражение стрелка не использует явно `return`. Для функции, возвращающие `void`, выражение также должна быть инструкция:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Элементы, воплощающие выражения по-прежнему регулируются правило, `async` поддерживается для методов, но не свойства:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Исключения

Это не двумя способами, о нем: обработка исключений является трудным. Новые возможности в C# 6 сделать обработки исключений, более гибкую и согласованную.

### <a name="exception-filters"></a>Фильтры исключений

По определению исключения возникают в необычных ситуациях, и может быть очень сложно причину и код о *все* способов, может возникнуть исключение определенного типа. C#6 появилась возможность защиты обработчик выполнения с фильтром вычисленный среды выполнения. Это делается путем добавления `when (bool)` шаблон после обычной `catch(ExceptionType)` объявления. В следующих фильтр, являющийся отличительным признаком ошибку синтаксического анализа, относящиеся к `date` параметр, в отличие от других ошибки синтаксического анализа.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>выражение await в catch... finally...

`async` Возможности, появившиеся в C# 5 были переломным для языка. В C# 5, `await` не разрешалось использовать в `catch` и `finally` блокирует досадной, получает номер `async/await` возможностей. C#6 удаляет это ограничение, позволяя асинхронных результатов ожидать согласованно через программу как показано в следующем фрагменте:

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Сводка

C# Языка продолжает увеличиваться для повышения производительности разработчиков при также повышение уровня хорошего и вспомогательные инструменты. В этом документе предоставило обзор новых возможностей языка C# 6 и кратко показано, как они используются.

## <a name="related-links"></a>Связанные ссылки

- [Новый язык функций в C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

