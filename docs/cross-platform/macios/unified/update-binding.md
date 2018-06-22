---
title: Миграция привязку к унифицированный интерфейс API
description: В этой статье рассматриваются действия, необходимые для обновления существующего Xamarin привязки проекта для поддержки единого API-интерфейсы для приложений, Xamarin.IOS и Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57f27bf0d3aaa2a7ba14f23481a8f2bb2d87f2
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918491"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Миграция привязку к унифицированный интерфейс API

_В этой статье рассматриваются действия, необходимые для обновления существующего Xamarin привязки проекта для поддержки единого API-интерфейсы для приложений, Xamarin.IOS и Xamarin.Mac._

## <a name="overview"></a>Обзор

Начиная с 1 февраля 2015 г. Apple требует, что новый передачу iTunes и магазине приложений Mac должны быть 64-разрядные приложения. В результате любого нового Xamarin.iOS и Xamarin.Mac приложения необходимо использовать новый API единой вместо существующего классического MonoTouch и MonoMac API-интерфейсы для поддержки 64-разрядная версия.

Кроме того все привязки проекта Xamarin должно также поддерживать новые API единой должны быть включены в 64-разрядных Xamarin.iOS или Xamarin.Mac проекта. В этой статье мы рассмотрим шаги, необходимые для обновления существующего проекта привязки для использования в единой API.

## <a name="requirements"></a>Требования

Для завершения действия, описанные в этой статье требуется следующее:

 -  **Visual Studio для Mac** -последнюю версию Visual Studio для Mac установлен и настроен на компьютере разработчика.
 -  **Apple Mac** — это Apple mac, необходимые для построения проектов привязки для iOS и Macintosh.

Проекты привязки не поддерживаются в Visual studio на компьютере с ОС Windows.

## <a name="modify-the-using-statements"></a>Изменить с помощью инструкции

API-интерфейсы единой упрощает никогда совместное использование кода Mac и iOS, а также позволяя для поддержки 32- и 64 разрядных приложений с тем же двоичный. Путем перетаскивания _MonoMac_ и _MonoTouch_ префиксы из пространств имен, общего доступа достигается между проектами приложений Xamarin.Mac и Xamarin.iOS.

Поэтому необходимо изменять какие-либо контракты привязки (и другие `.cs` файлы проекта привязки) для удаления _MonoMac_ и _MonoTouch_ префиксы из наших `using` инструкции.

Например, при наличии следующих с помощью инструкций в контракте привязки:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Мы бы удалили `MonoTouch` префикс, что приводит к следующим образом:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Опять же, необходимо сделать это для любого `.cs` файл в проект привязки. После этого изменения на месте Далее шагом является обновление проектов привязки для использования нового собственные типы данных.

Дополнительные сведения об интерфейсе API единой см. в разделе [единой API](~/cross-platform/macios/unified/index.md) документации. Дополнительные сведения о поддержке 32- и 64 разрядных приложений и сведения о платформах см [32 и 64 бит замечания о платформе](~/cross-platform/macios/32-and-64/index.md) документации.

## <a name="update-to-native-data-types"></a>Обновление для собственных типов данных

Сопоставляет Objective-C `NSInteger` тип данных, который `int32_t` на 32-разрядных системах и с `int64_t` на 64-разрядных системах. Чтобы сопоставить это поведение, новый API единой заменяет прежнего использования `int` (в .NET определяется как всегда, `System.Int32`) в новый тип данных: `System.nint`.

Вместе с новой `nint` представляет тип данных API единой `nuint` и `nfloat` типов для сопоставления `NSUInteger` и `CGFloat` типов, а также.

Учитывая все вышесказанное, нам нужно просмотреть наш API и убедитесь, что любой экземпляр `NSInteger`, `NSUInteger` и `CGFloat` , мы ранее сопоставлен `int`, `uint` и `float` обновления к новому `nint`, `nuint` и `nfloat` типов.

Например если определение метода Objective-C:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Если предыдущие контракта привязка имеет следующее определение:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Корпорация Майкрософт будет обновлять новую привязку, чтобы быть:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Если выполняется отображение в более новой версии 3-й стороны библиотеку чем то, что мы изначально связан с, нужно проверить `.h` файлы заголовка для библиотеки и см. При наличии существующих явные вызовы `int`, `int32_t`, `unsigned int`, `uint32_t` или `float` были обновлены до быть `NSInteger`, `NSUInteger` или `CGFloat`. В этом случае изменять `nint`, `nuint` и `nfloat` будет необходимо сделать для их сопоставлений, а также типы.

