---
title: iOS 9 совместимости
description: Даже если не планируется добавить функции iOS 9 в приложение незамедлительно, следует перестроить приложения с последней версией Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b7cbec3f064e6000f991fb0f9ce256415f6ce5dd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777555"
---
# <a name="ios-9-compatibility"></a>iOS 9 совместимости

_Даже если не планируется добавить функции iOS 9 в приложение незамедлительно, следует перестроить приложения с последней версией Xamarin._

> [!IMPORTANT]
> Сведения на этой странице, для клиентов с приложения уже находится в хранилище приложения, предназначенные для iOS 8 или более ранней версии, который не уже отправили обновлений на совместимость iOS 9. Если вы уже используете последние версии - Xcode 7 и 9 Xamarin.iOS — для разработки приложений, посетите [Знакомство с iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Если отображается Первая бета-версии iOS 9, мы определили две проблемы с более ранними версиями Xamarin, объявленное как более старые приложения, когда не удается запустить на iOS 9.

Эти две проблемы (как [на наших форумах](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) были:

- Построение приложения для iOS 8 или более ранней версии, не смогла запустить на 32-разрядных устройствах (включая приложения, созданного с помощью [единой API](~/cross-platform/macios/unified/index.md)).
- P/Invoke с полный путь не указан.

Обновления установки Xamarin последняя стабильная канала выпуска, перестроение и повторного развертывания приложения, устраняет эти две проблемы.

_Даже если не планирует немедленно обновить приложение с помощью функции iOS 9, мы рекомендуем повторное построение с последней версией Xamarin и повторно отправить в магазин приложений_.



Это позволит гарантировать, что приложение будет запускаться на iOS 9, после обновления клиентов.
Можно продолжать поддерживать iOS 8 - перестроение с последним выпуском не влияет на целевой версии приложения.

При наличии дополнительных проблем при проверке существующие приложения на iOS 9 чтения [Повышение совместимости](#compat) разделе ниже.


### <a name="updating-with-visual-studio"></a>Обновление с помощью Visual Studio

Рекомендуется явным образом проверить, Visual Studio обновлено до последней стабильной версии.

## <a name="what-about-components-nugets-and-other-libraries"></a>Как насчет компонентов, Nugets и другие библиотеки?

Это сделать **не** нужно ждать новых версий все компоненты или Nugets используется для решения проблемы в двух упомянутых выше.
Повторное создание приложения с последней стабильной версии Xamarin.iOS путем устранения неполадок.

Поставщики компонентов и авторов Nuget, **не** требуется для отправки новые сборки только для устранения двух проблем, упомянутых выше. Тем не менее если любой компонент или Nuget использует `UICollectionView` или загрузить представления из **Xib** обновление файлов *может* требуются для устранения проблемы совместимости iOS 9, указанные ниже.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Повышение совместимости в коде

Есть несколько вариантов кода шаблоны, которые *используется* работать в более ранних версиях iOS критические iOS 9. Ниже приведены некоторые из возможных причин (и способы их решения), могут возникнуть во время тестирования на iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView имеет значение null в конструкторах

**Причина:** в iOS 9 `initWithFrame:` конструктор больше не требуется из-за изменения в iOS 9 как [UICollectionView документации состояния](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Если вы зарегистрировали класса для указанного идентификатора, необходимо создать новую ячейку ячейки инициализируется путем вызова его `initWithFrame:` метод.

**Исправление:** добавить `initWithFrame:` конструктор, подобный следующему:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Связанные образцы: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView не удается init с автору кода при загрузке представления из Xib/Nib

**Причина:** `initWithCoder:` конструктор является тем, который вызывается при загрузке представления из файла Xib интерфейс построителя. Если этот конструктор не экспортируется неуправляемого кода невозможно вызвать нашей управляемой версии. Ранее (например) в iOS 8) `IntPtr` конструктор был вызван для инициализации представления.

**Исправление:** Создание и экспорт `initWithCoder:` конструктор, подобный следующему:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Подходящий пример: [чат](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Сообщение об ошибке Dyld: изображение не кэша с именем...

Может возникнуть сбой со следующей информацией в журнале:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Причина:** является ошибка в собственный компоновщика Apple, что происходит при осуществлении закрытый framework открытый (JavaScriptCore стал доступным в iOS 7, раньше было закрытый framework), и целевой объект развертывания приложения для версии iOS при Framework был закрытым. В этом случае компоновщик Apple будет связан с закрытой версии framework вместо открытого версии.

**Исправление:** она будет устранена IOS 9, но есть простое решение, в то же время самостоятельно применять: просто поддержки более поздней версии iOS в проекте (можно попробовать iOS 7 в данном случае). Другие платформы может испытывать проблемы, например WebKit framework стал доступным в iOS 8 (и чтобы разрабатывать приложения для iOS 7 приведет к этой ошибке; должно быть предназначено iOS 8 для использования в приложении WebKit).



## <a name="related-links"></a>Связанные ссылки

- [сведения о выпуске совместимость iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Введение в iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Обновление приложений Xamarin.iOS для iOS9 (видео)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
