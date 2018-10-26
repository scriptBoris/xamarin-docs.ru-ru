---
title: Xamarin.iOS 9 – Устранение неполадок
description: В этой статье содержатся различные советы по устранению неполадок для работы с iOS 9 в Xamarin.iOS. Советы по охватывают синтаксический анализ XML, симуляторы, ограничения макета, проблемы с сетью и многим другим темам.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 322bb630194f973d37d7ca27a0ca9fe1b548b240
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107219"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin.iOS 9 – Устранение неполадок

_В этой статье предоставляет некоторые советы по устранению неполадок для работы с iOS 9 в приложениях Xamarin.iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Ошибка при синтаксическом анализе XML

Конструктор iOS Xamarin не поддерживают функции Xcode 7. Раскадровки не сможет загружаться в конструкторе с _«Возникла проблема при анализе XML-код»_ при попытке использовать новый iOS 9 (Xcode 7) конструктора элементы, такие как StackView.

iOS Designer поддержку компонентов Xcode 7 предназначена для предстоящего выпуска функция 6 цикла. Предварительная версия 6 цикла сейчас доступна в альфа-канал и обеспечивает ограниченную поддержку для новых возможностей Xcode 7.

Частичное решение для Visual Studio для Mac: щелкните ее правой кнопкой мыши и выберите **открыть с помощью** > **конструктора Interface Builder Xcode**.

## <a name="where-are-the-ios-8-simulators"></a>Где находятся 8 симуляторов iOS?

Если вы установили Xcode 7 (или более поздней), он будет автоматически замены всех симуляторах iOS 8 симуляторов iOS 9 по умолчанию. Если вам по-прежнему нужно проверить в iOS 8, можно запустить Xcode, а затем загрузите и установите симуляторах iOS 8.

В Xcode, выберите **Xcode** меню затем **предпочтения...**   >  **Загружает**:

