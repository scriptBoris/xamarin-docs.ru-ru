---
title: iOS 9 совместимости
description: Даже если вы не планируете немедленно добавить в приложение функции iOS 9, следует перестроить приложения при помощи последнюю версию Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6ade1c05c8e1cc64a4d24df1284d86175083ab80
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119894"
---
# <a name="ios-9-compatibility"></a>iOS 9 совместимости

_Даже если вы не планируете немедленно добавить в приложение функции iOS 9, следует перестроить приложения при помощи последнюю версию Xamarin._

> [!IMPORTANT]
> Информация на этой странице предназначена для клиентов с приложениями уже в App Store, нацеленных на iOS 8 или более ранних версиях, не уже отправили обновлений на совместимость с iOS 9. Если вы уже используете последние версии - Xcode 7 и 9 Xamarin.iOS — для разработки приложений посетите [введение в iOS 9](~/ios/platform/introduction-to-ios9/index.md).

При первой бета-версии iOS 9 были доступны, мы определили две проблемы с более старыми версиями Xamarin, объявленное в качестве более старые приложения, когда не удается запустить на iOS 9.

Эти две проблемы (как [на наших форумах](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) были:

- Создание приложений для iOS 8 или более ранней версии, не смогла запустить на 32-разрядных устройствах (включая приложения, созданные с помощью [единый API](~/cross-platform/macios/unified/index.md)).
- P/Invoke, выдающие полный путь не указан.

Обновления установки Xamarin на последний выпуск в стабильном канале и затем перестройки и повторного развертывания приложения, устраняет эти две проблемы.

_Даже если вы не планируете их прямо сейчас обновить приложение с помощью функции iOS 9, мы рекомендуем повторное построение с последней версией Xamarin и повторно отправить в App Store_.



Это позволит гарантировать, что приложение будет запускаться на iOS 9, после обновления клиентов.
Можно продолжать поддерживать iOS 8 - перестроение с последним выпуском не влияет на целевой версии приложения.

При наличии дальнейших проблем при тестировании существующие приложения на iOS 9, чтение [улучшение совместимости](#compat) разделе ниже.


### <a name="updating-with-visual-studio"></a>Обновление с помощью Visual Studio

Рекомендуется явно проверять что Visual Studio обновляется до последней стабильной версии.

## <a name="what-about-components-nugets-and-other-libraries"></a>Как насчет компонентов, пакеты NuGet и другие библиотеки?

Вы выполните **не** понадобиться получить новые версии компоненты и пакеты NuGet для решения двух проблем, упомянутых выше.
Эти проблемы исправлены путем простого повторного создания приложения с помощью последней стабильной версии Xamarin.iOS.

Кроме того, поставщики компонентов и Nuget авторов, **не** требуется для отправки новых сборок только для устранения двух проблем, упомянутых выше. Тем не менее если таковые имеются компонента или Nuget использует `UICollectionView` или загрузить представления из **Xib** обновление файлов *может* быть необходимы для устранения проблем совместимости iOS 9, описанные ниже.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Улучшение совместимости в коде

Есть несколько вариантов кода шаблоны, которые *используется* работать в более старых версиях iOS критические в iOS 9. Ниже приведены некоторые из возможных причин (и их решения), могут возникнуть при тестировании на iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView имеет значение null, если в конструкторах

**Причина:** в iOS 9 `initWithFrame:` конструктор больше не требуется, из-за изменения в поведении в iOS 9 как [утверждается в документации UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Если вы зарегистрировали класса для указанного идентификатора и необходимо создать новую ячейку, ячейки инициализируется путем вызова его `initWithFrame:` метод.

**FIX:** добавить `initWithFrame:` конструктор, подобный следующему:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Связанные примеры: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>При загрузке представления из Xib/Nib init с кодировщик не UIView

**Причина:** `initWithCoder:` конструктор та же вызывается при загрузке представления из интерфейса построитель Xib-файла. Если этот конструктор не экспортируется неуправляемый код не может вызывать наши управляемой версии. Ранее (например) в iOS 8) `IntPtr` конструктор был вызван для инициализации представления.

**FIX:** Создание и экспорт `initWithCoder:` конструктор, подобный следующему:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Подходящий пример: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Сообщение об ошибке Dyld: изображение не кэша с именем...

Может возникнуть сбой со следующей информацией, в журнале:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Причина:** является ошибка в собственный компоновщика компании Apple, это происходит, когда они делают закрытый framework открытый (JavaScriptCore стал доступным в iOS 7, прежде чем что он был закрытый framework), а целевое приложение для развертывания — для версии iOS при Framework был закрытый. В этом случае компоновщик Apple будет связан с закрытой версии framework, а не общедоступной версии.

**FIX:** эта возможность будет добавлена для iOS 9, но есть простым решением проблемы, можно применить вручную в то же время: просто предназначенных для более поздней версии iOS в проекте (попробуйте iOS 7 в данном случае). Другие платформы может испытывать проблемы, например WebKit framework стал доступным в iOS 8 (и поэтому операционной системы iOS 7 приведет эта ошибка; целевых iOS 8, чтобы использовать WebKit в приложении).



## <a name="related-links"></a>Связанные ссылки

- [сведения о выпуске совместимости iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Введение в iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Обновление приложений Xamarin.iOS для iOS9 (видео)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
