---
title: HomeKit в Xamarin.iOS
description: HomeKit — Apple платформа для управления устройствами автоматизации дома. В этой статье представлены HomeKit и рассматриваются Настройка стандартные теста в симуляторе стандартную программу HomeKit и написания простого приложения Xamarin.iOS для взаимодействия с эти стандартные.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 0dfc6e9ba5098df66a72292d6c8b89ea1bbd1f97
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787465"
---
# <a name="homekit-in-xamarinios"></a>HomeKit в Xamarin.iOS

_HomeKit — Apple платформа для управления устройствами автоматизации дома. В этой статье представлены HomeKit и рассматриваются Настройка стандартные теста в симуляторе стандартную программу HomeKit и написания простого приложения Xamarin.iOS для взаимодействия с эти стандартные._

[![](homekit-images/accessory01.png "Приложения с поддержкой пример HomeKit")](homekit-images/accessory01.png#lightbox)

Apple реализовала HomeKit в iOS 8, чтобы легко интегрировать несколько устройств автоматизации дома от различных поставщиков в один блок, согласовано. Переместив общий протокол для обнаружения, Настройка и управление устройствами автоматизации дома HomeKit позволяет устройствам из несвязанных поставщиков для работы друг с другом, без необходимости координировать усилия определенных поставщиков.

С HomeKit можно создать приложения Xamarin.iOS, управляющий любого устройства включена HomeKit без с использованием поставщика предоставленные API или приложения. Используя HomeKit, можно сделать следующее:

- Обнаруживать новые устройства включена HomeKit автоматизации дома и добавить их в базу данных, сохраняемого на всех устройствах iOS пользователя.
- Программой установки, настройки, отображения и управления любое устройство в HomeKit _главная база данных конфигурации_.
- Взаимодействовать с любым предварительно настроенных HomeKit устройством и их для выполнения отдельных действий или работы в сочетании, такие как включение все индикаторы в Кухня по командам.

Помимо обслуживает устройств в Главная база данных конфигурации для приложения включено HomeKit, HomeKit предоставляет доступ к Siri голосовые команды. Имея правильно настроенного HomeKit установки, пользователь может выдавать голосовые команды такие как «Siri, включить индикаторы в вашем доме.»

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>База данных конфигурации домашней

HomeKit систематизирует все устройства автоматизации в заданную позицию в коллекцию Home. Эта коллекция позволяет пользователю группировать устройствах автоматизации дома в логически организованы единиц с метками удобочитаемом, удобное для восприятия.

Коллекция Главная хранится Главная конфигурации базы данных, автоматически будет резервных копий и синхронизированных на всех устройствах iOS пользователя. HomeKit предоставляет следующие классы для работы с главная база данных конфигурации:

- `HMHome` — Это контейнер верхнего уровня, который содержит все сведения и конфигурации для всех устройств, домашних автоматизации в одном физическом расположении (например) один семейства проживания). Пользователь может иметь более одного проживания, такие как основной дома и дома отпуска. Или они могут иметь различные «дома» на одно и то же свойство, как основной дома и дома гостевой через гараже. В любом случае, по крайней мере один `HMHome` объекта _должен_ настроен и сохранено, прежде чем можно указать другие сведения о HomeKit.
- `HMRoom` -While необязательно, `HMRoom` позволяет пользователю определять конкретных комнат внутри домом (`HMHome`) такие как: Кухня ванных, гараже или бытовой. Все устройства автоматизации дома в определенное место в своих дом, в группу пользователей `HMRoom` и работать с ними, как единое целое. Например вопрос Siri отключение индикатора гараже.
- `HMAccessory` -Это значение представляет отдельного, физической HomeKit включить устройство автоматизации, установленного в домашнего пользователя (например, смарт-термостат). Каждый `HMAccessory` назначается `HMRoom`. Если пользователь еще не настроили все комнаты, HomeKit назначает стандартные комната специальные по умолчанию.
- `HMService` -Представляет службу, предоставляемую классом данного `HMAccessory`, такие как включить или выключить состояние светлый или ее цвет (если поддерживается изменение цвета). Каждый `HMAccessory` может иметь более одной службы, например потребителям дверца гараже, который также включает индикатор. Кроме того данный `HMAccessory` , возможно, службы, такие как обновление встроенного по, которые находятся вне пользовательского элемента управления.
- `HMZone` -Позволяет пользователю группировать коллекции из `HMRoom` объектов в логические зоны, например быть установлен, Downstairs или подвала. Хотя это необязательно, это позволяет взаимодействия как постановку Siri для включения всех свет downstairs off.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Подготовка приложения HomeKit

