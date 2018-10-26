---
title: Введение в iOS 6
description: Этот документ содержит ссылки на руководства, описывающие возможности, представленные в iOS 6. Представления коллекций, PassKit, платформа для социальных сетей, и изменения в StoreKit обсуждаются.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 25926d82e060b91b007da9c2295b328cb049e8df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103872"
---
# <a name="introduction-to-ios-6"></a>Введение в iOS 6

_iOS 6 включает множество новых технологий для разработки приложений, которая позволяет использовать Xamarin.iOS 6 для C# разработчиков._

[ ![](images/ios6-large.jpg "Логотип iOS 6")](images/ios6-large.jpg#lightbox)

Благодаря iOS 6 и Xamarin.iOS 6 разработчики получили широкий набор возможностей в их распоряжении, для создания приложений iOS, включая объекты, что целевой объект iPhone 5.
В этом документе приводится перечень доступны более интересных новых возможностей и ссылки на статьи, для каждого раздела справки. Кроме того он затрагивает несколько изменений, которые будут важны, так как разработчики перемещают iOS 6 и новое разрешение iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Общие сведения о представлениях коллекций](~/ios/user-interface/controls/uicollectionview.md)

Представления коллекций разрешить доступ к содержимому будет отображаться с использованием произвольного макетов. Они позволяют легко создавать макеты сеткоподобной по умолчанию, то же время поддерживает также пользовательских макетов. Дополнительные сведения см. в разделе, [введение к представлениям коллекции](~/ios/user-interface/controls/uicollectionview.md) [ ](~/ios/user-interface/controls/uicollectionview.md)руководства.


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Введение в PassKit](~/ios/platform/passkit.md)

Платформа PassKit позволяет приложениям взаимодействовать с цифровой проходов, управление которыми осуществляется в расчетной. Дополнительные сведения см. в разделе, [введение в руководство по пакету передачи](~/ios/platform/passkit.md).


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Общие сведения о EventKit](~/ios/platform/eventkit.md)

Платформа EventKit предоставляет способ доступа к календарям, событиями календаря и напоминания данные хранятся в базе данных календаря. Доступ к календарям и календарь событий был доступен с iOS 4, но iOS 6 теперь предоставляет доступ к данным напоминания. Дополнительные сведения см. в разделе [я](~/ios/platform/eventkit.md) [Знакомство с EventKit](~/ios/platform/eventkit.md) руководства.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Общие сведения о платформе социальной сети](~/ios/platform/social-framework.md)

Платформа для социальных сетей предоставляет единый API для взаимодействия с социальными сетями, включая Twitter и Facebook, а также SinaWeibo для пользователей в Китае. Дополнительные сведения см. в разделе, [Общие сведения о платформе социальной сети](~/ios/platform/social-framework.md) руководства.


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[Изменения в StoreKit](changes-to-storekit.md)

Apple представила два новых возможностях Store Kit: приобретение и скачивание iTunes или содержимое приложения Store в приложении и размещения файлов содержимого, для покупки из приложений. Дополнительные сведения см. в разделе, [примет Store Kit](changes-to-storekit.md) руководства.


## <a name="other-changes"></a>Другие изменения


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload и ViewDidUnload устарело

`ViewWillUnload` И `ViewDidUnload` методы `UIViewController` больше не вызываются в iOS 6. В предыдущих версиях iOS эти методы могут используемые приложения для сохранения состояния, прежде чем выгружает представления и код очистки соответственно.

Например, Visual Studio для Mac будет создать метод, вызванный `ReleaseDesignerOutlets`, как показано ниже, который будет вызываться из `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Тем не менее, в iOS 6, он больше не нужно вызывать `ReleaseDesignerOutlets`.   
   
   
   
Для кода очистки, iOS 6 приложения должны использовать `DidReceiveMemoryWarning`. Тем не менее, код, который вызывает `Dispose` должно проводиться расчетливо и только для памяти с большим объемом объектов, как показано ниже:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Опять же, вызвав `Dispose` как описано выше редко требуется. В целом, наиболее приложения должны выполнять заключается в удалении обработчиков событий.

В случае сохранения состояния, приложения могут выполнять это в `ViewWillDisappear` и `ViewDidDisappear` вместо `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 разрешения

устройства iPhone 5 иметь разрешение 640 x 1136. Приложения, ориентированные на предыдущих версиях iOS будут отображаться letterboxed при запуске на iPhone 5, как показано ниже:

 [![](images/01-letterboxed.png "Приложения, ориентированные на предыдущих версиях iOS будет отображаться letterboxed при запуске на iPhone 5")](images/01-letterboxed.png#lightbox)

Чтобы приложение отображалось в полноэкранном режиме на iPhone 5, просто добавьте образ с именем `Default-568h@2x.png` необходимости разрешение 640 x 1136. На следующем снимке экрана показан приложение работало после этот образ был включен:

 [![](images/02-fullscreen.png "На этом снимке экрана показано приложение работало после этот образ был включен")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Создание подкласса UINavigationBar

В iOS 6 `UINavigationBar` может быть подклассом. Это позволяет дополнительный контроль над вида `UINavigationBar`. Например, приложения могут подкласс для добавления вложенных представлений, анимация эти представления и изменения границ `UINavigationBar`.

В приведенном ниже коде показан пример подклассами `UINavigationBar` , добавляющий `UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Добавление подкласса `UINavigationBar` для `UINavigationController`, использовать `UINavigationController` конструктор, принимающий тип `UINavigationBar` и `UIToolbar`, как показано ниже:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

С помощью этого `UINavigationBar` подкласс приводит представление изображения, отображаемого, как показано на следующем снимке экрана:

 [![](images/03-navbar.png "В результате подкласс UINavigationBar представление изображения, отображаемого, как показано на следующем снимке экрана")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Ориентация интерфейса

До iOS 6 приложения может переопределять `ShouldAutorotateToInterfaceOrientation`, возвращая true для любого ориентаций конкретном контроллере поддерживается. Например следующий код будет использоваться для поддержки только книжной:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

В iOS 6 `ShouldAutorotateToInterfaceOrientation` является устаревшим.
Вместо этого приложения можно переопределять `GetSupportedInterfaceOrientations` на контроллер корневого представления, как показано ниже:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

На устройстве iPad, по умолчанию для всех четырех направлениях Если `GetSupportedInterfaceOrientation` не реализован. На iPhone и iPod Touch, значение по умолчанию — все ориентации, за исключением `PortraitUpsideDown`.
