---
title: Разрешения в Xamarin.Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: b8a8005c69c8aaee5d92bdabb3429bd52fc76b4a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770239"
---
# <a name="permissions-in-xamarinandroid"></a>Разрешения в Xamarin.Android


## <a name="overview"></a>Обзор

Android приложения выполняются в своих собственных "песочницы" и для обеспечения безопасности причинам нет доступа к некоторым системным ресурсам или оборудования на устройстве. Пользователю необходимо явно предоставить разрешение для приложения перед его могут использовать эти ресурсы. Например приложение не может получить доступ к GPS на устройстве без явного разрешения от пользователя. Android вызовет `Java.Lang.SecurityException` Если приложение пытается получить доступ к защищенному ресурсу без разрешения.

Разрешения, объявляются в **AndroidManifest.xml** разработчиком приложения, при разработке приложения. Android имеет два различных рабочих процессов для получения согласия пользователя для этих разрешений:
 
* Для приложений, нацеленных на Android 5.1 (уровень API 22) или более ранней запрос на разрешение во время установки приложения. Приложение может не установлено, если пользователь не предоставит разрешения. После установки приложения нет возможности отменять разрешения только путем удаления приложения.
* Начиная с Android 6.0 (API уровня 23), пользователи были получают больший контроль над разрешениями; они могут предоставить или отменить разрешения, при условии, что приложение установлено на устройстве. На этом снимке экрана показаны параметры разрешений приложение "Контакты" Google. Оно содержит различные разрешения и дает пользователю возможность включить или отключить разрешения:

![Пример разрешения экрана](permissions-images/01-permissions-check.png) 

Приложения Android необходимо проверить во время выполнения, чтобы увидеть, если они имеют разрешения на доступ к защищенному ресурсу. Если приложение не имеет разрешения, он должен создать запросы с помощью новых API, предоставляемые пакета SDK для Android для пользователя для предоставления разрешений. Разрешения делятся на две категории:

