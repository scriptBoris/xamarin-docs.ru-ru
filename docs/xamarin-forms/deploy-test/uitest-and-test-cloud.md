---
title: Автоматизация тестирования Xamarin.Forms с помощью Центра приложений
description: Компонент UITest Xamarin можно использовать с Xamarin.Forms для написания тестов пользовательского интерфейса, запускаемых в облаке на сотнях устройств.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: b674db3d-c526-4e31-a9f4-b6d6528ce7a9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 41b23b9521b0324aeb6e94cd48ae5525c7639e07
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052113"
---
# <a name="automate-xamarinforms-testing-with-app-center"></a>Автоматизация тестирования Xamarin.Forms с помощью Центра приложений

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)

_Компонент UITest Xamarin можно использовать с Xamarin.Forms для написания тестов пользовательского интерфейса, запускаемых в облаке на сотнях устройств._

## <a name="overview"></a>Обзор

Служба **[тестов Центра приложений](/appcenter/test-cloud/)** позволяет разработчикам создавать автоматические тесты пользовательского интерфейса для приложений iOS и Android. После внесения некоторых незначительных изменений приложения Xamarin.Forms можно тестировать с помощью Xamarin.UITest, включая совместное использование одного кода теста. В этой статье приводятся конкретные советы по работе Xamarin.UITest с Xamarin.Forms.

В этом руководстве предполагается, что вы уже знакомы с Xamarin.UITest. Для получения сведений о Xamarin.UITest рекомендуется обратиться к следующим руководствам:

- [Введение в службу тестов Центра приложений](/appcenter/test-cloud/)
- [Введение в UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)

После добавления проекта UITest в решение Xamarin.Forms выполняются действия по написанию и запуску тестов для приложения Xamarin.Forms, аналогичные действиям для приложений Xamarin.Android и Xamarin.iOS.

## <a name="requirements"></a>Требования

Чтобы убедиться, что проект готов для автоматического тестирования пользовательского интерфейса, см. раздел о [Xamarin.UITest](/appcenter/test-cloud/uitest/).

## <a name="adding-uitest-support-to-xamarinforms-apps"></a>Добавление поддержки UITest в приложения Xamarin.Forms

UITest автоматизирует пользовательский интерфейс путем активации элементов управления на экране и ввода данных в любом месте, где пользователь обычно взаимодействует с приложением. Чтобы включить тесты, которые могут *нажимать кнопку* или *вводить текст в поле*, коду теста потребуется способ определения элементов управления на экране.

Чтобы код UITest ссылался на элементы управления, каждому элементу управления требуется уникальный идентификатор. В Xamarin.Forms рекомендуется задавать эти идентификаторы при помощи свойства [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId), как показано ниже:

```csharp
var b = new Button {
    Text = "Click me",
    AutomationId = "MyButton"
};
var l = new Label {
    Text = "Hello, Xamarin.Forms!",
    AutomationId = "MyLabel"
};
```

Свойство [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) можно также задать в XAML:

```xaml
<Button x:Name="b" AutomationId="MyButton" Text="Click me"/>
<Label x:Name="l" AutomationId="MyLabel" Text="Hello, Xamarin.Forms!" />
```

> [!NOTE]
> [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) является [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), поэтому его также можно задать выражением привязки.

Уникальный [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) следует добавить всем элементам управления, необходимым для тестирования (включая кнопки, текстовые элементы и метки, для которых может запрашиваться значение).

> [Внимание!] Возникнет исключение `InvalidOperationException`, если вы попытаетесь задать свойство [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) в каком-то [`Element`](xref:Xamarin.Forms.Element) более одного раза.

### <a name="ios-application-project"></a>Проект приложения iOS

