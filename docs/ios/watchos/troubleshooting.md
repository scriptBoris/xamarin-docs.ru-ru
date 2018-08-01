---
title: watchOS Устранение неполадок
description: В этом документе рассматриваются известные проблемы и решения для разработки watchOS с помощью Xamarin. Он описывает изображений с проблем, вручную добавлять файлы контроллер интерфейса, запустив приложение watch из командной строки и многое другое.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4e84028336669738c40da9e37cd22f32ba11dfc1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791769"
---
# <a name="watchos-troubleshooting"></a>watchOS Устранение неполадок

Эта страница содержит дополнительные сведения и решения для функции находится в разработке. Некоторые из этих способов применяются только к выпускам предварительного просмотра.

- [Известные проблемы](#knownissues)

- [Удаление альфа-канала из изображений значков](#noalpha)

- [Ручное добавление файлов контроллера интерфейса](#add) для Xcode Interface Builder.

- [Запуск WatchApp из командной строки](#command_line)

<a name="knownissues" />

## <a name="known-issues"></a>Известные проблемы

### <a name="general"></a>Общие

<a name="deploy" />

- Более ранних версиях Visual Studio для Mac неправильно отображается одно из **AppleCompanionSettings** значки, как если бы 88 x 88 точек; что приводит к **отсутствует значок ошибки** при попытке отправить в приложение Хранилище.
    Этот значок должны иметь размер 87 x 87 (29 единицы измерения для **@3x** Сетчатка экраны). Не удается устранить эту проблему в Visual Studio для Mac - либо изменение ресурса изображения в Xcode или вручную изменить **Contents.json** файла (для соответствия [в этом примере](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Если проект расширения Контрольные значения **Info.plist > идентификатор пакета WKApp** не [правильно установить](~/ios/watchos/get-started/project-references.md) для сопоставления приложения Контрольные значения **идентификатор пакета**, отладчик не удастся подключиться и Visual Будет ожидать Studio для Mac с сообщением *«Ожидание подключения отладчика»*.

- Отладка поддерживается в **уведомления** режиме, но может быть ненадежной. Повторная попытка иногда будет работать. Убедитесь, что приложение Watch **Info.plist** `WKCompanionAppBundleIdentifier` соответствует идентификатор пакета, родительского или контейнер приложения iOS (т. е., выполняется на iPhone).

- Конструктор iOS не содержит entrypoint стрелок для обзора или уведомления интерфейса контроллеров.

- Не удается добавить два `WKNotificationControllers` раскадровки.
    Обходной путь: `notificationCategory` всегда вставляется элемент в раскадровке XML с тем же `id`. Чтобы обойти эту проблему, можно добавить контроллеры уведомления два (или более), откройте файл раскадровки в текстовом редакторе и вручную изменить `id` элемент быть уникальным.

    [![](troubleshooting-images/duplicate-id-sml.png "Открытие раскадровки файл в текстовом редакторе и вручную изменить как уникальный идентификатор элемента")](troubleshooting-images/duplicate-id.png#lightbox)

- Может появится сообщение об ошибке «приложение не была построена» при попытке запустить приложение. Это происходит после **Очистить** Если имеет значение запускаемого проекта в проект расширения контрольных значений.
    Исправление заключается в выборе **сборки > перестроить все** и повторном запуске приложения.

### <a name="visual-studio"></a>Visual Studio

Поддержка iOS конструктор для комплекта средств для наблюдения за *требует* решения настроены правильно. Если ссылки проекта не заданы (в разделе [как задать ссылки](~/ios/watchos/get-started/project-references.md)) рабочей области конструирования не будет работать правильно.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Удаление альфа-канала из изображения значков

Значки не должны содержать альфа-канала (альфа-канал определяет прозрачные области изображения), в противном случае будут отклонены приложения во время отправки в магазин приложений с сообщением об ошибке следующего вида:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Можно легко удалить альфа-канал в Mac OS X с помощью **предварительного просмотра** приложения:

1. Открыть изображение значка в **предварительного просмотра** и выберите **файл > Экспорт**.

2. Появление диалогового окна будет включать **альфа-канал** флажок, если присутствует альфа-канала.

    ![](troubleshooting-images/remove-alpha-sml.png "Появление диалогового окна будет включать альфа флажок, если присутствует альфа-канала")

3. *Untick* **альфа-канал** флажок и **Сохранить** файл в нужном месте.

4. Изображение значка теперь следует передавать проверки компании Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Вручную добавлять файлы контроллер интерфейса

> [!IMPORTANT]
> Поддержка Xamarin WatchKit включает в себя проектирование раскадровки Контрольные значения в конструкторе операций ввода-вывода (в Visual Studio для Mac и Visual Studio), не требующий следующие действия. Просто присвойте контроллеру интерфейса имя класса в Visual Studio для свойства Mac планшета и файлы кода C# создается автоматически.


*Если* использовании построителя интерфейс Xcode, выполните следующие действия для создания новых контроллеров интерфейс для наблюдения за приложения и включите синхронизацию с Xcode, выходов и действия, доступные в C#:

1. Откройте приложение watch **Interface.storyboard** в **Xcode интерфейс построителя**.
    
    ![](troubleshooting-images/add-6.png "Открытие раскадровки в построителе интерфейс Xcode")

2. Перетащите новую `InterfaceController` на раскадровку:

    ![](troubleshooting-images/add-1.png "InterfaceController")

3. Теперь можно перетаскивать элементы управления на контроллер интерфейса (например) подписи и кнопки), но нельзя создать розетки или действия еще, так как не **.h** файл заголовка. Следующие шаги вызовет необходимая **.h** создания файла заголовка.

    ![](troubleshooting-images/add-2.png "Кнопки макета")

4. Закройте раскадровку и вернуться в Visual Studio для Mac. Создайте новый файл C# **MyInterfaceController.cs** (или любое имя по своему усмотрению) в **смотреть расширения приложения** проекта (не Контрольное значение само приложение где раскадровки). Добавьте следующий код (обновление пространства имен, classname и имени конструктора):

        using System;
        using WatchKit;
        using Foundation;
        
        namespace WatchAppExtension  // remember to update this
        {
            public partial class MyInterfaceController // remember to update this
            : WKInterfaceController
            {
                public MyInterfaceController // remember to update this
                (IntPtr handle) : base (handle)
                {
                }
                public override void Awake (NSObject context)
                {
                    base.Awake (context);
                    // Configure interface objects here.
                    Console.WriteLine ("{0} awake with context", this);
                }
                public override void WillActivate ()
                {
                    // This method is called when the watch view controller is about to be visible to the user.
                    Console.WriteLine ("{0} will activate", this);
                }
                public override void DidDeactivate ()
                {
                    // This method is called when the watch view controller is no longer visible to the user.
                    Console.WriteLine ("{0} did deactivate", this);
                }
            }
        }

5. Создайте еще один новый файл C# **MyInterfaceController.designer.cs** в **смотреть расширения приложения** проекта и добавьте приведенный ниже код. Не забудьте обновить пространство имен, classname и `Register` атрибута:

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;
    
    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```
    
    Совет: Можно (необязательно) сделать этот файл является дочерним узлом первый файл, перетащив его в другой файл C# в Visual Studio для Mac планшета решения. Затем он будет отображаться следующим образом:
    
    ![](troubleshooting-images/add-5.png "Панель решения")

6. Выберите **сборки > собрать все** , чтобы Xcode синхронизации распознает новый класс (через `Register` атрибут), мы использовали.

7. Повторно открыть раскадровку, щелкнув файл раскадровки приложения контрольных значений и выбрав **открыть с помощью > Xcode интерфейс построителя**:

    ![](troubleshooting-images/add-6.png "Открытие раскадровки в построителе интерфейса")

8. Выберите новый контроллер интерфейса и присвойте ему classname, определенного выше, например. `MyInterfaceController`.
Если все работал правильно, она должна отображаться автоматически в **класса:** раскрывающийся список и можно выбрать его оттуда.

    ![](troubleshooting-images/add-4.png "Пользовательский класс параметров")

9. Выберите **помощника редактор** открыть в Xcode (значок с двумя круга), можно увидеть раскадровки и код side-by-side:

    ![](troubleshooting-images/add-7.png "Элемент панели инструментов редактора помощника")

    Если фокус находится в области кода, обеспечения взглянуть на **.h** файл заголовка и если не щелкните правой кнопкой мыши иерархическую панель и выберите нужный файл (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Выберите MyInterfaceController")

10. Теперь можно создать выходов и действия по **Ctrl + перетащите** из раскадровки в **.h** файл заголовка.

    ![](troubleshooting-images/add-9.png "Создание выходов и действий")

    При освобождении перетаскивание, будет предложено выбрать, следует ли создать розетки или действия и выберите его имя.

    ![](troubleshooting-images/add-a.png "Розетки и диалоговое окно действий")

11. После сохранения изменений раскадровки Xcode закрыт, вернитесь в Visual Studio для Mac. Он обнаружит изменения файла заголовка и автоматически добавлять код **. designer.cs** файла:


        [Register ("MyInterfaceController")]
        partial class MyInterfaceController
        {
            [Outlet]
            WatchKit.WKInterfaceButton myButton { get; set; }
        
            void ReleaseDesignerOutlets ()
            {
                if (myButton != null) {
                    myButton.Dispose ();
                    myButton = null;
                }
            }
        }


Теперь можно ссылаться на элемент управления (или выполнения действий) в C#!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Запустив приложение Контрольные значения из командной строки

> [!IMPORTANT]
> Можно запустить приложение Watch в режиме обычного приложения по умолчанию, а также в **Обзор** или **уведомления** режимы с использованием [выполнения пользовательских параметров](~/ios/watchos/get-started/installation.md#custommodes) в Visual Studio для Mac и Visual Studio.


Также можно использовать из командной строки для управления симулятор iOS. — Средство командной строки, используемые для запуска наблюдения за приложениями **mtouch**.

Ниже приведен полный пример (выполняется в виде одной строки в окне терминала).

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

Параметр, необходимо выполнить обновление приложения `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

Полный путь к пакету основным приложением *для приложения iOS, которое содержит приложение watch и расширение*.

> [!NOTE]
> Необходимо ввести путь является по *App-файл приложения iPhone*, т. е. один, будут развернуты на симуляторе iOS, которая содержит приложение watch и расширения контрольных значений.

Пример

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Режим уведомлений

Тестирование приложения [ **уведомления** режим](~/ios/watchos/platform/notifications.md), задайте `watchlaunchmode` параметр `Notification` и укажите путь к JSON-файл, содержащий полезные данные уведомления теста.

Параметр полезных данных *необходимые* для режим уведомления.

Например добавьте следующие аргументы для команды mtouch.

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Остальные аргументы

Далее описаны остальные аргументы.

### <a name="--sdkroot"></a>--sdkroot

Обязательно. Указывает путь к Xcode (6.2 или более поздней версии).

Пример

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--устройства

Имитатор устройство для выполнения. Здесь можно указать двумя способами: с помощью udid конкретного устройства, или используя сочетание среды выполнения и тип устройства.

Точные значения и зависит от между машинами, можно запросить, используя Apple **simctl** средство:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Пример

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Время выполнения и тип устройства**

Пример

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
