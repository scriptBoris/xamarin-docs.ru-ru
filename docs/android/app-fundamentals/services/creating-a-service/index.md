---
title: Создание службы
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 8c2086025ccb5fe41b3ffddc9cd650c1e0c81fbc
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53813989"
---
# <a name="creating-a-service"></a>Создание службы

Xamarin.Android служб накладываются два правила непреодолимой Android служб:

* Их необходимо расширить [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Должен быть снабжен атрибутом [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Еще одно требование Android служб — что они должны быть зарегистрированы в **AndroidManifest.xml** и получив уникальное имя. Xamarin.Android автоматическую регистрацию в манифесте службы во время сборки с помощью необходимого атрибута XML.

Этот фрагмент кода — это самый простой пример создания службы в Xamarin.Android, эти два требованиям:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Во время компиляции, Xamarin.Android будет зарегистрировать службу, внедряя следующий XML-элемент в **AndroidManifest.xml** (Обратите внимание, что Xamarin.Android создается случайное имя службы):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

Можно совместно использовать одну службу с другими приложениями Android, _Экспорт_ его. Это достигается путем установки `Exported` свойство `ServiceAttribute`. При экспорте услуга, `ServiceAttribute.Name` свойство также должно быть присвоено введите понятное имя открытого для службы. В этом фрагменте показано, как для экспорта и имя службы:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

**AndroidManifest.xml** элемент для этой службы затем будет выглядеть примерно так:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Службы имеют свои собственные жизненный цикл с помощью методов обратного вызова, которые вызываются при создании службы. Точно, какие методы вызываются зависит от типа службы. Службы должны реализовывать методы другом жизненном цикле чем привязанную службу, хотя это гибридная служба должен реализовывать методы обратного вызова для работы службы и связанные службы. Эти методы являются членами `Service` класса; запускается как служба определит, какие методы жизненного цикла будет вызываться. Эти методы жизненного цикла обсуждаются более подробно позже.

По умолчанию служба запустится в том же процессе, что приложение Android. Можно запустить службу в своем собственном процессе, задав `ServiceAttribute.IsolatedProcess` присваивается значение true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

Следующий шаг — проверить как запустить службу, а затем перейдем к рассмотрению реализации три разных типа служб.

> [!NOTE]
> Служба выполняется в потоке пользовательского интерфейса, поэтому если какой-либо работы с выполняемой, который блокирует пользовательского интерфейса, служба должна использовать потоки для выполнения работы.

## <a name="starting-a-service"></a>Запуск службы

Самый простой способ запуска службы в Android — отправляемое `Intent` которого содержит метаданные для определения, какая служба должна быть запущена. Существует два различных стиля способов, которые могут использоваться для запуска службы.

-   **Явное намерение** &ndash; _явным намерением_ определит точно какие службы следует использовать для завершения выполнения данного действия. Явные цели может рассматриваться как букву, имеющий определенный адрес; Android направит цель службы, которая идентифицируется явным образом. Этот фрагмент кода является одним из примеров применения явной целью, чтобы запустить службу, именуемую `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Неявное намерение** &ndash; такого рода намерение слабо определяет действия, что пользователь хочет выполнить, но неизвестно точное службы для выполнения этого действия. Неявное намерение можно рассматривать как буквы, устранена «To Whom ИТ мая проблемой...».
    Android к содержимому объекта Intent и определите, есть существующей службы, которая согласуется с целью.

    _Фильтром намерений_ используется для обеспечения соответствия неявное намерение с зарегистрированной службы. Фильтром намерений представляет собой элемент XML, который добавляется **AndroidManifest.xml** которого содержит необходимые метаданные для обеспечения соответствия службы с помощью объекта intent неявное.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Если Android есть несколько возможных соответствий для неявное намерение, он может запрашивать у пользователя выберите компонент для обработки действия:

![Снимок экрана: диалоговое окно устранения неоднозначности](images/creating-a-service-01.png "снимок экрана: диалоговое окно устранения неоднозначности")

> [!IMPORTANT]
> Начиная с Android 5.0 (AP, уровень 21) неявное намерение не может использоваться для запуска службы.

Если это возможно, приложения должны использовать явные объекты Intent для запуска службы. Неявное намерение не требует определенной службы, чтобы запустить &ndash; данный запрос некоторых служб, установленных на устройстве для обработки запроса. Неоднозначные запрос может привести к неверной службе обработки запроса или другом приложении без необходимости запуска (что повышает нагрузку для ресурсов на устройстве).

Как цель зависит от типа службы и обсуждаются более подробно далее в руководства по определенной для каждого типа службы.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Создание фильтром намерений для неявного намерений

Чтобы связать службу с помощью объекта Intent неявное, приложение Android необходимо предоставить некоторые метаданные для определения возможности службы. Эти метаданные обеспечивается _фильтры намерений_. Фильтры намерений содержать некоторые сведения, например, действие или тип данных, которые должны присутствовать в объекта Intent для запуска службы. В Xamarin.Android, фильтр намерений регистрируется в **AndroidManifest.xml** , дополняя услуга с [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Например, следующий код добавляет фильтр намерений с помощью связанного действия `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Это приводит к записи в **AndroidManifest.xml** файл &ndash; запись поставляется в комплекте с приложением в виде, аналогичный приведенному ниже:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

С его основными в сторону службы Xamarin.Android давайте рассмотрим различные подтипы служб более подробно.


## <a name="related-links"></a>Связанные ссылки

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
