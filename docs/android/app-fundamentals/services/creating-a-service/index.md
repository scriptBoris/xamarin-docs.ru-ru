---
title: Создание службы
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/03/2018
ms.openlocfilehash: 00785ad161f5f05fd70b059bb0a3f1c8d6c31f97
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33850838"
---
# <a name="creating-a-service"></a>Создание службы

Xamarin.Android службы должна иметь два правила непреодолимой Android служб:

* Они должны расширять [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Должен быть снабжен атрибутом [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Другим требованием Android служб является, то они должны быть зарегистрированы в **AndroidManifest.xml** и присвоено уникальное имя. Xamarin.Android автоматическую регистрацию в манифесте службы во время сборки с необходимого атрибута XML.

Этот фрагмент кода входит Простейший пример создания службы в Xamarin.Android, отвечающий следующим двум требованиям:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Во время компиляции Xamarin.Android зарегистрирует службу, внедряя следующий XML-элемент в **AndroidManifest.xml** (Обратите внимание, что Xamarin.Android создается случайное имя службы):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

Можно использовать службу с другими приложениями Android, _Экспорт_ его. Это делается путем присвоения свойству `Exported` свойство `ServiceAttribute`. При экспорте в службу, `ServiceAttribute.Name` свойства также должен быть установлен для предоставления общих понятное имя для службы. В этом фрагменте показано, как экспортировать и имя службы:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

**AndroidManifest.xml** элемент для этой службы будет затем выглядеть следующим образом:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Службы имеют свои собственные жизненного цикла с помощью методов обратного вызова, которые вызываются при создании службы. Точно какие методы вызываются зависит от типа службы. Служба, запускаемая необходимо реализовать жизненного цикла различных методов, чем связанные службы, пока гибридная служба необходимо реализовать методы обратного вызова для запущенной службы и связанных служб. Эти методы являются элементами `Service` класса; запускается как служба определит, какие методы жизненного цикла будет вызываться. Эти методы жизненного цикла обсуждаются более подробно далее.

По умолчанию служба будет запускаться в том же процессе, как приложения. Можно запустить службу в собственном процессе, задав `ServiceAttribute.IsolatedProcess` значение true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

Следующий шаг — проверить запуск службы и затем перейти к рассмотрим, как реализовать три различных типа службы.

> [!NOTE]
> Служба выполняется в потоке пользовательского интерфейса, если какой-либо работы с выполняемой, который блокирует пользовательского интерфейса, служба должна использовать потоков для выполнения работы.

## <a name="starting-a-service"></a>Запуск службы

Самый простой способ запуска службы в Android — для перенаправления `Intent` которого содержит метаданные для определения, какая служба должна быть запущена. Существует два различных стилей целей, которые могут использоваться для запуска службы.

-   **Явные намерение** &ndash; _явным намерением_ определят точно какие службы следует использовать для выполнения данного действия. Явные целью можно рассматривать как буква, которая имеет определенный адрес; Цель службы, которое явно обозначен направит Android. Этот фрагмент является одним из примеров использования явное целью для запуска службы вызывается `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Неявные намерение** &ndash; этот тип целей слабо определяет действия, что пользователь хочет выполнить, но неизвестно точное службы для выполнения этого действия. Неявные целью можно рассматривать как это буква, адресованное «Кому Whom ИТ может проблемой...».
    Android к содержимому цель и определить наличие существующей службы, которая согласуется с целью.

    _Намерения фильтра_ позволяет соответствовать неявное цели с зарегистрированной службы. Блокировка с намерением фильтр является XML-элемента, добавляемого **AndroidManifest.xml** которого содержит необходимые метаданные службы с целью неявное критерий.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Если существует несколько возможных соответствий для неявного целью Android, он может запрашивать у пользователя выберите компонент для обработки действия:

![Снимок экрана: диалоговое окно устранения неоднозначности](images/creating-a-service-01.png "снимок экрана: диалоговое окно устранения неоднозначности")

> [!IMPORTANT]
> Начиная с Android 5.0 (уровень AP 21) неявное целью не может использоваться для запуска службы.

Если это возможно, приложения должны использовать явную целей для запуска службы. Неявные целью не требует запуска службы &ndash; данный запрос некоторых служб, установленных на устройстве для обработки запроса. Неоднозначный запроса может привести к не в службу обработки запроса или другого приложения без необходимости запуска (что увеличивает нехватки ресурсов на устройстве).

Как назначение зависит от типа службы и обсуждаются более подробно далее в конкретных руководств для каждого типа службы.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Создание условий фильтра для неявного целей

Связываемый с неявное назначение службы, приложение Android необходимо предоставить некоторые метаданные для идентификации функциональные возможности службы. Эти метаданные обеспечивается _намерения фильтры_. Блокировка с намерением фильтров содержит некоторые сведения, например действие или тип данных, которые должны присутствовать в попытка запустить службу. В Xamarin.Android, блокировка с намерением фильтра регистрируется в **AndroidManifest.xml** по службе [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Например, следующий код добавляет намерения фильтр с связанного действия `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Это приведет к записи включаются в **AndroidManifest.xml** файл &ndash; запись, которая поставляется с приложением, таким образом, аналогичный приведенному ниже:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

С основами службы Xamarin.Android в сторону, давайте рассмотрим различные подтипы служб более подробно.


## <a name="related-links"></a>Связанные ссылки

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
