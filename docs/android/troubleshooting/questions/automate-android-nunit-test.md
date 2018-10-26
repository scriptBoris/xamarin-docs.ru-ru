---
title: Как автоматизировать тестовый проект Android NUnit?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: b785ef171d2cb00d4f8f5a17f37d49de17fd3da9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106862"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Как автоматизировать тестовый проект Android NUnit?

> [!NOTE]
> В этом руководстве объясняется, как автоматизировать тестовый проект Android NUnit, не проект Xamarin.UITest. Xamarin.UITest руководства можно найти [здесь](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

При создании **приложение модульного тестирования (Android)** проект в Visual Studio (или **Android модульный тест** проект в Visual Studio для Mac), этот проект, будут запускаться автоматически тестов по умолчанию.
Чтобы запустить тесты NUnit на целевом устройстве, можно создать [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) подкласс, в котором запущена с помощью следующей команды: 

```shell
adb shell am instrument 
```

Следующие шаги поясняют, этот процесс:

1.  Создайте новый файл с именем **TestInstrumentation.cs**: 

    ```cs 
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;
     
    namespace App.Tests {
     
        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {
     
            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }
     
            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```
    В этом файле [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (из **Xamarin.Android.NUnitLite.dll**) является подклассом для создания `TestInstrumentation`.

2.  Реализуйте [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) конструктор и [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) метод. `AddTests` Метод элементы управления, какие тесты все равно выполняются.

3.  Изменить `.csproj` файл, чтобы добавить **TestInstrumentation.cs**. Пример:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

3.  Используйте следующую команду для запуска модульных тестов. Замените `PACKAGE_NAME` с именем пакета приложения (имя пакета можно найти в приложении `/manifest/@package` атрибут находится в **AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  При необходимости можно изменить `.csproj` добавляемый файл `RunTests` целевой объект MSBuild. Это дает возможность вызвать модульных тестов с помощью команды следующего вида:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Следует отметить, что этот новый целевой объект не является обязательным; в более ранних `adb` команда может использоваться вместо `msbuild`.)

Дополнительные сведения об использовании `adb shell am instrument` команду для запуска модульных тестов, см. в разделе разработчика Android [запуск тестов с помощью ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) раздела.


> [!NOTE]
> С помощью [Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) release, по умолчанию имена пакетов для вызываемых оболочек времени Android будет основываться на контрольной суммы MD5 имени выполняется экспорт типа с указанием сборки. Это позволяет полное доменное имя, совпадающее должен предоставляться из двух разных сборках и не возникает ошибка упаковки. Поэтому убедитесь, что используется `Name` свойство `Instrumentation` атрибут для создания имени для чтения ACW или класс.

_Имя ACW должен использоваться в `adb` Приведенная выше команда_.
Переименование рефакторинга C# класс таким образом потребуется изменение `RunTests` команду, чтобы использовать правильное имя ACW.

