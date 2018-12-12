---
title: Советы по обновлению кода в Unified API
description: В этом документе рассматриваются распространенные ошибки и полезные рекомендации, при обновлении приложения с помощью Xamarin Unified API.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: ce5c8f7cf30407e64464c412359263b52e134675
ms.sourcegitcommit: 2868c968f418cd7cc110f9664f3c3ffb6df1f9af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53267395"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Советы по обновлению кода в Unified API

При обновлении более старые решения Xamarin на единый API, могут возникнуть следующие ошибки.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>Не удалось найти NSInvalidArgumentException раскадровки ошибки

Существует [ошибки](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) в текущей версии Visual Studio для Mac, которая может происходить после преобразование проекта в Unified API-интерфейсы с помощью инструмента автоматического перемещения. После обновления, если вы получаете сообщение об ошибке в форме:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Необходимо выполнить следующую команду, чтобы решить эту проблему, найдите следующий файл целевой сборки действий.

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

В этом файле, вам потребуется найти следующее объявление целевой объект.

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

И добавьте `DependsOnTargets="_CollectBundleResources"` к нему атрибут. Пример:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Сохраните файл, перезагрузите Visual Studio для Mac и выполнить чистую и перестроение проекта. Решением этой проблемы необходимо освободить компанией Xamarin чуть ниже.

## <a name="useful-tips"></a>Полезные советы

После использования средства миграции, по-прежнему возможны некоторые ошибки компилятора, требуя ручного вмешательства.
Возможно, потребуется вручную исправить излишними:

* Сравнение `enum`s может потребоваться `(int)` приведения.

* `NSDictionary.IntValue` Теперь возвращает `nint`, имеется `Int32Value` который можно использовать вместо этого.

* `nfloat` и `nint` типы не могут быть помечены `const`;   `static readonly nint` является разумным альтернативой.

* Вещи, которые ранее были непосредственно в `MonoTouch.` стали обычно в пространстве имен `ObjCRuntime.` пространства имен (например: `MonoTouch.Constants.Version` теперь `ObjCRuntime.Constants.Version`).

* Код, который выполняет сериализацию объектов может быть поврежден, при попытке сериализации `nint` и `nfloat` типов. Не забудьте проверить код сериализации работает неправильно после миграции.

* Иногда автоматизированные средства промахов код внутри `#if #else` директивы условной компиляции. В этом случае необходимо вручную внести исправления (см. описание ошибок приводится ниже).

