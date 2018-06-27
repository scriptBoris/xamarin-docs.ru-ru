---
title: Краткое руководство по работе с приложением Xamarin.Forms с несколькими экранами
description: Эта статья описывает расширение приложения Phoneword путем добавления второго экрана для отслеживания журнала вызовов приложения.
ms.prod: quickstart
ms.assetid: 255d93b9-518c-4e5d-a9cd-4dd8a7945a7f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/06/2018
ms.openlocfilehash: c931b4f74fbfbbb7396e492cb7ad7ae5d0097bad
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242395"
---
# <a name="xamarinforms-multiscreen-quickstart"></a>Краткое руководство по работе с приложением Xamarin.Forms с несколькими экранами

В этом кратком руководстве демонстрируется расширение приложения Phoneword путем добавления второго экрана для отслеживания журнала вызовов приложения. Ниже показано итоговое приложение:

[![](quickstart-images/intro-app-examples-sml.png "Приложение Phoneword")](quickstart-images/intro-app-examples.png#lightbox "Приложение Phoneword")

Вы можете расширить приложение Phoneword указанным ниже образом:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Запустите Visual Studio. На начальной странице щелкните **Открыть проект**, а затем в диалоговом окне **Открытие проекта** выберите файл решения проекта Phoneword:

    ![](quickstart-images/vs/open-solution.png "Открытие проекта")

2. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый элемент...**:

    ![](quickstart-images/vs/add-new-item.png "Добавление нового элемента")

3. В диалоговом окне **Добавление нового элемента** выберите **Элементы Visual C# > Xamarin.Forms > Страница содержимого**, присвойте новому файлу имя **CallHistoryPage** и нажмите кнопку **Добавить**. При этом в проект добавляется страница **CallHistoryPage**:

    ![](quickstart-images/vs/add-callhistorypage-class.png "Шаблоны проекта Xamarin.Forms")

4. Удалите в файле **CallHistoryPage.xaml** весь код шаблона и замените его приведенным ниже. Этот код декларативно определяет пользовательский интерфейс для страницы:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>
    ```

    Сохраните изменения в файле **CallHistoryPage.xaml**, нажав клавиши **CTRL+S**, и закройте файл.

5. В **обозревателе решений** дважды щелкните файл **App.xaml.cs** в общем проекте **Phoneword**, чтобы открыть его:

    ![](quickstart-images/vs/open-app-class.png "Открытие файла App.xaml.cs")

6. В файле **App.xaml.cs** импортируйте пространство имен `System.Collections.Generic`, добавьте объявление свойства `PhoneNumbers`, инициализируйте это свойство в конструкторе `App` и инициализируйте свойство [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) значением [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). Коллекция `PhoneNumbers` будет использоваться для хранения списка всех преобразованных номеров телефона, вызываемых из приложения:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    Сохраните изменения в файле **App.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

7. В **обозревателе решений** дважды щелкните файл **MainPage.xaml** в общем проекте **Phoneword**, чтобы открыть его:

    ![](quickstart-images/vs/open-mainpage-xaml.png "Открытие файла MainPage.xaml")

8. В файле **MainPage.xaml** добавьте элемент управления [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) в конце элемента управления [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Эта кнопка будет использоваться для перехода по странице журнала вызовов:

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    Сохраните изменения в файле **MainPage.xaml**, нажав клавиши **CTRL+S**, и закройте файл.

9. В **обозревателе решений** дважды щелкните файл **MainPage.xaml**, чтобы его открыть:

    ![](quickstart-images/vs/open-mainpage-codebehind.png "Открытие файла MainPage.xaml.cs")

10. В файле **MainPage.xaml.cs** добавьте метод обработчика событий `OnCallHistory`, а затем измените метод обработчика событий `OnCall` для добавления преобразованного номера телефона в коллекцию `App.PhoneNumbers` и включения `callHistoryButton` при условии, что переменная `dialer` имеет значение, отличное от `null`.

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    Сохраните изменения в файле **MainPage.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

11. В Visual Studio выберите элемент меню **Сборка > Построить решение** (или нажмите клавиши **CTRL+SHIFT+B**). Выполняется сборка приложения, а в строке состояния Visual Studio отображается сообщение об успешном выполнении:

    ![](quickstart-images/vs/build-successful.png "Успешное выполнение сборки")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка приложения не будет проходить успешно.

12. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку "Воспроизвести") для запуска приложения:

    ![](quickstart-images/vs/start.png "Панель инструментов Visual Studio")
    ![](quickstart-images/vs/phone-result-uwp.png "Приложение UWP Phoneword")

13. В **обозревателе решений** щелкните правой кнопкой мыши проект **Phoneword.Droid** и выберите **Назначить запускаемым проектом**.
14. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения), чтобы запустить приложение в эмуляторе Android.
15. Если у вас есть устройство iOS, которое удовлетворяет системным требованиям к Mac для разработки Xamarin.Forms, используйте аналогичную методику для развертывания приложения на устройстве iOS. Также вы можете развернуть приложение в [удаленном симуляторе iOS](~/tools/ios-simulator.md).

    Примечание. Телефонные звонки поддерживаются не во всех симуляторах.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Запуск Visual Studio для Mac На начальной странице щелкните **Открыть**, а затем в диалоговом окне выберите файл решения проекта Phoneword:

    ![](quickstart-images/xs/open-solution.png "Открытие решения")

2. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword** и выберите **Добавить > Новый файл...**:

    ![](quickstart-images/xs/add-new-file.png "Добавление нового файла")

3. В диалоговом окне **Новый файл** выберите **Forms > XAML ContentPage Forms**, назовите новый файл **CallHistoryPage** и нажмите кнопку **Создать**. При этом в проект добавляется страница **CallHistoryPage**:

    ![](quickstart-images/xs/add-callhistorypage-class.png "Добавление ContentPage Forms")

4. На **Панели решения** дважды щелкните файл **CallHistoryPage.xaml**, чтобы открыть его:

    ![](quickstart-images/xs/open-callhistorypage-xaml.png "Открытие файла CallHistoryPage.xaml")

5. Удалите в файле **CallHistoryPage.xaml** весь код шаблона и замените его приведенным ниже. Этот код декларативно определяет пользовательский интерфейс для страницы:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>      
    ```

    Сохраните изменения в файле **CallHistoryPage.xaml**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

6. На **Панели решения** дважды щелкните файл **App.xaml.cs**, чтобы открыть его:

    ![](quickstart-images/xs/open-app-class.png "Открытие файла App.xaml.cs")

7. В файле **App.xaml.cs** импортируйте пространство имен `System.Collections.Generic`, добавьте объявление свойства `PhoneNumbers`, инициализируйте это свойство в конструкторе `App` и инициализируйте свойство [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) значением [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). Коллекция `PhoneNumbers` будет использоваться для хранения списка всех преобразованных номеров телефона, вызываемых из приложения:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    Сохраните изменения в файле **App.xaml.cs**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

8. На **Панели решения** дважды щелкните файл **MainPage.xaml**, чтобы открыть его:

    ![](quickstart-images/xs/open-mainpage-xaml.png "Открытие файла MainPage.xaml")

9. В файле **MainPage.xaml** добавьте элемент управления [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) в конце элемента управления [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Эта кнопка будет использоваться для перехода по странице журнала вызовов:

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    Сохраните изменения в **MainPage.xaml**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

10. На **Панели решения** дважды щелкните файл **MainPage.xaml.cs**, чтобы открыть его:

    ![](quickstart-images/xs/open-mainpage-codebehind.png "Открытие файла MainPage.xaml.cs")

11. В файле **MainPage.xaml.cs** добавьте метод обработчика событий `OnCallHistory`, а затем измените метод обработчика событий `OnCall` для добавления преобразованного номера телефона в коллекцию `App.PhoneNumbers` и включения `callHistoryButton` при условии, что переменная `dialer` имеет значение, отличное от `null`.

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    Сохраните изменения в **MainPage.xaml.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **&#8984; + S**), а затем закройте файл.

12. В Visual Studio для Mac выберите элемент меню **Сборка > Собрать все** (или нажмите клавиши **&#8984;+B**). Выполняется сборка приложения, а на панели инструментов Visual Studio для Mac отображается сообщение об успешном выполнении:

    ![](quickstart-images/xs/build-successful.png "Успешное выполнение сборки")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка приложения не будет проходить успешно.

13. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку "Воспроизвести") для запуска приложения в симуляторе iOS:

    ![](quickstart-images/xs/start.png "Панель инструментов Visual Studio для Mac")
    ![](quickstart-images/xs/phone-result-ios.png "Симулятор iOS")

    Примечание. Телефонные звонки в симуляторе iOS не поддерживаются.

14. На **Панели решения** щелкните правой кнопкой мыши проект **Phoneword.Droid** и выберите **Назначить запускаемым проектом**:

    ![](quickstart-images/xs/set-startup-project.png "Назначение запускаемым проектом")

15. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку "Воспроизвести") для запуска приложения в эмуляторе Android:

    ![](quickstart-images/xs/phone-result-android.png "Эмулятор Android")

    Примечание. Телефонные звонки в эмуляторах Android не поддерживаются.

-----

Поздравляем! Вы создали приложение Xamarin.Forms с несколькими экранами. В [следующем разделе](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/deepdive.md) этого руководства описываются шаги, предпринятые в данном пошаговом руководстве, чтобы вы могли изучить принципы навигации и привязки данных с помощью Xamarin.Forms.


## <a name="related-links"></a>Связанные ссылки

- [Phoneword (пример)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
- [PhonewordMultiscreen (пример)](https://developer.xamarin.com/samples/xamarin-forms/PhonewordMultiscreen/)
