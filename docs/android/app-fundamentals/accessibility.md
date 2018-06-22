---
title: Специальные возможности на Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 2a49d15651b8c6ab7417a69d934af5d20bfc13d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763908"
---
# <a name="accessibility-on-android"></a>Специальные возможности на Android

На этой странице описаны способы использования интерфейсов API Android специальных возможностей для создания приложений в соответствии с [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md).
Ссылаться на [специальных операций ввода-вывода](~/ios/app-fundamentals/accessibility.md) и [специальных возможностей OS X](~/mac/app-fundamentals/accessibility.md) страницы для других API платформы.


## <a name="describing-ui-elements"></a>Описание элементов пользовательского интерфейса

Android предоставляет `ContentDescription` свойство, используемое с экрана при чтении API-интерфейсы для предоставления доступное описание назначения элемента управления.

Можно задать описание содержимого, в языке C#, или в файле AXML макета.

**C#**

Описание можно задать в коде, чтобы любая строка (или строковый ресурс).

```csharp
saveButton.ContentDescription = "Save data";
```

**AXML макета**

В XML с помощью макетов `android:contentDescription` атрибута:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Использовать подсказку для TextView

Для `EditText` и `TextView` использовать элементы управления для ввода данных `Hint` свойство, чтобы предоставить описания ожидается, что входные данные (вместо `ContentDescription`).
При вводе текст самого текста «читается» вместо подсказка.

**C#**

Задать `Hint` свойства в коде:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML макета**

В XML-файлы макета используют `android:hint` атрибута:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>Поля с метками ввода LabelFor ссылки

Чтобы связать метку с элементом управления ввода данных, используйте `LabelFor` свойства

**C#**

В C#, задайте `LabelFor` описывает свойство с Идентификатором ресурса этом это содержимое элемента управления (обычно это свойство задается на метку и ссылается на входной других элементов управления):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML макета**

В формате XML используйте `android:labelFor` свойства для идентификатора другого элемента управления ссылки:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Объявления для специальных возможностей

Используйте `AnnounceForAccessibility` метод для какого-либо просмотреть взаимодействия событие или состояние изменений для пользователей при включении специальных возможностей элемента управления. Этот метод не требуется для большинства операций встроенных сопровождения предоставляет достаточно обратной связи, когда следует использовать, когда Дополнительные сведения могут быть полезными для пользователя.

В следующем примере кода показан простой пример вызывающую `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Изменение параметров фокус

Доступны навигации зависит от элементов управления, имеющий фокус, чтобы помочь пользователю понять, какие операции доступны. Android предоставляет `Focusable` свойство, которое можно пометить все элементы управления, в частности возможность получать фокус во время перехода.

**C#**

Чтобы предотвратить элементе управления фокус ввода с помощью C#, задайте `Focusable` свойства `false`:

```csharp
label.Focusable = false;
```

**AXML макета**

В формате XML-файлы набор `android:focusable` атрибута:

```xml
<android:focusable="false" />
```

Также можно управлять порядком фокус с `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` атрибутов, обычно устанавливается в макете AXML. Используйте эти атрибуты, чтобы убедиться, что пользователь может легко перемещаться по элементам управления на экране.


## <a name="accessibility-and-localization"></a>Специальные возможности и локализация

В приведенных выше примерах, являются описание подсказки и содержимое набор непосредственно отображаемого значения. Предпочтительнее использовать значения в **Strings.xml** файла, например это:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Использование текста из файла строк ниже приводится в C# и AXML макет файлы.

**C#**

Вместо строковых литералов в коде, поиск переведенные значения из строки файлов с `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

В XML-ФАЙЛ макета атрибуты специальных возможностей, таких как `hint` и `contentDescription` можно задать идентификатор строки:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

Преимущество сохранения текста в отдельном файле заключается в том несколько языковых переводах файла могут быть предоставлены в вашем приложении. . В разделе [руководство по локализации для Android](~/android/app-fundamentals/localization.md) чтобы узнать, как добавить локализованные строки файлы проекта приложения.


## <a name="testing-accessibility"></a>Тестирование специальных возможностей

Выполните [эти действия](http://developer.android.com/training/accessibility/testing.html#how-to) для включения TalkBack и проводник по сенсорного ввода для проверки специальных возможностей на устройствах Android.

Может потребоваться установить [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) из Google Play, если он не отображается в **параметры > специальных возможностей**.


## <a name="related-links"></a>Связанные ссылки

- [Кросс платформенных специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md)
- [Интерфейсы API Android специальных возможностей](http://developer.android.com/guide/topics/ui/accessibility/index.html)