Из-за требований безопасности, накладываемые HomeKit Xamarin.iOS приложения, в котором используется платформа HomeKit должна быть настроена на оба портала разработчиков Apple и в файле проекта Xamarin.iOS.

Выполните следующие действия:

1. Войдите на [портала разработчиков Apple](http://developer.apple.com).
2. Щелкните **сертификатов, профили & идентификаторы**.
3. Если это еще не сделано, щелкните **идентификаторы** и создайте код для приложения (например `com.company.appname`), в противном случае изменить свой существующий идентификатор.
4. Убедитесь, что **HomeKit** службы проверки для данного ИД: 

    [![](homekit-images/provision01.png "Включить службу HomeKit для данного ИД")](homekit-images/provision01.png#lightbox)
5. Сохраните изменения.
4. Щелкните **профили подготовки** > **разработки** и создание нового профиля подготовки для приложения: 

    [![](homekit-images/provision02.png "Создание нового профиля подготовки для приложения")](homekit-images/provision02.png#lightbox)
5. Загрузите и установите новый профиль подготовки либо использовать Xcode для загрузки и установки профиля.
6. Измените параметры проекта Xamarin.iOS и убедитесь, что вы используете подготовительный профиль, который вы только что создали: 

    [![](homekit-images/provision03.png "Выберите только что созданный профиль подготовки")](homekit-images/provision03.png#lightbox)
7. Далее следует изменить на **Info.plist** файл и убедитесь, что вы используете идентификатор приложения, который использовался для создания профиля подготовки: 

    [![](homekit-images/provision04.png "Задать идентификатор приложения ")](homekit-images/provision04.png#lightbox)
8. Наконец, измените вашей **Entitlements.plist** файл и убедитесь, что **HomeKit** выбрал права: 

    [![](homekit-images/provision05.png "Включить правах HomeKit")](homekit-images/provision05.png#lightbox)
9. Сохраните изменения ко всем файлам.

С этими параметрами на месте приложения готова для доступа к API-интерфейсы HomeKit Framework. Подробные сведения о подготовке см. в разделе нашей [подготовки устройства](~/ios/get-started/installation/device-provisioning/index.md) и [подготовки приложения](~/ios/get-started/installation/device-provisioning/index.md) руководства.

> [!IMPORTANT]
> Тестирование приложение включен HomeKit требуется реальных операций ввода-вывода, правильно подготовлен для разработки приложений. Не удается проверить HomeKit из эмулятора iOS.

## <a name="the-homekit-accessory-simulator"></a>Имитатор аксессуаров HomeKit

Предоставляет способ, чтобы протестировать все возможные автоматизации дома устройств и служб, без необходимости иметь физического устройства Apple создан _HomeKit аксессуаров симулятор_. Это симулятор можно установки и настройки виртуальных устройств HomeKit.

### <a name="installing-the-simulator"></a>Установка симулятора

Apple предоставляет имитатор HomeKit стандартную программу как отдельный загружаемый из Xcode, поэтому необходимо установить ее перед продолжением.

Выполните следующие действия:

1. В веб-браузере, посетите [загрузки для разработчиков Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Загрузить **дополнительные средства для Xcode xxx** (где xxx — Xcode, установленной версии): 

    [![](homekit-images/simulator01.png "Загрузить дополнительные средства для Xcode")](homekit-images/simulator01.png#lightbox)
3. Откройте образ диска и установить средства в вашей **приложений** каталога.

С имитатором HomeKit стандартную программу установки виртуальный стандартные могут создаваться для тестирования.

### <a name="creating-virtual-accessories"></a>Создание виртуального стандартные

Запуск эмулятора HomeKit стандартную программу и создать несколько виртуальных стандартные, выполните следующие действия:

1. В папке приложения запустите симулятор стандартную программу HomeKit: 

    [![](homekit-images/simulator02.png "Имитатор аксессуаров HomeKit")](homekit-images/simulator02.png#lightbox)
2. Нажмите кнопку **+** и выберите пункт **нового вспомогательного компонента...** : 

    [![](homekit-images/simulator03.png "Добавление нового вспомогательного компонента")](homekit-images/simulator03.png#lightbox)
3. Введите сведения о новых стандартную программу и нажмите кнопку **Готово** кнопки: 

    [![](homekit-images/simulator04.png "Введите сведения о новый метод")](homekit-images/simulator04.png#lightbox)
4. Нажмите кнопку **добавить службу...** и выберите из раскрывающегося списка тип службы: 

    [![](homekit-images/simulator05.png "Выберите из раскрывающегося списка тип службы")](homekit-images/simulator05.png#lightbox)
5. Укажите **имя** службы и нажмите кнопку **Готово** кнопки: 

    [![](homekit-images/simulator06.png "Введите имя для службы")](homekit-images/simulator06.png#lightbox)
6. Можно предоставить необязательный характеристики службы, щелкнув **добавьте характеристика** кнопки и настроить необходимые параметры: 

    [![](homekit-images/simulator07.png "Настройка обязательных параметров")](homekit-images/simulator07.png#lightbox)
7. Повторите предыдущие действия для создания одного из каждого типа устройств виртуальных автоматизации дома, поддерживающий HomeKit.

С некоторых образец виртуального HomeKit стандартные создан и настроен можно использовать и управлять этими устройствами из приложения Xamarin.iOS.

## <a name="configuring-the-infoplist-file"></a>Настройка файла Info.plist

Новые возможности для iOS 10 (и выше), разработчику придется добавить `NSHomeKitUsageDescription` ключа в приложение `Info.plist` файл и предоставить объявление строки, почему приложение хочет получить доступ к базе данных HomeKit пользователя. Эта строка будет выводиться при первом запуске приложения:

[![](homekit-images/info01.png "Диалоговое окно разрешений HomeKit")](homekit-images/info01.png#lightbox)

Чтобы задать этот ключ, выполните следующее:

1. Дважды щелкните `Info.plist` файла в **обозревателе решений** чтобы открыть его для редактирования.
2. В нижней части экрана, перейдите в **источника** представления.
3. Добавьте новый **входа** в список.
4. В раскрывающемся списке выберите **конфиденциальность - Описание использования HomeKit**: 

    [![](homekit-images/info02.png "Выберите конфиденциальность - Описание использования HomeKit")](homekit-images/info02.png#lightbox)
5. Введите описание для почему приложение хочет получить доступ к базе данных HomeKit пользователя: 

    [![](homekit-images/info03.png "Введите описание")](homekit-images/info03.png#lightbox)
6. Сохраните изменения в файле.

> [!IMPORTANT]
> Не удалось задать `NSHomeKitUsageDescription` ключа в `Info.plist` файла приведет к приложению _без вмешательства пользователя со сбоем_ (закрывается в системе во время выполнения) без ошибок, при выполнении в iOS 10 (или более поздней).

## <a name="connecting-to-homekit"></a>Подключение к HomeKit

Взаимодействие с HomeKit, необходимо сначала создается экземпляр приложения Xamarin.iOS `HMHomeManager` класса. Диспетчер Главная точка входа центра в HomeKit и отвечает за предоставление списка доступных домов обновления и обслуживание этого списка и возвращение пользователя _основной домашней_.

`HMHome` Объект содержит все сведения о домашней выдать, включая все комнаты, группы или зоны, он может содержать, а также все стандартные автоматизации дома, которые были установлены. Перед выполнением любых операций в HomeKit, по крайней мере один `HMHome` должно быть создано и назначены в качестве домашней первичной.

Приложение отвечает за проверка домашней первичной, а также создание и назначение один, если это не так.

### <a name="adding-a-home-manager"></a>Добавление домашней Manager

Добавление приложения Xamarin.iOS HomeKit осведомленности, изменить **AppDelegate.cs** файл, чтобы изменить его и сделать его выглядеть следующим образом:

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

При первом запуске приложения, пользователя будет запрошено, следует ли разрешить ему доступ к информации о HomeKit:

[![](homekit-images/home01.png "Пользователь будет запрашиваться, следует ли разрешить ему доступ к информации о HomeKit")](homekit-images/home01.png#lightbox)

Если пользователь отвечает **ОК**, то приложение будет работать с их стандартные HomeKit в противном случае не будет, и все вызовы HomeKit будут завершаться ошибкой.

Главная диспетчер в месте Далее приложения потребуется ли домашней основной был настроен и если нет, позволяют для пользователя создать и назначить один.

### <a name="accessing-the-primary-home"></a>Доступ к домашней основной

Как уже говорилось выше, домашняя основной необходимо будет создана и настроена перед HomeKit доступен и что ответственность приложения предоставляет способ для пользователя создать и назначить домашней основной, если он еще не существует.

Если сначала приложение запускается или возвращает из фона, ее нужно отслеживать `DidUpdateHomes` событие `HMHomeManager` класс для проверки наличия домашней первичной. Если он не существует, он должен предоставить интерфейс пользователя для ее создания.

Следующий код можно добавить к представлению контроллеру для проверки домашней основной:

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

Домашняя страница диспетчера создает подключение к HomeKit, `DidUpdateHomes` событие будет запущено, будут загружены все существующие домов коллекции диспетчеров домов и корневую папку источника будут загружены, если он доступен.

### <a name="adding-a-primary-home"></a>Добавление основной домом

Если `PrimaryHome` свойство `HMHomeManager` — `null` после `DidUpdateHomes` событий, необходимо предоставить способом для пользователя для создания и назначения основной домом перед продолжением.

Обычно приложения предоставит пользователю, назовите новый дом, в которой передается домашней диспетчер для установки как домашняя основной. Для **HomeKitIntro** пример приложения, модальные представления был создан в конструкторе операций ввода-ВЫВОДА и вызывается `AddHomeSegue` перейти в интерфейсе основного приложения.

Он содержит текстовое поле для пользователя, введите имя для нового дома и кнопку, чтобы добавить корневую папку. Когда пользователь нажимает кнопку **добавить домашнюю** кнопки, приведенный ниже код вызывает домашней диспетчер для добавления корневую папку:

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

`AddHome` Будет пытаться создать новый дом и возвращает его в процедуру обратного вызова данного метода. Если `error` свойство не `null`, произошла ошибка, и он должны быть представлены пользователю. Наиболее распространенные ошибки, вызванные неуникальное имя home или домашней Manager, не может соединиться с HomeKit.

Если домашняя выполнено успешно, необходимо вызвать `UpdatePrimaryHome` метод, чтобы задать новый дом как домашняя первичной. Опять же если `error` свойство не `null`, произошла ошибка, и он должны быть представлены пользователю.

Необходимо также отслеживать Главная руководителя `DidAddHome` и `DidRemoveHome` события и обновления интерфейса пользователя приложения, при необходимости.

> [!IMPORTANT]
> `AlertView.PresentOKAlert` Метод, используемый в приведенном выше примере — это вспомогательный класс в приложении HomeKitIntro, которое позволяет работать с iOS оповещения проще.


## <a name="finding-new-accessories"></a>Стандартные новый поиск

После определения или загружен из домашней диспетчера домашней первичный Xamarin.iOS приложения можно вызывать `HMAccessoryBrowser` найти все новые стандартные автоматизации дома и добавить их в корневой папке.

Вызовите `StartSearchingForNewAccessories` метод для поиска новых стандартные и `StopSearchingForNewAccessories` метод при завершении.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` нельзя оставлять запущен на длительное время, поскольку отрицательно влияет на время автономной работы и производительность устройства iOS. Apple предлагает вызова `StopSearchingForNewAccessories` после минуты или только поиска при появлении пользовательского интерфейса аксессуаров найти для пользователя.

`DidFindNewAccessory` Событие будет вызываться при обнаруживаются новые стандартные, и они будут добавлены `DiscoveredAccessories` списка в браузере аксессуар.

`DiscoveredAccessories` Список будет содержать коллекцию `HMAccessory` объектами, которые определяют выдать HomeKit включена автоматизации дома устройство и его доступных служб, таких как индикаторы или гараже дверца управления.

После обнаружения нового вспомогательного компонента его должны отображаться для пользователя и поэтому выберите его и добавить его в корневой папке. Пример

[![](homekit-images/accessory01.png "Поиск нового вспомогательного компонента")](homekit-images/accessory01.png#lightbox)

Вызовите `AddAccessory` метод, чтобы добавить выбранный метод коллекцию корневую папку. Пример:

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

Если `err` свойство не `null`, произошла ошибка, и он должны быть представлены пользователю. В противном случае пользователь будет предложено ввести код установки для устройства добавить:

[![](homekit-images/accessory02.png "Введите код настройки для устройства, добавление")](homekit-images/accessory02.png#lightbox)

В симуляторе аксессуаров HomeKit это число можно найти в **код установки** поля:

[![](homekit-images/accessory03.png "Код настройки поля в симуляторе аксессуаров HomeKit")](homekit-images/accessory03.png#lightbox)

На реальных HomeKit аксессуары код установки либо печатаются на наклейке на самом устройстве, в окне «продукт» или в руководстве пользователя стандартную программу.

Необходимо отслеживать аксессуаров браузера `DidRemoveNewAccessory` событий и обновление пользовательского интерфейса для удаления из списка доступных стандартную программу, когда пользователь добавил ее в свою коллекцию Главная.

## <a name="working-with-accessories"></a>Работа с стандартные

Один раз в домашней первичный и было установлено и стандартные были добавлены к ней, можно представить список стандартные (и при необходимости комнат) для пользователя, для работы с.

`HMRoom` Объект содержит все сведения о данной комнаты и все стандартные, принадлежащих к ней. Комнаты, при необходимости могут быть организованы в одной или нескольких зон. Объект `HMZone` содержит все сведения о данной зоны и все комнаты, принадлежащих к ней.

Для данного примера мы будем обеспечить вещей простой и работать с домашней стандартные напрямую, вместо организуя их в комнаты или зон.

`HMHome` Объект содержит список назначенных стандартную программу, могут быть представлены пользователю в его `Accessories` свойство. Пример:

[![](homekit-images/accessory04.png "Пример стандартную программу")](homekit-images/accessory04.png#lightbox)

Здесь форме, пользователь может выбрать данный метод и работать со службами, которые он предоставляет.

## <a name="working-with-services"></a>Работа со службами

Когда пользователь взаимодействует с определенным устройством HomeKit включена автоматизации дома, это обычно через службы, которые он предоставляет. `Services` Свойство `HMAccessory` класс содержит коллекцию `HMService` предлагает устройства объекты, которые определяют службы.

Службы, индикаторы, термостаты, гараже дверца привилегией, коммутаторы и блокировок. Некоторые устройства (например потребителям дверца гараж) обеспечит более одной службы, например индикатор и возможность открытия или закрытия двери.

Помимо конкретных служб, которые предоставляет заданного аксессуаров, каждый метод содержит `Information Service` , определяющий свойства, такие как имя, изготовитель, модель и серийный номер.

### <a name="accessory-service-types"></a>Типы служб периферийных устройств

Можно загрузить с помощью следующих типов служб `HMServiceType` перечисления:

- **AccessoryInformation** -предоставляет сведения об устройстве данного автоматизации дома (аксессуар).
- **AirQualitySensor** -определяет качество воздуха датчика.
- **Батарея** -определяет состояние батареи это устройство.
- **CarbonDioxideSensor** -определяет уровень выброса углекислого газа датчика.
- **CarbonMonoxideSensor** -определяет Угарный датчика.
- **ContactSensor** -определяет контактные датчика (например, окно Открытие или закрытие).
- **Дверца** -определяет состояние дверца датчика (такие как открыт или закрыт).
- **Вентилятор** -определяет удаленный управляемой вентилятора.
- **GarageDoorOpener** -определяет гараже дверца потребителям.
- **HumiditySensor** -определяет влажности датчика.
- **LeakSensor** -определяет утечки датчика (как для активной бак или стиральные машины).
- **Лампочки** -определяет light автономный или источника света, который является частью другой стандартную программу (например, начального дверца гараж).
- **LightSensor** -определяет датчик освещения.
- **LockManagement** -определяет службу, которая управляет блокировки дверцы автоматических.
- **LockMechanism** -определяет удаленной управляемой блокировки (например, блокировки дверцы).
- **MotionSensor** -определяет датчик движения.
- **OccupancySensor** -определяет датчика (LDM).
- **Розетки** -определяет удаленный управляемой розетке.
- **SecuritySystem** -определяет домашней безопасности системы.
- **StatefulProgrammableSwitch** -определяет программируемый переключатель, который остается в состоянии позволяют запустить один раз (например, зеркало коммутатора).
- **StatelessProgrammableSwitch** -определяет программируемый переключатель, который возвращает в исходное состояние после запустилась (как кнопка).
- **SmokeSensor** -определяет состояния датчика.
- **Переключение** -определяет выключатель как коммутатор Стандартная стенок.
- **Датчик температуры** -определяет датчика температуры.
- **Термостат** -определяет Интеллектуальный термостат, используемые для управления системой Кондиционирования.
- **Окно** -определяет автоматических окна, графиком быть удаленно открыт или закрыт.
- **WindowCovering** -определяет, удаленно управляемые окно, охватывающие как жалюзи, открыт или закрыт.

### <a name="displaying-service-information"></a>Отображение сведений о службе

После загрузки `HMAccessory` можно запросить отдельные `HNService` объектов, которые он предоставляет и отображать эти сведения для пользователя:

[![](homekit-images/accessory05.png "Отображение сведений о службе")](homekit-images/accessory05.png#lightbox)

Необходимо всегда следует проверять `Reachable` свойство `HMAccessory` прежде чем работать с ним. Это устройство может быть недоступен пользователь не входит в диапазон устройства или если он не подключен.

После выбора службы пользователя можно просмотреть или изменить один или несколько характеристик служба могла мониторинг или управление устройством заданного автоматизации дома.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Работа с характеристиками

Каждый `HMService` объект может содержать коллекцию `HMCharacteristic` объекты, которые могут предоставить сведения о состоянии службы (например, открытие или закрытие двери) или разрешить пользователю корректировать состояния (например, задавать цвет источника света).

`HMCharacteristic` не только предоставляет сведения о характеристика и его состояние, но также предоставляет методы для работы с состоянием через _метаданных характеристикой_ (`HMCharacteristisMetadata`). Эти метаданные может предоставлять свойства (например, диапазоны минимальное и максимальное значение), которые полезны при отображении сведений пользователю или пользователям, для изменения состояния.

`HMCharacteristicType` Перечисление содержит набор метаданных характеристикой значения, которые невозможно определить или изменить следующим образом:

 - AdminOnlyAccess
 - AirParticulateDensity
 - AirParticulateSize
 - AirQuality
 - AudioFeedback
 - BatteryLevel
 - Яркость
 - CarbonDioxideDetected
 - CarbonDioxideLevel
 - CarbonDioxidePeakLevel
 - CarbonMonoxideDetected
 - CarbonMonoxideLevel
 - CarbonMonoxidePeakLevel
 - ChargingState
 - ContactState
 - CoolingThreshold
 - CurrentDoorState
 - CurrentHeatingCooling
 - CurrentHorizontalTilt
 - CurrentLightLevel
 - CurrentLockMechanismState
 - CurrentPosition
 - CurrentRelativeHumidity
 - CurrentSecuritySystemState
 - CurrentTemperature
 - CurrentVerticalTilt
 - FirmwareVersion
 - HardwareVersion
 - HeatingCoolingStatus
 - HeatingThreshold
 - HoldPosition
 - Цветовой тон
 - Identify
 - InputEvent
 - LeakDetected
 - LockManagementAutoSecureTimeout
 - LockManagementControlPoint
 - LockMechanismLastKnownAction
 - Журналы
 - Изготовитель
 - Модель
 - MotionDetected
 - name
 - ObstructionDetected
 - OccupancyDetected
 - OutletInUse
 - OutputState
 - PositionState
 - PowerState
 - RotationDirection
 - RotationSpeed
 - Насыщенность
 - серийный номер
 - SmokeDetected
 - SoftwareVersion
 - StatusActive
 - StatusFault
 - StatusJammed
 - StatusLowBattery
 - StatusTampered
 - TargetDoorState
 - TargetHeatingCooling
 - TargetHorizontalTilt
 - TargetLockMechanismState
 - TargetPosition
 - TargetRelativeHumidity
 - TargetSecuritySystemState
 - TargetTemperature
 - TargetVerticalTilt
 - TemperatureUnits
 - Версия

### <a name="working-with-a-characteristics-value"></a>Работа с характеристиками значение

Чтобы убедиться, что приложение имеет последнее состояние данного характеристика, вызовите `ReadValue` метод `HMCharacteristic` класса. Если `err` свойство не `null`, произошла ошибка, и он может или не могут быть представлены пользователю.

Эта характеристика `Value` свойство содержит текущее состояние заданного характеристика как `NSObject`и поэтому не может работать непосредственно в C#.

Для чтения значения, был добавлен следующий вспомогательный класс **HomeKitIntro** образец приложения:

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

`NSObjectConverter` Используется каждый раз, когда приложению требуется прочитать текущее состояние характеристика. Пример.

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

Выше строки преобразует значение в `float` , затем могут использоваться в коде C# Xamarin.

Для изменения `HMCharacteristic`, вызовите его `WriteValue` метод и заключить новое значение в `NSObject.FromObject` вызова. Пример.

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Если `err` свойство не `null`, произошла ошибка и должны быть представлены пользователю.

### <a name="testing-characteristic-value-changes"></a>Тестирование изменений характеристик значение

При работе с `HMCharacteristics` и имитацию стандартные, изменения в `Value` свойство можно отслеживать в симуляторе стандартную программу HomeKit.

С **HomeKitIntro** приложение, работающее в реальном iOS оборудования устройства, внесение изменений в это характеристика значение следует рассматривать в симуляторе аксессуаров HomeKit практически мгновенно. Например изменение состояния источника света в приложении iOS:

[![](homekit-images/test01.png "Изменение состояния света в приложения iOS")](homekit-images/test01.png#lightbox)

Следует изменить состояние источника света в симуляторе HomeKit стандартную программу. Если значение не изменится, проверять состояние сообщения об ошибке при написании новых значений характеристик и убедитесь, что метод по-прежнему доступен.

## <a name="advanced-homekit-features"></a>Функции расширенного HomeKit

В этой статье рассмотрены основные компоненты, необходимые для работы с HomeKit стандартные в приложении Xamarin.iOS. Однако существуют некоторые дополнительные возможности HomeKit, не описанные в этой статье:

- **Комнаты** -HomeKit включены стандартные могут при необходимости организованы комнат конечным пользователем. Это позволяет HomeKit стандартные присутствует в виде, удобном для пользователя, для понимания и работы с. Дополнительные сведения о создании и поддержание комнат. в разделе Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) документации.
- **Зоны** -комнат при необходимости могут быть организованы в зонах конечным пользователем. Зоны ссылается на коллекцию рабочих групп, которые пользователь может рассматривать как единое целое. Например: быть установлен, Downstairs или подвала. Опять же это позволяет HomeKit для представления и работать с стандартные способом, который имеет смысл для конечного пользователя. Дополнительные сведения по созданию и обеспечению зоны см. в разделе Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) документации.
- **Действия и задает действие** -действия, изменить характеристики службы периферийных устройств и могут быть сгруппированы в наборы. Задает действие в качестве сценарии для управления группой стандартные и координации своих действий. Например сценарий «Просмотр ТВ» может закрыть сложность, dim индикаторы и включите телевизора и звуковой системы. Дополнительные сведения о создании и ведении действия и действия наборов см. в разделе Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) и [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) документации.
- **Триггеры** - триггер может активировать один или несколько задать действие, если данный набор условий, были удовлетворены. Например включают индикатор portch и блокировки всех внешних двери, когда он получает темной за пределами. Дополнительные сведения о создании и ведении триггеров см. в разделе Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) документации.

Поскольку эти функции используют те же методы, представленные выше, они должны быть легко реализовать следующие компании Apple [руководство HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [рекомендации по пользовательскому интерфейсу HomeKit](https://developer.apple.com/homekit/ui-guidelines/) и [ Справочник по HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Рекомендации по оценке App HomeKit

Перед отправкой HomeKit Xamarin.iOS приложение в iTunes Connect для выпуска в iTunes App Store, убедитесь, что выполнены правилам Apple приложений HomeKit включена для:

 - Основная цель приложения _должен_ быть автоматизации дома, если с помощью платформы HomeKit.
 - Текст маркетингового приложения необходимо уведомить пользователей, что используется HomeKit, и им необходимо предоставить политику конфиденциальности.
 - Сбор сведений о пользователе или с помощью HomeKit для рекламных запрещено.

Для полной ознакомиться с рекомендациями, см. в разделе Apple [приложения хранилища просмотрите рекомендации](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>Новые возможности iOS 9

Apple внесены следующие изменения и дополнения HomeKit IOS 9.

- **Поддержка существующих объектов** — Если изменяется существующий стандартную программу, диспетчер Главная (`HMHomeManager`) сообщит вам определенного элемента, который был изменен.
- **Постоянные идентификаторы** -теперь включают все соответствующие классы HomeKit `UniqueIdentifier` включено свойство для уникальной идентификации данного элемента по HomeKit приложения (или экземпляров одного приложения).
- **Управление пользователями** -добавлен контроллер встроенное представление для обеспечения управления пользователями через пользователей, имеющих доступ к устройствам HomeKit в домашней основного пользователя.
- **Возможности пользователя** - HomeKit пользователи теперь имеют набор прав, определяющие, какие функции, они получают возможность использовать в HomeKit и HomeKit включены стандартные. Приложение только отображать соответствующие возможности для текущего пользователя. Например только администраторы должны иметь возможность обслуживать других пользователей.
- **Предопределенные сцен** -предопределенных сцен будут созданы для четырех распространенные события, возникающие обычному пользователю HomeKit: Приступая, оставьте, возврат, перейдите к Bed. Эти предопределенные сцен нельзя удалить из дома.
- **Сцен и Siri** -Siri имеет более глубокого поддержку сцен в iOS 9 и может распознать имя любой сцены, определенные в HomeKit. Пользователь может выполнить это может быть просто голосом ее имя на Siri.
- **Категории аксессуаров** -набор стандартных категорий были добавлены все стандартные и помогает идентифицировать тип стандартную программу, добавляемый в домашней или из работали в приложении. Эти новые категории доступны во время установки аксессуар.
- **Поддержка Apple Watch** - HomeKit теперь доступен для watchOS и Apple Watch будут иметь возможность управления устройствами с поддержкой HomeKit без iPhone, рядом с часами. HomeKit для watchOS поддерживает следующие возможности: просмотр дома, управление стандартные и выполнении кадром.
- **Новый тип событий триггера** — помимо триггеры типа таймера, поддерживаемые в iOS 8, iOS 9 теперь поддерживает триггеров событий на основе аксессуаров состояния (например, датчик данных) или географическое положение. Использование триггеров событий `NSPredicates` для задания условий их выполнение.
- **Удаленный доступ** -с помощью удаленного доступа пользователь теперь является возможность управления их HomeKit включена Главная стандартные автоматизации, находясь вдали от дома в удаленном расположении. В iOS 8. это была поддерживается только если у данного пользователя в 3-го поколения Apple TV в корневую папку. В iOS 9 это ограничение удаляется и удаленный доступ с помощью iCloud и HomeKit аксессуаров протокола (получена строка HAP) поддерживается.
- **Новые возможности энергии низкий Bluetooth (Разрешить)** -HomeKit теперь поддерживает дополнительные вспомогательные типы, которые могут обмениваться данными по протоколу энергии низкий Bluetooth (Разрешить). С помощью получена строка HAP безопасное туннелирование, аксессуаров HomeKit можно по-прежнему предоставлять другим аксессуаров Bluetooth по Wi-Fi (если оно не входит в диапазон Bluetooth). В iOS 9 стандартные ЛЮЧИТЬ иметь полную поддержку уведомлений и метаданных.
- **Новые категории аксессуаров** -Apple добавлены следующие новые категории аксессуаров в iOS 9: Coverings окна, чего двери и Windows, систем сигнализации, датчики и программируемые коммутаторов.

Дополнительные сведения о новых функциях HomeKit в iOS 9 см. в разделе Apple [индекс HomeKit](https://developer.apple.com/homekit/) и [новые возможности HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) видео.

## <a name="summary"></a>Сводка

В этой статье внес framework автоматизации дома HomeKit компании Apple. Показано, как установить и настроить с помощью симулятора аксессуаров HomeKit тестовых устройств и создание простого приложения Xamarin.iOS для обнаружения, взаимодействовать с и управления устройствами автоматизации дома с помощью HomeKit.



## <a name="related-links"></a>Связанные ссылки

- [Образцы iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [Новые возможности iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Руководство по HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Рекомендации по HomeKit пользовательскому интерфейсу](https://developer.apple.com/homekit/ui-guidelines/)
- [Справочник по HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
