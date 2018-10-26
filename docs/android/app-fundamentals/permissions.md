---
title: Разрешения в Xamarin.Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: a5295ef05eae213206fada135801bb4fadbcf6dd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106342"
---
# <a name="permissions-in-xamarinandroid"></a>Разрешения в Xamarin.Android


## <a name="overview"></a>Обзор

Android приложения выполняются в свои собственные "песочницы", и для обеспечения безопасности причинам не имеют доступа к некоторым системным ресурсам или оборудования на устройстве. Пользователю необходимо явно предоставить приложению разрешение на, прежде чем он может использовать эти ресурсы. Например приложение не может получить доступ к GPS на устройстве без явного разрешения от пользователя. Android вызывает исключение `Java.Lang.SecurityException` Если приложение пытается получить доступ к защищенному ресурсу без разрешения.

Разрешения, объявляются в **AndroidManifest.xml** разработчиком приложения, при этом приложение разрабатывается. Android имеет два различных рабочих процессов для получения согласия пользователя для этих разрешений:
 
* Для приложений, нацеленных на Android 5.1 (уровень API 22) или более ранней версии запрос на разрешение произошла во время установки приложения. Если пользователь не предоставит разрешения, затем приложение не устанавливались. После установки приложения, нет способа отозвать разрешения, за исключением путем удаления приложения.
* Начиная с Android 6.0 (уровень API 23), пользователи получили больше контроля над разрешениями; они могут предоставить или отменить разрешения, до тех пор, пока приложение устанавливается на устройстве. На этом снимке экрана показаны параметры разрешений для приложения Google контакты. Оно перечисляет различные разрешения и пользователь может включить или отключить разрешения:

![Пример разрешения экрана](permissions-images/01-permissions-check.png) 

Приложения Android необходимо проверить во время выполнения, чтобы увидеть, если у них нет разрешения на доступ к защищенному ресурсу. Если приложение не имеет разрешения, его необходимо выполнять запросов с помощью новых интерфейсов API, предоставляемые Android SDK для пользователя для предоставления разрешений. Разрешения можно разделить на две категории:

