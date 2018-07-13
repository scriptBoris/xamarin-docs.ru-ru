---
title: Индексирование приложения и создание глубинных ссылок
description: В этой статье показано, как использовать индексирование приложения и создание глубинных ссылок, чтобы предоставить доступ к содержимому приложения Xamarin.Forms для поиска на устройствах iOS и Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: 7a102765a3633b8abaf01b3f090d8253230bc16b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996100"
---
# <a name="application-indexing-and-deep-linking"></a>Индексирование приложения и создание глубинных ссылок

_Индексирование приложения позволяет приложениям, которые в противном случае забывается после использует некоторые в ногу с отображаются в результатах поиска. Создание глубинных ссылок позволяет приложениям реагировать на результат поиска, который содержит данные приложения, как правило, перейдя на страницу, на которые ссылается прямой ссылки. В этой статье показано, как использовать индексирование приложения и создание глубинных ссылок, чтобы предоставить доступ к содержимому приложения Xamarin.Forms для поиска на устройствах iOS и Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Глубокие компоновки с Xamarin.Forms и Azure, по [Xamarin University](https://university.xamarin.com/)**


Индексирование приложения Xamarin.Forms и создание глубинных ссылок предоставляют API-Интерфейс для публикации метаданных для приложения индексирования, как пользователи переходят по приложениям. Затем можно искать индексированное содержимое, в поиске Spotlight, Google поиска или поиска в Интернете. Нажав на результат поиска, который содержит прямая ссылка будет активировать событие, обрабатываемый приложением и обычно используется для перехода к странице, на которые ссылается прямая ссылка.

В примере приложения показано приложение списка задач, где данные хранятся в локальной базе данных SQLite, как показано на следующем снимке экрана:

![](deep-linking-images/screenshots.png "Приложения TodoList")

Каждый `TodoItem` индексируется экземпляра, созданных пользователем. Поиск конкретных платформ можно найти индексированных данных из приложения. Когда пользователь нажимает на элемент в результатах поиска для приложения, приложение запускается, `TodoItemPage` осуществляется переход и `TodoItem` ссылаться из сложного отображается ссылка.

Дополнительные сведения об использовании базу данных SQLite, см. в разделе [работа с локальной базой данных](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> Индексирование приложения Xamarin.Forms, а также глубокого связывание функциональность доступна только на платформах iOS и Android и требует iOS 9 и API 23 соответственно.

## <a name="setup"></a>Установка

Следующие разделы содержат любые дополнительные инструкции по установке с помощью этой функции на платформах iOS и Android.

### <a name="ios"></a>iOS

На платформе iOS есть без дополнительной настройки, необходимые для использования этой функции.

### <a name="android"></a>Android

На платформе Android существует ряд необходимых условий, которые должны быть выполнены для использовать индексирование приложения, а также глубокого связывание функциональные возможности:

1. Версия приложения необходимо выполнить динамическую в Google Play.
1. Дополнительное веб-сайт должен быть зарегистрирован для приложения в консоли разработчика Google. Когда приложение связано с веб-сайта, URL-адреса могут быть проиндексированы, работающих веб-сайт и приложение, которое затем может быть предоставлен в результатах поиска. Дополнительные сведения см. в разделе [приложения индексирование поиска Google](https://support.google.com/googleplay/android-developer/answer/6041489) на веб-сайте компании Google.
1. Приложения должны поддерживать намерения URL-адрес HTTP на `MainActivity` можно реагировать на класс, который сообщить приложению, индексирование типов данных схемы URL-адресов приложения. Дополнительные сведения см. в разделе [Настройка фильтра намерение](~/android/platform/app-linking.md#configure-intent-filter).

Как только эти условия выполнены, следующие дополнительные должен использовать индексирование приложения Xamarin.Forms и создание глубинных ссылок на платформе Android:

1. Установка [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) пакет NuGet в проект приложения Android.
1. В `MainActivity.cs` файл, импортировать `Xamarin.Forms.Platform.Android.AppLinks` пространства имен.
1. В `MainActivity.OnCreate` переопределения, добавьте следующую строку кода под `Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

Дополнительные сведения см. в разделе [глубокого ссылку содержимого с помощью Xamarin.Forms URL-адреса однократным](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) в блоге Xamarin.

## <a name="indexing-a-page"></a>Индексации страницы

Процесс индексации страницы и предоставление ее поиск в Google и Spotlight выглядит следующим образом:

1. Создание [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) , содержащий метаданные, необходимые для индексирования на странице, а также прямая ссылка для возврата на страницу, когда пользователь выбирает индексированное содержимое в результатах поиска.
1. Зарегистрировать [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляра для его индексации для поиска.

В следующем примере кода демонстрируется создание [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляр:

```csharp
AppLinkEntry GetAppLink (TodoItem item)
{
  var pageType = GetType ().ToString ();
  var pageLink = new AppLinkEntry {
    Title = item.Name,
    Description = item.Notes,
    AppLinkUri = new Uri (string.Format ("http://{0}/{1}?id={2}",
      App.AppName, pageType, WebUtility.UrlEncode (item.ID)), UriKind.RelativeOrAbsolute),
    IsLinkActive = true,
    Thumbnail = ImageSource.FromFile ("monkey.png")
  };

  return pageLink;
}
```

[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) Экземпляр содержит ряд свойств, значения которых необходимы для страницы индекса и Создание прямых ссылок. [ `Title` ](xref:Xamarin.Forms.IAppLinkEntry.Title), [ `Description` ](xref:Xamarin.Forms.IAppLinkEntry.Description), И [ `Thumbnail` ](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) свойства используются для идентификации индексированное содержимое, когда он появится в результатах поиска. [ `IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) Свойству `true` для указания, что индексированное содержимое в настоящий момент просматривается. [ `AppLinkUri` ](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) Свойство `Uri` , содержащий сведения, необходимые для возврата к текущей странице и отобразить текущее `TodoItem`. В следующем примере показано пример `Uri` для примера приложения:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

Это `Uri` содержит все сведения, необходимые для запуска `deeplinking` приложение, перейдите к `DeepLinking.TodoItemPage`и отобразить `TodoItem` с `ID` из `ec38ebd1-811e-4809-8a55-0d028fce7819`.

## <a name="registering-content-for-indexing"></a>Регистрация содержимого для индексирования

Один раз [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) создан экземпляр, он должен быть зарегистрирован для индексирования в результатах поиска. Это осуществляется с помощью [ `RegisterLink` ](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) метод, как показано в следующем примере кода:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Это добавляет [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляр приложения [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) коллекции.

> [!NOTE]
> `RegisterLink` Метод может также использоваться для обновления содержимого, которое индексируется для страницы.

Один раз [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляр был зарегистрирован для индексирования, он может присутствовать в результатах поиска. На следующем снимке экрана показан индексированное содержимое отображается в результатах поиска на платформе iOS:

![](deep-linking-images/ios-search.png "Индексированное содержимое в результатах поиска на iOS")

## <a name="de-registering-indexed-content"></a>Отмена регистрации индексировать содержимое

[ `DeregisterLink` ](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) Метод используется для удаления индексированное содержимое в результатах поиска, как показано в следующем примере кода:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

При этом удаляются [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляр из приложения [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) коллекции.

> [!NOTE]
> В Android не позволяет удалить индексированное содержимое из результатов поиска.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Отвечает на прямой ссылки

Если индексированное содержимое отображается в результатах поиска и выбран пользователем, `App` класса приложение будет получать запрос на обработку `Uri` содержащихся в индекс содержимого. Этот запрос может обрабатываться в [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) переопределить, как показано в следующем примере кода:

```csharp
public class App : Application
{
  ...

  protected override async void OnAppLinkRequestReceived (Uri uri)
  {
    string appDomain = "http://" + App.AppName.ToLowerInvariant () + "/";
    if (!uri.ToString ().ToLowerInvariant ().StartsWith (appDomain)) {
      return;
    }

    string pageUrl = uri.ToString ().Replace (appDomain, string.Empty).Trim ();
    var parts = pageUrl.Split ('?');
    string page = parts [0];
    string pageParameter = parts [1].Replace ("id=", string.Empty);

    var formsPage = Activator.CreateInstance (Type.GetType (page));
    var todoItemPage = formsPage as TodoItemPage;
    if (todoItemPage != null) {
      var todoItem = App.Database.Find (pageParameter);
      todoItemPage.BindingContext = todoItem;
      await MainPage.Navigation.PushAsync (formsPage as Page);
    }

    base.OnAppLinkRequestReceived (uri);
  }
}
```

[ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) Метод проверяет, что, полученных `Uri` предназначен для приложения, перед анализом `Uri` в страницу, чтобы осуществить переход и параметр для передачи на страницу. Экземпляр страницы создан, осуществлять переходы и `TodoItem` представлены на странице извлекается параметр. [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) Части страницы, чтобы осуществить переход затем устанавливается `TodoItem`. Это гарантирует, что при `TodoItemPage` отображается по [ `PushAsync` ](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) метод, он будет отображать `TodoItem` которого `ID` содержится в прямая ссылка.

## <a name="making-content-available-for-search-indexing"></a>Предоставление содержимого для индексирования поиска

Каждый раз, отображается страница, представленный прямой ссылки, [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) можно присвоить свойство `true`. В iOS и Android это делает [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляра, доступных для индексирования поиска и только на устройствах iOS, но также делает `AppLinkEntry` экземпляра, доступных для обработки. Дополнительные сведения об Эстафетной, см. в разделе [Общие сведения об Эстафетной](~/ios/platform/handoff.md).

В следующем примере кода демонстрируется параметр [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) свойства `true` в [ `Page.OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) переопределить:

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

Аналогичным образом, когда страницу, представленную объектом прямая ссылка будет выполнен переход от, [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) можно присвоить свойство `false`. В iOS и Android, при этом прекращается выполнение [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) экземпляр объявления для индексирования поиска, а также на iOS только, она также останавливает рекламы `AppLinkEntry` экземпляр для обработки. Это можно сделать в [ `Page.OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) переопределить, как показано в следующем примере кода:

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>Предоставление данных передачи

В iOS относящиеся к приложению данные могут храниться, при индексировании страницы. Это достигается путем добавления данных к [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) коллекции, являющийся `Dictionary<string, string>` для хранения пар "ключ значение", которые используются в перемещение вручную. Переадресуемое дает пользователю начать действие на одном из своих устройств и по-прежнему действия на другом устройствах (которые определяются в учетной записи iCloud). Следующий код является примером хранения пар ключ значение конкретного приложения:

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Значения, хранящиеся в [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) коллекции будут храниться в метаданных для индексированного страницы и будет восстановлена, когда пользователь нажимает на результат поиска, содержащий прямой ссылки (или когда перемещение вручную используется для просмотра содержимого на другом вошедшего в систему устройства).

Кроме того можно указать значения следующих ключей:

- `contentType` — `string` , задающий идентификатор универсальный тип индексированного содержимого. Согласно рекомендуемому соглашению для это значение является именем типа страницы, содержащей индексированное содержимое.
- `associatedWebPage` — `string` , представляющий веб-страницу, если индексированное содержимое также можно просмотреть в Интернете, или если приложение поддерживает прямых ссылок Safari.
- `shouldAddToPublicIndex` — `string` либо `true` или `false` , управляет ли добавить индексированное содержимое индекс общедоступном облаке компании Apple, который будет затем представлен пользователям, которые еще не установили приложение на своем устройстве iOS. Тем не менее только потому, что содержимое, установленный для открытого индексирования, это не значит, что оно будет автоматически добавляться к индексу общедоступном облаке компании Apple. Дополнительные сведения см. в разделе [открытый индексирование поиска](~/ios/platform/search/nsuseractivity.md). Обратите внимание, что этот ключ следует установить значение `false` при добавлении персональных данных для [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) коллекции.

> [!NOTE]
> `KeyValues` Коллекции не используется на платформе Android.

Дополнительные сведения об Эстафетной, см. в разделе [Общие сведения об Эстафетной](~/ios/platform/handoff.md).

## <a name="summary"></a>Сводка

В этой статье показано, как использовать индексирование приложения и создание глубинных ссылок, чтобы предоставить доступ к содержимому приложения Xamarin.Forms для поиска на устройствах iOS и Android. Индексирование приложения позволяет приложениям ногу, отображаются в результатах поиска, которые бы в противном случае забыть о после использует несколько.


## <a name="related-links"></a>Связанные ссылки

- [Глубокое связывание (пример)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [iOS API-интерфейсы поиска](~/ios/platform/search/index.md)
- [Связывание приложения в Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
