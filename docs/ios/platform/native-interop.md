---
title: Ссылки на собственные библиотеки в Xamarin.iOS
description: В этом документе описывается связывание собственные библиотеки C в приложении Xamarin.iOS. Он описывает сборку универсальной собственным библиотекам и доступ к методам C из C#.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: a94c70bb7068847ed1b410dd7eddc70921fdf307
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106043"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Ссылки на собственные библиотеки в Xamarin.iOS

Xamarin.iOS поддерживает связывание с собственные библиотеки C и библиотек Objective-C. В этом документе рассматриваются как связать ваши собственные библиотеки C, к проекту Xamarin.iOS. Сведения о выполнении, то же самое для библиотек Objective-C, см. в разделе наших [типы привязки Objective-C](~/ios/platform/binding-objective-c/index.md) документа.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Создание универсальной собственные библиотеки (i386, ARMv7 и ARM64)

Часто желательно создавать ваши собственные библиотеки для каждого из поддерживаемых платформ для разработки приложений iOS (симулятор и ARMv7 и ARM64 i386 для самих устройств). Если у вас проект Xcode для библиотеки, это совсем несложно сделать.

Чтобы создать версию i386 вашей собственной библиотеки, выполните следующую команду из терминала:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Это приведет к собственной статической библиотеки в разделе `MyProject.xcodeproj/build/Release-iphonesimulator/`. Копирование (или перемещения) Библиотека файл архива (libMyLibrary.a) чтобы использовать безопасные для последующего использования, задав ему уникальное имя (например **libMyLibrary i386.a**) таким образом, чтобы он не пересекаться с версии библиотеки, которую вы будете в arm64 и armv7 разрабатывать дальше.

Чтобы создать версию ARM64 вашей собственной библиотеки, выполните следующую команду:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Это время построения собственной библиотеки будут находиться в `MyProject.xcodeproj/build/Release-iphoneos/`. Опять же, скопируйте (или перемещения) этот файл в безопасном месте, переименовать его в нечто подобное **libMyLibrary arm64.a** таким образом, чтобы он не будут пересекаться.

Теперь сборки ARMv7 версию библиотеки:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Копирование (или перемещения) полученный файл библиотеки в том же расположении вы переместили две версии библиотеки, переименовать его в нечто подобное **libMyLibrary armv7.a**.

Чтобы сделать универсальные двоичный, можно использовать `lipo` средство следующим образом:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

При этом создается `libMyLibrary.a` будет называться библиотеку универсальных (fat), которая будет подходить для использования для всех целей разработки iOS.


### <a name="missing-required-architecture-i386"></a>Отсутствует необходимая архитектура i386

