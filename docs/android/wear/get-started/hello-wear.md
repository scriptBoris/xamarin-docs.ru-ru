---
title: Сведения о wear
description: Создание первого приложения для Android Wear и запустите его на устройстве или эмуляторе одежды. В этом пошаговом руководстве содержатся пошаговые инструкции по созданию небольшой проект Android Wear, которая выполняет нажатия кнопок и отображает счетчик нажмите кнопку на устройстве Android Wear. Здесь объясняется, как выполнить отладку приложения, с помощью эмулятора износа или устройстве Android Wear, подключенный через Bluetooth на телефоне с Android. Он также предоставляет набор отладки советы для Android Wear.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: a8e27063040ff91f72a1cbf932b1b277a5dee63d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104925"
---
# <a name="hello-wear"></a>Сведения о wear

_Создание первого приложения для Android Wear и запустите его на устройстве или эмуляторе одежды. В этом пошаговом руководстве содержатся пошаговые инструкции по созданию небольшой проект Android Wear, которая выполняет нажатия кнопок и отображает счетчик нажмите кнопку на устройстве Android Wear. Здесь объясняется, как выполнить отладку приложения, с помощью эмулятора износа или устройстве Android Wear, подключенный через Bluetooth на телефоне с Android. Он также предоставляет набор отладки советы для Android Wear._

![Снимок экрана приложения износа завершения этого руководства](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>Ваше первое приложение Wear

Выполните следующие действия, чтобы создать свое первое приложение Xamarin.Android одежды.

### <a name="1-create-a-new-android-project"></a>1. Создайте новый проект Android

Создайте новый **приложение Android Wear**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Создание Android Wear приложения в диалоговом окне нового проекта](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Создание Android Wear приложения в диалоговом окне нового решения](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Этот шаблон автоматически включает **Носимого библиотеки Android Xamarin** NuGet (и зависимости) таким образом вы получаете доступ к износа конкретных мини-приложения. Если вы не видите шаблон одежды, просмотрите [установки и настройки](~/android/wear/get-started/installation.md) руководство еще раз проверьте, что вы установили поддерживаемым пакетом SDK для Android. 

### <a name="2-choose-the-correct-target-framework"></a>2. Выберите правильное **требуемой версии .NET Framework**

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Убедитесь, что **минимум Android к целевому объекту** присваивается **Android 5.0 (Lollipop)** или более поздней версии: 

[![Задание целевой платформы Android 5.0, в Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Установите требуемую версию .NET framework **Android 5.0 (Lollipop)** или более поздней версии:

[![Задание целевой платформы Android 5.0, в Visual Studio для Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Дополнительные сведения об установке требуемой версии .NET framework, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Изменить **Main.axml** макета

Настройте макет, содержащий `TextView` и `Button` примера: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. Изменить **MainActivity.cs** источника

Добавьте код для увеличения значения счетчика и отобразить ее при каждом нажатии кнопки. 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. Программа установки, в эмуляторе или на устройстве

Следующий шаг настройки эмулятора или устройства для развертывания и запуска приложения. Если вы еще не знакомы с процессом развертывания и запуска приложений Xamarin.Android в целом, см. в разделе [Привет, Android: Краткое руководство](~/android/get-started/hello-android/hello-android-quickstart.md).

Если у вас устройство Android Wear, таких как Android Wear Smartwatch, можно запустить приложение на эмуляторе. Сведения об отладке приложений износа в эмуляторе, см. в разделе [отладки Android Wear на эмуляторе](~/android/wear/deploy-test/debug-on-emulator.md).

При наличии на устройстве Android Wear, таких как Android Wear Smartwatch запуском приложения на устройстве, а не с помощью эмулятора. Дополнительные сведения об отладке на устройстве Android Wear см. в разделе [отладка на устройстве Wear](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Запуск приложения Android Wear

Устройство Android Wear появятся в раскрывающееся меню устройства. Должно быть выбрано правильное устройство Android Wear или AVD перед запуском отладки. После выбора устройства, щелкните кнопку воспроизведения, чтобы развернуть приложение на эмуляторе или устройстве.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Выбор в меню Visual Studio устройства AVD, Wear](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Выбор AVD, Wear в Visual Studio для Mac меню устройства](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Может появиться **минутку...**  сообщение (или другой вставки панели), сначала: 

![Посмотрите, как эмулятор отображает минутку...](hello-wear-images/please-wait.png)

Если вы используете эмулятор Контрольные значения, может занять некоторое время для запуска приложения. При использовании Bluetooth, занимает больше времени, чтобы развернуть приложение, чем через USB. (Например, занимает около 5 минут развернуть это приложение часы LG G, подключенных к Bluetooth на телефон Nexus 5.)

После успешного развертывания приложения на экране устройства износа должен отображаться экран следующим образом:

[![Начальный экран приложения Wear](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Коснитесь **CLICK ME!** кнопка на устройстве Android Wear и см. в разделе инкремент счетчика каждой кнопки:

[![Снимок экрана Wear приложения после 3 щелчков](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с [Wear примеры](https://developer.xamarin.com/samples/android/Android%20Wear/) включая Android Wear приложений с помощью карт разрабатываются дополнительные приложения телефона.

Когда будете готовы для распространения приложения, см. в разделе [работа с упаковкой](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Связанные ссылки

- [Щелкните мне приложение (пример)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
