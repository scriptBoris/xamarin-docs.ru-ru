---
title: Xamarin.iOS 9 — Устранение неполадок
description: В этой статье предоставляет различные советы по устранению неполадок для работы с iOS 9 в Xamarin.iOS. Советы по охватывает синтаксический анализ XML, симуляторов, ограничения макета, проблемы с сетью и многих других темах.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: c44d737efcf5092eb4b27d5311271005de65318b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787667"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin.iOS 9 — Устранение неполадок

_В этой статье предоставляет несколько советов по устранению неполадок для работы с iOS 9 в Xamarin.iOS приложений._

## <a name="there-was-a-problem-parsing-the-xml"></a>Ошибка при разборе XML

Xamarin iOS конструктор пока не поддерживает функции Xcode 7. Раскадровки будет невозможно загрузить в конструктор с _«Возникла проблема при разборе XML»_ при попытке использовать новый iOS 9 (Xcode 7) конструктора элементы, такие как StackView.

iOS конструктор поддержку функций Xcode 7 предназначена для предстоящем выпуске функция 6 цикла. Предварительная версия 6 цикла в настоящее время доступна в альфа-канал и имеет ограниченную поддержку для включения новых возможностей Xcode 7.

Частичное решение для Visual Studio для Mac: щелкните ее правой кнопкой мыши и выберите **открыть с помощью** > **Xcode интерфейс построителя**.

## <a name="where-are-the-ios-8-simulators"></a>Где находятся iOS 8 симуляторов?

Если вы установили Xcode 7 (или более поздней), он будет автоматически замены всех симуляторах iOS 8 симуляторах iOS 9 по умолчанию. Если по-прежнему необходимо протестировать на iOS 8, можно запустить Xcode, а затем загрузите и установите симуляторах iOS 8.

В Xcode, выберите **Xcode** меню затем **установки...**   >  **Загружает**:

[![](troubleshooting-images/ios8.png "Загружает симуляторах iOS 8")](troubleshooting-images/ios8.png#lightbox)

Нажмите кнопку **проверка и установить сейчас** кнопку, чтобы переустановить симуляторах iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Ограничения макета с ошибками атрибут влево или вправо

В iOS 8 (или более ранних) элементов пользовательского интерфейса в раскадровки использовать сочетание обоих **право** & **влево** атрибуты (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) и  **Начальные** & **задний** атрибуты (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) в тот же макет.

Если же раскадровки в работать с iOS 9, приведет к исключение в следующей форме:

> Завершение приложения из-за неперехваченное исключение «NSInvalidArgumentException», причина: "*** + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: ограничение не может выполняться между начальные и конечные атрибут и атрибут вправо или влево. Используйте начальные и конечные для оба или ни. "

iOS 9 применяет макеты, которые можно использовать любой **право** & **влево** _или_ **начальные**  &   **В конце** атрибутов, но *не* оба. Чтобы устранить эту проблему, измените все ограничения макет, чтобы использовать один и тот же набор в свой файл раскадровки, атрибут.

Дополнительные сведения см. в разделе [Ошибка ограничения iOS 9](http://stackoverflow.com/questions/32692841/ios-9-constraint-error) обсуждение переполнения стека.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ITMS ошибка-90535: Непредвиденный ключ CFBundleExecutable

После переключения на iOS 9 из приложения использует сторонних производителей компонентов (в частности нашей существующий компонент карты Google), компилировался и выполнялся на iOS 8 (или более ранней), при попытке отправить новую сборку в iTunes Connect, возникает сообщение об ошибке в форме:

> Ошибка ITMS-90535: Непредвиденная CFBundleExecutable ключ. Пакет на «Payload/app-name.app/component.bundle» не содержит исполняемый объект пакета...

Этой проблемы можно обычно решается путем нахождения именованного набора в проекте а затем — изменить так же, как сообщение об ошибке предлагает - `Info.plist` , находящийся в пакете, удалив `CFBundleExecutable` ключа. `CFBundlePackageType` Ключа должно быть присвоено `BNDL` также.

После внесения этих изменений, выполнить чистую и заново весь проект. Можно отправить в iTunes Connect без проблем после внесения этих изменений.

Дополнительные сведения см. в разделе это [переполнения стека](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) обсуждений.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Ошибка CFNetwork SSLHandshake (-9824)

При попытке подключения к Интернету напрямую или с веб-страниц в iOS 9, может получить ошибку в виде:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

Или в форме:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

В iOS9 безопасность транспорта приложения (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, приложение серверных) и приложения. Кроме того, ATS требует взаимодействия с использованием `HTTPS` протокола и высокого уровня API связи с помощью TLS версии 1.2 с безопасной пересылки.

Поскольку ATS включена по умолчанию в приложениях, собранных для iOS 9 и OS X 10.11 (El Capitan), все подключения, использующие `NSURLConnection`, `CFURL` или `NSURLSession` будет зависеть от ATS требования безопасности. Если эти требования не подходят для подключений, произойдет сбой с исключением.

См. в разделе [Opting масштабированием ATS](~/ios/app-fundamentals/ats.md) части нашей [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md) сведения о том, как решить эту проблему.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Мои существующие приложения не выполняются на iOS 9

См. наш [сведения о совместимости iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) инструкции повторное построение и повторно развертывать существующие приложения для запуска на iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView имеет значение Null в конструкторах

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

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView не удается Init с автору кода при загрузке представления из Xib/Nib

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

## <a name="dyld-message-no-cache-image-with-name"></a>Сообщение об ошибке Dyld: Изображение не кэша с именем...

Может возникнуть сбой со следующей информацией в журнале:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Причина:** является ошибка в собственный компоновщика Apple, что происходит при осуществлении закрытый framework открытый (JavaScriptCore стал доступным в iOS 7, раньше было закрытый framework), и целевой объект развертывания приложения для версии iOS при Framework был закрытым. В этом случае компоновщик Apple будет связан с закрытой версии framework вместо открытого версии.

**Исправление:** она будет устранена IOS 9, но есть простое решение, в то же время самостоятельно применять: просто поддержки более поздней версии iOS в проекте (можно попробовать iOS 7 в данном случае). Другие платформы может испытывать проблемы, например WebKit framework стал доступным в iOS 8 (и чтобы разрабатывать приложения для iOS 7 приведет к этой ошибке; должно быть предназначено iOS 8 для использования в приложении WebKit).

## <a name="untrusted-enterprise-developer"></a>Недоверенные Enterprise Developer

При попытке с версией iOS 9 приложения Xamarin.iOS на оборудовании реальных операций ввода-вывода, может появиться сообщение о том, что учетной записи разработчика не доверенным на устройстве. Пример:

[![](troubleshooting-images/untrusted01.png "Недоверенные Enterprise Developer предупреждения")](troubleshooting-images/untrusted01.png#lightbox)

Чтобы решить эту проблему, выполните следующие действия.

1. Запустить Xcode (последняя версия бета-версия) на разработку Mac.
2. Выберите **устройств** из **окна** меню, чтобы открыть окно устройств: 

    [![](troubleshooting-images/untrusted02.png "Окно устройства")](troubleshooting-images/untrusted02.png#lightbox)
3. В разделе **устройств** боковая панель, выберите устройства, щелкните правой кнопкой мыши и выберите **Показать профили подготовки...** : 

    [![](troubleshooting-images/untrusted03.png "Профили подготовки SShow")](troubleshooting-images/untrusted03.png#lightbox)
4. Выберите каждый профиль подготовки в настоящее время на устройстве и нажмите кнопку **-** кнопку, чтобы удалить его: 

    [![](troubleshooting-images/untrusted04.png "Удаление профиля подготовки")](troubleshooting-images/untrusted04.png#lightbox)
5. Из **Xcode** последовательно выберите пункты **установки...**  и **учетные записи**: 

    [![](troubleshooting-images/untrusted05.png "Настройки учетной записи для Xcode")](troubleshooting-images/untrusted05.png#lightbox)
6. Щелкните **просмотра сведений...**  , а затем нажмите кнопку **загрузить все** кнопки: 

    [![](troubleshooting-images/untrusted06.png "Загрузка всех профилей")](troubleshooting-images/untrusted06.png#lightbox)
7. После завершения обновления списка щелкните **сделать** кнопку и закройте окно «Параметры».
8. Удалите существующую версию приложения Xamarin.iOS, которое вы пытались тестирования с устройства iOS.
9. Вернитесь в Visual Studio для Mac, выполните чистую сборку и попытайтесь повторно запустить приложение на устройстве.

Может потребоваться остановить и перезапустить Visual Studio для Mac новые профили подготовки, загруженных Xcode, будут видны. Может также потребоваться настроить **подписывание пакета iOS** варианты приложения Xamarin.iOS выбрать новый профили подготовки.

## <a name="launch-screen-issues"></a>Запустите экрана проблемы

iOS 9 следит за выполнением требования запуска экрана, чтобы один и тот же образ запуска можно больше не использовать повторно для поддержки ориентации другой интерфейс. В разделе Apple [UILanchImage ссылки](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) Дополнительные сведения.

Кроме того, можно использовать файл раскадровки для представления экрана запуска приложения а не с помощью набора **.png** файлы изображений. Это теперь Apple предпочитаемый способ представления запуска экранов. См. в разделе нашей [введение в единой раскадровки](~/ios/user-interface/storyboards/unified-storyboards.md) для дополнительных сведений.

Наконец приложение необходимо использовать файл раскадровки для его запуска экрана и поддерживает все четыре интерфейса ориентации (книжной ориентации, верхней стороной вниз Книжная, Альбомная слева и Альбомная справа) следует учитывать для запуска панели слайд за или в режиме разделенного представления. Чтобы больше узнать о новых возможностей многозадачной iOS 9, см. в разделе нашей [многозадачность для iPad](~/ios/platform/multitasking.md) руководства.

## <a name="nsinternalinconsistencyexception-exception"></a>Исключение NSInternalInconsistencyException

При компиляции и запуске существующего приложения Xamarin.iOS IOS 9, может появиться ошибка в форме:

> Objective-C исключение.  Имя: NSInternalInconsistencyException причина: приложения windows, должны иметь корневой контроллер представление в конце запуска приложений

Это ошибка возникает потому, что приложение Windows, должны иметь корневой View-Controller в конце запуска приложений и не существующего приложения.

Существует по крайней мере два способа решения этой проблемы:

1. Обновление приложения для использования файла раскадровки вместо `xib` файлов для определения его пользовательского интерфейса. Макет раскадровки этого класса, требуется много времени, в зависимости от размера приложения и базы знаний использования iOS конструктор (или интерфейс построителя в Xcode). Дополнительные сведения см. в разделе нашей [введение в единой раскадровки](~/ios/user-interface/storyboards/unified-storyboards.md) документации.
2. Программа установки `RootViewController` свойства приложения окно в `FinishedLaunching` метод `AppDelegate` класса, чтобы она указывала на представление-контроллер в пользовательском Интерфейсе приложения.

## <a name="when-to-initialize-views-and-view-controllers"></a>При инициализации представления и просмотр контроллеров

С помощью Xamarin.iOS можно делать в представлении или представлении контроллера инициализация внутри конструкторов, которые вызываются, когда что-нибудь предоставляется в управляемый код, однако нарушает работу конструктора операций ввода-вывода.

В целом не инициализируйте все, что может выполнять обратный вызов кода Objective-C из конструктора, так как нельзя быть уверенным, когда он будет вызван. Это также означает имеется более местах (другие .ctor) или вызывает переопределение (как Objective-C не имеет событий) которых следует выполнить эту инициализацию.

## <a name="related-links"></a>Связанные ссылки

- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [Новые возможности iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Обновление приложений Xamarin.iOS для iOS9 (видео)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
