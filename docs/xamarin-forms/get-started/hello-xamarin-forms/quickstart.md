---
title: Краткое руководство по Xamarin.Forms
description: Эта статья описывает создание приложения, которое преобразует введенный пользователем буквенно-цифровой телефонный номер в числовой телефонный номер и затем набирает его.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2018
ms.openlocfilehash: 7399cab611b726eb7bb72928f504086fb842fb74
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242437"
---
# <a name="xamarinforms-quickstart"></a>Краткое руководство по Xamarin.Forms

Это пошаговое руководство демонстрирует создание приложения, которое преобразует введенный пользователем буквенно-цифровой телефонный номер в числовой телефонный номер и затем набирает его. Ниже показано итоговое приложение:

[![](quickstart-images/intro-app-examples-sml.png "Приложение Phoneword")](quickstart-images/intro-app-examples.png#lightbox "Приложение Phoneword")

Вы можете создать приложение Phoneword следующим образом:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. На **начальном экране** запустите Visual Studio. Открывается начальная страница:

    ![](quickstart-images/vs/start-page.png "Visual Studio")

2. В Visual Studio щелкните **Создать проект...**, чтобы создать проект:

    ![](quickstart-images/vs/new-solution.png "Создание проекта")

3. В диалоговом окне **Новый проект** щелкните **Кроссплатформенный**, выберите шаблон **Мобильное приложение (Xamarin.Forms)**, присвойте параметру "Имя" значение **Phoneword**, выберите подходящее расположение для проекта и нажмите кнопку **ОК**:

    ![](quickstart-images/vs/new-project.w157.png "Шаблоны кроссплатформенных проектов")

    > [!NOTE]
    > Если не присвоить решению имя **Phoneword**, это приведет к многочисленным ошибкам сборки.

4. В диалоговом окне **Новое кроссплатформенное приложение** щелкните **Пустое приложение**, выберите **.NET Standard** в качестве стратегии совместного использования кода, а затем нажмите кнопку **OK**:

    ![](quickstart-images/vs/new-app.png "Новое кроссплатформенное приложение")

5. В **обозревателе решений** дважды щелкните файл **MainPage.xaml** в проекте **Phoneword**, чтобы открыть его:

    ![](quickstart-images/vs/open-mainpage-xaml.png "Открытие файла MainPage.xaml")

6. Удалите в **MainPage.xaml** весь код шаблона и замените его приведенным ниже. Этот код декларативно определяет пользовательский интерфейс для страницы:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    Сохраните изменения в файле **MainPage.xaml**, нажав клавиши **CTRL+S**, и закройте файл.

7. В **обозревателе решений** разверните узел **MainPage.xaml** и дважды щелкните файл **MainPage.xaml**, чтобы открыть его:

    ![](quickstart-images/vs/open-mainpage-codebehind.png "Открытие файла MainPage.xaml.cs")

8. Удалите в **MainPage.xaml.cs** весь код шаблона и замените его приведенным ниже. Методы `OnTranslate` и `OnCall` выполняются при нажатии в пользовательском интерфейсе кнопок **Translate** (Преобразовать) и **Call** (Вызов), соответственно:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, которые будут исправлены позже.

    Сохраните изменения в файле **MainPage.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

9. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый элемент...**:

    ![](quickstart-images/vs/add-new-item.png "Добавление нового элемента")

10. В диалоговом окне **Добавление нового элемента** выберите **Visual C# > Код> Класс**, назовите новый файл **PhoneTranslator** и нажмите кнопку **Добавить**:

    ![](quickstart-images/vs/add-translator-class.w157.png "Добавление нового класса")

11. Удалите в **PhoneTranslator.cs** весь код шаблона и замените его приведенным ниже. Этот код преобразует слово-номер в телефонный номер:

    ```csharp
    using System.Text;

    namespace Phoneword
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    Сохраните изменения в **PhoneTranslator.cs**, нажав клавиши **CTRL+S**, и закройте файл.

12. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый элемент...**:

    ![](quickstart-images/vs/add-new-item.png "Добавление нового элемента")

13. В диалоговом окне **Добавление нового элемента** выберите **Visual C# > Код> Интерфейс**, назовите новый файл **IDialer** и нажмите кнопку **Добавить**:

    ![](quickstart-images/vs/add-idialer-interface.w157.png "Добавление нового интерфейса")

14. Удалите в **IDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код определяет метод `Dial`, который должен быть реализован на каждой платформе, чтобы набрать преобразованный телефонный номер:

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```

    Сохраните изменения в **IDialer.cs**, нажав клавиши **CTRL+S**, и закройте файл.

    > [!NOTE]
    > Итак, общий код для приложения полностью готов. Теперь мы реализуем код набора номера для конкретной платформы в качестве [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

15. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword.iOS** и выберите **Добавить > Новый элемент...**:

    ![](quickstart-images/vs/add-new-item-ios.png "Добавление нового элемента")

16. В диалоговом окне **Добавление нового элемента** выберите **Apple > Код > Класс**, назовите новый файл **PhoneDialer** и нажмите кнопку **Добавить**:

    ![](quickstart-images/vs/new-phone-dialer-ios.w157.png "Добавление нового класса")

17. Удалите в **PhoneDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код создает метод <code>Dial</code>, который будет использоваться платформой iOS для набора преобразованного телефонного номера:

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    Сохраните изменения в **PhoneDialer.cs**, нажав клавиши **CTRL+S**, и закройте файл.

18. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword.Android** и выберите **Добавить > Новый элемент...**:

    ![](quickstart-images/vs/add-new-item-android.png "Добавление нового элемента")

19. В диалоговом окне **Добавление нового элемента** выберите **Visual C# > Android > Класс**, назовите новый файл **PhoneDialer** и нажмите кнопку **Добавить**:

    ![](quickstart-images/vs/new-phone-dialer-android.w157.png "Добавление нового класса")

20. Удалите в **PhoneDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код создает метод `Dial`, который будет использоваться платформой Android для набора преобразованного телефонного номера:

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionDial);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    Обратите внимание: в этом коде предполагается, что вы используете последний API Android. Сохраните изменения в **PhoneDialer.cs**, нажав клавиши **CTRL+S**, и закройте файл.

21. В **обозревателе решений** откройте проект **Phoneword.Android**, двойным щелчком откройте файл **MainActivity.cs**, удалите весь код шаблона и замените его следующим фрагментом кода:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);
                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }
    ```

    Сохраните изменения в файле **MainPage.xaml.cs**, нажав сочетание клавиш **CTRL+S**, и закройте файл.

22. В **обозревателе решений** дважды щелкните **Свойства** в проекте **Phoneword.Android** и выберите вкладку **Манифест Android**:

    ![](quickstart-images/vs/android-manifest.png "Открытие манифеста Android")

23. В разделе **Требуемые разрешения** включите разрешение **CALL_PHONE**. Это позволяет приложению совершить телефонный звонок:

    ![](quickstart-images/vs/android-manifest-changed.png "Включение разрешения CallPhone")

    Сохраните изменения в манифесте, нажав клавиши **CTRL+S**, и закройте файл.

24. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword.UWP** и выберите **Добавить > Новый элемент...**:

    ![](quickstart-images/vs/add-new-item-uwp.png "Добавление нового элемента")

25. В диалоговом окне **Добавление нового элемента** выберите **Visual C# > Код > Класс**, назовите новый файл **PhoneDialer** и нажмите кнопку **Добавить**:

    ![](quickstart-images/vs/new-phone-dialer-uwp.w157.png "Добавление нового класса")

26. Удалите в **PhoneDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код создает метод `Dial` и вспомогательные методы, которые будут использоваться универсальной платформой Windows для набора преобразованного телефонного номера:

    ```csharp
    using Phoneword.UWP;
    using System;
    using System.Threading.Tasks;
    using Windows.ApplicationModel.Calls;
    using Windows.UI.Popups;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.UWP
    {
        public class PhoneDialer : IDialer
        {
            bool dialled = false;

            public bool Dial(string number)
            {
                DialNumber(number);
                return dialled;
            }

            async Task DialNumber(string number)
            {
                var phoneLine = await GetDefaultPhoneLineAsync();
                if (phoneLine != null)
                {
                    phoneLine.Dial(number, number);
                    dialled = true;
                }
                else
                {
                    var dialog = new MessageDialog("No line found to place the call");
                    await dialog.ShowAsync();
                    dialled = false;
                }
            }

            async Task<PhoneLine> GetDefaultPhoneLineAsync()
            {
                var phoneCallStore = await PhoneCallManager.RequestStoreAsync();
                var lineId = await phoneCallStore.GetDefaultLineAsync();
                return await PhoneLine.FromIdAsync(lineId);
            }
        }
    }
    ```

    Сохраните изменения в **PhoneDialer.cs**, нажав клавиши **CTRL+S**, и закройте файл.

27. В **обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** в проекте **Phoneword.UWP** и выберите пункт **Добавить ссылку...**:

    ![](quickstart-images/vs/uwp-add-reference.png "Добавление ссылки")

28. В диалоговом окне **Диспетчер ссылок** выберите **Универсальная платформа Windows > Расширения > Windows Mobile Extensions for UWP** (Мобильные расширения Windows для UWP) и нажмите кнопку **ОК**:

    ![](quickstart-images/vs/uwp-add-reference-extensions.png "Добавление мобильных расширений Windows для UWP")

29. В **обозревателе решений** дважды щелкните файл **Package.appxmanifest** в проекте **Phoneword.UWP**, чтобы открыть его:

    ![](quickstart-images/vs/uwp-manifest.png "Открытие манифеста UWP")

30. На странице **Возможности** включите функцию **Телефонный звонок**. Это позволяет приложению совершить телефонный звонок:

    ![](quickstart-images/vs/uwp-manifest-changed.png "Включение функции \"Телефонный звонок\"")

    Сохраните изменения в манифесте, нажав клавиши **CTRL+S**, и закройте файл.

31. В Visual Studio выберите элемент меню **Сборка > Построить решение** (или нажмите клавиши **CTRL+SHIFT+B**). Выполняется сборка приложения, а в строке состояния Visual Studio отображается сообщение об успешном выполнении:

    ![](quickstart-images/vs/build-successful.png "Успешное выполнение сборки")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка приложения не будет проходить успешно.

32. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword.UWP** и выберите **Назначить запускаемым проектом**:

    ![](quickstart-images/vs/uwp-set-as-startup-project.png "Назначение запускаемым проектом")

33. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку "Воспроизвести") для запуска приложения:

    ![](quickstart-images/vs/start.png "Панель инструментов Visual Studio")
    ![](quickstart-images/vs/phone-result-uwp.png "Приложение UWP Phoneword")

34. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword.Android** и выберите **Назначить запускаемым проектом**.
35. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения), чтобы запустить приложение в эмуляторе Android.
36. Если у вас есть устройство iOS, которое удовлетворяет системным требованиям к Mac для разработки Xamarin.Forms, используйте аналогичную методику для развертывания приложения на устройстве iOS. Также вы можете развернуть приложение в [удаленном симуляторе iOS](~/tools/ios-simulator.md).

    Примечание. Телефонные звонки поддерживаются не во всех симуляторах.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Запустите Visual Studio для Mac и на начальной странице щелкните **Новый проект...** , чтобы создать новый проект:

    ![](quickstart-images/xs/new-solution.png "Создание решения")

2. В диалоговом окне **Выберите шаблон из нового проекта** щелкните **Многоплатформенность > Приложение** и выберите шаблон **Приложение с пустыми формами**, а затем нажмите кнопку **Далее**:

    ![](quickstart-images/xs/choose-template.png "Выбор шаблона")

3. В диалоговом окне **Configure your Blank Forms app** (Настройка приложения с пустыми формами) присвойте новому приложению имя **Phoneword**, убедитесь, что переключатель установлен в положение **Use .NET Standard** (Использовать .NET Standard) и нажмите кнопку **Далее**:

    ![](quickstart-images/xs/configure-app.png "Настройка приложения Forms")

4. В диалоговом окне **Configure your new Blank Forms app** (Настройка нового приложения с пустыми формами) оставьте для проекта и решения имя **Phoneword**, выберите подходящее расположение для проекта и нажмите кнопку **Создать**, чтобы создать проект:

    ![](quickstart-images/xs/configure-project.png "Настройка проекта Forms")

    > [!NOTE]
    > Если не присвоить решению и проекту имя **Phoneword**, это приведет к многочисленным ошибкам сборки.

5. На **Панели решения** дважды щелкните файл **MainPage.xaml**, чтобы открыть его:

    ![](quickstart-images/xs/open-mainpage-xaml.png "Открытие файла MainPage.xaml")

6. Удалите в **MainPage.xaml** весь код шаблона и замените его приведенным ниже. Этот код декларативно определяет пользовательский интерфейс для страницы:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    Сохраните изменения в **MainPage.xaml**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

7. На **Панели решения** дважды щелкните файл **MainPage.xaml.cs**, чтобы открыть его:

    ![](quickstart-images/xs/open-mainpage-codebehind.png "Открытие файла MainPage.xaml.cs")

8. Удалите в **MainPage.xaml.cs** весь код шаблона и замените его приведенным ниже. Методы `OnTranslate` и `OnCall` выполняются при нажатии в пользовательском интерфейсе кнопок **Translate** (Преобразовать) и **Call** (Вызов), соответственно:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, которые будут исправлены позже.

    Сохраните изменения в **MainPage.xaml.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **&#8984; + S**), а затем закройте файл.

9. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый файл...**:

    ![](quickstart-images/xs/add-new-translator-file.png "Добавление нового файла")

10. В диалоговом окне **Новый файл** выберите **Общие > Пустой класс**, присвойте новому файлу имя **PhoneTranslator** и нажмите кнопку **Создать**:

    ![](quickstart-images/xs/add-translator-class.png "Добавление нового класса")

11. Удалите в **PhoneTranslator.cs** весь код шаблона и замените его приведенным ниже. Этот код преобразует слово-номер в телефонный номер:

    ```csharp
    using System.Text;

    namespace Phoneword
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    Сохраните изменения в **PhoneTranslator.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **&#8984; + S**), а затем закройте файл.

12. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый файл...**:

    ![](quickstart-images/xs/add-new-interface.png "Добавление нового файла")

13. В диалоговом окне **Новый файл** выберите **Общие > Пустой интерфейс**, присвойте новому файлу имя **IDialer** и нажмите кнопку **Создать**:

    ![](quickstart-images/xs/add-idialer-interface.png "Добавление нового интерфейса")

14. Удалите в **IDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код определяет метод `Dial`, который должен быть реализован на каждой платформе, чтобы набрать преобразованный телефонный номер:

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```
    Сохраните изменения в **IDialer.cs**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

    > [!NOTE]
    > Итак, общий код для приложения полностью готов. Теперь мы реализуем код набора номера для конкретной платформы в качестве [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

15. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword.iOS** и выберите **Добавить > Новый файл...**:

    ![](quickstart-images/xs/add-new-file-ios.png "Добавление нового файла")

16. В диалоговом окне **Новый файл** выберите **Общие > Пустой класс**, присвойте новому файлу имя **PhoneDialer** и нажмите кнопку **Создать**:

    ![](quickstart-images/xs/new-phonedialer-ios.png "Добавление нового класса")

17. Удалите в **PhoneDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код создает метод `Dial`, который будет использоваться платформой iOS для набора преобразованного телефонного номера:

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    Сохраните изменения в **PhoneDialer.cs**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

18. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword.Droid** и выберите **Добавить > Новый файл...**:

    ![](quickstart-images/xs/add-new-file-android.png "Добавление нового файла")

19. В диалоговом окне **Новый файл** выберите **Общие > Пустой класс**, присвойте новому файлу имя **PhoneDialer** и нажмите кнопку **Создать**:

    ![](quickstart-images/xs/new-phonedialer-android.png "Добавление нового класса")

20. Удалите в **PhoneDialer.cs** весь код шаблона и замените его приведенным ниже. Этот код создает метод `Dial`, который будет использоваться платформой Android для набора преобразованного телефонного номера:

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionDial);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    Обратите внимание: в этом коде предполагается, что вы используете последний API Android. Сохраните изменения в **PhoneDialer.cs**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

21. На **панели решения** откройте проект **Phoneword.Droid**, двойным щелчком откройте файл **MainActivity.cs**, удалите весь код шаблона и замените его следующим фрагментом кода:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true,
                  ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);
                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }
    ```

    Сохраните изменения в **MainPage.xaml.cs**, выбрав **Файл > Сохранить** (или нажав сочетание клавиш **&#8984;+S**), а затем закройте файл.

22. На **Панели решения** разверните папку **Свойства** и дважды щелкните файл **AndroidManifest.xml**:

    ![](quickstart-images/xs/android-manifest.png "Открытие манифеста Android")

23. В разделе **Требуемые разрешения** включите разрешение **CallPhone**. Это позволяет приложению совершить телефонный звонок:

    ![](quickstart-images/xs/android-manifest-changed.png "Включение разрешения CallPhone")

    Сохраните изменения в **AndroidManifest.xml**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

24. В Visual Studio для Mac выберите элемент меню **Сборка > Собрать все** (или нажмите клавиши **&#8984;+B**). Выполняется сборка приложения, а на панели инструментов Visual Studio для Mac отображается сообщение об успешном выполнении.

    ![](quickstart-images/xs/build-successful.png "Успешное выполнение сборки")

25. При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка приложения не будет проходить успешно.
26. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку "Воспроизвести") для запуска приложения в симуляторе iOS:

    ![](quickstart-images/xs/start.png "Панель инструментов Visual Studio для Mac")
    ![](quickstart-images/xs/phoneword-result-ios.png "Симулятор iOS")

    Примечание. Телефонные звонки в симуляторе iOS не поддерживаются.

27. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword.Droid** и выберите **Назначить запускаемым проектом**:

    ![](quickstart-images/xs/set-startup-project.png "Назначение запускаемым проектом")

28. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку "Воспроизвести") для запуска приложения в эмуляторе Android:

    ![](quickstart-images/xs/phoneword-result-android.png "Эмулятор Android")

    Примечание. Телефонные звонки в эмуляторах Android не поддерживаются.

-----

Поздравляем! Вы создали приложение Xamarin.Forms. [Следующий раздел](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md) этого руководства описывает шаги, предпринятые в данном пошаговом руководстве, чтобы вы могли изучить основы разработки приложений с помощью Xamarin.Forms.


## <a name="related-links"></a>Связанные ссылки

- [Доступ к собственным функциям через DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword (пример)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
