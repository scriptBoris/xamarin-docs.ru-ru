---
title: 'Привет, Android: краткое руководство'
description: В этом состоящим из двух частей руководстве вы узнаете, как создать первое приложение Xamarin.Android в Visual Studio или Visual Studio для Mac. Вы также получите представление об основах разработки приложений Android с помощью Xamarin. Кроме того, в этом руководстве будут описаны инструменты, понятия и действия, необходимые для сборки и развертывания приложения Xamarin.Android.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 44007FA1-3ABC-4935-BF52-4613AF0553A6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/20/2018
ms.openlocfilehash: beb90587e0d720de7770056c8b51264099edecdc
ms.sourcegitcommit: fb55eba393e43bcc9e9d1fef9ef1f1310e99f620
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/21/2018
ms.locfileid: "39189025"
---
# <a name="hello-android-quickstart"></a>Привет, Android: краткое руководство

_В этом руководстве из двух частей описано, как создать первое приложение Xamarin.Android в Visual Studio или Visual Studio для Mac. Вы также узнаете об основах разработки приложений Android с помощью Xamarin. Кроме того, в руководстве описаны инструменты, понятия и действия, необходимые для сборки и развертывания приложения Xamarin.Android._

## <a name="hello-android-quickstart"></a>Привет, Android: краткое руководство

В этом пошаговом руководстве вы создадите приложение, которое преобразовывает введенный пользователем буквенно-цифровой телефонный номер в числовой телефонный номер и отображает результат пользователю. Окончательный вариант приложения выглядит примерно так:

[![Снимок экрана готового приложения](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)


## <a name="requirements"></a>Требования

Для выполнения данного пошагового руководства необходимо следующее:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Windows 7 или более поздней версии.

-   Visual Studio 2015 Professional или более поздней версии.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

-   Последняя версия Visual Studio для Mac.

-   OS X Yosemite или более поздней версии.

-----

В этом пошаговом руководстве предполагается, что на вашей платформе установлена и запущена последняя версия Xamarin.Android. Инструкции по установке Xamarin.Android см. в разделе [Установка Xamarin.Android](~/android/get-started/installation/index.md).
Перед началом работы скачайте и распакуйте набор [значков приложения Xamarin и экранов запуска](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true).

## <a name="configuring-emulators"></a>Настройка эмуляторов

Если вы используете эмулятор Android, рекомендуем настроить в нем использование аппаратного ускорения. Инструкции по настройке аппаратного ускорения см. в разделе [Аппаратное ускорение для производительной работы эмулятора](~/android/get-started/installation/android-emulator/hardware-acceleration.md).


## <a name="walkthrough"></a>Пошаговое руководство

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Запустите Visual Studio.  Щелкните **Файл > Создать > Проект**, чтобы создать проект.

В диалоговом окне **Новый проект** выберите шаблон **Приложение Android**.
Присвойте проекту имя `Phoneword`. Нажмите кнопку **ОК**.

[![Новый проект называется Phoneword](hello-android-quickstart-images/vs/01-new-project-name-w157-sml.png)](hello-android-quickstart-images/vs/01-new-project-name-w157.png#lightbox)

В диалоговом окне **Новое приложение Android** щелкните **Пустое приложение** и нажмите кнопку **ОК**, чтобы создать новый проект.

[![Выбор шаблона "Пустое приложение"](hello-android-quickstart-images/vs/02-blank-app-w157-sml.png)](hello-android-quickstart-images/vs/02-blank-app-w157.png#lightbox)

### <a name="creating-the-layout"></a>Создание макета

После создания проекта разверните папку **Ресурсы** и затем папку **макета** в **обозревателе решений**.
Дважды щелкните файл **activity_main.axml**, чтобы открыть его в Android Designer. Это файл макета для экрана приложения:

[![Открытие файла activity_main.axml](hello-android-quickstart-images/vs/04-open-layout-sml.png)](hello-android-quickstart-images/vs/04-open-layout.png#lightbox)

На **панели элементов** (область слева) введите `text` в поле поиска и перетащите мини-приложение **Text (Large)** (Крупный текст) в область конструктора (в центре):

[![Добавление мини-приложения крупного текста](hello-android-quickstart-images/vs/04-large-text-sml.png)](hello-android-quickstart-images/vs/04-large-text.png#lightbox)

Выбрав элемент управления **Text (Large)** (Крупный текст) в области конструктора, используйте область **Свойства**, чтобы изменить свойство `text` мини-приложения **Text (Large)** (Крупный текст) на `Enter a Phoneword:`, как показано здесь:

[![Задание свойств крупного текста](hello-android-quickstart-images/vs/05-enter-a-phoneword-sml.png)](hello-android-quickstart-images/vs/05-enter-a-phoneword.png#lightbox)

Перетащите мини-приложение **Обычный текст** из **панели элементов** в область конструктора и поместите его под мини-приложением **Text (Large)** (Крупный текст):

[![Добавление мини-приложения обычного текста](hello-android-quickstart-images/vs/06-plain-text-sml.png)](hello-android-quickstart-images/vs/06-plain-text.png#lightbox)

Выбрав мини-приложение **Обычный текст** в области конструктора, используйте область **Свойства**, чтобы изменить свойство `id` мини-приложения **Обычный текст** на `@+id/PhoneNumberText`, а свойство `text` на `1-855-XAMARIN`:

[![Задание свойств обычного текста](hello-android-quickstart-images/vs/07-add-properties-sml.png)](hello-android-quickstart-images/vs/07-add-properties.png#lightbox)

Перетащите элемент **Кнопка** из **панели элементов** в область конструктора и поместите его под мини-приложением **Обычный текст**:

[![Перетаскивание кнопки преобразования в структуру](hello-android-quickstart-images/vs/08-drag-button-sml.png)](hello-android-quickstart-images/vs/08-drag-button.png#lightbox)

Выбрав элемент **Кнопка** в области конструктора, используйте область **Свойства**, чтобы изменить свойство `id` элемента **Кнопка** на `@+id/TranslateButton`, а свойство `text` на `Translate`:

[![Задание свойств кнопки преобразования](hello-android-quickstart-images/vs/09-translate-button-sml.png)](hello-android-quickstart-images/vs/09-translate-button.png#lightbox)

Перетащите элемент **TextView** из **панели элементов** в область конструктора и поместите его под мини-приложением **Кнопка**. Задайте для свойства `id` элемента **TextView** значение `@+id/TranslatedPhoneWord` и измените `text` на пустую строку:

[![Задание свойств для текстового представления.](hello-android-quickstart-images/vs/10-textview-properties-sml.png)](hello-android-quickstart-images/vs/10-textview-properties.png#lightbox)    

Сохраните изменения, нажав клавиши **CTRL+S**.

### <a name="writing-translation-code"></a>Написание кода преобразования

Следующим шагом является добавление кода для преобразования телефонных номеров из буквенно-цифровых в цифровые. Добавьте новый файл в проект, щелкнув правой кнопкой мыши проект **Phoneword** в **обозревателе решений** и выбрав **Добавить > Новый элемент...**, как показано ниже:

[![Добавление нового элемента](hello-android-quickstart-images/vs/12-add-new-item-sml.png)](hello-android-quickstart-images/vs/12-add-new-item.png#lightbox)

В диалоговом окне **Добавить новый элемент** выберите **Visual C# > Код > Файл кода** и назовите новый файл кода **PhoneTranslator.cs**:

[![Добавление PhoneTranslator.cs](hello-android-quickstart-images/vs/14-add-class-sml-w157.png)](hello-android-quickstart-images/vs/14-add-class-w157.png#lightbox)

Создается пустой класс C#. Вставьте в этот файл следующий код:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Сохраните изменения в **PhoneTranslator.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **CTRL+S**), а затем закройте файл.

### <a name="wiring-up-the-interface"></a>Подключение интерфейса

Следующим шагом является добавление кода для подключения пользовательского интерфейса путем вставки вспомогательного кода в класс `MainActivity`. Начните с подключения кнопки **Translate** (Преобразование). В классе `MainActivity` найдите метод `OnCreate`. Следующим шагом является добавление кода кнопки в `OnCreate`, под вызовами `base.OnCreate(bundle)` и `SetContentView
(Resource.Layout.Main)`. Сначала измените код шаблона, чтобы метод `OnCreate` принял следующий вид:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // New code will go here
        }
    }
}
```

Получите ссылку на элементы управления, созданные в файле макета с помощью Android Designer. Добавьте следующий код внутрь метода `OnCreate` после вызова `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Добавьте код, который реагирует на нажатие пользователем кнопки **Translate** (Преобразовать).
Добавьте следующий код в метод `OnCreate` (после строк, добавленных на предыдущем шаге):

```csharp
// Add code to translate number
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    string translatedNumber = Core.PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Сохраните результаты работы, выбрав **Файл > Сохранить все** (или нажав клавиши **CTRL+SHIFT+S**), и выполните сборку приложения, выбрав **Сборка > Перестроить решение** (или нажав клавиши **CTRL+SHIFT+B**). 

При наличии ошибок просмотрите предыдущие шаги и исправьте все ошибки, пока сборка приложения не будет проходить успешно. Если возникает ошибка сборки, например _Resource does not exist in the current context_ (Ресурс не существует в текущем контексте), убедитесь, что имя пространства имен в **MainActivity.cs** совпадает с именем проекта (`Phoneword`), после чего полностью перестройте решение. Если по-прежнему возникают ошибки сборки, убедитесь, что установлены последние обновления Xamarin.Android.

### <a name="setting-the-label-and-app-icon"></a>Настройка метки и значка приложения

Теперь, когда вы получили работающее приложение, &ndash; пора внести последние штрихи. В **MainActivity.cs** измените `Label` для `MainActivity`. `Label` — это то, что Android отображает в верхней части экрана, чтобы пользователи знали, что они находятся в приложении.
В начале класса `MainActivity` измените `Label` на `Phone Word`, как показано ниже:

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

Пришло время задать значок приложения. По умолчанию Visual Studio предоставляет для проекта значок по умолчанию. Давайте удалим эти файлы из решения и заменим их другим значком. Разверните папку **Ресурсы** на **Панели решения**. Обратите внимание, что имеется 5 папок, которые начинаются с префикса **mipmap-**, и каждая из них содержит один файл **Icon.png**:

[![Папки mipmap- и файлы Icon.png](hello-android-quickstart-images/vs/21-mipmap-folders-sml.png)](hello-android-quickstart-images/vs/21-mipmap-folders.png#lightbox)

Нужно удалить каждый из этих файлов значков из проекта. Щелкните правой кнопкой мыши каждый из файлов **Icon.png** и выберите пункт **Удалить** в контекстном меню:
   
[![Удаление файла Icon.png по умолчанию](hello-android-quickstart-images/vs/21-delete-icon-sml.png)](hello-android-quickstart-images/vs/21-delete-icon.png#lightbox)
   
Нажмите кнопку **Удалить** в диалоговом окне.

Затем скачайте и распакуйте [набор значков приложения Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Этот ZIP-файл содержит значки для приложения. Все значки выглядят одинаково, но имеют разное разрешение и правильно отображаются на разных устройствах с различной плотностью экрана.  Этот набор файлов нужно скопировать в проект Xamarin.Android. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши папку **mipmap-hdpi** и выберите **Добавить > Существующие элементы**:

[![Добавление файлов](hello-android-quickstart-images/vs/22-add-files-sml.png)](hello-android-quickstart-images/vs/22-add-files.png#lightbox)

Из диалогового окна выбора перейдите в каталог с распакованными значками Xamarin AdApp и откройте папку **mipmap-hdpi**. Выберите **Icon.png** и нажмите кнопку **Добавить**.

Повторите эти шаги для каждой папки **mipmap-**, пока не скопируете содержимое папок **mipmap-** со значками приложения Xamarin в аналогичные папки **mipmap-** в проекте **Phoneword**.

Скопировав все значки в проект Xamarin.Android, откройте диалоговое окно **Параметры проекта**, щелкнув правой кнопкой мыши проект на **Панели решения**. Выберите **Сборка > Приложение Android** и выберите **@mipmap/icon** в поле со списком **Значок приложения**:

[![Задание значка проекта](hello-android-quickstart-images/vs/25-set-project-icon-sml.png)](hello-android-quickstart-images/vs/25-set-project-icon.png#lightbox)

### <a name="running-the-app"></a>Запуск приложения

Наконец, протестируйте приложение, запустив его на устройстве или эмуляторе Android и преобразовав слово-номер:

[![Снимок экрана готового приложения](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Запустите Visual Studio для Mac из папки **Applications** (Приложения) или из **Spotlight**. 

Щелкните **Создать решение...**, чтобы создать проект.

В диалоговом окне **Выберите шаблон из нового проекта** щелкните **Android > Приложение** и выберите шаблон **Приложение Android**. Нажмите кнопку **Далее**.

[![Выбор шаблона приложения Android](hello-android-quickstart-images/xs/03-choose-template-sml.png)](hello-android-quickstart-images/xs/03-choose-template.png#lightbox)

В диалоговом окне **Настройте приложение Android** назовите новое приложение `Phoneword` и нажмите кнопку **Далее**.

[![Настройка приложения Android](hello-android-quickstart-images/xs/04-configure-android-app-sml.png)](hello-android-quickstart-images/xs/04-configure-android-app.png#lightbox)

В диалоговом окне **Настройте приложение Android** оставьте имена проекта и решения в виде `Phoneword` и нажмите кнопку **Создать** для создания проекта.

### <a name="creating-the-layout"></a>Создание макета

После создания проекта разверните папку **Ресурсы** и затем папку **макета** на **Панели решения**.
Дважды щелкните **Main.axml**, чтобы открыть его в Android Designer. Это файл макета для экрана при просмотре в Android Designer:

[![Открытие Main.axml](hello-android-quickstart-images/xs/05-open-layout-sml.png)](hello-android-quickstart-images/xs/05-open-layout.png#lightbox)

Выберите **Hello World, Click Me!** (элемент **Кнопка**) в области конструктора и нажмите клавишу **DELETE**, чтобы удалить его. 

На **панели элементов** (область справа) введите `text` в поле поиска и перетащите мини-приложение **Text (Large)** (Крупный текст) в область конструктора (в центре):

[![Добавление мини-приложения крупного текста](hello-android-quickstart-images/xs/06-large-text-sml.png)](hello-android-quickstart-images/xs/06-large-text.png#lightbox)

Выбрав мини-приложение **Text (Large)** (Крупный текст) в области конструктора, используйте область **Свойства**, чтобы изменить свойство `Text` мини-приложения **Text (Large)** (Крупный текст) на `Enter a Phoneword:`, как показано ниже:

[![Задание свойств мини-приложения крупного текста](hello-android-quickstart-images/xs/07-enter-a-phoneword-sml.png)](hello-android-quickstart-images/xs/07-enter-a-phoneword.png#lightbox)

После этого перетащите мини-приложение **Обычный текст** из **панели элементов** в область конструктора и поместите его под мини-приложением **Text (Large)** (Крупный текст). Обратите внимание, что можно использовать поле поиска для поиска мини-приложений по имени:

[![Добавление мини-приложения обычного текста](hello-android-quickstart-images/xs/08-plain-text-sml.png)](hello-android-quickstart-images/xs/08-plain-text.png#lightbox)

Выбрав мини-приложение **Обычный текст** в области конструктора, используйте область **Свойства**, чтобы изменить свойство `Id` мини-приложения **Обычный текст** на `@+id/PhoneNumberText`, а свойство `Text` на `1-855-XAMARIN`:

[![Задание свойств мини-приложения обычного текста](hello-android-quickstart-images/xs/09-add-properties-sml.png)](hello-android-quickstart-images/xs/09-add-properties.png#lightbox)

Перетащите элемент **Кнопка** из **панели элементов** в область конструктора и поместите его под мини-приложением **Обычный текст**:

[![Добавление кнопки](hello-android-quickstart-images/xs/10-drag-button-sml.png)](hello-android-quickstart-images/xs/10-drag-button.png#lightbox)

Выбрав элемент **Кнопка** в области конструктора, используйте область **Свойства**, чтобы изменить свойство `Id` элемента **Кнопка** на `@+id/TranslateButton`, а свойство `Text` на `Translate`:

[![Настройка в качестве кнопки преобразования](hello-android-quickstart-images/xs/11-translate-button-sml.png)](hello-android-quickstart-images/xs/11-translate-button.png#lightbox)

Перетащите элемент **TextView** из **панели элементов** в область конструктора и поместите его под мини-приложением **Кнопка**. Выбрав **TextView,** задайте для свойства `id` элемента **TextView** значение `@+id/TranslatedPhoneWord` и измените `text` на пустую строку:

[![Задание свойств для текстового представления.](hello-android-quickstart-images/xs/12-textview-properties-sml.png)](hello-android-quickstart-images/xs/12-textview-properties.png#lightbox)    

Сохраните изменения, нажав клавиши **&#8984;+S**.

### <a name="writing-translation-code"></a>Написание кода преобразования

Теперь добавьте код для преобразования телефонных номеров из буквенно-цифровых в цифровые. Добавьте новый файл в проект, щелкнув значок шестеренки рядом с проектом **Phoneword** на **Панели решения** и выбрав **Добавить > Новый файл...**:

[![Добавление нового файла в проект](hello-android-quickstart-images/xs/14-add-new-file-sml.png)](hello-android-quickstart-images/xs/14-add-new-file.png#lightbox)

В диалоговом окне **Новый файл** выберите **Общие > Пустой класс**, присвойте новому файлу имя **PhoneTranslator** и нажмите кнопку **Создать**. Создается пустой класс C#.

Удалите весь код шаблона в новом классе и замените его следующим:

```csharp
using System.Text;
using System;
namespace Core
{
    public static class PhonewordTranslator
    {
        public static string ToNumber(string raw)
        {
            if (string.IsNullOrWhiteSpace(raw))
                return "";
            else
                raw = raw.ToUpperInvariant();

            var newNumber = new StringBuilder();
            foreach (var c in raw)
            {
                if (" -0123456789".Contains(c))
                {
                    newNumber.Append(c);
                }
                else
                {
                    var result = TranslateToNumber(c);
                    if (result != null)
                        newNumber.Append(result);
                }
                // otherwise we've skipped a non-numeric char
            }
            return newNumber.ToString();
        }
        static bool Contains (this string keyString, char c)
        {
            return keyString.IndexOf(c) >= 0;
        }
        static int? TranslateToNumber(char c)
        {
            if ("ABC".Contains(c))
                return 2;
            else if ("DEF".Contains(c))
                return 3;
            else if ("GHI".Contains(c))
                return 4;
            else if ("JKL".Contains(c))
                return 5;
            else if ("MNO".Contains(c))
                return 6;
            else if ("PQRS".Contains(c))
                return 7;
            else if ("TUV".Contains(c))
                return 8;
            else if ("WXYZ".Contains(c))
                return 9;
            return null;
        }
    }
}
```

Сохраните изменения в **PhoneTranslator.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **&#8984;+S**), а затем закройте файл. Убедитесь, что ошибки времени компиляции отсутствуют, перестроив решение.

### <a name="wiring-up-the-interface"></a>Подключение интерфейса

Следующим шагом является добавление кода для подключения пользовательского интерфейса путем добавления вспомогательного кода в класс `MainActivity`.
Дважды щелкните **MainActivity.cs** на **Панели решения**, чтобы открыть его.

Сначала добавьте обработчик событий для кнопки **Translate** (Преобразование). В классе `MainActivity` найдите метод `OnCreate`. Добавьте код кнопки в `OnCreate`, под вызовами `base.OnCreate(bundle)` и `SetContentView (Resource.Layout.Main)`. Удалите любой имеющийся код для обработки кнопок (т. е. код, который ссылается на `Resource.Id.myButton` и создает соответствующий обработчик щелчка), чтобы метод `OnCreate` принял следующий вид:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;

namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Our code will go here
        }
    }
}
```

После этого нужно получить ссылку на элементы управления, созданные в файле макета с помощью Android Designer. Добавьте следующий код внутрь метода `OnCreate` после вызова `SetContentView`:

```csharp
// Get our UI controls from the loaded layout
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
```

Добавьте код, который реагирует на нажатие пользователем кнопки **Translate** (Преобразовать), добавив следующий код в метод `OnCreate` (после строк, добавленных на предыдущем шаге):

```csharp
// Add code to translate number
string translatedNumber = string.Empty;

translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

Сохраните свою работу и выполните сборку приложения, выбрав **Сборка > Собрать все** (или нажав клавиши **&#8984;+B**). Если приложение компилируется, в верхней части Visual Studio для Mac отображается сообщение об успешном выполнении:

При наличии ошибок просмотрите предыдущие шаги и исправьте все ошибки, пока сборка приложения не будет проходить успешно. Если возникает ошибка сборки, например _Resource does not exist in the current context_ (Ресурс не существует в текущем контексте), убедитесь, что имя пространства имен в **MainActivity.cs** совпадает с именем проекта (`Phoneword`), после чего полностью перестройте решение. Если по-прежнему возникают ошибки сборки, убедитесь, что установлены последние обновления Xamarin.Android и Visual Studio для Mac.

### <a name="setting-the-label-and-app-icon"></a>Настройка метки и значка приложения

Теперь, когда вы получили работающее приложение, пора внести последние штрихи. Сначала измените `Label` для `MainActivity`.
`Label` — это то, что Android отображает в верхней части экрана, чтобы пользователи знали, что они находятся в приложении. В начале класса `MainActivity` измените `Label` на `Phone Word`, как показано ниже:

```csharp
namespace Phoneword
{
    [Activity (Label = "Phone Word", MainLauncher = true)]
    public class MainActivity : Activity
    {
        ...
    }
}
```

Пришло время задать значок приложения. По умолчанию Visual Studio для Mac предоставляет для проекта значок по умолчанию. Давайте удалим эти файлы из решения и заменим их другим значком. Разверните папку **Ресурсы** на **Панели решения**. Обратите внимание, что имеется 5 папок, которые начинаются с префикса **mipmap-**, и каждая из них содержит один файл **Icon.png**:

[![Папки mipmap- и файлы Icon.png](hello-android-quickstart-images/xs/23-mipmap-folders-sml.png)](hello-android-quickstart-images/xs/23-mipmap-folders.png#lightbox)

Нужно удалить каждый из этих файлов значков из проекта. Щелкните правой кнопкой мыши каждый из файлов **Icon.png** и выберите пункт **Удалить** в контекстном меню:

[![Удаление файла Icon.png по умолчанию](hello-android-quickstart-images/xs/23-delete-icon-sml.png)](hello-android-quickstart-images/xs/23-delete-icon.png#lightbox)

Нажмите кнопку **Удалить** в диалоговом окне.

Затем скачайте и распакуйте [набор значков приложения Xamarin](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true). Этот ZIP-файл содержит значки для приложения. Все значки выглядят одинаково, но имеют разное разрешение и правильно отображаются на разных устройствах с различной плотностью экрана.  Этот набор файлов нужно скопировать в проект Xamarin.Android. На **Панели решения** Visual Studio для Mac щелкните правой кнопкой мыши папку **mipmap-hdpi** и выберите **Добавить > Добавить файлы**:

[![Добавление файлов](hello-android-quickstart-images/xs/24-add-files-sml.png)](hello-android-quickstart-images/xs/24-add-files.png#lightbox)

Из диалогового окна выбора перейдите в каталог с распакованными значками Xamarin AdApp и откройте папку **mipmap-hdpi**. Выберите **Icon.png** и нажмите кнопку **Открыть**.

В диалоговом окне **Добавить файл в папку** выберите **Copy the file into the directory** (Копировать файл в каталог) и нажмите кнопку **ОК**:

[![Диалоговое окно "Copy the file to the directory" (Копировать файл в каталог)](hello-android-quickstart-images/xs/26-copy-to-directory-sml.png)](hello-android-quickstart-images/xs/26-copy-to-directory.png#lightbox)

Повторите эти шаги для каждой папки **mipmap-**, пока не скопируете содержимое папок **mipmap-** со значками приложения Xamarin в аналогичные папки **mipmap-** в проекте **Phoneword**.

Скопировав все значки в проект Xamarin.Android, откройте диалоговое окно **Параметры проекта**, щелкнув правой кнопкой мыши проект на **Панели решения**. Выберите **Сборка > Приложение Android** и выберите **@mipmap/icon** в поле со списком **Значок приложения**:

[![Задание значка проекта](hello-android-quickstart-images/xs/28-set-project-icon-sml.png)](hello-android-quickstart-images/xs/28-set-project-icon.png#lightbox)

### <a name="running-the-app"></a>Запуск приложения

Наконец, протестируйте приложение, запустив его на устройстве или эмуляторе Android и преобразовав слово-номер:

[![Снимок экрана готового приложения](hello-android-quickstart-images/intro-app-examples-sml.png)](hello-android-quickstart-images/intro-app-examples.png#lightbox)

-----

Поздравляем! Вы создали свое первое приложение Xamarin.Android!
Пришло время закрепить и углубить приобретенные знания и навыки. Следующий раздел [Привет, Android: теперь подробнее](~/android/get-started/hello-android/hello-android-deepdive.md).


## <a name="related-links"></a>Связанные ссылки

- [Значки приложения Xamarin Android (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (пример)](https://developer.xamarin.com/samples/monodroid/Phoneword)
