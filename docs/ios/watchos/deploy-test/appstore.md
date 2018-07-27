---
title: Развертывание watchOS приложения в App Store
description: В этом документе описывается развертывание приложений для watchOS с использованием Xamarin в App Store. Он смотрит на распространение профилей подготовки и iTunes Connect, и она также предоставляет некоторые советы по устранению неполадок.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 90058f5074759fdded5d259004cb40c0cb7ea212
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39276071"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Развертывание watchOS приложения в App Store

> [!IMPORTANT]
> Обязательно просмотрите [отправки Apple Watch руководства](https://developer.apple.com/app-store/watch/)и см. в разделе [Устранение неполадок](#Troubleshooting) любые проблемы, возможно, вам.

- Убедитесь, что у вас есть:
  - [**Профили подготовки распространения** ](#provisioning) создан для ваших проектов.
  - **Цель развертывания** (`MinimumOSVersion`) для iOS родительского приложения, равным **8.2** или более ранней версии (8.3 не поддерживается).

- В [ **iTunes Connect**](#iTunes_Connect):

  - Создание записи приложения iOS (или добавить **новой версии** в существующее приложение).
  - Добавьте значок и снимки экрана.

- Затем в [Visual Studio для Mac](#xamarin_studio) (Visual Studio не поддерживается в настоящее время):

  - Щелкните правой кнопкой мыши приложение для iOS и выберите **Назначить запускаемым проектом**.
  - Изменить на **App Store** конфигурации.
  - Используйте **архив** возможность создать архив приложения.

- Наконец, переключитесь в [Xcode 6.2 +](#xcode)

  - Перейдите к **окна > Организатор** и выберите **архивы**.
  - Выберите приложение и архив в списке.
  - (Необязательно) **Проверки...**  архива.
  - **Отправить...**  архива и выполните указанные действия для отправки в iTunes Connect для проверки и утверждения.

Чтение конкретные советы, связанные с этими ниже. См. в разделе [Устранение неполадок](#Troubleshooting) разделе при возникновении проблем.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Профили подготовки распространения

Для сборки для развертывания App Store, вам нужно создать **профиля подготовки к распространению** для каждого идентификатора приложения в решении.

Если у вас есть подстановочный знак идентификатора приложения *только один профиль подготовки будут необходимы*; но если у вас есть отдельный идентификатор приложения для каждого проекта, то вам потребуется профиля подготовки для каждого идентификатора приложения:

![](appstore-images/provisioningprofile-distribution-sml.png "Профиль распространения App Store")

После создания всех трех профилей, они отображаются в списке. Не забудьте загрузить и установить каждый из них (его двойным щелчком):

![](appstore-images/provisioningprofiles-sml.png "Список доступных профилей")

Вы можете проверить профиль подготовки в **параметры проекта** , выбрав **сборки > подписывание пакета iOS** Администрирование **AppStore | iPhone** Конфигурация.

**Профиль подготовки** списке будут показаны все соответствующие профили — вы должны увидеть соответствующие профили, созданные вами в этом раскрывающемся списке.

![](appstore-images/options-selectprofile-sml.png "Диалоговое окно подписывание пакета iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Выполните [Общие сведения о распространении приложений](~/ios/deploy-test/app-distribution/index.md), в частности:

- [Настройка приложения в iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Публикация в App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

При настройке приложения в iTunes Connect, не забудьте добавить значок и снимки экрана:

![](appstore-images/itunesconnect-watch-sml.png "Значок просмотра и снимки экрана в iTunes Connect")

Файл значка должен составлять 1024 x 1024 пикселей, а также будет иметь маску циклическая применения к нему, когда она появится. Значок не должен содержать альфа-канал.

Хотя бы один снимок экрана является обязательным, не более пяти могут быть отправлены.
Они должны быть 312 x 390 точек и демонстрации приложение для Apple Watch в действии.
Имитатор 42 мм контрольных значений позволяет делайте снимки экрана при таком размере.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio для Mac

1. Убедитесь, что приложения iOS является запускаемым проектом. Если нет, щелкните правой кнопкой мыши, чтобы задать его.

  ![](appstore-images/xs-startup.png "Задание запускаемого проекта")

2. Выберите **AppStore** конфигурация сборки:

  ![](appstore-images/xs-appstore.png "Конфигурацию сборки из магазина App Store")

3. Выберите **сборки > архив** элемент меню, чтобы запустить процесс архивации:

  ![](appstore-images/xs-archive.png "Меню \"сборка\"")

Вы также можете **представление > архивов...**  элемент меню, чтобы увидеть архивы, которые были созданы ранее.

  ![](appstore-images/xs-archives-sml.png "Представление \"архивы\"")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode обеспечит автоматическое отображение архивы, созданные в Visual Studio для Mac.

1. Запустите Xcode и выберите **окна > Организатор**:

  ![](appstore-images/xc-organizer.png "Меню \"окно\"")

2. Переключиться в режим **архивы** вкладку и выберите архив, который был создан с помощью Visual Studio для Mac:

  ![](appstore-images/xc-archives.png "На вкладке архивы")

3. При необходимости **проверки...**  архива, затем выберите **отправки...**  к отправке приложения в iTunes Connect.

4. Выберите группы разработчиков (Если вы принадлежите к более чем одному) и подтвердите отправки:

  ![](appstore-images/xc-submit1.png "В разделе группы разработки")

5. Посетите iTunes Connect еще раз, чтобы увидеть отправленный двоичного файла. Перейти к странице конфигурации приложения и выберите **предварительный выпуск** в верхнем меню, чтобы увидеть **построения** списка:

  [![](appstore-images/itc-prerelease-sml.png "На странице конфигурации приложения в iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

Вы можете отправить в приложении для утверждения на **версии** страницы. Ссылаться на [Общие сведения о распространении приложений iOS](~/ios/deploy-test/app-distribution/index.md) Дополнительные сведения.


## <a name="troubleshooting"></a>Устранение неполадок

Ниже приведены некоторые ошибки, которые могут возникнуть при отправке в App Store и действия, которые можно предпринять для их исправления.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Архив меню не отображается в Visual Studio для Mac

Выполните [описанные выше действия](#xamarin_studio) настроить решение для архивации. Если запускаемый проект не может быть установлен правильно, убедитесь, что изначально устанавливается конфигурацию сборки для отладки или выпуска, прежде чем пытаться изменить запускаемого проекта. Выберите конфигурацию сборки обратно в **AppStore**.

### <a name="invalid-icon"></a>Значок «Недопустимо»

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Выполните [по удалению альфа-канал](~/ios/watchos/troubleshooting.md) из вашего набора значков.

### <a name="cfbundleversion-mismatch"></a>Несоответствие CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Все проекты в решении в приложение iOS, расширение просмотра и приложении для Apple Watch - следует использовать тот же номер версии. Измените каждый **Info.plist** файл таким образом, чтобы номер версии, обеспечивающее точное совпадение.

### <a name="missing-icons"></a>Отсутствуют значки

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Следуйте инструкциям в [работа со значками](~/ios/watchos/app-fundamentals/icons.md) Чтобы добавить все необходимые изображения в проект приложения Watch.

### <a name="missing-icon"></a>Отсутствует значок

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Убедитесь в наличии последней версии Visual Studio для Mac и что вашей **AppIcon.appiconset** содержит полный набор образов. Если вы по-прежнему видите эту ошибку, просмотрите исходный код **Contents.json** для подтверждения того, он содержит запись для всех необходимых образов. Кроме того, как только вы убедились, вы используете последнюю версию Xamarin, удалении и повторном создании **AppIcon.appiconset**.

> [!IMPORTANT]
> Для поддержки значок Watch компьютера Mac в Visual Studio есть известная ошибка: ожидается, что 88 x 88 пикселей изображения для **29x29@3x** образ (который должен быть 87 x 87 пикселей).


Не удается устранить эту проблему в Visual Studio для Mac - либо изменение ресурса изображения в Xcode или вручную изменить **Contents.json** файла (в соответствии с [в этом примере](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Недопустимый поддержки WatchKit

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Данное сообщение может появиться во время проверки и отправки, или в автоматические сообщения электронной почты из iTunes Connect после очевидно успешной отправки.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Вы должны **архив** приложения в Visual Studio для Mac и затем переключиться в Xcode 6.2 + проверить и отправить в iTunes Connect.


Используйте Xamarin стабильный канал и Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Недопустимый профиль подготовки

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**Профили подготовки распространения** для всех трех проектов в решении приложения Контрольные значения должны быть предоставлены: приложение iOS, расширение просмотра и приложении для Apple Watch - либо явно (три профиля) или через профиль один подстановочный знак. Проверьте наличие профили подготовки в центре разработки для iOS и что вы скачали и добавить их на компьютер Mac.

### <a name="invalid-code-signing-entitlements"></a>Недопустимый код подписи прав

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Обеспечьте профилей подготовки настройки правильно в центре разработчиков Apple, а что вы скачали и установили их. Также проверьте, что они установлены в Visual Studio для Mac в окне свойств для каждого проекта.

### <a name="invalid-architecture"></a>Недопустимая архитектура

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Можно добавлять только приложения Watch [Unified API (64-разрядная версия)](~/cross-platform/macios/unified/index.md) приложений Xamarin.iOS.
Щелкните правой кнопкой мыши на проекте приложения iOS, а затем перейдите к **параметры > Создать > сборка iOS > вкладка "Дополнительно"** и убедитесь, что **Поддерживаемые архитектуры** для AppStore iPhone конфигурация включает в себя **ARM64** (например) **Для ARMv7 и ARM64**).

### <a name="this-bundle-is-invalid"></a>Этот пакет является недопустимым.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

Приложение iOS родительского должно иметь MinimumOSVersion, значение «8.2"или выше.

### <a name="non-public-api-usage"></a>Использование API non-public

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Убедитесь, что вы используете последнюю версию средства Xcode и Xamarin.
Ваш код не должны работать все интерфейсы API, не являющиеся открытыми.

### <a name="build-error-mt5309"></a>Создание ошибки MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Эта ошибка, скорее всего является результатом вашей переименован установку Xcode из **Xcode.app**. Например, эта ошибка возникает при переименовании установку, чтобы **XCode 6.2.app**.



## <a name="related-links"></a>Связанные ссылки

- [Руководство по отправке WatchKit Apple](https://developer.apple.com/app-store/watch/)
