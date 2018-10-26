---
title: HomeKit в Xamarin.iOS
description: HomeKit — платформы Apple для управления устройствами для домашней автоматизации. В этой статье представлены HomeKit и рассматривается настройка теста "Стандартные", в симуляторе HomeKit периферийных устройств и написания простого приложения Xamarin.iOS для взаимодействия с эти стандартные.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 22b6fd101c0b983fe7b4a7d0891dc4674a11a02a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121195"
---
# <a name="homekit-in-xamarinios"></a>HomeKit в Xamarin.iOS

_HomeKit — платформы Apple для управления устройствами для домашней автоматизации. В этой статье представлены HomeKit и рассматривается настройка теста "Стандартные", в симуляторе HomeKit периферийных устройств и написания простого приложения Xamarin.iOS для взаимодействия с эти стандартные._

[![](homekit-images/accessory01.png "Приложения с поддержкой пример HomeKit")](homekit-images/accessory01.png#lightbox)

Apple представила HomeKit в iOS 8, чтобы легко интегрируйте единое единого, согласованного несколькими устройствами для домашней автоматизации от различных поставщиков. Повысив уровень общего протокола для обнаружения, Настройка и управление устройствами для домашней автоматизации, HomeKit позволяет устройствам из несвязанных поставщиков для совместной работы и все это без необходимости координировать усилия определенных поставщиков.

С помощью HomeKit можно создать приложение Xamarin.iOS, которое управляет любого устройства включена HomeKit без API или приложения, используя поставщика указанные. С помощью HomeKit, можно сделать следующее:

- Обнаруживать новые устройства включена HomeKit автоматизации дома и добавить их в базу данных, будут сохраняться между всеми устройствами iOS пользователя.
- Установки, настройки, отображения и управления любого устройства в HomeKit _главная база данных конфигурации_.
- Взаимодействовать с любой предварительно настроенных устройств HomeKit и команды их для выполнения отдельного действия или работать совместно, такие как включение все индикаторы на кухне.

Помимо роли устройств в Главная база данных конфигурации для приложения включено HomeKit, HomeKit предоставляет доступ к Siri голосовых команд. Учитывая соответствующим образом настроенная Установка HomeKit, пользователь может выдавать голосовые команды такие как «Siri, включить свет в гостиной.»

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>База данных конфигурации домашней

HomeKit позволяет упорядочить все устройства автоматизации в заданную позицию в коллекцию Главная. Эта коллекция позволяет сгруппировать их устройствами для домашней автоматизации в логически упорядочиваются единиц с метками удобочитаемом, понятном пользователю.

Коллекция Главная хранится в Главная базу данных конфигурации, будут автоматически резервных копий и синхронизированы на всех устройствах iOS пользователя. HomeKit предоставляет следующие классы для работы с главная база данных конфигурации:

- `HMHome` — Это контейнер верхнего уровня, который содержит все сведения и конфигурации для всех устройств автоматизации дома в одном месте (например) одного семейства проживания). Пользователь может иметь более одного проживания, такие как основной дома и отпуска дома. Или они могут иметь различные «дома» для одного свойства, такие как основной дома и дома гостевой через гараже. В любом случае по крайней мере `HMHome` объект _необходимо_ быть настроены и сохранено, прежде чем можно ввести другие сведения о HomeKit.
- `HMRoom` — Наряду с необязательным, `HMRoom` позволяет пользователю определять свободно конкретное помещение внутри домом (`HMHome`) такие как: кухни ванной, обзоров и гостиной. Пользователя можно сгруппировать все устройства автоматизации дома в определенном месте в их доме в `HMRoom` и работать с ними как единое целое. Например вопрос Siri, чтобы отключить свет обзоров.
- `HMAccessory` -Это представляет отдельный, физических HomeKit включить устройства автоматизации, которая была установлена в проживания пользователя (например, Интеллектуальный термостат). Каждый `HMAccessory` назначается `HMRoom`. Если пользователь еще не настроен любой комнаты, HomeKit присваивает стандартные комната специальные по умолчанию.
- `HMService` — Представляет службу, предоставляемую заданного `HMAccessory`, например состоянием on/off света или его цвет (если поддерживается изменение цвета). Каждый `HMAccessory` может иметь более одной службы, например потребителям дверцу обзоров, которая также включает индикатор. Кроме того заданного `HMAccessory` , возможно, службы, такие как обновление встроенного по, которые находятся вне пользовательского элемента управления.
- `HMZone` -Позволяет пользователю группирует коллекцию `HMRoom` объекты в логической зоны, например быть установлен, Downstairs или подвала. Хотя это и необязательно, это позволяет взаимодействия как постановку Siri для включения всех света downstairs off.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Подготовка приложения HomeKit

