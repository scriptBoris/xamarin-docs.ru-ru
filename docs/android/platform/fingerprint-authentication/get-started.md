---
title: Начало работы с проверкой подлинности отпечатков пальцев
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 70d27ef3d7518619a246c25aac128b2fd1ed70c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764402"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Начало работы с проверкой подлинности отпечатков пальцев

Чтобы приступить к работе, давайте сначала рассматривается настройка проекта Xamarin.Android, таким образом, чтобы приложения могли использовать проверку подлинности отпечатков пальцев:

1. Обновление **AndroidManifest.xml** для объявления разрешениях, необходимых для API-интерфейсы отпечатков пальцев.
2. Получить ссылку на `FingerprintManager`.
3. Проверьте, что устройство является возможность просмотра отпечатков пальцев.

## <a name="requesting-permissions-in-the-application-manifest"></a>Манифест запроса разрешений в приложении

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Приложение должно запросить `USE_FINGERPRINT` разрешение в манифесте. Следующем снимке экрана показано, как добавить это разрешение для приложения в Visual Studio 2015:

[![Разрешение использования\_отпечатков ПАЛЬЦЕВ на экране манифеста Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Приложение должно запросить `USE_FINGERPRINT` разрешение в манифесте. Следующем снимке экрана показано, как добавить это разрешение для приложения в Visual Studio для Mac:

[![Включение UseFingerprint на экране приложения Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Получение экземпляра FingerprintManager

После этого приложение должно получить экземпляр `FingerprintManager` или `FingerprintManagerCompat` класса. Для обеспечения совместимости с более ранними версиями Android, приложение следует использовать API совместимости найдены в пакет NuGet для поддержки Android версии 4. Следующий фрагмент кода показано, как получить соответствующий объект из операционной системы: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

В приведенном выше фрагменте `context` является любой Android `Android.Content.Context`. Как правило, это операции, который выполняет проверку подлинности.

## <a name="checking-for-eligibility"></a>Проверка допустимости

Приложение должно выполнить несколько проверок, чтобы убедиться, что существует возможность использовать проверку подлинности отпечатков пальцев. В итоге существует пять условия, которые приложение использует для проверки права:  
 

**API уровня 23** &ndash; к API-интерфейсам отпечатков пальцев требуют API уровня 23 или более поздней версии. `FingerprintManagerCompat` Класса будет служить оболочкой проверку уровня API для вас. По этой причине рекомендуется использовать **библиотеку поддержки Android версии 4** и `FingerprintManagerCompat`; это будет учетная запись для одного из этих проверок.

**Оборудование** &ndash; при первом запуске приложения, она должна проверять на наличие сканеров отпечатков пальцев:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```
    
**Устройство защищено** &ndash; пользователь должен иметь защищен с помощью блокировки экрана устройства. Если пользователь не имеет защищено устройства с блокировка экрана и безопасность важна для приложения, затем следует уведомить пользователя, экран блокировки должен быть настроен. В следующем фрагменте кода показано, как проверить этот pre requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Зарегистрированные отпечатки пальцев** &ndash; пользователь должен иметь по крайней мере один отпечаток зарегистрирован в операционной системе. Эта проверка разрешение должно выполняться перед каждой попытки проверки подлинности:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Разрешения** &ndash; приложения должны запрашивать разрешение пользователя перед использованием приложения. Для Android 5.0 и ниже пользователь предоставляет разрешение в качестве условия для установки приложения. Android 6.0 появилась новая модель разрешений, которая проверяет разрешения во время выполнения. Этот фрагмент кода приведен пример того, как для проверки разрешений на базе Android 6.0:

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

Приложение должно проверять условия, 3, 4 и 5, каждый раз, когда он хочет использовать проверку подлинности отпечатков пальцев. Первые два условия может проверяться в первый раз, когда приложение запускается на устройстве и сохранять результаты (в общие параметры например).

Дополнительные сведения о том, как запросить разрешения в Android 6.0 см. в руководстве Android [запрос разрешений во время выполнения](http://developer.android.com/training/permissions/requesting.html).



## <a name="related-links"></a>Связанные ссылки

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Запрос разрешений во время выполнения](http://developer.android.com/training/permissions/requesting.html)
