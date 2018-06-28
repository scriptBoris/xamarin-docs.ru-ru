---
title: Возможности C# 6 новые общие сведения
description: Последнюю версию языка C# — версия 6 – постоянно развивается языка меньше стандартных действий, улучшен и согласованность. Синтаксис инициализации очистки, возможность использования await в блоках catch/finally и условием null? оператор особенно полезны.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: fffb78d6f2599c231617ed025d41282beffd319b
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066459"
---
# <a name="c-6-new-features-overview"></a>Возможности C# 6 новые общие сведения

_Последнюю версию языка C# — версия 6 – постоянно развивается языка меньше стандартных действий, улучшен и согласованность. Синтаксис инициализации очистки, возможность использования await в блоках catch/finally и условием null? оператор особенно полезны._

В этом документе представлены новые возможности C# 6. Полностью поддерживается моно компилятором, и разработчики можно начать использовать новые функции на всех целевых платформах Xamarin.

Эта статья содержит краткие фрагментов кода C# 6, иллюстрирующие использование.
Образец приложения является командной строки программа, которая выполняется на всех целевых платформах Xamarin и выполняет различные функции.


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Новые возможности в C# 6 по [университета Xamarin](https://university.xamarin.com/)**


## <a name="development-environment"></a>Среда разработки

### <a name="mac"></a>Mac

* **Visual Studio для Mac** обеспечивает поддержку C# 6: можно создавать, а также компилировать приложения Xamarin с помощью функции C# 6.
  Дополнительные сведения о [Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/).

### <a name="windows"></a>Windows

* **Visual Studio 2015 и 2017 г** и полную поддержку C# 6 выше. Более ранних версиях Visual Studio не будет поддерживать C# 6.

* **Xamarin Studio для Windows** не поддерживает функции C# 6 в редакторе.



## <a name="compiler"></a>Компилятор

Компилятор моно C# 6 включается в моно 4.0 и более поздней версии, который является [бесплатно загрузить](http://www.mono-project.com/download/).
Visual Studio для Mac автоматически обновляет моно установки в вашей системе.

