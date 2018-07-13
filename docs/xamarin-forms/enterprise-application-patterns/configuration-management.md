---
title: Управление конфигурацией
description: В данной главе объясняется, как мобильное приложение eShopOnContainers реализует управление конфигурацией для предоставления параметров приложения и параметров пользователя.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6f32d8f328232bdfc644da57bdb3201c60010063
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995365"
---
# <a name="configuration-management"></a>Управление конфигурацией

Параметры позволяют разделение данных, используемый для настройки поведения приложения из кода, позволяя поведение, которое должно быть изменено без повторной сборки приложения. Существует два типа параметров: параметры приложения и параметры пользователя.

Параметры приложений — это данные, приложение создает и управляет ими. Он может включать данные, например предопределенной конечными точками, ключи API и состояния среды выполнения. Параметры приложения зависят от существования приложения, поэтому имеют значение только для этого приложения.

Параметры пользователя являются настраиваемых параметров приложения, которые влияют на поведение приложения и не требуют частого повторной настройки. Например приложение может быть реализована возможность указать, где для получения данных из и способ ее отображения на экране.

Xamarin.Forms включает в себя постоянные словарь, который может использоваться для хранения данных параметров. Этот словарь может осуществляться с использованием [ `Application.Current.Properties` ](xref:Xamarin.Forms.Application.Properties) свойство и любые данные, помещенные в него сохраняется, когда приложение переходит в спящий режим и восстанавливается, когда приложение возобновляет работу или повторного запуска. Кроме того [ `Application` ](xref:Xamarin.Forms.Application) класс также имеет [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) метод, который позволяет приложению иметь его параметры, сохраненные при необходимости. Дополнительные сведения о данного словаря, см. в разделе [словарь свойств](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Слабое место хранения данных, используя словарь постоянных Xamarin.Forms — это не просто привязанным к данным. Таким образом, мобильное приложение eShopOnContainers использует библиотеку Xam.Plugins.Settings из [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Эта библиотека предоставляет согласованный, строго типизированным, кросс платформенных подход для сохранения и извлечения параметров приложения и пользователя, при использовании управления собственные параметры, предоставляемые каждой платформы. Кроме того она проста в использовании привязки данных для доступа к данным параметры, предоставляемые библиотекой.

> [!NOTE]
> Хотя Xam.Plugin.Settings библиотеки можно хранить параметры приложения и пользователя, его нет различий между двумя.

## <a name="creating-a-settings-class"></a>Создание класса параметров

При использовании библиотеки Xam.Plugins.Settings один статический класс должен быть создан, будет содержать приложения и пользовательские параметры, необходимые приложению. В следующем примере кода показан класс параметры в мобильном приложении eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Параметры можно считывать и записывать `ISettings` API, предоставляемый библиотеке Xam.Plugins.Settings. Эта библиотека предоставляет одноэлементным. он может использоваться для доступа к API, `CrossSettings.Current`, и класса параметров приложения должны предоставлять этом единственном экземпляре через `ISettings` свойство.

> [!NOTE]
> Директивы using для пространства имен Plugin.Settings и Plugin.Settings.Abstractions должны быть добавлены к классу, который требуется доступ к типам Xam.Plugins.Settings библиотеки.

## <a name="adding-a-setting"></a>При добавлении параметра

Каждый параметр состоит из ключа, значение по умолчанию и свойство. В следующем примере кода показаны все три элемента, для параметра пользователя, который представляет базовый URL-адрес для веб-служб, мобильное приложение eShopOnContainers подключается к:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

Ключ является всегда const строка, определяющая имя ключа, значением по умолчанию для параметра статического только для чтения значение требуемого типа. Значения по умолчанию обеспечивает допустимое значение доступны, если удается извлечь параметр Отменить задание.

`UrlBase` Статическое свойство с помощью двух методов из `ISettings` API для чтения или записи значения параметра. `ISettings.GetValueOrDefault` Метод используется для получения значения параметра из платформы хранения. Если значение не определено для параметра, вместо этого извлекается значение по умолчанию. Аналогичным образом `ISettings.AddOrUpdateValue` метод используется для сохранения значения параметра для конкретной платформы хранения.

Вместо этого, которые определяют значение по умолчанию внутри `Settings` класс, `UrlBaseDefault` строка получает свое значение из `GlobalSetting` класса. В следующем коде показано в примере `BaseEndpoint` свойство и `UpdateEndpoint` метод в этом классе:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Каждый раз `BaseEndpoint` свойство задано, `UpdateEndpoint` вызывается метод. Этот метод обновляет ряд свойств, которые основаны на `UrlBase` пользовательского параметра, которые предоставляются `Settings` класс, представляющий различные конечные точки, мобильное приложение eShopOnContainers подключается к.

## <a name="data-binding-to-user-settings"></a>Привязка данных к параметров пользователя

В мобильном приложении eShopOnContainers `SettingsView` предоставляет два параметра пользователя. Эти параметры позволяют настраивать ли приложение должно получить данные из микрослужб, которые разворачиваются как контейнеры Docker, или ли приложение должно получать данные из макеты служб, которые не требуют подключения к Интернету. При выборе для извлечения данных из контейнерных микрослужб, необходимо указать URL-адрес базовой конечной точки для микрослужб. Рис. 7-1 показана `SettingsView` когда пользователь выбрал для извлечения данных из контейнерных микрослужб.

![](configuration-management-images/settings-endpoint.png "Пользовательские параметры, предоставляемые в мобильном приложении eShopOnContainers")

**Рис. 7-1**: пользовательские параметры, предоставляемые в мобильном приложении eShopOnContainers

Можно использовать привязку к данным, чтобы получить и задать параметры, предоставляемые `Settings` класса. Это достигается путем элементы управления для представления привязки к свойствам модели представления, которые в свою очередь доступа к свойствам в `Settings` класса и вызов свойства изменить уведомление, если изменилось значение параметров. Сведения о том, как мобильное приложение eShopOnContainers создает представление модели и связывает их с представлениями, см. в разделе [автоматическое создание модели представления с указателем модели представления](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

В следующем коде показано в примере [ `Entry` ](xref:Xamarin.Forms.Entry) управления из `SettingsView` , позволяющий пользователю ввести URL-адрес базовой конечной точки для контейнерных микрослужб:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Это [ `Entry` ](xref:Xamarin.Forms.Entry) привязан элемент управления `Endpoint` свойство `SettingsViewModel` класса, используя двустороннюю привязку. В следующем примере кода показано свойство конечной точки:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Когда `Endpoint` свойству `UpdateEndpoint` вызывается метод, возникает, если предоставленное значение является допустимым, а изменить свойство уведомления. В следующем коде показано в примере `UpdateEndpoint` метод:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Этот метод обновляет `UrlBase` свойство в `Settings` класса со значением URL-адрес базовой конечной точки, введенный пользователем, что приведет к будут передаваться в хранилище с платформой.

Когда `SettingsView` осуществляется переход, `InitializeAsync` метод в `SettingsViewModel` класс выполняется. В следующем примере кода показан этот метод.

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Метода задает `Endpoint` свойства к значению `UrlBase` свойство в `Settings` класса. Доступ к `UrlBase` вызывает библиотеку Xam.Plugins.Settings для получения значения параметров из хранилища с платформой. Сведения о том, как `InitializeAsync` вызова метода, см. в разделе [передачи параметров во время навигации](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Этот механизм гарантирует, что каждый раз, когда пользователь переходит к SettingsView, параметры пользователя извлекаются из хранилища с платформой и представить с помощью привязки данных. Затем при изменении пользователем значения параметров, привязка данных гарантирует, что они передаются непосредственно для сохранения платформы хранения.

## <a name="summary"></a>Сводка

Параметры позволяют разделение данных, используемый для настройки поведения приложения из кода, позволяя поведение, которое должно быть изменено без повторной сборки приложения. Параметры приложений — это данные, приложение создает и управляет ими, и параметров пользователя настраиваемых параметров приложения, которые влияют на поведение приложения и не требуют частого повторной настройки.

Библиотека Xam.Plugins.Settings предоставляет согласованный, типобезопасную, кроссплатформенный подход для сохранения и извлечения приложения и параметры пользователя и привязка данных может использоваться для доступа к параметрам, созданные с помощью библиотеки.


## <a name="related-links"></a>Связанные ссылки

- [Скачайте электронную книгу (2 МБ в формате PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (пример)](https://github.com/dotnet-architecture/eShopOnContainers)
