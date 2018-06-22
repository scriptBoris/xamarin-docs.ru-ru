---
title: Как вручную установить поддержку Android библиотеки, необходимые для пакетов Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e760a87cbd1e0220ed5cf3a350d3539ffe29650e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766004"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Как вручную установить поддержку Android библиотеки, необходимые для пакетов Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Примеры действий по Xamarin.Android.Support.v4 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Загрузите нужный пакет Xamarin.Android.Support NuGet (например, установив его с помощью диспетчера пакетов NuGet).

Используйте `ildasm` , чтобы проверить, какая версия **android_m2repository.zip** требуется пакет NuGet:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Пример выходных данных:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Загрузить **android\_m2repository.zip** Google по URL-адресу возвращается из **ildasm**. Кроме того, можно проверить, какая версия _Android репозитория поддержки_ в настоящее время установки Android SDK Manager:

![«Диспетчер android SDK Manager отображение Android поддержки репозиторий версии установлены 32»](install-android-support-library-images/sdk-extras.png)

Если версия совпадает со структурой, необходимые для пакета NuGet, нет необходимости загружать никаких новых компонентов. Вы можно вместо этого повторно заархивировать существующих **m2repository** каталог, в котором находится в папке **дополнения\\android** в _путь пакета SDK_ (как показано в верхней части Android Окно диспетчера пакета SDK).

Вычислить хэш MD5 URL-адреса, возвращенные **ildasm**. Формате результирующая строка для использования прописных букв и без пробелов. Например, настройте `$url` переменную как требуется, а затем запустите приведенный ниже 2 (на основе [исходный код C# Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) в PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Пример выходных данных:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Копировать **android\_m2repository.zip** в **% LOCALAPPDATA %\\Xamarin\\моментально\\**  папки. Переименуйте файл для использования хэш MD5 из предыдущих MD5-хэш в вычислении шаг. Пример:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Необязательно) Распакуйте файлы в **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\содержимого\\**  (создание **содержимого\\m2repository** подкаталог). Если пропустить этот шаг, затем первого построения, использующий библиотеку займет немного больше времени, так как он потребуется для завершения этого шага.
Номер версии для подкаталога (**23.4.0.0** в этом примере) не является довольно совпадает с версией пакета NuGet. Можно использовать `ildasm` найти правильный номер версии:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Пример выходных данных:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Загрузите нужный пакет Xamarin.Android.Support NuGet (например, установив его с помощью диспетчера пакетов NuGet).

Дважды щелкните _Xamarin.Android.Support.v4_ сборку _ссылки_ раздела проекта для Android в Visual Studio для Mac для открытия сборки в браузере сборки. Убедитесь, что _язык_ имеет значение раскрывающегося списка _C#_ и выберите верхнего уровня _Xamarin.Android.Support.v4_ сборки в дереве навигации браузера сборки. Найдите `SourceUrl` свойства в одном из `IncludeAndroidResourcesFrom` или `JavaLibraryReference` атрибуты:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Загрузить **android\_m2repository.zip** с помощью Google `SourceUrl` , возвращенные **ildasm**. Кроме того, можно проверить, какая версия _Android репозитория поддержки_ в настоящее время установки Android SDK Manager:

![«Диспетчер android SDK Manager отображение Android поддержки репозиторий версии установлены 32»](install-android-support-library-images/sdk-extras.png)

Если версия совпадает со структурой, необходимые для пакета NuGet, нет необходимости загружать никаких новых компонентов. Вы можно вместо этого повторно заархивировать существующих **m2repository** каталог, в котором находится в папке **дополнения/android** в _путь пакета SDK_ (как показано в верхней части окна Диспетчер Android SDK Manager) .

Вычислить хэш MD5 URL-адреса, возвращенные **ildasm**. Формате результирующая строка для использования прописных букв и без пробелов. Например, настройте параметр строки URL-адреса при необходимости и затем выполните следующую команду **Terminal.app** командной строки:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Другой вариант — использовать `csharp` интерпретатора для запуска [же код C#, использующий Xamarin.Android сам](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Чтобы сделать это, измените `url` переменную как требуется и затем выполните следующую команду **Terminal.app** командной строки:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
Пример выходных данных:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Копировать **android\_m2repository.zip** для **$HOME/.local/share/Xamarin/zips/** папки. Переименуйте файл для использования хэш MD5 из предыдущих MD5-хэш в вычислении шаг. Пример:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Необязательно) Распакуйте архив в: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(создание **содержимого или m2repository** подкаталог). Если пропустить этот шаг, затем первого построения, использующий библиотеку займет немного больше времени, так как он потребуется для завершения этого шага.

Номер версии для подкаталога (**23.4.0.0** в этом примере) не является довольно совпадает с версией пакета NuGet. Как и в **ildasm** шаг ранее, можно использовать обозреватель сборки в Visual Studio для Mac найти правильный номер версии. Найдите `Version` свойства в одном из `IncludeAndroidResourcesFrom` или `JavaLibraryReference` атрибуты:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Дополнительные ссылки

- [Ошибка 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) — Inaccurate «не удалось загрузить. Загрузить {0} и поместить его в каталог {1}.» и «установите пакет: «{0}» в SDK установщика» сообщения об ошибках, связанных с пакетами Xamarin.Android.Support

### <a name="next-steps"></a>Следующие шаги

В этом документе рассматриваются текущее поведение начиная с августа 2016 г. Метода, описанного в этом документе не является частью стабильный проверочный набор для Xamarin, поэтому он может быть поврежден.

Для получения дополнительной помощи, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения см. в разделе [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах контактов, предложения, а также как файл новую ошибку, при необходимости.