Из-за требований безопасности, налагаемых HomeKit приложение Xamarin.iOS, которое использует фреймворк HomeKit необходимо правильно настроить оба на портале разработчика Apple и в файле проекта Xamarin.iOS.

Выполните следующие действия:

1. Войдите на [портал разработчиков Apple](http://developer.apple.com).
2. Щелкните **сертификаты, идентификаторы и профили**.
3. Если вы еще не сделали, щелкнуть **идентификаторы** и создайте код для приложения (например `com.company.appname`), иначе изменить свой существующий идентификатор.
4. Убедитесь, что **HomeKit** службы проверки для данного ИД: 

    [![](homekit-images/provision01.png "Включить HomeKit службу для данного ИД")](homekit-images/provision01.png#lightbox)
5. Сохраните изменения.
4. Щелкните **профили подготовки** > **разработки** и создание нового профиля подготовки для приложения: 

    [![](homekit-images/provision02.png "Создание нового профиля подготовки для приложения")](homekit-images/provision02.png#lightbox)
5. Скачайте и установите новый профиль подготовки или использовать Xcode, чтобы загрузить и установить профиль.
6. Измените параметры проекта Xamarin.iOS и убедитесь, что вы используете только что созданный профиль подготовки: 

    [![](homekit-images/provision03.png "Выберите только что созданный профиль подготовки")](homekit-images/provision03.png#lightbox)
7. Далее следует изменить ваш **Info.plist** файл и убедитесь, что вы используете идентификатор приложения, который использовался для создания профиля подготовки: 

    [![](homekit-images/provision04.png "Задать идентификатор приложения ")](homekit-images/provision04.png#lightbox)
8. И, наконец, изменить ваш **Entitlements.plist** файл и убедитесь, что **HomeKit** прав был выбран: 

    [![](homekit-images/provision05.png "Включить HomeKit назначением")](homekit-images/provision05.png#lightbox)
9. Сохраните изменения ко всем файлам.

Эти параметры заданы приложение теперь может получить доступ к API-интерфейсы HomeKit Framework. Подробные сведения о подготовке, см. в разделе наших [подготовки устройств](~/ios/get-started/installation/device-provisioning/index.md) и [подготовки приложений](~/ios/get-started/installation/device-provisioning/index.md) руководства.

> [!IMPORTANT]
> Тестирование приложения включен HomeKit требует реальных операций ввода-вывода устройства, которое было соответствующим образом подготовлено для разработки приложений. HomeKit невозможно тестировать с симулятором iOS.

## <a name="the-homekit-accessory-simulator"></a>Имитатор HomeKit периферийных устройств

Предоставляет способ для тестирования всех возможных автоматизации дома устройств и служб, не нужно устанавливать физическое устройство, компания Apple создала _симулятор периферийного устройства HomeKit_. Этот симулятор может установки и настройки виртуальных устройств HomeKit.

### <a name="installing-the-simulator"></a>Установка симулятора

Apple предоставляет симулятор периферийного устройства HomeKit как отдельный загружаемый из Xcode, необходимо будет установить его, прежде чем продолжить.

Выполните следующие действия:

1. В веб-браузер, посетите [загрузки для разработчиков Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Скачайте **дополнительные средства для Xcode xxx** (где xxx — это версия Xcode, который вы установили): 

    [![](homekit-images/simulator01.png "Скачать дополнительные средства для Xcode")](homekit-images/simulator01.png#lightbox)
3. Откройте образ диска и установить средства в вашей **приложений** каталога.

С помощью симулятора HomeKit периферийных устройств, установки стандартные виртуальные могут создаваться для тестирования.

### <a name="creating-virtual-accessories"></a>Создание виртуального стандартные

Чтобы запустить симулятор периферийного устройства HomeKit и создать несколько виртуальных "Стандартные", сделайте следующее:

1. Из папки Applications запустите симулятор HomeKit периферийного устройства: 

    [![](homekit-images/simulator02.png "Имитатор HomeKit периферийных устройств")](homekit-images/simulator02.png#lightbox)
2. Нажмите кнопку **+** и выберите **новый периферийного устройства...** : 

    [![](homekit-images/simulator03.png "Добавление нового вспомогательного компонента")](homekit-images/simulator03.png#lightbox)
3. Заполните сведения о новых периферийных устройств и нажмите кнопку **Готово** кнопки: 

    [![](homekit-images/simulator04.png "Введите сведения о новых периферийных устройств")](homekit-images/simulator04.png#lightbox)
4. Нажмите кнопку **Добавление службы...** и выберите тип службы из раскрывающегося списка: 

    [![](homekit-images/simulator05.png "Выберите тип службы из раскрывающегося списка")](homekit-images/simulator05.png#lightbox)
5. Укажите **имя** службы и нажмите кнопку **Готово** кнопки: 

    [![](homekit-images/simulator06.png "Введите имя для службы")](homekit-images/simulator06.png#lightbox)
6. Можно указать необязательные характеристики службы, щелкнув **добавьте характеристика** кнопку и настроить необходимые параметры: 

    [![](homekit-images/simulator07.png "Настройка обязательных параметров")](homekit-images/simulator07.png#lightbox)
7. Повторите предыдущие действия для создания одного из каждого типа устройств виртуального автоматизации дома, который поддерживает HomeKit.

С некоторых пример виртуальной HomeKit "Стандартные" создается и настраивается теперь можно использовать и управлять этими устройствами из приложения Xamarin.iOS.

## <a name="configuring-the-infoplist-file"></a>Настройка файла Info.plist

Новые возможности для iOS 10 (и выше), разработчику нужно будет добавить `NSHomeKitUsageDescription` ключа в приложение `Info.plist` файл и предоставить объявление строки, почему приложение хочет получить доступ к базе данных HomeKit пользователя. Эта строка будет отображен пользователю при первом запуске приложения:

[![](homekit-images/info01.png "Диалоговое окно разрешений HomeKit")](homekit-images/info01.png#lightbox)

Чтобы задать этот ключ, сделайте следующее:

1. Дважды щелкните `Info.plist` файл **обозревателе решений** чтобы открыть его для редактирования.
2. В нижней части экрана, переключитесь в **источника** представления.
3. Добавьте новый **запись** в список.
4. В раскрывающемся списке выберите **конфиденциальность — Описание использования HomeKit**: 

    [![](homekit-images/info02.png "Выберите конфиденциальность — Описание использования HomeKit")](homekit-images/info02.png#lightbox)
5. Введите описание для почему приложение хочет получить доступ к базе данных HomeKit пользователя: 

    [![](homekit-images/info03.png "Введите описание")](homekit-images/info03.png#lightbox)
6. Сохраните изменения в файле.

> [!IMPORTANT]
> Не удалось установить `NSHomeKitUsageDescription` в `Info.plist` приведет к файлу в приложении _автоматически сбоя_ (закрывается системой во время выполнения) без ошибок, при выполнении в iOS 10 (или выше).

## <a name="connecting-to-homekit"></a>Подключение к HomeKit

Для взаимодействия с HomeKit, необходимо сначала создать экземпляр приложения Xamarin.iOS `HMHomeManager` класса. Диспетчер Главная точкой в HomeKit и отвечает за предоставление список доступных домов, обновление и обслуживание этого списка и возвращает пользователя _основной домашней_.

`HMHome` Объект содержит все сведения о домашней предоставляют, включая комнаты, группы или зоны, которые он может содержать, а также любые стандартные автоматизации дома, которые были установлены. Перед выполнением любых операций в HomeKit, по крайней мере `HMHome` должен быть создан и назначен в качестве домашней первичной.

Приложение отвечает за проверка наличия домом основной, а также создание и назначение одно, если это не так.

### <a name="adding-a-home-manager"></a>Добавление домашней Manager

Чтобы добавить HomeKit awareness в приложение Xamarin.iOS, отредактируйте **AppDelegate.cs** файл, чтобы изменить его и это должно выглядеть следующим образом:

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

При первом запуске приложения, пользователя попросят стоит ли разрешить доступ к нужной информации HomeKit:

[![](homekit-images/home01.png "Пользователь будет запрашиваться, следует ли разрешить доступ к нужной информации HomeKit")](homekit-images/home01.png#lightbox)

Если пользователь отвечает на **ОК**, то приложение будет работать с их стандартные HomeKit в противном случае он не будет и все вызовы HomeKit будет завершаться ошибкой.

Диспетчером Главная на месте Далее приложение будет нужно ли основной домом был настроен и если это не так, позволяют пользователю создавать и назначить один.

### <a name="accessing-the-primary-home"></a>Доступ к домашней основной

Как уже говорилось, основной домом необходимо создать и настроить перед HomeKit доступен и что приложение отвечает за предоставление способа для пользователя создать и назначить основной домом, если он еще не существует.

Когда приложение сначала запускает или возвращает из фона, ему необходимо отслеживать `DidUpdateHomes` событие `HMHomeManager` класс для проверки существования основного домом. Если он существует, он должен предоставить интерфейс пользователя для ее создания.

Контроллер представления для проверки основного домашней может добавлен следующий код:

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

Когда главная Manager устанавливает подключение к HomeKit, `DidUpdateHomes` событие будет запущено, любые существующие домов будут загружены в коллекции диспетчеров домов и домашней основной будут загружены, если он доступен.

### <a name="adding-a-primary-home"></a>Добавление основной домом

Если `PrimaryHome` свойство `HMHomeManager` — `null` после `DidUpdateHomes` событий, необходимо указать для пользователя способом, создать и назначить основной домом перед продолжением.

Обычно приложения, предоставит пользователю, назовите новый дом, который затем передается для домашних диспетчер установки в качестве домашней первичной. Для **HomeKitIntro** пример приложения, модальные представления был создан в конструкторе IOS и вызывается `AddHomeSegue` перехода от основной интерфейс приложения.

Он содержит текстовое поле для пользователю ввести имя нового дома и кнопка для добавления дома. Когда пользователь касается **добавить домашней** кнопки, следующий код вызывает домашней диспетчер, который необходимо добавить Домашняя страница:

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

`AddHome` Будет пытаться создать новый дом и вернуть его процедура обратного вызова данного метода. Если `error` свойство не является `null`, произошла ошибка, и он должен отображаться для пользователя. Наиболее распространенные ошибки вызываются неуникальное имя домашней или домашних Manager, не может взаимодействовать с HomeKit.

Если домашняя страница была создана успешно, необходимо вызвать `UpdatePrimaryHome` метод для установки нового дома в качестве домашней первичной. Опять же если `error` свойство не является `null`, произошла ошибка, и он должен отображаться для пользователя.

Необходимо также отслеживать Главная руководителя `DidAddHome` и `DidRemoveHome` события и обновления интерфейса пользователя приложения при необходимости.

> [!IMPORTANT]
> `AlertView.PresentOKAlert` Метод, используемый в приведенном выше примере — это вспомогательный класс, в приложении HomeKitIntro, которое упрощает работу с iOS оповещения проще.


## <a name="finding-new-accessories"></a>Стандартные новый поиск

После домом основного определения или загруженные из домашней Manager, можно вызвать приложение Xamarin.iOS `HMAccessoryBrowser` найти любые новые стандартные автоматизации дома и добавить их на домашнюю страницу.

Вызовите `StartSearchingForNewAccessories` метод, с которого начинается поиск новых стандартные и `StopSearchingForNewAccessories` метод при завершении обработки.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` не следует запускать за длительные периоды времени, поскольку это отрицательно сказаться на времени автономной работы и производительности устройства iOS. Apple предлагает вызова `StopSearchingForNewAccessories` после минуту или только поиска, когда поиск периферийного устройства представлен интерфейс пользователя для пользователя.

`DidFindNewAccessory` Событий будет вызываться, когда обнаруживаются новые стандартные, и они будут добавлены к `DiscoveredAccessories` списка в браузере периферийных устройств.

`DiscoveredAccessories` Список будет содержать коллекцию `HMAccessory` объекты, определяющие предоставленного HomeKit поддержкой автоматизации дома устройства и его доступных служб, таких как светодиодных индикаторов или управления дверцу обзоров.

После обнаружения новой периферийных устройств, он должен отображаться для пользователя и поэтому выберите его и добавить его на домашнюю страницу. Пример

[![](homekit-images/accessory01.png "Поиск нового вспомогательного компонента")](homekit-images/accessory01.png#lightbox)

Вызовите `AddAccessory` метод для добавления выбранного периферийного устройства домашнюю коллекцию. Пример:

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

Если `err` свойство не является `null`, произошла ошибка, и он должен отображаться для пользователя. В противном случае пользователь будет предложено ввести код установки добавить устройство:

[![](homekit-images/accessory02.png "Введите код настройки добавить устройство")](homekit-images/accessory02.png#lightbox)

В симуляторе периферийного устройства HomeKit этот номер можно найти в разделе **программный код установки** поля:

[![](homekit-images/accessory03.png "Поле код настройки в симуляторе периферийного устройства HomeKit")](homekit-images/accessory03.png#lightbox)

Для реальных стандартные HomeKit программный код установки либо печатаются на метку на самом устройстве, на странице продукта или в руководстве пользователя периферийных устройств.

Вы должны отслеживать аксессуаров браузера `DidRemoveNewAccessory` событий и обновления пользователь интерфейс для удаления периферийного устройства из списка доступных, когда пользователь добавил ее в свою коллекцию Главная.

## <a name="working-with-accessories"></a>Работа с "Стандартные"

Один раз в домашней основной и установления и к нему были добавлены "Стандартные", можно представить список стандартные (и, при необходимости, комнаты) для пользователя, для работы с.

`HMRoom` Объект содержит всю информацию о данной комнаты и любые стандартные, принадлежащих к ней. Комнаты, при необходимости могут быть организованы в одной или нескольких зон. Объект `HMZone` содержит всю информацию о данной зоны и все комнаты, принадлежащих к ней.

Для данного примера мы будем обеспечить вещей простой и работают с домашней стандартные напрямую, вместо их объединения в зале или зоны.

`HMHome` Объект содержит список назначенных периферийного устройства, которое будет представлено пользователю в его `Accessories` свойство. Пример:

[![](homekit-images/accessory04.png "Пример периферийного устройства")](homekit-images/accessory04.png#lightbox)

Здесь форме, пользователь может выбрать заданного аксессуаров и работать со службами, которые она предоставляет.

## <a name="working-with-services"></a>Работа со службами

Когда пользователь взаимодействует с определенным устройством HomeKit поддержкой автоматизации дома, обычно это происходит через службы, которые он предоставляет. `Services` Свойство `HMAccessory` класс содержит коллекцию `HMService` объектов, которые определяются службы, предлагает устройства.

Службы, индикаторы, термостатов, привилегией дверцу обзоров, коммутаторы и блокировки. Некоторые устройства (например, открытия дверцы обзоров) обеспечит больше одной службы, такие как источник света, а также возможность открыть или закрыть дверцу.

Помимо определенных служб, предоставляемых данной аксессуаров, содержит каждый периферийного устройства `Information Service` , определяющий свойства, такие как имя, изготовитель, модель и серийный номер.

### <a name="accessory-service-types"></a>Типы служб периферийных устройств

Доступны следующие типы службы `HMServiceType` перечисления:

- **AccessoryInformation** -предоставляет сведения об устройстве заданного автоматизации дома (периферийных устройств).
- **AirQualitySensor** -определяет качество air датчика.
- **Батарея** -определяет состояние батареи периферийных устройств.
- **CarbonDioxideSensor** -определяет скрытая газ датчика.
- **CarbonMonoxideSensor** -определяет угарного датчика.
- **ContactSensor** -определяет контактные датчика (например, окно, открытие или закрытие).
- **Дверца** -определяет датчик двери состояния (например, открыт или закрыт).
- **Вентилятор** -определяет удаленного управляемой вентилятора.
- **GarageDoorOpener** -определяет открытия дверцы обзоров.
- **HumiditySensor** -определяет влажности.
- **LeakSensor** -определяет датчик утечки (как и для нагреватель горячей воды или командует машины).
- **Лампочки** -определяет источник света отдельного или источника света, который является частью другого периферийного устройства (например, обзоров дверцу потребителям).
- **LightSensor** -определяет датчик освещения.
- **LockManagement** -определяет служба, которая управляет блокировку автоматических двери.
- **LockMechanism** -определяет удаленной управляемой блокировки (например, блокировки дверцы).
- **MotionSensor** -определяет датчик движения.
- **OccupancySensor** -определяет степень заполненности датчика.
- **Розетки** -определяет удаленного управляемой розетке.
- **SecuritySystem** -определяет систему безопасности домашних.
- **StatefulProgrammableSwitch** -определяет программируемых переключатель, который остается в состоянии предоставляют запускается один раз (например, переворачивающейся коммутатор).
- **StatelessProgrammableSwitch** -определяет программируемых переключатель, который возвращает в исходное состояние после активации (например, кнопки).
- **SmokeSensor** -определяет датчик дыма.
- **Переключение** -определяет Признак включения как переключатели standard по часам.
- **Датчик температуры** -определяет датчика температуры.
- **Термостат** -определяет Интеллектуальный термостат, используемые для управления при использовании системы Кондиционирования.
- **Окно** -определяет автоматических окно, что Карина удаленно открытия или закрытия.
- **WindowCovering** -определяет, удаленно управляемые охватывающих как жалюзи, которые можно открыть или закрыть окно.

### <a name="displaying-service-information"></a>Отображение сведений о службе

После загрузки `HMAccessory` можно запросить отдельные `HNService` объектов он предоставляет и отобразить эти сведения для пользователя:

[![](homekit-images/accessory05.png "Отображение сведений о службе")](homekit-images/accessory05.png#lightbox)

Необходимо всегда следует проверять `Reachable` свойство `HMAccessory` прежде чем работать с ним. Периферийного устройства может быть недоступен пользователь выходит за пределы диапазона устройства или если он не подключен.

После выбора службы пользователя можно просмотреть или изменить один или несколько характеристик этой службы для мониторинга или управления устройством заданного автоматизации дома.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Работа с характеристиками

Каждый `HMService` объект может содержать коллекцию `HMCharacteristic` объекты, которые можно указать сведения о состоянии службы (например, открытие или закрытие двери) или разрешить пользователю корректировать состояния (например, задание цвета освещения).

`HMCharacteristic` не только предоставляет сведения о характеристика и его состояние, но также предоставляет методы для работы с состоянием через _метаданных характеристика_ (`HMCharacteristisMetadata`). Эти метаданные может предоставлять свойства (например, минимальное и максимальное значение в диапазоне), могут быть полезны при отображения сведений для пользователя или разрешить их, для изменения состояния.

`HMCharacteristicType` Перечисление содержит набор значений метаданных характеристики, которые невозможно определить или изменить следующим образом:

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
 - currentPosition
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
 - Серийный номер
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

### <a name="working-with-a-characteristics-value"></a>Работа с характеристика значением

Чтобы гарантировать это приложение имеет последнее состояние данного характеристика, вызвать `ReadValue` метод `HMCharacteristic` класса. Если `err` свойство не является `null`, произошла ошибка, и он может или не будут отображаться для пользователя.

Эта характеристика `Value` свойство содержит текущее состояние заданного характеристика как `NSObject`и поэтому не может работать непосредственно в C#.

Для чтения значения, был добавлен следующий класс помощника **HomeKitIntro** пример приложения:

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

`NSObjectConverter` Используется каждый раз, когда приложению требуется считывать текущее состояние характеристика. Пример.

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

Приведенную выше строку преобразует значение в `float` который затем может быть использован в Xamarin C# кода.

Для изменения `HMCharacteristic`, вызовите его `WriteValue` метод и новое значение в `NSObject.FromObject` вызова. Пример.

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Если `err` свойство не является `null`, произошла ошибка, которая должна отображаться для пользователя.

### <a name="testing-characteristic-value-changes"></a>Тестирование изменений типичное значение

При работе с `HMCharacteristics` и имитации "Стандартные", изменения `Value` свойство можно отслеживать в симуляторе HomeKit периферийных устройств.

С помощью **HomeKitIntro** приложение в реальных iOS оборудования устройства, внесение изменений в это характеристика значение следует рассматривать в симуляторе периферийного устройства HomeKit практически мгновенно. Например изменение состояния источника света в приложении iOS:

[![](homekit-images/test01.png "Изменение состояния источника света в приложении iOS")](homekit-images/test01.png#lightbox)

Должны изменять состояние источника света в симуляторе HomeKit периферийных устройств. Если значение не изменяется, проверьте состояние сообщения об ошибке, при написании новых значений характеристик и убедитесь, что периферийного устройства по-прежнему доступен.

## <a name="advanced-homekit-features"></a>Возможности расширенной HomeKit

Статья описывает основные компоненты, необходимые для работы с HomeKit "Стандартные", в приложении Xamarin.iOS. Однако существуют некоторые дополнительные возможности HomeKit, не охваченных в этом введении:

- **Комнаты** -HomeKit включены стандартные могут при необходимости организованы в комнатах конечным пользователем. Это позволяет HomeKit присутствует стандартные способом, который облегчает пользователю понять и работать с. Дополнительные сведения о создании и обслуживании комнаты, см. в разделе Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) документации.
- **Зоны** -комнаты при необходимости могут быть организованы в зонах конечным пользователем. Зоны ссылается на коллекцию помещениях, пользователь может обрабатывать как единое целое. Например: быть установлен, Downstairs или подвала. Опять же это позволяет HomeKit для представления и работать с стандартные способом, который лучше всего подходит для конечного пользователя. Дополнительные сведения о создании и обслуживании зоны, см. в разделе Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) документации.
- **Действия и задает действие** -действия, изменить характеристики службы периферийных устройств и могут быть сгруппированы в наборы. Наборов действий в качестве сценарии для управления группой стандартные и координировать их действия. Например сценарий «Просмотра Телевизора» может закрыть сложность, свет и включите телевизора и звуковой системы. Дополнительные сведения о создании и обслуживании действий и наборов действий, см. в разделе Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) и [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) документации.
- **Триггеры** - триггер может активировать одно или дополнительные действия задается при заданные условия будут соблюдены. Например включите portch света и блокировать все внешние двери, если он возвращает темный за пределами. Дополнительные сведения о создании и обслуживании триггеры, см. в разделе Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) документации.

Так как эти функции используют те же методы, представленные выше, они должны быть легко добавить следующие Apple [руководство HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [рекомендации по пользовательскому интерфейсу HomeKit](https://developer.apple.com/homekit/ui-guidelines/) и [ Ссылка на платформу HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Рекомендации по оценке приложения HomeKit

Перед отправкой HomeKit включенного приложения Xamarin.iOS в iTunes Connect для выпуска в iTunes App Store, убедитесь, что выполнены требования Apple для приложений HomeKit включен:

 - Основное назначение приложения _необходимо_ быть автоматизации дома, при использовании HomeKit framework.
 - Маркетинговый текст приложения необходимо уведомить пользователей, что используется HomeKit и предоставлять политики конфиденциальности.
 - Сбор сведений о пользователе или использовании HomeKit рекламных целях запрещено.

Для полной просмотрите рекомендации, см. в разделе Apple [приложения Store просмотрите рекомендации](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>Новые возможности в iOS 9

Apple внес следующие изменения и дополнения в HomeKit для iOS 9:

- **Обслуживание существующих объектов** — при изменении существующего периферийного устройства диспетчера Home (`HMHomeManager`) появится соответствующее сообщение определенного элемента, который был изменен.
- **Постоянные идентификаторы** -теперь включают все соответствующие классы HomeKit `UniqueIdentifier` свойство для уникальной идентификации заданного элемента в HomeKit включено приложения (или экземпляров одного приложения).
- **Управление пользователями** -добавлен контроллер встроенные представления, для обеспечения управления пользователями через пользователей, имеющих доступ к устройствам HomeKit в домашней основного пользователя.
- **Возможности пользователя** - HomeKit пользователи теперь имеют привилегии, определяющие, какие функции они смогут использовать в HomeKit и HomeKit включены стандартные. В приложении только должна отображаться соответствующих возможностях для текущего пользователя. К примеру только администраторы должны иметь возможность поддерживать другим пользователям.
- **Предопределенные сцены** -стандартных сцен были созданы для четырех событий, возникающих для среднестатистического пользователя HomeKit: начать работу, оставьте, вернуть, перейдите в испытательной. Невозможно удалить эти предопределенные сцены из дома.
- **Сцен и Siri** -Siri имеет поддержку глубже сцен в iOS 9 и может распознать имя любого сцены, определенные в HomeKit. Пользователь может выполнить сцены просто голосом ее имя на Siri.
- **Аксессуаров категории** -набор стандартных категорий были добавлены все стандартные и помогает идентифицировать тип периферийного устройства, добавляемого в домашней или работали из в приложении. Эти новые категории доступны во время установки периферийных устройств.
- **Поддержка Apple Watch** - HomeKit теперь доступна для watchOS и Apple Watch будут иметь возможность управления устройствами с поддержкой HomeKit без iPhone, рядом с часами. HomeKit для watchOS поддерживает следующие возможности: просмотр домов, стандартные управление и выполнение сцены.
- **Новый тип триггера событий** — в дополнение к триггерах типа таймера, поддерживается в iOS 8, iOS 9 теперь поддерживает триггеры событий с учетом состояния периферийного устройства (например, данные датчиков) или географическом положении. Использовать триггеры событий `NSPredicates` для задания условий для их выполнения.
- **Удаленный доступ** -с помощью удаленного доступа, пользователь теперь соответствует возможность управления их HomeKit включена Главная стандартные службы автоматизации, находящихся за пределами дома в удаленном расположении. В iOS 8 это был поддерживается только если пользователь имеет в 3-го поколения Apple TV в доме. В iOS 9 это ограничение снято, и удаленный доступ поддерживается с помощью iCloud и HomeKit периферийного устройства протокола (получена строка HAP).
- **Новые возможности подключения Bluetooth низким энергопотреблением (BLE)** -HomeKit теперь поддерживает дополнительные типы периферийных устройств, которые могут взаимодействовать по протоколу подключения Bluetooth низким энергопотреблением (BLE). С помощью получена строка HAP безопасное туннелирование, аксессуаров HomeKit может по-прежнему предоставлять другим периферийного устройства Bluetooth по Wi-Fi (если он выходит за пределы диапазона Bluetooth). В iOS 9 стандартные BLE имеют полную поддержку уведомлений и метаданных.
- **Новые категории периферийного устройства** -Apple добавлены следующие новые категории периферийного устройства в iOS 9: Coverings окна, мотороллере двери и Windows, систем сигнализации, датчики и программируемые коммутаторов.

Дополнительные сведения о новых возможностях HomeKit в iOS 9 см. в разделе Apple [индекс HomeKit](https://developer.apple.com/homekit/) и [новые возможности в HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) видео.

## <a name="summary"></a>Сводка

В этой статье был представлен платформы автоматизации дома HomeKit Apple. Он показал, как установить и настроить тестовых устройств, использования симулятора HomeKit периферийных устройств и как создать простое приложение Xamarin.iOS для обнаружения, взаимодействовать и управления устройствами для домашней автоматизации с помощью HomeKit.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [Новые возможности в iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Руководство по HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Рекомендации по HomeKit пользовательского интерфейса](https://developer.apple.com/homekit/ui-guidelines/)
- [Ссылка на платформу HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