Дополнительные сведения об этих изменениях типов данных см. в разделе [собственные типы](~/cross-platform/macios/nativetypes.md) документа.

## <a name="update-the-coregraphics-types"></a>Обновление типов CoreGraphics

Точка, размер и прямоугольник типы данных, которые используются с `CoreGraphics` использовать 32- или 64 бита в зависимости от устройства, они работают под управлением. Первоначально привязан Xamarin iOS и Mac API-интерфейсов можно использовать существующие структуры данных, которые случились для сопоставления типов данных в `System.Drawing` (`RectangleF` например).

Требования для поддержки 64 бита и новые типы данных в собственном формате, следующие изменения будет необходимо внести для существующего кода, при вызове `CoreGraphic` методов:

- **CGRect** -используйте `CGRect` вместо `RectangleF` при определении плавающей точки прямоугольных областей.
- **CGSize** -используйте `CGSize` вместо `SizeF` при определении плавающей точки размеры (ширина и высота).
- **CGPoint** -используйте `CGPoint` вместо `PointF` при расположение (координаты X и Y) определение плавающей точки.

Учитывая все вышесказанное, нам потребуется просмотреть наш API и убедитесь, что любой экземпляр `CGRect`, `CGSize` или `CGPoint` ранее был привязан к `RectangleF`, `SizeF` или `PointF` изменить собственный тип `CGRect`, `CGSize` или `CGPoint` напрямую.

Например если инициализатор Objective-C:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Если наш предыдущая привязка включен следующий код:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Корпорация Майкрософт будет обновлять этот код для:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Все изменения кода, теперь в месте, нам нужно изменить привязки проекта, либо поместить файл для привязки к единой API-интерфейсы.

## <a name="modify-the-binding-project"></a>Изменить привязки проекта

На последнем этапе обновления проектов привязки для использования API-интерфейсы единой, нам нужно либо изменить `MakeFile` , мы используем для построения проекта или типа проекта Xamarin (если выполняется привязка из среды Visual Studio для Mac) и указать _btouch_  для привязки к API единой вместо классического из них.


### <a name="updating-a-makefile"></a>Обновление файла MakeFile

При использовании файла makefile для построения проектов привязки в Xamarin. Библиотеки DLL, необходимо включить `--new-style` параметр командной строки и вызова `btouch-native` вместо `btouch`.

Поэтому даны следующие `MakeFile`:

```csharp
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```

Необходимо вызвать `btouch` для `btouch-native`, поэтому мы бы настроить нашей макроопределения следующим образом:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Корпорация Майкрософт будет обновлять вызов `btouch` и добавьте `--new-style` параметр следующим образом:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Теперь можно выполнить нашей `MakeFile` в обычном режиме, чтобы построить новую версию 64-разрядная версия нашем API.

### <a name="updating-a-binding-project-type"></a>Обновление типа привязки проекта

Если мы используем Visual Studio для привязки шаблона проекта для Mac в нашем API сборки, нам нужно будет обновить до новой версии API единой привязки шаблона проекта. Самый простой способ сделать это — начать новый проект привязки единой API и копировать все существующий код и параметры.

Выполните следующие действия:

1. Запустите Visual Studio для Mac.
2. Выберите **файл** > **новый** > **решения...**
3. В диалоговом окне нового решения выберите **iOS** > **единой API** > **iOS привязки проекта**: 

    [![](update-binding-images/image01new.png "В диалоговом окне нового решения выберите iOS / единой API / Project привязки iOS")](update-binding-images/image01new.png#lightbox)
4. В диалоговом окне «Настройка нового проекта» введите **имя** новый проект привязки и нажмите кнопку **ОК** кнопки.
5. Включите библиотеки Objective-C, нужно создавать привязки для 64-разрядную версию.
6. Скопировать исходный код из существующего проекта привязки классический API 32-разрядная версия (такие как `ApiDefinition.cs` и `StructsAndEnums.cs` файлов).
7. Внести указанные изменения файлов исходного кода.

Со всеми изменениями можно построить новый 64-разрядную версию API-интерфейса, как и 32-разрядной версии.

## <a name="summary"></a>Сводка

В этой статье мы показали изменения, которые должны быть выполнены в существующий проект Xamarin привязки для поддержки новых API единой и 64-разрядных устройствах и шаги, необходимые для построения нового 64-разрядная версия совместимую версию API.



## <a name="related-links"></a>Связанные ссылки

- [IOS и Mac](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [Требования к платформам 32 или 64-разрядная](~/cross-platform/macios/32-and-64/index.md)
- [Обновление существующего приложений iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
