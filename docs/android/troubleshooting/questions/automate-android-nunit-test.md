---
title: Как автоматизировать Android NUnit тестового проекта?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2018
ms.openlocfilehash: f63a25f36682038b7fcd85d711d980b9e3ec869d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763775"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Как автоматизировать Android NUnit тестового проекта?

> [!NOTE]
> В этом руководстве объясняется, как автоматизировать Android NUnit тестовый проект, не Xamarin.UITest проекта. Можно найти руководства Xamarin.UITest [здесь](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

При создании **приложение модульного тестирования (Android)** проекта в Visual Studio (или **Android модульного теста** проекта в Visual Studio для Mac), в этом проекте будут автоматически выполнения тестов по умолчанию.
Для выполнения тестов NUnit на целевое устройство, можно создать [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) подкласс, в котором запускается с помощью следующей команды: 

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
    В этом файле [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (из **Xamarin.Android.NUnitLite.dll**) является подклассом создание `TestInstrumentation`.

2.  Реализуйте [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) конструктор и [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) метод. `AddTests` Метод элементов управления, фактически выполняются тесты.

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

4.  При необходимости можно изменить `.csproj` файл, чтобы добавить `RunTests` целевой объект MSBuild. Это дает возможность вызова неуправляемого кода модульных тестов с помощью команды следующим образом:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Следует отметить, что этот новый целевой объект не является обязательным; раннюю `adb` команда может использоваться вместо `msbuild`.)

Дополнительные сведения об использовании `adb shell am instrument` команду для запуска модульных тестов см. в разделе разработчика Android [запуск тестов с помощью ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) раздела.


> [!NOTE]
> С [Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) выпуска пакета имена по умолчанию для Android с помощью вызываемых оболочек будет основываться на команду MD5SUM экспортируемого типа имени сборки. Это позволяет полностью уточненное имя, совпадающее должен предоставляться из двух разных сборках и не возникает ошибка упаковки. Поэтому убедитесь, что используется `Name` свойство `Instrumentation` атрибут для создания имени для чтения ACW или класс.

_Необходимо использовать имя ACW в `adb` выше команда_.
Переименование и рефакторинг классов C# таким образом потребуется изменение `RunTests` команду, чтобы использовать правильное имя ACW.

