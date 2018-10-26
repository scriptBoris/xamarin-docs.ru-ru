---
title: Как вручную установить библиотеки поддержки Android, необходимые для пакетов Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 84ee33fe174c01656144e55bc3cbba7c773950fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120649"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Как вручную установить библиотеки поддержки Android, необходимые для пакетов Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Примеры действий по Xamarin.Android.Support.v4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Скачайте нужный пакет Xamarin.Android.Support NuGet (например, установив его с помощью диспетчера пакетов NuGet).

Используйте `ildasm` Чтобы проверить, какая версия **android_m2repository.zip** пакету NuGet нужен:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Пример выходных данных:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Скачайте **android\_m2repository.zip** из Google с помощью URL-адрес возвращаются из **ildasm**. Кроме того, можно проверить, какая версия _репозиторий поддержки Android_ в настоящее время установки диспетчер пакетов SDK для Android:

![«Диспетчер пакетов SDK android, показывающий репозиторий поддержки Android версии установлены 32»](install-android-support-library-images/sdk-extras.png)

Если версия совпадает со значением, необходимые для пакета NuGet, не нужно загружать никакие новые файлы. Вы можете вместо переупаковать существующий **m2repository** каталог, который находится в папке **дополнения\\android** в _путь к пакету SDK_ (как показано в верхней части Android Окно диспетчера пакета SDK).

Вычислите хэш MD5 URL-адрес, возвращенный **ildasm**. Формате результирующая строка использование прописных букв и без пробелов. Например, настроить `$url` переменную как требуется, а затем выполните приведенный ниже 2 (на основе [исходный C# код из Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) в PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Пример выходных данных:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Копировать **android\_m2repository.zip** в **% LOCALAPPDATA %\\Xamarin\\zips\\**  папки. Переименуйте файл, чтобы использовать MD5-хэш из предыдущего вычисления шага хэша MD5. Пример:

**% LOCALAPPDATA %\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Необязательно) Распакуйте файл в **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\содержимого\\**  (создание **содержимого\\m2repository** подкаталог). Если пропустить этот шаг, затем первая сборка, использующий библиотеку займет немного больше времени, так как он потребуется для выполнения этого шага.
Номер версии для подкаталог (**23.4.0.0** в этом примере) — это не совсем же версии пакета NuGet. Можно использовать `ildasm` найти правильный номер версии:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Пример выходных данных:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Скачайте нужный пакет Xamarin.Android.Support NuGet (например, установив его с помощью диспетчера пакетов NuGet).

Дважды щелкните _Xamarin.Android.Support.v4_ сборку под _ссылки_ части проекта Android в Visual Studio для Mac открыть ее в обозреватель сборок. Убедитесь, что _языка_ раскрывающегося списка присваивается _C#_ и выберите верхнего уровня _Xamarin.Android.Support.v4_ сборки в дереве навигации обозреватель сборок . Найдите `SourceUrl` свойства в одном из `IncludeAndroidResourcesFrom` или `JavaLibraryReference` атрибуты:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Скачайте **android\_m2repository.zip** из Google с помощью `SourceUrl` возвращаемые **ildasm**. Кроме того, можно проверить, какая версия _репозиторий поддержки Android_ в настоящее время установки диспетчер пакетов SDK для Android:

![«Диспетчер пакетов SDK android, показывающий репозиторий поддержки Android версии установлены 32»](install-android-support-library-images/sdk-extras.png)

Если версия совпадает со значением, необходимые для пакета NuGet, не нужно загружать никакие новые файлы. Вы можете вместо переупаковать существующий **m2repository** каталог, который находится в папке **дополнения/android** в _путь к пакету SDK_ (как показано в верхней части окна диспетчера пакетов SDK для Android) .

Вычислите хэш MD5 URL-адрес, возвращенный **ildasm**. Формате результирующая строка использование прописных букв и без пробелов. Например, настроить строку URL-адрес при необходимости, а затем запустите следующую команду **Terminal.app** командной строки:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Другой вариант — использовать `csharp` интерпретатор для запуска [же C# кода, использующего Xamarin.Android сам](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Чтобы сделать это, измените `url` переменную как требуется и затем выполните следующую команду **Terminal.app** командной строки:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
Пример выходных данных:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Копировать **android\_m2repository.zip** для **$HOME/.local/share/Xamarin/zips/** папки. Переименуйте файл, чтобы использовать MD5-хэш из предыдущего вычисления шага хэша MD5. Пример:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Необязательно) Распакуйте файл в: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(создание **содержимое/m2repository** подкаталог). Если пропустить этот шаг, затем первая сборка, использующий библиотеку займет немного больше времени, так как он потребуется для выполнения этого шага.

Номер версии для подкаталог (**23.4.0.0** в этом примере) — это не совсем же версии пакета NuGet. Как и в **ildasm** шаг ранее, можно использовать обозреватель сборок в Visual Studio для Mac, чтобы найти правильный номер версии. Найдите `Version` свойства в одном из `IncludeAndroidResourcesFrom` или `JavaLibraryReference` атрибуты:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Дополнительные ссылки

- [Ошибка 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) — Inaccurate «не удалось загрузить. Загрузите {0} и поместите его {1} каталога.» и «. Установите пакет: "{0}" доступен в установщике пакета SDK» сообщения об ошибках, связанных с пакетами Xamarin.Android.Support

### <a name="next-steps"></a>Следующие шаги

В этом документе рассматриваются текущее поведение по состоянию на август 2016 г. Метод, описанный в этом документе не входит стабильной тестирования набора для Xamarin, поэтому он может привести к нарушению в будущем.

Дополнительная помощь, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения, ознакомьтесь с разделом [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах связи, предложения, а также как файл новую ошибку, при необходимости.

