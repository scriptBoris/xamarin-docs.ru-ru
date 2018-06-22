---
title: Привет, износ
description: Создание первого приложения Android носят и запустите его на устройстве или эмуляторе одежды. Это пошаговое руководство содержит пошаговые инструкции по созданию небольшой проект Android носят, обрабатывает нажатие кнопки и отображает счетчика щелкните на устройстве одежды. В этом примере описана отладка приложения с помощью эмулятора износ или износ устройства, подключенного через Bluetooth на телефоне с Android. Он также предоставляет набор отладки советы для Android с.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 17c12c4ec818c21d6697932315874ea4f63e6109
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798418"
---
# <a name="hello-wear"></a>Привет, износ

_Создание первого приложения Android носят и запустите его на устройстве или эмуляторе одежды. Это пошаговое руководство содержит пошаговые инструкции по созданию небольшой проект Android носят, обрабатывает нажатие кнопки и отображает счетчика щелкните на устройстве одежды. В этом примере описана отладка приложения с помощью эмулятора износ или износ устройства, подключенного через Bluetooth на телефоне с Android. Он также предоставляет набор отладки советы для Android с._

![Снимок экрана приложения износ должны завершаться в этом учебнике](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>Своего первого приложения износ

Выполните следующие действия для создания первого приложения Xamarin.Android одежды.

### <a name="1-create-a-new-android-project"></a>1. Создание нового проекта Android

Создайте новый **приложения Android с**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Создание нового Android носят приложения в диалоговом окне нового проекта](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Создание нового Android носят приложения в диалоговом окне нового решения](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Этот шаблон автоматически включает **Xamarin Android переносном библиотеки** NuGet (и зависимостях) таким образом вы получаете доступ к мини-приложения, зависящие от износа. Если вы не видите шаблона одежды, просмотрите [установку и настройку](~/android/wear/get-started/installation.md) руководство по повторно проверьте наличие установленной среды поддерживаемым пакетом SDK для Android. 

### <a name="2-choose-the-correct-target-framework"></a>2. Выбрать правильный **требуемой версии .NET Framework**

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Убедитесь, что **Android минимум целевой** равно **Android 5.0 (без описания операций)** или более поздней версии: 

[![Задание целевой платформы для Android 5.0 в Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Убедитесь, требуемая версия .NET framework задано **Android 5.0 (без описания операций)** или более поздней версии:

[![Задание целевой платформы для Android 5.0 в Visual Studio для Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Дополнительные сведения о задании целевой платформы см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Изменить **Main.axml** макета

Настроить макет, содержащий `TextView` и `Button` для образца: 

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

Добавьте код для увеличения значения счетчика и его отображения при каждом нажатии кнопки. 

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

### <a name="5-setup-an-emulator-or-device"></a>5. Программа установки эмулятора или устройства

Следующим шагом настроить эмулятор или устройство для развертывания и запуска приложения. Если вы еще не знакомы с процессом развертывания и выполнения приложений Xamarin.Android см в разделе [Привет, Android краткое руководство](~/android/get-started/hello-android/hello-android-quickstart.md).

Если нет устройства с Android, например Android Smartwatch, носят, приложение можно запустить в эмуляторе. Сведения об отладке износ приложения в эмуляторе, см. в разделе [отладки Android носить в эмуляторе,](~/android/wear/deploy-test/debug-on-emulator.md).

При наличии устройства с Android, например Android Smartwatch с запуском приложения на устройстве, вместо того чтобы использовать эмулятор. Дополнительные сведения об отладке на устройстве износ см. в разделе [отладка на устройстве с](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Запуск приложения Android износ

Устройства с Android должны появиться в раскрывающееся меню устройство. Не забудьте выбрать правильное устройство с Android или AVD, перед запуском отладки. После выбора устройства, щелкните кнопку воспроизведения, чтобы развернуть приложение на эмуляторе или устройстве.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Выбор AVD, носят в меню Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Выбор AVD, носят в Visual Studio для Mac меню](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Вы можете увидеть **течение минуты...**  сообщения (или другой interstitial экран), сначала: 

![Посмотрите, как эмулятор отображает только минуты...](hello-wear-images/please-wait.png)

Если вы используете эмулятор Контрольные значения, может потребоваться время для запуска приложения. При использовании Bluetooth займет больше времени для развертывания приложения, чем по USB. (Например, занимающий около 5 минут для развертывания этого приложения часы LG G, Bluetooth подключен телефон 5 хранилища.)

После успешного развертывает приложение, экран устройства износ отображать экран следующим образом:

[![Начальный экран приложения износ](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Коснитесь **CLICK ME!** кнопка на лицевой стороне устройства Износ и инкремент счетчика каждого касанием см.:

[![Снимок экрана носят приложения после 3 щелчков мыши](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Следующие шаги

Извлечение [носят образцы](https://developer.xamarin.com/samples/android/Android%20Wear/) включая приложения для Android носить с приложениями Phone помощник по поиску.

Когда будете готовы для распространения приложения, в разделе [работа с упаковкой](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Связанные ссылки

- [Щелкните мне приложение (пример)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