* Вручную экспортированных методов, с помощью `[Export]` может не устраняться автоматически с помощью средства миграции, например в snippert этот код, необходимо вручную обновить тип возвращаемого значения на `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * На единый API не поддерживает неявное преобразование между NSDate и DateTime платформы .NET, так как он не преобразования без потери данных. Чтобы избежать ошибок, связанных с `DateTimeKind.Unspecified` преобразовать .NET `DateTime` для локальных или UTC до приведения к `NSDate`.

 * Категория Objective-C методы теперь создаются как методы расширения в единый API. Например, код, который ранее использовал `UIView.DrawString` теперь будет ссылаться на `NSString.DrawString` в единый API.

 * Кода, используя классы AVFoundation с `VideoSettings` следует изменить для использования `WeakVideoSettings` свойство. Для этого требуется `Dictionary`, который доступен как свойство для классов параметров, например:

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * NSObject `.ctor(IntPtr)` конструктор было изменено с открытым, чтобы защищенные ([для предотвращения неправильного использования](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

 * `NSAction` было [заменить](~/cross-platform/macios/unified/overview.md#NSAction) с starndard .NET `Action`. Некоторые делегаты simple (один параметр) также были заменены `Action<T>`.

Наконец, см. [различия единый API классической v](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) для поиска изменений к интерфейсам API в коде. Поиск [эту страницу](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/) поможет найти классический API-интерфейсов и что они были обновлены до.

**Примечание:** `MonoTouch.Dialog` пространство имен остается неизменным после миграции. Если код использует **MonoTouch.Dialog** должны продолжать использовать это пространство имен — сделать *не* изменить `MonoTouch.Dialog` для `Dialog`!

## <a name="common-compiler-errors"></a>Типичные ошибки компилятора

Ниже перечислены другие примеры распространенных ошибок вместе с решением:

**Ошибки CS0012: Тип «MonoTouch.UIKit.UIView» определен в сборке, которая не используется.**

Исправление: Обычно это означает, что проект ссылается на компонент или пакет NuGet, который не было создано с помощью на единый API. Следует удалить и повторно добавить все компоненты и NuGet пакеты. Если это не устранит ошибку, внешнюю библиотеку может пока не поддерживает единый API.

**Ошибка MT0034: Не может содержать «monotouch.dll» и «Xamarin.iOS.dll» в том же проекте Xamarin.iOS - «Xamarin.iOS.dll» указывается явным образом, хотя ссылается «monotouch.dll» "Xamarin.Mobile, Version = 0.6.3.0, язык и региональные параметры = neutral, PublicKeyToken = null".**

Исправление: Удалить компонент, который вызывает эту ошибку и снова добавить в проект.

**Ошибки CS0234: Имя типа или пространства имен «Foundation» не существует в пространстве имен «MonoTouch». Возможно, отсутствует ссылка на сборку?**

Исправление: Средство автоматической миграции в Visual Studio для Mac *следует* обновить все `MonoTouch.Foundation` ссылки на `Foundation`, однако в некоторых случаях их потребуется обновить вручную. Похожие ошибки может отображаться для других пространств имен в `MonoTouch`, такие как `UIKit`.

**Ошибка CS0266: Не удается неявно преобразовать тип «double» для «System.float»**

FIX: измените тип и привести к `nfloat`. Эта ошибка также может возникать для других типов с 64-разрядные эквиваленты (такие как `nint`,)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Ошибка CS0266: Не удается неявно преобразовать тип «CoreGraphics.CGRect» для «System.Drawing.RectangleF». Существует явное преобразование (отсутствует приведение?)**

Исправление: Изменение экземпляров для `RectangleF` для `CGRect`, `SizeF` для `CGSize`, и `PointF` для `CGPoint`. Пространство имен `using System.Drawing;` следует заменить `using CoreGraphics;` (если он еще не существует).

**Ошибка CS1502: Наиболее подходящий перегруженный метод "CoreGraphics.CGContext.SetLineDash (System.nfloat, System.nfloat[])" содержит недопустимые аргументы**

Исправление: Измените тип массива на `nfloat[]` и явным образом приведите `Math.PI`.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Ошибка CS0115: «WordsTableSource.RowsInSection (UIKit.UITableView, int)» помечен как переопределение, но не найден подходящий метод для переопределения**

Исправление: Изменение типа возвращаемого значения и параметра для `nint`. Обычно это происходит в переопределения методов, например на `UITableViewSource`, в том числе `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`и т. д.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Ошибка CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView) "**

Исправление: Если тип возвращаемого значения изменяется на `nint`, привести возвращаемое значение к `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Ошибка CS1061: Тип «CoreGraphics.CGPath» не содержит определение для «AddElipseInRect»**

Исправление: Проверка орфографии для `AddEllipseInRect`. Среди других изменений имени:

* Измените «Color.Black» `NSColor.Black`.
* Измените MapKit «AddAnnotation» `AddAnnotations`.
* Измените AVFoundation «DataUsingEncoding» `Encode`.
* Измените AVFoundation «AVMetadataObject.TypeQRCode» `AVMetadataObjectType.QRCode`.
* Измените AVFoundation «VideoSettings» `WeakVideoSettings`.
* Изменить PopViewControllerAnimated для `PopViewController`.
* Измените CoreGraphics «CGBitmapContext.SetRGBFillColor» `SetFillColor`.

**Ошибка CS0546: невозможно переопределить, поскольку «MapKit.MKAnnotation.Coordinate» не имеет функции доступа set (CS0546)**

При создании пользовательские примечания о путем создания подклассов MKAnnotation координат поле имеет не метод задания только метода получения.

[Исправить](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* Добавить поле для отслеживания координаты
* возвращать это поле в методе получения координат свойства
* Переопределите метод SetCoordinate и задать поля
* Вызов SetCoordinate в ваш конструктор с параметром переданный координат

Он должен выглядеть следующим образом:

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>Связанные ссылки

- [Обновление приложений](~/cross-platform/macios/unified/updating-apps.md)
- [Обновление приложения для iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Обновление приложения Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Обновление приложений Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Обновление привязки](~/cross-platform/macios/unified/update-binding.md)
- [Работа с собственными типами в кроссплатформенных приложениях](~/cross-platform/macios/native-types-cross-platform.md)
- [Классический и отличия Unified API](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
