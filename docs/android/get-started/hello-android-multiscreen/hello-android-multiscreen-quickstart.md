---
title: 'Привет, Android (несколько экранов): краткое руководство'
description: Это руководство из двух частей посвящено расширению функционала приложения Phoneword, предназначенного для работы со вторым экраном. Попутно вводятся сведения об основных стандартных блоках приложения Android с более детальным анализом архитектуры Android.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/30/2018
ms.openlocfilehash: d8f909ab522b5bbf08a2b666fd4f64340e60b3e5
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436935"
---
# <a name="hello-android-multiscreen-quickstart"></a>Привет, Android (несколько экранов): краткое руководство

_В этом руководстве из двух частей описано, как расширить возможности приложения Phoneword для работы со вторым экраном. Также здесь рассмотрены основные стандартные блоки приложения Android и приведен анализ архитектуры Android._

## <a name="hello-android-multiscreen-quickstart"></a>Привет, Android (несколько экранов). Краткое руководство

В пошаговых инструкциях вы добавите второй экран в приложение [Phoneword](https://developer.xamarin.com/samples/monodroid/Phoneword/), чтобы отслеживать журнал номеров, преобразуемых этим приложением. [Итоговое приложение](https://developer.xamarin.com/samples/monodroid/PhonewordMultiscreen/) будет иметь второй экран, где отображаются преобразованные номера, как показано на снимке экрана справа:

[![Снимки экрана примера приложения](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

Прилагаемый [подробный обзор](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md) описывает полученный результат, а также затрагивает архитектуру, навигацию и другие новые понятия Android, которые вам встретились.


## <a name="requirements"></a>Требования

Так как эта часть начинается с того момента, на котором заканчивается часть [Привет, Android](~/android/get-started/hello-android/index.md), вам нужно сначала изучить [Привет, Android: краткое руководство](~/android/get-started/hello-android/hello-android-quickstart.md).
Если вы хотите перейти сразу к указанному ниже пошаговому руководству, можете скачать готовую версию [Phoneword](https://developer.xamarin.com/samples/monodroid/Phoneword/) (из части "Привет, Android: краткое руководство") и использовать ее при работе с руководством.

## <a name="walkthrough"></a>Пошаговое руководство

В этом пошаговом руководстве вы добавите в приложение **Phoneword** экран **Translation History** (Журнал преобразований).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Сначала откройте приложение **Phoneword** в Visual Studio и измените файл **Main.axml** из **обозревателя решений**.

### <a name="updating-the-layout"></a>Обновление макета

На **панели элементов** перетащите элемент **Button** в область конструктора и расположите его под элементом TextView **TranslatedPhoneWord**. В области **Свойства** замените **ИД** кнопки на `@+id/TranslationHistoryButton`. 

[![Перетаскивание новой кнопки](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

Задайте для свойства **Текст** кнопки значение `@string/translationHistory`. Android Designer интерпретирует это буквально, но вы внесете некоторые изменения, чтобы текст кнопки отображался правильно:

[![Задание текста для кнопки журнала преобразований](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

Разверните узел **Значения** в папке **Ресурсы** в **обозревателе решений** и дважды щелкните файл строковых ресурсов **Strings.xml**:

[![Открытие файла Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

Добавьте значение и имя строки `translationHistory` в **Strings.xml**, а затем сохраните файл:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

Текст кнопки **Translation History** (Журнал преобразований) должен обновиться, отразив новое строковое значение:

[![Отражение нового строкового значения для кнопки](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

Выбрав кнопку **Translation History** (Журнал преобразований) в области конструктора, найдите параметр `enabled` в области **Свойства** и присвойте ему значение `false`, чтобы отключить кнопку. При этом кнопка в области конструктора темнеет:

[![Отключение кнопки журнала преобразований](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Создание второго действия

Создайте вторую операцию для обработки второго экрана. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый элемент...**:

[![Добавление нового файла](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

В диалоговом окне **Добавление нового элемента** выберите **Visual C# > Действия** и назовите файл действий **TranslationHistoryActivity.cs**.

Замените код шаблона в файле **TranslationHistoryActivity.cs** следующим кодом:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

В этом классе вы создаете `ListActivity` и программно заполняете его, поэтому не нужно создавать файл макета для этого действия. Более подробно эти вопросы рассмотрены в разделе [Привет, Android (несколько экранов). Подробные сведения](~/android/get-started/hello-android/hello-android-deepdive.md).

### <a name="adding-translation-history-code"></a>Добавление кода журнала преобразований

Это приложение собирает телефонные номера (которые пользователь преобразовал на первом экране) и передает их на второй экран. Эти номера хранятся в виде списка строк. Для поддержки списков (и целей, которые используются позднее) добавьте следующие директивы `using` в начало файла **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Создайте пустой список, который можно заполнить телефонными номерами.
Класс `MainActivity` будет выглядеть следующим образом:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

В классе `MainActivity` добавьте следующий код, чтобы зарегистрировать кнопку **Translation History** (Журнал преобразований) (поместите эту строку сразу после объявления `translateButton`):

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Добавьте в конец метода `OnCreate` следующий код, чтобы подключить кнопку **Translation History** (Журнал преобразований):

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Измените кнопку **Translate** (Преобразовать), чтобы добавить телефонный номер в список `phoneNumbers`. Обработчик `Click` для `TranslateHistoryButton` должен иметь вид, аналогичный следующему коду:

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

Сохраните изменения и выполните сборку приложения, чтобы убедиться в отсутствии ошибок.

### <a name="running-the-app"></a>Запуск приложения

Разверните приложение в эмуляторе или на устройстве. На следующих снимках экрана показано запущенное приложение **Phoneword**:

[![Снимки экрана с примерами](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Сначала откройте проект **Phoneword** в Visual Studio для Mac и измените файл **Main.axml** из **Панели решения**.

### <a name="updating-the-layout"></a>Обновление макета

На **панели элементов** перетащите элемент **Button** в область конструктора и расположите его под элементом TextView **TranslatedPhoneWord**. На панели **Свойства** замените **ИД** кнопки на `@+id/TranslationHistoryButton`. 

[![Перетаскивание новой кнопки](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

Задайте для свойства **Текст** кнопки значение `@string/translationHistory`. Android Designer интерпретирует это буквально, но вы внесете некоторые изменения, чтобы текст кнопки отображался правильно:

[![Задание текста для кнопки журнала преобразований](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)


Разверните узел **Значения** в папке **Ресурсы** на **Панели решения** и дважды щелкните файл строковых ресурсов **Strings.xml**:

[![Открытие строк](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)


Добавьте значение и имя строки `translationHistory` в **Strings.xml**, а затем сохраните файл:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

Текст кнопки **Translation History** (Журнал преобразований) должен обновиться, отразив новое строковое значение:

[![Отражение нового строкового значения для кнопки](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)


Выбрав кнопку **Translation History** (Журнал преобразований) в области конструктора, откройте вкладку **Поведение** на **Панели решения** и дважды щелкните флажок **Включено**, чтобы отключить кнопку. При этом кнопка в области конструктора темнеет:

[![Отключение кнопки журнала преобразований](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Создание второго действия

Создайте вторую операцию для обработки второго экрана. На **Панели решения** щелкните серый значок шестеренки рядом с проектом **Phoneword** и выберите **Добавить > Новый файл...**:

В диалоговом окне **Новый файл** выберите **Android > Действия**, укажите имя действия `TranslationHistoryActivity` и нажмите кнопку **Добавить**.

Замените код шаблона в `TranslationHistoryActivity` следующим кодом:

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

В этом классе вы создаете `ListActivity` и программно заполняете его, поэтому не нужно создавать файл макета для этого действия. Более подробно эти вопросы рассмотрены в разделе [Привет, Android (несколько экранов). Подробные сведения](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

### <a name="adding-translation-history-code"></a>Добавление кода журнала преобразований

Это приложение собирает телефонные номера (которые пользователь преобразовал на первом экране) и передает их на второй экран. Эти номера хранятся в виде списка строк. Для поддержки списков (и целей, которые используются позднее) добавьте следующие директивы `using` в начало файла **MainActivity.cs**:

```csharp
using System.Collections.Generic;
using Android.Content;
```

Создайте пустой список, который можно заполнить телефонными номерами. Класс `MainActivity` будет выглядеть следующим образом:

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

В классе `MainActivity` добавьте следующий код, чтобы зарегистрировать кнопку **Translation History** (Журнал преобразований) (поместите эту строку сразу после объявления `TranslationHistoryButton`):

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Добавьте в конец метода `OnCreate` следующий код, чтобы подключить кнопку **Translation History** (Журнал преобразований):

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Измените кнопку **Translate** (Преобразовать), чтобы добавить телефонный номер в список `phoneNumbers`. Обработчик `Click` для `TranslateHistoryButton` должен иметь вид, аналогичный следующему коду:

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>Запуск приложения

Разверните приложение в эмуляторе или на устройстве. На следующих снимках экрана показано запущенное приложение **Phoneword**:

[![Снимки экрана с примерами](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

-----

Поздравляем! Вы создали свое первое приложение Xamarin.Android с несколькими экранами! Пришло время закрепить и углубить приобретенные знания и навыки &ndash; вас ждет раздел [Привет, Android (несколько экранов). Подробные сведения](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).


## <a name="related-links"></a>Связанные ссылки

- [Значки приложения Xamarin и экраны запуска (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (пример)](https://developer.xamarin.com/samples/monodroid/Phoneword)
- [PhonewordMultiscreen (пример)](https://developer.xamarin.com/samples/monodroid/PhonewordMultiscreen)