Пользователи Windows должны иметь [Visual Studio 2015 или 2017 г ^](https://visualstudio.microsoft.com/) установлен для компиляции кода C# 6 (даже если выбор Xamarin Studio для Windows, как интерфейс IDE).

^ или *[2015 средства построения Microsoft](http://www.microsoft.com/download/details.aspx?id=48159)* для командной строки компиляции или серверы сборки, например.

## <a name="using-c-6"></a>С помощью C# 6

Компилятор C# 6 используется во всех последних версиях Visual Studio для Mac.
Их с помощью компиляторов командной строки убедитесь, что `mcs --version` возвращает 4.0 или более поздней версии.
Visual Studio для пользователей Mac можно проверьте, установлены ли у них есть моно 4 (или более поздней версии), обратившись к **о Visual Studio для Mac > Visual Studio для Mac > Показать подробности**.

## <a name="less-boilerplate"></a>Меньше стандартных действий
### <a name="using-static"></a>using static
Перечисления и определенных классов, таких как `System.Math`, будут в основном носителями статические значения и функций. В C# 6, можно импортировать все статические члены типа с одним `using static` инструкции. Сравните типичные тригонометрической функции в C# 5 и 6 C#:

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

`using static` не делает открытые `const` полей, таких как `Math.PI` и `Math.E`, доступны непосредственно:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>При помощи статических методов расширения

`using static` Территории работает немного по-разному с помощью методов расширения. Несмотря на то, что методы расширения, написанных с помощью `static`, они не имеет смысла без экземпляра, на которой выполняются операции. Поэтому если `using static` используется с типом, который определяет методы расширения, методы расширения, становятся доступными по типу целевого (метод `this` типа). Например `using static System.Linq.Enumerable` можно использовать для расширения API-Интерфейс `IEnumerable<T>` объекты без перерыва во всех типах LINQ:

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

В предыдущем примере демонстрируется различие в поведении: метод расширения `Enumerable.Where` связан с массивом при статический метод `String.Join` может вызываться без обращения к `String` типа.

### <a name="nameof-expressions"></a>nameof выражения
В некоторых случаях требуется сослаться на имя вы предоставили переменная или поле. В C# 6 `nameof(someVariableOrFieldOrType)` возвратит строку `"someVariableOrFieldOrType"`. Например при порождении `ArgumentException` вы скорее всего, имя которой аргумент является недопустимым для:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

Главным преимуществом `nameof` выражения является их проверяется тип и совместимы с энергопотреблением средство оптимизации кода. Проверка типов из `nameof` выражения особенно Добро пожаловать в ситуациях, где `string` используется для динамического сопоставления типов. Например, в iOS `string` позволяет указать тип, используемый для прототипа `UITableViewCell` объекты в `UITableView`. `nameof` можно убедиться, что эта связь не завершается ошибкой из-за орфографическая или сыром рефакторинг:

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

Оба вызова `SetBinding` проходят одинаковые значения: `nameof(ReactiveType.StringField)` — `"StringField"`, а не `"ReactiveType.StringField"` как изначально может ожидать.

## <a name="null-conditional-operator"></a>Оператор условием NULL
Ранее обновления для C# появились концепции типы, допускающие значения NULL и оператор объединения с null `??` для сокращения объема стандартный код, при обработке значений, допускающие значение NULL. C# 6 продолжает эту тему «оператором условием null» `?.`. При использовании в объект, находящийся в правой части выражения, оператор условием null возвращает значение элемента, если объект не является `null` и `null` в противном случае:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Оба `length0` и `length1` вывести тип `int?`)

В последней строке показано в предыдущем примере `?` оператор условием null в сочетании с `??` оператор объединения с null. Возвращает новый оператор C# 6 условием null `null` на 2-й элемент в массиве, после чего оператор объединения с null, будет запущено и предоставляет 0, чтобы `lengths` массива (независимо от, соответствующие или не присутствует, конечно, зависящие от проблемы).

Оператор условием null невероятно следует уменьшить объем стандартного null Проверка необходимости множество приложений.

Существуют некоторые ограничения на оператор условием null из-за неоднозначности. Не может следовать за немедленно `?` со списком аргументов скобки, как вы может надеемся, что делать с помощью делегата:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Тем не менее `Invoke` может использоваться для разделения `?` из списка аргументов и по-прежнему помеченной улучшения по `null`-Проверка блока шаблона:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Интерполяция строк
`String.Format` Функция традиционно использовал индексов в качестве заполнителей в строке формата, например, `String.Format("Expected: {0} Received: {1}.", expected, received`). Конечно Добавление нового значения всегда участвует досадных задача инвентаризации копирование аргументы, перенумерация заполнители и вставка нового аргумента в правильном порядке в списке аргументов.

C# 6 Новая строка интерполяции функция значительно улучшает работу `String.Format`. Теперь можно непосредственно имя переменные в строке с префиксом `$`. Например:

```csharp
$"Expected: {expected} Received: {received}."
```

Переменные Конечно, проверяются и переменную с ошибками или не доступен приведет к ошибке компилятора.

Заполнители не обязательно должны быть простыми переменными, они могут быть любым выражением. Эти заполнители, вы можете использовать кавычки *без* экранирование этих предложений. Обратите внимание, например, `"s"` в следующем:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

Интерполяции строк поддерживает выравнивания и форматирования синтаксис `String.Format`. Подобно тому, как писали ранее `{index, alignment:format}`, в C# 6 написании `{placeholder, alignment:format}`:

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
результаты в:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Интерполяции строк является синтаксическим сахаром для `String.Format`: не может использоваться с `@""` строковые литералы и не совместим с `const`, даже если не заполнители используются:

```csharp
const string s = $"Foo"; //Error : const requires value
```

В общем случае построения аргументов функции с интерполяции строк по использования необходимо по-прежнему следует с осторожностью экранирование, кодировки и проблем с языком и региональными параметрами. Запросы SQL и URL-адрес, конечно, критически важны для очистки. Как и в `String.Format`, строка использует интерполяцию `CultureInfo.CurrentCulture`. С помощью `CultureInfo.InvariantCulture` является несколько более много слов:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Инициализация

C# 6 предоставляет ряд четкими способов для указания свойств, полей и членов.

### <a name="auto-property-initialization"></a>Инициализация Auto свойство

Теперь автосвойства можно инициализировать таким же образом четкими как поля. Неизменяемые auto свойства могут быть написаны только метод получения:

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

Инициализация свойств автоматически является общей возможностью компактный и весьма полезным для разработчиков подчеркнуть постоянство в их объекты.

### <a name="index-initializers"></a>См. раздел Инициализаторы индекса.

C# 6 представлены инициализаторы индекса, которые позволяют задать ключ и значение в типах, которые имеют индексатора. Как правило, это для `Dictionary`-стиля структуры данных:

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

### <a name="expression-bodied-function-members"></a>Выражение телом функции-члены

Лямбда-функции имеют несколько преимуществ, один из которых просто сохраняется место на диске. Аналогичным образом члены класса телом выражения позволяют небольших функций выражать немного более четко, чем было возможно в предыдущих версиях C# 6.

Выражение телом функции-члены используйте синтаксис стрелок лямбда-выражения, а не блока традиционный синтаксис:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Синтаксис лямбда-выражения со стрелкой не использовать явное уведомление `return`. Для функции, которые возвращают `void`, выражение также должна быть инструкция:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Выражение телом члены, по-прежнему подвержены правило, `async` поддерживается для методов, но не свойства:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Исключения

Имеется не два способа о нем: трудным обработки исключений. Новые функции в C# 6 убедитесь обработки исключений в более гибкими и согласованность.

### <a name="exception-filters"></a>Фильтры исключений

По определению исключения возникают в необычных ситуациях и может быть очень сложно причину и код о *все* одним из способов, может возникнуть исключение определенного типа. C# 6 появилась возможность защиты обработчик выполнения с помощью фильтра вычисляется среды выполнения. Это делается путем добавления `when (bool)` шаблон после обычной `catch(ExceptionType)` объявления. В следующих фильтр отличает Ошибка синтаксического анализа, связанная с `date` параметр, в отличие от других ошибки синтаксического анализа.

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

### <a name="await-in-catchfinally"></a>Ожидание в catch... finally...

`async` Возможности, представленные в C# 5 были переломным для языка. В C# 5 `await` не была разрешена в `catch` и `finally` блокировку, получает номер отвлекающие действия `async/await` возможностей. C# 6 удаляет это ограничение, позволяя асинхронных результатов ожидать постоянно по программе как показано в следующем фрагменте:

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

В языке C# постоянно развивается для повышения производительности разработчиков при также повышение уровня рекомендуемые методы и средства поддержки. В этом документе, давших обзор новых возможностей языка C# 6 и кратко показано, как они используются.

## <a name="related-links"></a>Связанные ссылки

- [Новые возможности языка C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
- [С помощью C# 6 книги](https://developer.xamarin.com/workbooks/csharp/csharp6/csharp6.workbook)
