---
title: Контакты и ContactsUI в Xamarin.iOS
description: В этой статье рассматривается работа с новые контакты и контакты пользовательского интерфейса платформы приложения Xamarin.iOS. Эти платформы замените существующий адресной книги и пользовательский Интерфейс книги адрес, используемый в предыдущих версиях iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: e3f1533605d08df58d8d257714dd8135690c0e5d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105458"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Контакты и ContactsUI в Xamarin.iOS

_В этой статье рассматривается работа с новые контакты и контакты пользовательского интерфейса платформы приложения Xamarin.iOS. Эти платформы замените существующий адресной книги и пользовательский Интерфейс книги адрес, используемый в предыдущих версиях iOS._

С появлением iOS 9 Apple выпустила два новых платформ `Contacts` и `ContactsUI`, что замена в существующих адресной книге и инфраструктур пользовательского интерфейса адресной книги, используемые iOS 8 и более ранних версий.

Два новых платформ содержат следующие возможности:

- [**Контакты** ](#contacts) -предоставляет доступ к данным списка контактов пользователя.
    Так как большинство приложений требуется только доступ только для чтения, эта инфраструктура была оптимизирована для доступа потоками безопасно, только для чтения.

- [**ContactsUI** ](#contactsui) -предоставляет Xamarin.iOS элементы пользовательского интерфейса для отображения, редактирования выберите и создайте контактам на устройствах iOS.

[![](contacts-images/add01.png "Пример контакт лист на устройстве iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Существующий `AddressBook` и `AddressBookUI` платформ используется iOS 8 (и более ранних) стали нерекомендуемыми в iOS 9 и должны быть заменены с новым `Contacts` и `ContactsUI` платформ, как можно скорее для любого существующего приложения Xamarin.iOS. Новые приложения должны работать с новых платформ.




В следующих разделах мы рассмотрим эти новых платформ и способы их реализации в приложении Xamarin.iOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>Платформа контактов

Контакты Framework предоставляет доступ Xamarin.iOS к контактные данные пользователя. Так как большинство приложений требуется только доступ только для чтения, эта инфраструктура была оптимизирована для доступа потоками безопасно, только для чтения.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Объекты-контакты

`CNContact` Класс предоставляет доступ безопасно, только для чтения поток, для свойства контакта, такие как имена, адреса и номера телефонов. `CNContact` функции, например `NSDictionary` и содержит несколько коллекций только для чтения свойств (таких как адреса или номера телефонов):

[![](contacts-images/contactobjects.png "Общие сведения о контактных объектов")](contacts-images/contactobjects.png#lightbox)

Любое свойство, которое может иметь несколько значений (например, адрес электронной почты адрес или номер телефона), он будет представлен как массив `NSLabeledValue` объектов. `NSLabeledValue` — Это поток безопасном кортеж, состоящий из набора только для чтения метки и значения, где метка Определяет значение для пользователя (например домашней или рабочей электронной почты). Контакты framework предоставляет ряд предварительно определенных меток (через `CNLabelKey` и `CNLabelPhoneNumberKey` статические классы), вы можете использовать в приложении, или у вас есть возможность определить пользовательские метки для ваших потребностей.

Любое приложение Xamarin.iOS, которое необходимо настроить значения существующего контакта (или создавать новые), используйте `NSMutableContact` версии класса и его вложенных классов (таких как `CNMutablePostalAddress`).

Например следующий код создания контакта и добавить его в коллекцию пользователей, контактов:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Если этот код выполняется на устройстве iOS 9, контакт будут добавляться в коллекцию пользователей. Пример:

[![](contacts-images/add01.png "Добавить коллекцию пользователя контакт")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Контактные форматирования и локализации

Контакты framework содержит несколько объектов и методов, которые помогут вам форматирования и локализации содержимого для отображения пользователю. Например следующий код будет правильно отформатировать имя контактов и почтовый адрес для отображения:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Свойство метки, которые будут отображаться в пользовательском Интерфейсе приложения framework контакт имеет методы для локализации также эти строки. Опять же это зависит от текущего языкового стандарта, приложение запускается на устройства iOS. Пример:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Получение существующих контактов

С помощью экземпляра `CNContactStore` класса, может получать контактные данные из базы данных контактов пользователя. `CNContactStore` Содержит все методы, необходимые для выборку или обновление, контакты и группы из базы данных. Так как эти методы являются синхронными, рекомендуется запускать их в фоновом потоке для предотвращения блокировки интерфейса.

С помощью предикатов (на основе `CNContact` класс), можно отфильтровать результаты, возвращенные при получении контакты из базы данных. Чтобы получить только те контакты, которые содержат строку `Appleseed`, используйте следующий код:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Универсальные и составное предикаты не поддерживаются платформой контактов.

Например, чтобы ограничить выборку только **GivenName** и **FamilyName** свойства контакта, используйте следующий код:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Наконец чтобы найти в базе данных и возвращают результаты, используйте следующий код:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Если этот код выполняется после с примером, мы создали в **объект контакты** предыдущем разделе, будет возвращен контакта «Джон Appleseed», который мы только что создали.

### <a name="contact-access-privacy"></a>Конфиденциальность контактные доступа

Поскольку конечным пользователям можно разрешить или запретить доступ к его контактные данные на основе каждого приложения, первый раз вам позвонить `CNContactStore`, появится диалоговое окно, попросите его разрешить доступ для вашего приложения.

Запрос на разрешение будет появляться только один раз, в первый раз, приложение и последующих выполняется или вызовы `CNContactStore` будет использовать разрешения, которую пользователь выбрал в это время.

Приложения следует разрабатывать таким образом, он правильно обрабатывал запрет на доступ к их базы данных контактов пользователя.

#### <a name="fetching-partial-contacts"></a>Получение частичной контактов

Объект _частичного обратитесь к_ — это только часть доступных свойств будут выбраны из хранилища контактов для контакта. При попытке доступа к свойству, не были выбраны ранее, он приведет к исключению.

Можно легко проверить, см. в разделе, если данный контакт содержит требуемое свойство либо при помощи `IsKeyAvailable` или `AreKeysAvailable` методы `CNContact` экземпляра. Пример:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> `GetUnifiedContact` И `GetUnifiedContacts` методы `CNContactStore` класс _только_ возвращают частичного ограничен свойства, запрашиваемые из выборки ключах, предоставленных контакт.

### <a name="unified-contacts"></a>Унифицированный контактов

Пользователь может иметь различные источники контактные данные для одного человека в их базы данных контактов (например, iCloud, Facebook или Google Mail). В приложения для iOS и OS X, эти данные будут автоматически связаны друг с другом и отображаться пользователю как одно _единой обратитесь в службу_:

[![](contacts-images/unified01.png "Общие сведения о единой контактов")](contacts-images/unified01.png#lightbox)

Единая свяжитесь — это временный, находящееся в памяти представление ссылку контактных данных, получает свой собственный уникальный идентификатор (который должен использоваться к повторному получению контакта, при необходимости). По умолчанию платформа контактов возвращает единой контакта по возможности.

### <a name="creating-and-updating-contacts"></a>Создание и обновление контактов

Как мы видели в [обратитесь в службу объекты](#Contact_Objects) предыдущем разделе, можно использовать `CNContactStore` и экземпляром `CNMutableContact` для создания новых контактов, которые затем записываются в пользователя обратитесь в службу базы данных с помощью `CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

Объект `CNSaveRequest` также можно использовать для кэширования несколько контактов и группы изменений в одной операции и пакетных изменений `CNContactStore`.

Чтобы обновить неизменяемое контакт, полученный из операции выборки, сначала нужно запросить копию изменяемый, изменить и сохранить в хранилище контактов. Пример:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Уведомления об изменениях контакта

При изменении контакт Store контакт учитывает `CNContactStoreDidChangeNotification` к центру уведомлений по умолчанию. Если они в кэше или отображаются все контакты, необходимо обновить эти объекты из Store контакт (`CNContactStore`).

### <a name="containers-and-groups"></a>Контейнеры и группы

Контакты пользователя может существовать на устройстве пользователя или как контакты, синхронизированные на устройство из одного или нескольких учетных записей сервера (например, Google или Facebook). Каждый пул контактов имеет свой собственный _контейнера_ и заданного контакта может существовать только в одном контейнере.

[![](contacts-images/containers01.png "Общие сведения о контейнерах и групп")](contacts-images/containers01.png#lightbox)

Некоторые контейнеры создают возможность для контактов, упорядочить в один или несколько _группы_ или _подгрупп_. Это поведение зависит от резервного хранилища для заданного контейнера. Например имеет только один контейнер iCloud, но может иметь несколько групп (но не вложенные группы). С другой стороны, Microsoft Exchange не поддерживает группы, но может иметь несколько контейнеров (по одному для каждой папки Exchange).

[![](contacts-images/containers02.png "Перекрываться в пределах контейнеры и группы")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>Платформа ContactsUI

В ситуациях, где приложения не требуется пользовательский интерфейс ContactsUI framework можно использовать для представления элементов пользовательского интерфейса для отображения, редактирования, выберите и создайте контактов в приложении Xamarin.iOS.

Использование встроенных элементов управления Apple позволяет не только сократить объем кода, который необходимо создать для поддержки контактов в приложении Xamarin.iOS, но представить согласованный интерфейс для пользователей приложений.

### <a name="the-contact-picker-view-controller"></a>Контроллер представления средство выбора контактов

Контроллер представления выбора контакта (`CNContactPickerViewController`) управляет стандартное представление выбора контактов, позволяющий пользователю выбрать контакт или контакт свойства из базы данных контактов пользователя. Пользователь может выбрать один или несколько контактов, (с учетом его использования) и обратитесь в службу контроллера представления выбора не запрашивает разрешение перед отображением окна выбора.

Перед вызовом метода `CNContactPickerViewController` класса, определяют свойства, которые пользователь может выбрать и определить предикаты для управления отображением и Выбор свойств контакта.

Использовать экземпляр класса, который наследует от `CNContactPickerDelegate` реагировать на взаимодействие пользователя с помощью средства выбора. Пример:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Чтобы разрешить пользователю выбрать адрес электронной почты от контактов в их базе данных, можно использовать следующий код:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>Контроллер представления контактов

Контроллер представления контакт (`CNContactViewController`) класс предоставляет контроллер для представления стандартное представление "Контакт" для конечного пользователя. В представлении контакт можно отображать новые контакты New "," Unknown "или" существующая и должен быть указан тип, до отображения представления путем вызова правильного статический конструктор (`FromNewContact`, `FromUnknownContact`, `FromContact`). Пример.

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с платформами контакт и контакт пользовательского интерфейса в приложении Xamarin.iOS. Сначала рассматриваются различные типы объектов, которые предоставляет платформа контакта и как их использовать для создания новых или доступа к существующих контактов. Он также проверяет инфраструктуры обратитесь в службу пользовательского интерфейса для выбора существующих контактов и отображения контактных данных.


## <a name="related-links"></a>Связанные ссылки

- [Пример QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [Новые возможности в iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Ссылка на платформу контактов](https://developer.apple.com/documentation/contacts?language=objc)
- [Ссылка на платформу ContactsUI](https://developer.apple.com/documentation/contactsui?language=objc)