* **Обычный разрешения** &ndash; это разрешения которых мало угрозу безопасности для безопасности или конфиденциальности пользователя. Во время установки для Android 6.0 автоматически предоставит обычными разрешениями. Обратитесь к документации по Android для [полный перечень обычными разрешениями](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Опасные разрешения** &ndash; в отличие от обычных разрешения небезопасные разрешения, которые защиты конфиденциальности или безопасности пользователя. Их необходимо явно предоставленных пользователем. Отправка или получение сообщений SMS примером может служить действие, требующее опасные разрешения.

> [!IMPORTANT]
> Категории, к которой принадлежит разрешение может изменяться со временем.  Это возможно, что разрешение, которое было классифицировать как «normal» разрешение может быть с повышенными привилегиями в будущем уровни API опасные разрешения.

Опасные разрешения являются дальше разделить [ _группы разрешений_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Группы разрешений для хранения разрешения, которые логически связаны. Когда пользователь предоставляет разрешение на один член группы разрешений, Android автоматически предоставляет разрешение для всех членов этой группы. Например [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) группы разрешений содержит обе `WRITE_EXTERNAL_STORAGE` и `READ_EXTERNAL_STORAGE` разрешения. Если пользователь предоставляет разрешение на `READ_EXTERNAL_STORAGE`, то `WRITE_EXTERNAL_STORAGE` разрешение предоставляется автоматически в то же время.

Перед запросом одно или несколько разрешений, рекомендуется предоставить обоснование о том, почему приложения необходимо разрешение перед запросом разрешений. Как только пользователь понимает, что стоит, приложение может запрашивать разрешение у пользователя. Понимание того, что стоит, пользователь может принять обоснованное решение для предоставления разрешения и понимать последствия, если они не. 

Весь рабочий процесс проверки и запрашиваются разрешения называется _разрешения во время выполнения_ Проверьте и можно описать на следующей схеме: 

[![Блок-схема проверки разрешений во время выполнения](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Backports библиотеку поддержки Android некоторые новые интерфейсы API для разрешений в более старых версиях Android. Эти API-интерфейсы backported автоматически проверит версии Android, на устройстве, нет необходимости выполнять проверку уровня API каждый раз.  

В этом документе описывается, как добавить разрешения для приложения Xamarin.Android и как приложений, предназначенных для Android 6.0 (API уровня 23) или более поздней версии, должен выполнять проверку на разрешение во время выполнения.


> [!NOTE]
> Это возможно, что разрешения для оборудования может повлиять на способ фильтрации по Google Play приложение. Например если приложению требуется разрешение для камеры, затем Google Play не будет отображать приложения в магазин Google Play на устройстве, которое не поддерживает установку камеры.


<a name="requirements" />

## <a name="requirements"></a>Требования

Настоятельно рекомендуется, включают проекты Xamarin.Android [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) пакет NuGet. Это разрешение backport будет пакета, конкретные API для более старых версий Android, предоставляя один общий интерфейс без необходимости постоянно проверьте версии Android, приложение выполняется на.


## <a name="requesting-system-permissions"></a>Запрос разрешений системы

Первый шаг при работе с Android разрешения — объявляют разрешений в папке Android в файле манифеста. Это необходимо сделать независимо от уровня API, что приложение является для различных версий.

Приложения, предназначенные Android 6.0 или более поздней версии не может считать, что пользователю предоставлено разрешение в определенный момент в прошлом, что разрешение будет действовать в следующий раз. Приложения, ориентированного Android 6.0 всегда приходится выполнять проверку разрешений среды выполнения. Приложения, предназначенные Android 5.1 или более ранней не обязательно для выполнения проверки разрешений во время выполнения.

> [!NOTE]
> Приложения должны запрашивать только необходимых им разрешений.


### <a name="declaring-permissions-in-the-manifest"></a>Объявление разрешения в манифесте

Разрешения назначаются **AndroidManifest.xml** с `uses-permission` элемента. Например если приложение, для определения положения устройства требует детально и курс разрешения расположения. Манифест добавляются следующие два элемента: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Можно объявить разрешения с помощью средства, интегрированные в Visual Studio:

1. Дважды щелкните **свойства** в **обозревателе решений** и выберите **Android манифеста** вкладки в окне «Свойства»:

    [![Необходимые разрешения на вкладке манифеста Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Если приложение не имеет AndroidManifest.xml, нажмите кнопку **AndroidManifest.xml не найден. Нажмите, чтобы добавить один** как показано ниже:

    [![Сообщение не AndroidManifest.xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Выберите разрешения, необходимые приложению из **разрешения, необходимые** списка и сохранения:

    [![Пример КАМЕРЫ разрешений для выбранных](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Можно объявить разрешения с помощью средства поддержки, встроенные в Visual Studio для Mac:

1. Дважды щелкните проект в **Pad решения** и выберите **параметры > сборки > приложения Android**:

    [![Обязательный раздел разрешения показано](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Нажмите кнопку **Добавление манифеста Android** кнопку, если проект еще не **AndroidManifest.xml**:

    [![Отсутствует манифест проекта в Android](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Выберите разрешения, необходимые приложению из **разрешения, необходимые** списке и нажмите кнопку **ОК**:

    [![Пример КАМЕРЫ разрешений для выбранных](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android будут автоматически добавлены некоторые разрешения во время построения в отладочных построениях. Это сделает отладку приложения проще. В частности, два важных разрешения являются `INTERNET` и `READ_EXTERNAL_STORAGE`. Эти разрешения автоматически набор не сможет быть включено в **разрешения, необходимые** списка. Построение выпуска, тем не менее, используйте только те разрешения, которые явно задаются в **разрешения, необходимые** списка. 

Для приложений, предназначенных для Android 5.1 (уровень API 22) или более ранней нет ничего, которую нужно выполнить. Приложения, которые выполняются на базе Android 6.0 (API 23 уровня 23) или более поздней версии следует продолжить на следующем разделе для выполнения проверки права время выполнения. 


### <a name="runtime-permission-checks-in-android-60"></a>Проверка разрешений среды выполнения в Android 6.0

`ContextCompat.CheckSelfPermission` Метод (предусмотрено в библиотеку поддержки Android) используется для проверки, если определенное разрешение было предоставлено. Этот метод будет возвращать [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) перечисления, который имеет одно из двух значений:

* **`Permission.Granted`** &ndash; Указанное разрешение было предоставлено.
* **`Permission.Denied`** &ndash; Указанное разрешение не предоставлено.

Этот фрагмент кода приведен пример того, как проверку на разрешение камеры в действии: 

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

Рекомендуется для информирования пользователей о том, почему разрешение требуется для приложения, чтобы можно было внести обоснованное решение для предоставления разрешения. Примером будет приложение, которое принимает фотографии и теги geo их. Было ясно, пользователю необходимо разрешение камеры, но она может быть непонятно, почему приложению также местоположение устройства. Основной причиной должен вывести сообщение, чтобы помочь пользователю понять, почему разрешение расположение вариант и разрешение камеры не нужна.

`ActivityCompat.ShouldShowRequestPermissionRational` Метод используется для определения, если основной причиной должен отображаться пользователю. Этот метод будет возвращать `true` если должна отображаться обоснование для данного разрешения. На этом снимке экрана показан пример Snackbar, отображен приложением, объясняющее, почему приложение, необходимо, чтобы сведения о местоположении устройства:

![Основной причиной для расположения](permissions-images/07-rationale-snackbar.png) 

Если пользователь предоставляет разрешение, `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` метод должен вызываться. Этот метод необходимо задать следующие параметры:

* **Действие** &ndash; это действие, которое запрашивает разрешения и должна быть в курсе Android результатов.
* **разрешения** &ndash; список разрешений, которые были запрошены.
* **requestCode** &ndash; целочисленное значение, используемый для сопоставления результатов запроса на разрешение на `RequestPermissions` вызова. Значение должно быть больше нуля.

Этот фрагмент кода приведен пример двух методов, которые были описаны. Во-первых проверяется, чтобы определить, если стоит разрешение должно отображаться. В случае основной причиной для отображения Snackbar отображается с основной причиной. Если пользователь нажимает кнопку **ОК** в Snackbar, затем приложение будет запрашивать разрешения. Если пользователь не принимает обоснование, приложение должно не перейдите к запрашивать разрешения. Если основной причиной не показан, действие будет запрашивать разрешения:

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

`RequestPermission` может вызываться, даже если пользователь уже имеет разрешения. Последующие вызовы не обязательны, но они предоставляют пользователю возможность подтверждения (или revoke) разрешение. Когда `RequestPermission` — вызывается, управление передается операционной системы, который будет отображать пользовательский Интерфейс для приема разрешения:  

![Разрешение диалогового окна](permissions-images/08-location-permission-dialog.png)

После завершения работы пользователя Android возвращают результаты в действие через метод обратного вызова, `OnRequestPermissionResult`. Этот метод является частью интерфейса `ActivityCompat.IOnRequestPermissionsResultCallback` которого должен быть реализован с помощью этого действия. Этот интерфейс содержит один метод, `OnRequestPermissionsResult`, которой будет вызываться Android для информирования действие выбора пользователя. Если пользователь разрешения, приложение можно пойти дальше и использовать защищенный ресурс. Пример реализации `OnRequestPermissionResult` показано ниже: 

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

В этом руководстве описаны способы добавления и проверки разрешений в устройстве с Android. Различия в работе разрешений между старого приложения Android (API уровня < 23) и приложения Android (API уровня > 22). В этом примере обсуждали проверки разрешений во время выполнения, в Android 6.0.


## <a name="related-links"></a>Связанные ссылки

- [Список обычными разрешениями](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Пример приложения среды выполнения для разрешения](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Разрешения на обработку в Xamarin.Android](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest)
