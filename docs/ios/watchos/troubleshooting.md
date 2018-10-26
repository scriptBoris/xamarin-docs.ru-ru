---
title: watchOS Устранение неполадок
description: В этом документе рассматриваются известные проблемы и решения для разработки watchOS с помощью Xamarin. В нем описаны образы с проблемами, вручную добавлять файлы интерфейса контроллера, запустив приложение просмотра из командной строки и многое другое.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: feaa0c3547c01490a8ada82b17b4c7a824e03b0b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114656"
---
# <a name="watchos-troubleshooting"></a>watchOS Устранение неполадок

Эта страница содержит дополнительные сведения и обходные пути для функции находится в разработке. Некоторые из этих обходных путей применяются только к нашей предварительных выпусков.

- [Известные проблемы](#knownissues)

- [Удаление альфа-канала из изображений значков](#noalpha)

- [Ручное добавление файлов контроллера интерфейса](#add) для Xcode Interface Builder.

- [Запуск WatchApp из командной строки](#command_line)

<a name="knownissues" />

## <a name="known-issues"></a>Известные проблемы

### <a name="general"></a>Общие

<a name="deploy" />

- Более ранних версиях Visual Studio для Mac неправильно отображается одно из **AppleCompanionSettings** значки как 88 x 88 точек; что приводит к **отсутствует значок ошибки** при попытке отправить в приложение Store.
    Этот значок должен составлять пикселей 87 x 87 (29 единицы измерения для **@3x** Retina экранов). Не удается устранить эту проблему в Visual Studio для Mac - либо изменение ресурса изображения в Xcode или вручную изменить **Contents.json** файла (в соответствии с [в этом примере](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Если проект расширения Watch **Info.plist > идентификатор пакета WKApp** не [соответствующим образом изменить](~/ios/watchos/get-started/project-references.md) в соответствии с приложение Watch **идентификатор пакета**, отладчик не сможет подключиться и Visual Studio для Mac будет ожидать сообщение *«Ожидание подключения отладчика»*.

- Поддерживается отладка в **уведомления** режим, но могут быть ненадежными. Повторная попытка будет иногда работать. Убедитесь, что приложение Watch **Info.plist** `WKCompanionAppBundleIdentifier` соответствует идентификатору пакета приложения iOS родительского или контейнера (т. е., запущенным на iPhone).

- Конструктор iOS не содержит стрелок entrypoint для обзора или уведомления контроллеры интерфейса.

- Не удается добавить два `WKNotificationControllers` раскадровки.
    Инструкции по решению: `notificationCategory` элемент в раскадровке XML всегда вставляется с тем же `id`. Чтобы обойти эту проблему, можно добавить контроллеры уведомлений два (или более), откройте файл раскадровки в текстовом редакторе и вручную изменить `id` элемента должны быть уникальными.

    [![](troubleshooting-images/duplicate-id-sml.png "Открыв раскадровку в текстовом редакторе и вручную измените элемент id должны быть уникальными")](troubleshooting-images/duplicate-id.png#lightbox)

- Может появиться сообщение об ошибке «не было создано приложение» при попытке запустить приложение. Это происходит после **Очистить** если запускаемый проект присвоено проект расширения контрольных значений.
    Исправление заключается в выберите **сборки > перестроить все** и затем повторно запустить приложение.

### <a name="visual-studio"></a>Visual Studio

Конструктор iOS поддерживают набора контрольных значений для *требует* решение настроены правильно. Если не заданы ссылки проекта (см. в разделе [как задать ссылки](~/ios/watchos/get-started/project-references.md)) в область конструктора не будет работать правильно.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Удаление альфа-канал из изображений значков

Значки не должен содержать альфа-канал (альфа-канала определяет прозрачные области изображения), в противном случае будут отклонены приложения во время отправки App Store с сообщением об ошибке следующего вида:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Это просто удалить альфа-канал на Mac OS X с помощью **предварительной версии** приложения:

1. Открытие изображения значка в **предварительной версии** и выберите **файл > Экспорт**.

2. Появление диалогового окна будет включать **альфа-канал** флажок, если присутствует альфа-канал.

    ![](troubleshooting-images/remove-alpha-sml.png "Появление диалогового окна будет включать альфа флажок, если присутствует альфа-канала")

3. *Untick* **альфа-канал** флажок и **Сохранить** файл в нужном месте.

4. Изображение значка теперь должна пройти проверку Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Вручную добавлять файлы контроллер интерфейса

> [!IMPORTANT]
> Поддержка WatchKit Xamarin включает в себя проектирование watch раскадровки в конструктор iOS (в Visual Studio для Mac и Visual Studio), которая не требует действия, описанные ниже. Просто имя контроллера интерфейса класса в Visual Studio для Mac свойства рукописного ввода и C# файлы кода будет создана автоматически.


*Если* использовании построителя интерфейс Xcode, выполните следующие действия для создания новых контроллеров интерфейс для наблюдения за приложения и включите синхронизацию с Xcode, выходов и действия, доступные в C#:

1. Откройте приложение watch **Interface.storyboard** в **конструктора Interface Builder Xcode**.
    
    ![](troubleshooting-images/add-6.png "Открытие раскадровки в построителе интерфейс Xcode")

2. Перетащите новый `InterfaceController` на раскадровку:

    ![](troubleshooting-images/add-1.png "InterfaceController")

3. Теперь можно перетаскивать элементы управления на контроллер интерфейса (например) подписи и кнопки), но нельзя создать переменные экземпляров и действия, так как не **.h** файл заголовка. Ниже приведет к необходимая **.h** создаваемого файла заголовка.

    ![](troubleshooting-images/add-2.png "Кнопки в макет")

4. Закройте раскадровку и вернитесь в Visual Studio для Mac. Создайте новый C# файл **MyInterfaceController.cs** (или любое имя по своему усмотрению) в **смотреть расширение приложения** проекта (не watch самого приложения где раскадровки). Добавьте следующий код (обновление пространство имен, имени класса и имя конструктора):

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

5. Создайте еще один C# файл **MyInterfaceController.designer.cs** в **смотреть расширение приложения** проекта и добавьте приведенный ниже код. Не забудьте обновить пространство имен, имя класса и `Register` атрибут:

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
    
    Совет: Можно (необязательно) сделать этот файл дочерний узел первого файла, перетащив его на другой C# файл в Visual Studio для Mac панели решения. Оно затем будет выглядеть следующим образом:
    
    ![](troubleshooting-images/add-5.png "Панель решения")

6. Выберите **сборки > собрать все** таким образом, чтобы синхронизация Xcode распознает новый класс (через `Register` атрибут), мы использовали.

7. Повторно откройте раскадровку, щелкнув файл раскадровки приложения watch **открыть с помощью > конструктора Interface Builder Xcode**:

    ![](troubleshooting-images/add-6.png "Открытие раскадровки в построителе интерфейса")

8. Выберите новый контроллер интерфейса и присвойте ему имя класса, определенный выше, например. `MyInterfaceController`.
Если все работает правильно, он должен отображаться автоматически в **класса:** раскрывающегося списка, вы могли выбрать их оттуда.

    ![](troubleshooting-images/add-4.png "Пользовательский класс параметров")

9. Выберите **вспомогательном редакторе** просмотра в Xcode (значок с двумя перекрывающимися кругами), чтобы вы могли видеть, раскадровки и код side-by-side:

    ![](troubleshooting-images/add-7.png "Элемент панели инструментов редактора помощника")

    Когда фокус находится в области кода, убедитесь, взглянуть на **.h** файл заголовка и если не щелкните правой кнопкой мыши в строке навигации и выберите нужный файл (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "Выберите MyInterfaceController")

10. Теперь можно создавать переменные экземпляров и действия по **Ctrl + перетаскивание** из раскадровки в **.h** файл заголовка.

    ![](troubleshooting-images/add-9.png "Создание выходов и действий")

    Отпустите перетаскивания, вам будет предложено выбрать необходимость создания переменной экземпляра или действие и выберите его имя:

    ![](troubleshooting-images/add-a.png "Розетки и диалоговое окно действий")

11. После сохранения изменений раскадровки и Xcode закрыт, вернитесь в Visual Studio для Mac. Он обнаружит изменения файла заголовка и автоматически добавлять код **. designer.cs** файла:


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


Теперь можно ссылаться на элемент управления (или реализовать это действие) в C#!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Запуск приложения Watch из командной строки

> [!IMPORTANT]
> Можно запустить приложение просмотра в режиме обычного приложения по умолчанию, а также в **Обзор** или **уведомления** режимы с использованием [выполнения пользовательских параметров](~/ios/watchos/get-started/installation.md#custommodes) в Visual Studio для Mac и Visual Studio.


Также можно командной строки для управления симулятора iOS. — Средство командной строки, используемые для запуска приложения watch **mtouch**.

Ниже приведен полный пример (выполняется в виде одной строки в окне терминала).

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

Параметр, необходимо обновить в соответствии с приложения является `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

Полный путь к пакету основного приложения *для приложения iOS, который содержит приложение просмотра и расширение*.

> [!NOTE]
> Необходимо предоставить ведет к *App-файл приложения iPhone*, то есть того, который будет развертываться на симуляторе iOS и содержит приложение просмотра и расширение просмотра.

Пример

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Режим уведомлений

Для тестирования приложения [ **уведомления** режим](~/ios/watchos/platform/notifications.md), задайте `watchlaunchmode` параметр `Notification` и укажите путь к JSON-файл, содержащий полезные данные уведомления теста.

Параметр полезных данных является *требуется* для режим уведомлений.

Например добавьте эти аргументы команды mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Другие аргументы

Остальные аргументы будут объяснены ниже:

### <a name="--sdkroot"></a>--sdkroot

Обязательно. Указывает путь к Xcode (6.2 или более поздней версии).

Пример

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--устройства

Симулятор устройств для выполнения. Это можно указать двумя способами: с помощью udid конкретного устройства, или используя сочетание среды выполнения и типом устройства.

Точные значения и зависит от между машинами, можно запросить с помощью Apple **simctl** средство:

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