Если вы получаете `does not implement methodSignatureForSelector` или `does not implement doesNotRecognizeSelector` сообщения в выходных данных среды выполнения при попытке использовать библиотеку Objective-C в iOS Simulator, библиотеки, возможно, не была скомпилирована для архитектуры i386 (см. в разделе [стандартных универсальных Собственные библиотеки](#building_native) предыдущем разделе).

Чтобы проверить архитектур, поддерживаемые определенной библиотеке, используйте следующую команду в окне терминала:

```bash
lipo -info /full/path/to/libraryname.a
```

Где `/full/path/to/` — это полный путь к библиотеке использованное и `libraryname.a` не гарантируется имя библиотеки.

Если у вас есть источник в библиотеку, необходимо скомпилировать и объединить его архитектуре i386, если вы хотите тестировать приложение на симуляторе iOS.

### <a name="linking-your-library"></a>Связывание библиотеки

Любые сторонние библиотеки, которую использовать должно быть статически связано с приложением. 

Если требуется статическое связывание библиотеки «libMyLibrary.a», полученный из Интернета или сборки с Xcode, необходимо выполнить ряд действий:

-  Перевести библиотеки в проект
-  Настроить расширение Xamarin.iOS для связывания библиотеки
-  Доступ к методам из библиотеки.


Чтобы **перевести библиотеки в проект**, выберите проект в обозревателе решений и нажмите клавишу **параметр команды + +**. Перейдите к libMyLibrary.a и добавить его в проект. При появлении сообщите Visual Studio для Mac или Visual Studio скопировать его в проект. После его добавления, найдите libFoo.a в проекте, щелкните ее правой кнопкой и установите **действие при построении** для **none**.

Для **Настройка Xamarin.iOS для связывания библиотеки**, на странице параметров для конечного исполняемого файла (не сама библиотека, но итоговую программу), вам нужно добавить в **сборка iOS**его аргумент Extra (это в составе параметров проекта) «-gcc_flags» параметр следуют заключенные в кавычки строка, содержащая все дополнительные библиотеки, необходимые для программы, например:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

Приведенный выше пример будет связан **libMyLibrary.a**

Можно использовать `-gcc_flags` для указания любого набора аргументов командной строки для передачи компилятору GCC, выполняемые последняя ссылка исполняемого файла. Например эта командная строка также ссылается на CFNetwork framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Если вашей собственной библиотеки содержит код C++, необходимо также передать флаг - cxx в «Лишние аргументы», чтобы использовать правильный компилятор знал, Xamarin.iOS. Для C++ прежние параметры выглядит следующим образом:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Доступ к методы C из C&#35;

Доступны два вида библиотек неуправляемого кода в iOS:

-  Общие библиотеки, которые являются частью операционной системы.

-  Статические библиотеки, входящие в состав приложения.


Для доступа к методам, определенные в одном из них, использовать [функции P/Invoke Mono](http://www.mono-project.com/docs/advanced/pinvoke/) которого та же технология, которая использовалась бы в .NET, который составляет приблизительно:

-  Определить, какие функции C, необходимо вызвать
-  Определить его подпись
-  Определить, какую библиотеку, он находится в
-  Написать соответствующий объявление P/Invoke


При использовании P/Invoke, необходимо указать путь к библиотеке, связываемых с. Когда с помощью iOS общие библиотеки, вы можете либо жестко закодировать путь или можно использовать константы удобства, которые мы определили в наших [класс константы](https://developer.xamarin.com/api/type/Constants/), эти константы должны охватывать iOS общих библиотек.

Например, если вы хотите вызвать метод UIRectFrameUsingBlendMode из библиотеки UIKit компании Apple, который имеет следующую сигнатуру в C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

Объявление P/Invoke будет выглядеть следующим образом:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants.UIKitLibrary является просто это константа, определенная как «/ System/Library/Frameworks/UIKit.framework/UIKit», EntryPoint позволяет при необходимости указать внешнее имя (UIRectFramUsingBlendMode) при предоставлении доступа к другим именем в C#, короткий RectFrameUsingBlendMode.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Доступ к C Dylibs

Если у вас есть необходимость использовать Dylib C в приложении Xamarin.iOS, есть много дополнительной установки, который необходим перед вызовом `DllImport` атрибута.

Например, если у нас есть `Animal.dylib` с `Animal_Version` метод, который мы назовем в нашем приложении, необходимо сообщить Xamarin.iOS расположения библиотеки, прежде чем пытаться использовать его.

Чтобы сделать это, измените `Main.CS` файл и это должно выглядеть следующим образом:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Где `/full/path/to/` — это полный путь к Dylib использованное. Этот код в месте, мы можно будет привязать `Animal_Version` метод следующим образом:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Статические библиотеки

Поскольку статические библиотеки можно использовать только в iOS, нет без внешних общей библиотеки для компоновки, поэтому параметр path в атрибут DllImport должен использовать специальное имя `__Internal` (Обратите внимание, с двойного символа подчеркивания в начале имени) в отличие от имя пути.

Это заставляет DllImport, чтобы искать ее символ метода, который указывает ссылка в текущей программе, вместо того, чтобы загрузить его из общей библиотеки.