* **Обычный разрешения** &ndash; это разрешения, в которых мало угрозу безопасности для безопасности или конфиденциальности пользователя. Android 6.0 приводит к автоматическому предоставлению обычными разрешениями во время установки. Обратитесь к документации по Android для [полный список обычными разрешениями](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Опасные разрешения** &ndash; в отличие от обычных разрешений опасные разрешения, которые защитить безопасность или конфиденциальность пользователя. Они должны иметь явно предоставил пользователь. Отправлять или получать SMS-сообщения является примером действие, требующее опасные разрешения.

> [!IMPORTANT]
> Категория, к которой принадлежит разрешение со временем может меняться.  Вполне возможно, что разрешение, в которое был по категориям, как «обычная работа» разрешение может быть с повышенными привилегиями в будущем уровни API опасные разрешения.

Опасные разрешения являются дальше разделить [ _группы разрешений_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Группу разрешений будет содержать разрешения, которые логически связаны. Когда пользователь предоставляет доступ члену группы разрешений, Android автоматически предоставляет разрешение для всех членов этой группы. Например [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) группу разрешений содержит обе `WRITE_EXTERNAL_STORAGE` и `READ_EXTERNAL_STORAGE` разрешения. Если пользователь предоставляет разрешение на `READ_EXTERNAL_STORAGE`, а затем `WRITE_EXTERNAL_STORAGE` разрешение предоставляется автоматически в то же время.

Прежде чем запросить одно или несколько разрешений, рекомендуется предоставить обоснование о том, почему приложению требуется разрешение, прежде чем запрашивать разрешения. Как только пользователь понимает обоснование, приложение может запросить разрешение от пользователя. Изучив обоснование, пользователь мог обоснованное решение, если они хотят предоставить разрешение и понять последствия, если это не так. 

Весь рабочий процесс проверки и запрос разрешений называется _разрешения времени выполнения_ Проверьте и может быть описано на следующей схеме: 

[![Блок-схема проверки разрешений во время выполнения](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Библиотека поддержки Android бэкпортированным некоторые новые интерфейсы API, разрешениях, необходимых для более старых версиях Android. Эти дистрибутивы API-интерфейсы автоматически проверит версии Android на устройстве, поэтому нет необходимости выполнить проверку уровня API каждый раз.  

В этом документе описано, как добавить разрешения для приложения Xamarin.Android и как приложений, предназначенных для Android 6.0 (уровень API 23) или более поздней версии, необходимо выполнить проверку разрешений во время выполнения.


> [!NOTE]
> Вполне возможно, что разрешения для оборудования может повлиять на способ фильтрации приложение в Google Play. Например если приложению требуется разрешение для камеры, затем Google Play не покажет приложение в Google Play Store на устройстве, которое не поддерживает установку камеры.


<a name="requirements" />

## <a name="requirements"></a>Требования

Настоятельно рекомендуется, включают проекты Xamarin.Android [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) пакет NuGet. Это разрешение бэкпортировать будет пакета, конкретные API для более старых версиях Android, предоставляя одна общая интерфейса без необходимости постоянно проверка версии Android, на которой выполняется приложение.


## <a name="requesting-system-permissions"></a>Запрос разрешений системы

Первый шаг при работе с разрешения Android — объявить, что файл манифеста разрешения в Android. Это необходимо сделать независимо от уровня API, что приложение ориентирован.

Приложения, предназначенных для Android 6.0 или более поздней версии не может считать, что пользователю предоставлены разрешения в определенный момент в прошлом, что разрешение будет действовать в следующий раз. Приложение, нацеленное на Android 6.0 всегда необходимо выполнять разрешение проверку времени выполнения. Приложений, предназначенных для Android 5.1 или ниже не обязательно выполнять проверку разрешений во время выполнения.

> [!NOTE]
> Приложения должны запросить только необходимых им разрешений.


### <a name="declaring-permissions-in-the-manifest"></a>Объявление разрешения в манифесте

Добавляются разрешения **AndroidManifest.xml** с `uses-permission` элемент. Например если приложение определить местоположение устройства, требуется нормально и курс разрешения расположения. Следующие два элемента добавляются в манифест: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Можно объявить разрешения с помощью средства поддержки, встроенных в Visual Studio:

1. Дважды щелкните **свойства** в **обозревателе решений** и выберите **манифест Android** вкладки в окне Свойства:

    [![Необходимые разрешения на вкладке "манифест Android"](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Если приложение не имеет AndroidManifest.xml, нажмите кнопку **AndroidManifest.xml не найден. Нажмите, чтобы добавить один** как показано ниже:

    [![Сообщение не AndroidManifest.xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Выберите все разрешения, необходимые для приложения из **необходимые разрешения** списке и сохранить:

    [![Пример КАМЕРЫ разрешения, выбранные](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Можно объявить разрешения с помощью средства поддержки, встроенных в Visual Studio для Mac:

1. Дважды щелкните проект в **панели решения** и выберите **параметры > Создать > приложение Android**:

    [![Требуется раздел разрешений](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Нажмите кнопку **добавить манифест Android** кнопку, если проект уже содержит **AndroidManifest.xml**:

    [![Отсутствует манифест Android для проекта](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Выберите все разрешения, необходимые для приложения из **необходимые разрешения** списке и нажмите кнопку **ОК**:

    [![Пример КАМЕРЫ разрешения, выбранные](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android будут автоматически добавлены некоторые разрешения во время сборки в отладочных сборках. Это сделает отладку приложения проще. В частности, два важных разрешения являются `INTERNET` и `READ_EXTERNAL_STORAGE`. Эти разрешения автоматически набора не сможет включить в **необходимые разрешения** списка. Сборки выпуска, тем не менее, используйте только разрешения, которые явно задаются в **необходимые разрешения** списка. 

Для приложений, предназначенных для Android 5.1 (уровень API 22) или более ранней версии нет ничего, что нужно сделать. Приложения, которые будут выполняться на базе Android 6.0 (уровень API 23 23) или более поздней версии следует перейти к следующему разделу по выполнению время выполнения проверки права доступа. 


### <a name="runtime-permission-checks-in-android-60"></a>Проверка разрешений среды выполнения в Android 6.0

`ContextCompat.CheckSelfPermission` Метод (доступно с библиотекой поддержки Android) используется для проверки, если определенное разрешение было предоставлено. Этот метод будет возвращать [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) enum, имеющей одну из двух значений:

* **`Permission.Granted`** &ndash; Было предоставлено указанное разрешение.
* **`Permission.Denied`** &ndash; Указанное разрешение не предоставлено.

В этом фрагменте кода приведен пример того, как проверяющих наличие разрешения камеры в действии: 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

Это лучший способ информирования пользователя о том, почему разрешение необходима для приложения, чтобы обоснованное решение можно сделать для предоставления разрешения. Примером этого может служить приложение, которое принимает фотографии и geo тегов их. Должно быть понятно пользователю необходимо разрешение камеры, но он может быть непонятным приложению также требуется расположение устройства. Чтобы обосновать должен отображать сообщение, чтобы помочь пользователю понять, почему разрешение для расположения столь целесообразной, и разрешение камеры не нужна.

`ActivityCompat.ShouldShowRequestPermissionRational` Метод используется для определения того, если основной причиной должен отображаться для пользователя. Этот метод будет возвращать `true` если должна отображаться обоснование для данного разрешения. На этом снимке экрана показан пример Snackbar, отображаемый приложением, которое объясняет, почему приложение необходимо знать расположение устройства:

![Чтобы обосновать расположение](permissions-images/07-rationale-snackbar.png) 

Если пользователь предоставляет разрешение `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` метод должен вызываться. Этот метод необходимо задать следующие параметры:

* **Действие** &ndash; это действие, которое запрашивает разрешения и должен быть извещен по Android результатов.
* **разрешения** &ndash; список разрешений, которые были запрошены.
* **requestCode** &ndash; целочисленное значение, используются для сопоставления результатов запроса на разрешение `RequestPermissions` вызова. Значение должно быть больше нуля.

В этом фрагменте кода приведен пример двух методов, которые были описаны. Во-первых то выполняется проверка для определения того, если разрешение обоснование должен отображаться. Если основной причиной является для отображения, Snackbar выводится с основной причиной. Если пользователь нажимает кнопку **ОК** в Snackbar, затем приложение будет запрашивать разрешения. Если пользователь не принимает обоснование, приложение не продолжения для запроса разрешений. Если не содержится обоснование, действие будет запрашивать разрешение:

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission` может вызываться, даже если пользователь уже имеет разрешения. Последующие вызовы не требуются, но они предоставляют пользователю возможность подтверждения (или revoke) разрешение. Когда `RequestPermission` является именем, управление передается в операционную систему, который будет отображать пользовательский Интерфейс для принятия разрешения:  

![Разрешение диалогового окна](permissions-images/08-location-permission-dialog.png)

После завершения пользователь Android будет вернуть результат к действию через метод обратного вызова, `OnRequestPermissionResult`. Этот метод является частью интерфейса `ActivityCompat.IOnRequestPermissionsResultCallback` которого должен быть реализован с помощью действия. Этот интерфейс содержит один метод, `OnRequestPermissionsResult`, который будет вызываться системой Android для информирования действия пользователя вариантов. Если пользователь разрешения, приложение может пойти дальше и использовать защищенный ресурс. Пример реализации `OnRequestPermissionResult` показан ниже: 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permision_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  


## <a name="summary"></a>Сводка

В этом руководстве рассматриваются способы добавления и проверки разрешений на устройстве Android. Различия в работе разрешения между старые приложения Android (API уровня < 23) и приложения Android (API уровня > 22). В этом примере рассматривался проверки разрешений во время выполнения, в Android 6.0.


## <a name="related-links"></a>Связанные ссылки

- [Список обычными разрешениями](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Пример приложения среды выполнения разрешения](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Разрешения на обработку в Xamarin.Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