Для запуска тестов на платформе iOS в проект необходимо добавить [пакет NuGet агента Xamarin Test Cloud](https://www.nuget.org/packages/Xamarin.TestCloud.Agent/). После добавления пакета скопируйте следующий код в метод `AppDelegate.FinishedLaunching`:

```csharp
#if ENABLE_TEST_CLOUD
// requires Xamarin Test Cloud Agent
Xamarin.Calabash.Start();
#endif
```

В сборке Calabash используются не являющиеся общими API-интерфейсы Apple, поэтому Магазин приложений будет отклонять приложения. Однако компоновщик Xamarin.iOS удалит сборку Calabash из окончательного API, если на него отсутствует явная ссылка из кода.

> [!NOTE]
> В сборках выпуска отсутствует переменная компилятора `ENABLE_TEST_CLOUD`, что приводит к удалению сборки Calabash из пакета приложения. Однако в сборках отладки есть определенная директива компилятора, не позволяющая компоновщику удалять сборку.

На следующем снимке экрана показана переменная компилятора `ENABLE_TEST_CLOUD`, заданная для сборок отладки:

::: zone pivot="windows"

![](uitest-and-test-cloud-images/12-compiler-directive-vs.png "Параметры сборки")

::: zone-end
::: zone pivot="macos"

![](uitest-and-test-cloud-images/11-compiler-directive-xs.png "Параметры компилятора")

::: zone-end

### <a name="android-application-project"></a>Проект приложения Android

В отличие от iOS, проектам Android не требуется специальный код запуска.

## <a name="writing-uitests"></a>Написание тестов пользовательского интерфейса

Сведения о написании тестов пользовательского интерфейса см. в [документации по тестам пользовательского интерфейса](/appcenter/test-cloud/preparing-for-upload/uitest/). Приведенные ниже действия представляют собой сводные данные, конкретным образом описывающие создание [демонстрации Xamarin.Forms **с использованием теста пользовательского интерфейса UITest**](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/).

### <a name="use-automationid-in-the-xamarinforms-ui"></a>Использование AutomationId в пользовательском интерфейсе Xamarin.Forms

Перед написанием тестов пользовательского интерфейса необходимо убедиться, что пользовательский интерфейс Xamarin.Forms поддерживает сценарии. Все элементы управления в пользовательском интерфейсе должны иметь свойство [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId), чтобы на них можно было ссылаться в коде теста.

#### <a name="referring-to-the-automationid-in-uitests"></a>Ссылка на AutomationId в тестах пользовательского интерфейса

При написании тестов пользовательского интерфейса значение [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) на разных платформах предоставляется по-разному:

- в **iOS** используется поле `id`;
- в **Android** используется поле `label`.

Для написания кроссплатформенных тестов пользовательского интерфейса, которые будут находить [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) на iOS и Android, используйте запрос теста `Marked`:

```csharp
app.Query(c=>c.Marked("MyButton"))
```

Также работает короткая форма `app.Query("MyButton")`.

### <a name="adding-a-uitest-project-to-an-existing-solution"></a>Добавление проекта UITest в существующее решение

::: zone pivot="windows"

В Visual Studio есть шаблон, позволяющий добавить проект Xamarin.UITest в существующее решение Xamarin.Forms:

1. Щелкните решение правой кнопкой мыши и выберите **Файл > Новый проект**.
1. В разделе шаблонов **Visual C#** выберите категорию **Тест**. Выберите шаблон **Приложение тестирования пользовательского интерфейса > Кроссплатформенный**:

    ![Добавление нового проекта](uitest-and-test-cloud-images/08-new-uitest-project-vs.png "Добавление нового проекта")

    В решение будет добавлен новый проект с пакетами NuGet **NUnit**, **Xamarin.UITest** и **NUnitTestAdapter**.

    ![Диспетчер пакетов NuGet](uitest-and-test-cloud-images/09-new-uitest-project-xs.png "Диспетчер пакетов NuGet")

    **NUnitTestAdapter** является сторонним средством выполнения тестов, который позволяет Visual Studio запускать тесты NUnit из Visual Studio.

    Новый проект также содержит два класса. **AppInitializer** содержит код для инициализации и настройки тестов. Другой класс **Tests** содержит стандартный код для запуска тестов пользовательского интерфейса.

1. Добавьте ссылку на проект из проекта UITest в проект Xamarin.Android:

    ![Диспетчер ссылок проекта](uitest-and-test-cloud-images/10-test-apps-vs.png "Диспетчер ссылок проекта")

    Это позволит **NUnitTestAdapter** запускать тесты пользовательского интерфейса для приложения Android из Visual Studio.

::: zone-end
::: zone pivot="macos"

Добавить новый проект Xamarin.UITest в существующее решение можно вручную:

1. Начните с добавления нового проекта, выделив решение и выбрав **Файл > Добавить новый проект**. В диалоговом окне **Новый проект** выберите **Кроссплатформенные > Тесты > Xamarin Test Cloud > Приложение тестирования пользовательского интерфейса**:

    ![Выбор шаблона](uitest-and-test-cloud-images/02-new-uitest-project-xs.png "Выбор шаблона")

    В решение будет добавлен новый проект, уже имеющий пакеты **NUnit** и **Xamarin.UITest**:

    ![Пакеты NuGet Xamarin UITest](uitest-and-test-cloud-images/03-new-uitest-project-xs.png "Пакеты NuGet Xamarin UITest")

    Новый проект также содержит два класса. **AppInitializer** содержит код для инициализации и настройки тестов. Другой класс **Tests** содержит стандартный код для запуска тестов пользовательского интерфейса.

1. Выберите **Вид > Панели > Модульные тесты** для отображения панели модульных тестов. Разверните узел **UsingUITest > UsingUITest.UITests > Тестовые приложения**:

    ![Панель модульных тестов](uitest-and-test-cloud-images/04-unit-test-pad-xs.png "Панель модульных тестов")

1. Щелкните правой кнопкой мыши **Тестовые приложения**, выберите пункт **Добавить проект приложения** и в открывшемся диалоговом окне выберите проекты iOS и Android:

    ![Диалоговое окно тестовых приложений](uitest-and-test-cloud-images/05-add-test-apps-xs.png "Диалоговое окно тестовых приложений")

    Теперь на панели **Модульный тест** должна находиться ссылка на проекты iOS и Android. Это позволит средству запуска тестов Visual Studio для Mac локально выполнять тесты пользовательского интерфейса в двух проектах Xamarin.Forms.

#### <a name="adding-uitest-to-the-ios-app"></a>Добавление теста пользовательского интерфейса в приложение iOS

Существует ряд дополнительных изменений, которые необходимо внести в приложение iOS до начала работы Xamarin.UITest:

1. Добавьте пакет NuGet **агента Xamarin Test Cloud**. Щелкните правой кнопкой мыши **Пакеты**, выберите пункт **Добавить пакеты**, найдите пакет NuGet для **агента Xamarin Test Cloud** и добавьте его в проект Xamarin.iOS:

    ![Добавление пакетов NuGet](uitest-and-test-cloud-images/07-add-test-cloud-agent-xs.png "Добавление пакетов NuGet")

1. Измените метод `FinishedLaunching` класса **AppDelegate** для инициализации агента Xamarin Test Cloud при запуске приложения iOS, а также для задания свойства `AutomationId` представлений. Метод `FinishedLaunching` должен иметь вид, аналогичный приведенному ниже коду:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    #if ENABLE_TEST_CLOUD
    Xamarin.Calabash.Start();
    #endif

    global::Xamarin.Forms.Forms.Init();

    LoadApplication(new App());

    return base.FinishedLaunching(app, options);
}
```

::: zone-end

После добавления Xamarin.UITest в решение Xamarin.Forms можно создавать тесты пользовательского интерфейса, запускать их локально и отправлять их в Xamarin Test Cloud.

## <a name="summary"></a>Сводка

Приложения Xamarin.Forms можно легко тестировать с помощью **Xamarin.UITest**, используя простой механизм для предоставления [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) в качестве уникального идентификатора вида для автоматизации тестирования. После добавления проекта UITest в решение Xamarin.Forms выполняются действия по написанию и запуску тестов для приложения Xamarin.Forms, аналогичные действиям для приложений Xamarin.Android и Xamarin.iOS.

Сведения об отправке тестов в службу тестов Центра приложений см. в статье об [отправке тестов пользовательского интерфейса](/appcenter/test-cloud/preparing-for-upload/uitest/). Дополнительные сведения о компоненте UITest см. в [документации по службе тестов Центра приложений](/appcenter/test-cloud/).

## <a name="related-links"></a>Связанные ссылки

- [UITestSample](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Тестирование Центра приложений](/appcenter/test-cloud/)
- [Xamarin.UITest](/appcenter/test-cloud/uitest/)
- [NUnit](http://www.nunit.org)
