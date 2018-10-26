---
title: Приступая к работе с проверкой подлинности по отпечатку пальца
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: 05069272bfa25cc1f003d4aeb83e15bd223c2830
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118478"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Приступая к работе с проверкой подлинности по отпечатку пальца

Чтобы начать работу, давайте сначала рассмотрим, как настроить проект Xamarin.Android, чтобы приложение не может использовать проверку подлинности по отпечатку пальца:

1. Обновление **AndroidManifest.xml** для объявления разрешениях, необходимых для API-интерфейсы отпечатков пальцев.
2. Получить ссылку на `FingerprintManager`.
3. Проверьте, что устройство является возможность сканирования отпечатков пальцев.

## <a name="requesting-permissions-in-the-application-manifest"></a>Манифест запроса разрешений в приложении

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Приложение Android необходимо запросить `USE_FINGERPRINT` разрешения в манифесте. Снимке экрана ниже показано, как добавить это разрешение для приложения в Visual Studio 2015:

[![Разрешение использования\_отпечатков ПАЛЬЦЕВ на экране манифеста Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Приложение Android необходимо запросить `USE_FINGERPRINT` разрешения в манифесте. Снимке экрана ниже показано, как добавлять это разрешение для приложения в Visual Studio для Mac:

[![Включение UseFingerprint на экране приложения Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Получение экземпляра FingerprintManager

После этого приложение должно получить экземпляр `FingerprintManager` или `FingerprintManagerCompat` класса. Для совместимости с более старых версиях Android, приложение Android следует использовать API совместимости найдены в пакете NuGet поддержки Android версии 4. Следующий фрагмент кода демонстрирует, как получить соответствующий объект из операционной системы: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

В предыдущем фрагменте `context` является любой Android `Android.Content.Context`. Как правило, это действие, которое выполняет проверку подлинности.

## <a name="checking-for-eligibility"></a>Проверка допустимости

Приложение должно выполнить несколько проверок, чтобы убедиться, что существует возможность использовать проверку подлинности по отпечатку пальца. В целом существует пять условия, которые приложение использует для проверки права:  

**Уровень API 23** &ndash; к API-интерфейсам отпечатков пальцев необходим уровень API 23 или более поздней версии. `FingerprintManagerCompat` Класс будет реализовать проверку уровня API для вас. По этой причине рекомендуется использовать **библиотеки поддержки Android v4** и `FingerprintManagerCompat`; это будет учетная запись для одного из этих проверок.

**Оборудование** &ndash; при первом запуске приложения, оно должно проверить наличие сканер отпечатков пальцев:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Устройство не защищено** &ndash; пользователь должен иметь устройства, защищенные с помощью блокировки экрана. Если пользователь не содержит защищенные устройства с помощью блокировки экрана и безопасность важна в приложение, затем пользователь уведомляется блокировка экрана должен быть настроен. В следующем фрагменте кода показано, как проверить это Предварительное требование:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Зарегистрированных отпечатков** &ndash; пользователь должен иметь по крайней мере один отпечатков пальцев, зарегистрированные в операционной системе. Эта проверка разрешение должно выполняться перед каждой попытки проверки подлинности:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Разрешения** &ndash; приложения должны запрашивать разрешение пользователя перед использованием приложения. Для Android 5.0 и ниже пользователь предоставляет разрешение условием установки приложения. Android 6.0 введена новая модель разрешение проверки разрешений во время выполнения. В этом фрагменте кода приведен пример того, как для проверки разрешений на базе Android 6.0:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // http://developer.android.com/training/permissions/requesting.html
}
```

Проверка всех этих условий, каждый раз, это приложение предлагает варианты проверки подлинности будет гарантируют, что пользователь получает удобства работы пользователей. Изменения или обновления к устройству или операционной системы может повлиять на доступность проверки подлинности по отпечатку пальца. Если вы решили кэшировать результаты любого из этих проверок, не забудьте принять во внимание следующие сценарии обновления.

Дополнительные сведения о том, как запросить разрешения в Android 6.0 см. в руководстве по Android [запрос прав доступа во время выполнения](http://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Связанные ссылки

- [Контекст](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Запрос разрешений во время выполнения](http://developer.android.com/training/permissions/requesting.html)