[![](troubleshooting-images/ios8.png "Загружает симуляторов iOS 8")](troubleshooting-images/ios8.png#lightbox)

Нажмите кнопку **проверка и установить сейчас** кнопку, чтобы переустановить симуляторах iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Ограничения макета с ошибками атрибут влево/вправо

В iOS 8 (и более ранние версии), элементы пользовательского интерфейса в раскадровках использовать сочетание обоих **справа** & **слева** атрибуты (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) и  **Начальные** & **заднего** атрибуты (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) в тот же макет.

Если же раскадровки в в iOS 9, это приведет к исключение в следующей форме:

> Завершение приложения из-за неперехваченное исключение «NSInvalidArgumentException», причина: "*** + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: между начальные и конечные невозможно создать ограничение атрибут и атрибут вправо/влево. Используйте начальные и конечные для или не использующая оба. "

iOS 9 принудительно макеты, будет ли использоваться **справа** & **слева** _или_ **начальные**  &   **В конце** атрибутов, но *не* оба. Чтобы устранить эту проблему, измените все ограничения макета, чтобы использовать один и тот же набор в свой файл раскадровки, атрибут.

Дополнительные сведения см. в разделе [Ошибка ограничения iOS 9](http://stackoverflow.com/questions/32692841/ios-9-constraint-error) обсуждении Stack Overflow.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>Ошибка ITMS-90535: Непредвиденная CFBundleExecutable ключ

После переключения на устройства iOS 9 из приложения использует компонентов сторонних производителей (в частности наших существующий компонент Google Maps), которые компилировался и выполнялся на iOS 8 (или более ранней версии), при попытке отправить новую сборку в iTunes Connect, вы получите ошибку, в форме:

> Ошибка ITMS-90535: Непредвиденная CFBundleExecutable ключ. Пакет на «Payload/app-name.app/component.bundle» не содержит исполняемый объект пакета...

Этой проблемы можно обычно решается поиск именованных пакета в проекта, а затем — так же, как сообщение об ошибке предлагает - редактировать `Info.plist` это в пакете, удалив `CFBundleExecutable` ключ. `CFBundlePackageType` Ключа должно быть присвоено `BNDL` также.

После внесения этих изменений, выполнить чистую и перестроить весь проект. Можно отправить в iTunes Connect без проблем после внесения этих изменений.

Дополнительные сведения см. это [Stack Overflow](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) обсуждений.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Ошибка CFNetwork SSLHandshake (-9824)

При попытке подключения к Интернету напрямую или с веб-представление в iOS 9, в форме может появиться ошибка:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

Или в форме:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

В iOS9 приложение Transport Security (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, server серверной части приложения) и приложения. Кроме того, ATS требуется взаимодействие с использованием `HTTPS` протокол и высокого уровня API связи с помощью TLS версии 1.2 с безопасной пересылки.

Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9 и OS X 10.11 (El Capitan), все подключения с помощью `NSURLConnection`, `CFURL` или `NSURLSession` будут применяться требования к безопасности ATS. Если эти требования не соответствуют подключений, они вызовут сбой с исключением.

См. в разделе [Opting горизонтального ATS](~/ios/app-fundamentals/ats.md) части нашей [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md) сведения о том, как решить эту проблему.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Мои существующие приложения не выполняются на iOS 9

См. в разделе наших [сведения о совместимости iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) инструкции повторного создания и повторного развертывания существующих приложений под управлением iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView имеет значение Null в конструкторах

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

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>При загрузке представления из Xib/Nib Init с кодировщик не UIView

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

## <a name="dyld-message-no-cache-image-with-name"></a>Сообщение об ошибке Dyld: Изображение не кэша с именем...

Может возникнуть сбой со следующей информацией, в журнале:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Причина:** является ошибка в собственный компоновщика компании Apple, это происходит, когда они делают закрытый framework открытый (JavaScriptCore стал доступным в iOS 7, прежде чем что он был закрытый framework), а целевое приложение для развертывания — для версии iOS при Framework был закрытый. В этом случае компоновщик Apple будет связан с закрытой версии framework, а не общедоступной версии.

**FIX:** эта возможность будет добавлена для iOS 9, но есть простым решением проблемы, можно применить вручную в то же время: просто предназначенных для более поздней версии iOS в проекте (попробуйте iOS 7 в данном случае). Другие платформы может испытывать проблемы, например WebKit framework стал доступным в iOS 8 (и поэтому операционной системы iOS 7 приведет эта ошибка; целевых iOS 8, чтобы использовать WebKit в приложении).

## <a name="untrusted-enterprise-developer"></a>Недоверенные Enterprise Developer

При попытке запустить версию приложения Xamarin.iOS для iOS 9 на оборудовании реальных операций ввода-вывода, может появиться сообщение о том, что учетную запись разработчика не доверенной на устройстве. Пример:

[![](troubleshooting-images/untrusted01.png "Предупреждение недоверенных Enterprise Developer")](troubleshooting-images/untrusted01.png#lightbox)

Чтобы решить эту проблему, сделайте следующее:

1. Запустите Xcode (последняя версия бета-версия) на разработки Mac.
2. Выберите **устройств** из **окно** меню, чтобы открыть окно устройств: 

    [![](troubleshooting-images/untrusted02.png "Окно устройства")](troubleshooting-images/untrusted02.png#lightbox)
3. В разделе **устройств** боковая панель, выберите вашего устройства, щелкните правой кнопкой мыши и выберите **Показать профили подготовки...** : 

    [![](troubleshooting-images/untrusted03.png "Профили подготовки SShow")](troubleshooting-images/untrusted03.png#lightbox)
4. Выберите каждый профиль подготовки, в настоящее время на устройстве и нажмите кнопку **-** кнопку, чтобы удалить его: 

    [![](troubleshooting-images/untrusted04.png "Удаление профиля подготовки")](troubleshooting-images/untrusted04.png#lightbox)
5. Из **Xcode** меню, выберите **предпочтения...**  и **учетные записи**: 

    [![](troubleshooting-images/untrusted05.png "Предпочтения Xcode учетной записи")](troubleshooting-images/untrusted05.png#lightbox)
6. Нажмите кнопку **просмотреть сведения...**  кнопку, а затем щелкните **загрузить все** кнопки: 

    [![](troubleshooting-images/untrusted06.png "Скачивание всех профилей")](troubleshooting-images/untrusted06.png#lightbox)
7. После завершения обновления списка щелкните **сделать** кнопку и закройте окно предпочтений.
8. Удалите существующую версию приложения Xamarin.iOS, которое вы пытались тестирования с устройства iOS.
9. Вернитесь в Visual Studio для Mac, выполните чистую сборку и повторите для повторного запуска приложения на устройстве.

Может потребоваться остановить и перезапустить Visual Studio для Mac встречавшиеся новые профили подготовки, загруженные Xcode. Также может потребоваться установить **подписывание пакета iOS** варианты для выбора новые профили подготовки приложения Xamarin.iOS.

## <a name="launch-screen-issues"></a>Запустите экрана проблемы

iOS 9 теперь обеспечивает требованиям экран запуска, так, что один и тот же образ запуска больше не может многократно для поддержки другой интерфейс ориентации. См. в разделе Apple [UILanchImage ссылки](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) Дополнительные сведения.

При необходимости можно использовать файл раскадровки для представления экрана запуска вашего приложения, в отличие от с помощью набора **.png** файлы изображений. Это теперь Apple предпочтительный способ представить экраны запуска. См. в нашем [Знакомство с унифицированным раскадровкам](~/ios/user-interface/storyboards/unified-storyboards.md) для дополнительных сведений.

Наконец ваше приложение должно использовать файл раскадровки для экрана его запуска и поддерживает все четыре интерфейса ориентации (книжная, ногами Книжная, Альбомная слева и Альбомная справа) следует учитывать для запуска панели слайдов через или в режиме разделенное представление. Чтобы подробнее узнать о новой возможности многозадачность iOS 9, см. наш [многозадачность для iPad](~/ios/platform/multitasking.md) руководства.

## <a name="nsinternalinconsistencyexception-exception"></a>Исключение NSInternalInconsistencyException

При компиляции и запуска существующего приложения Xamarin.iOS для iOS 9, может возникнуть ошибка в форме:

> Objective-C исключение.  Имя: NSInternalInconsistencyException причина: приложения windows должны иметь корневой контроллер представления в конце запуска приложений

Это ошибка возникает потому, что приложения Windows должны иметь корневой контроллер представления в конце запуска приложений и не существующего приложения.

Существует по крайней мере два способа решения этой проблемы:

1. Обновить приложение, чтобы использовать файл раскадровки, а не `xib` файлы для определения его пользовательского интерфейса. Это требуется довольно много времени, в зависимости от размера приложения и умение IOS в конструктор (или конструктора Interface Builder), макет раскадровки. Дополнительные сведения см. наш [Знакомство с унифицированным раскадровкам](~/ios/user-interface/storyboards/unified-storyboards.md) документации.
2. Программа установки `RootViewController` свойство приложения окно в `FinishedLaunching` метод в `AppDelegate` класс указывал на контроллер представления в пользовательском Интерфейсе приложения.

## <a name="when-to-initialize-views-and-view-controllers"></a>Когда следует инициализировать представлениях и контроллерах представлений

С помощью Xamarin.iOS можно делать инициализация представления или контроллера представления внутри конструкторов, которые вызываются, когда что-то предоставляется в управляемый код, однако нарушает работу конструктора iOS.

В целом не инициализируйте все, что может выполнять обратный вызов код Objective-C из конструктора, так как нельзя быть уверенным, когда он будет вызываться. Это также означает нет лучше места (другие .ctor) или вызовов, чтобы переопределить (как Objective-C не имеет событий) которых следует выполнить эту инициализацию.

## <a name="related-links"></a>Связанные ссылки

- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [Новые возможности в iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Обновление приложений Xamarin.iOS для iOS9 (видео)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
