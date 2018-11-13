---
title: Специальные возможности на Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 3cce3270b9df2aad0037b1ab96f169cc4b564766
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528134"
---
# <a name="accessibility-on-android"></a>Специальные возможности на Android

Эта страница описывает, как использовать API Android специальных возможностей для создания приложений в соответствии с [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md).
Ссылаться на [специальных возможностей iOS](~/ios/app-fundamentals/accessibility.md) и [специальных возможностей OS X](~/mac/app-fundamentals/accessibility.md) страниц для других API платформы.


## <a name="describing-ui-elements"></a>Описание элементов пользовательского интерфейса

Android предоставляет `ContentDescription` свойство, которое используется API-интерфейсы чтения с экрана для предоставления доступное описание назначения элемента управления.

Описание содержимого можно задать в среде C# или в файле макета AXML.

**C#**

Описание можно задать в коде, чтобы любая строка (или строковый ресурс):

```csharp
saveButton.ContentDescription = "Save data";
```

**Макета AXML**

В XML с помощью макетов `android:contentDescription` атрибут:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Использовать подсказку для TextView

Для `EditText` и `TextView` использовать элементы управления для ввода данных, `Hint` свойство, чтобы предоставить описание ожидается указываем входные данные (а не `ContentDescription`).
После введения какой-либо текст, сам текст «читается» вместо подсказка.

**C#**

Задать `Hint` свойства в коде:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Макета AXML**

В XML-файлы макета используют `android:hint` атрибут:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>Поля с метками ввода LabelFor ссылки

Чтобы связать метку с элемент управления для ввода данных, используйте `LabelFor` свойства

**C#**

В C#, задайте `LabelFor` присваивается идентификатор ресурса элемента управления, который описывает это содержимое (обычно это свойство задано для метки и ссылается на входной другого элемента управления):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Макета AXML**

В макете использования в формате XML `android:labelFor` свойство для ссылки на идентификатор другого элемента управления:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Объявления для объекта специальных возможностей

Используйте `AnnounceForAccessibility` метод для какого-либо просмотреть взаимодействия событие или состояние изменений для пользователей, при включении специальных возможностей элемента управления. Этот метод не требуется для большинства операций, встроенные записи позволяют недостаточно узнавать, когда следует использовать, где может быть полезно для пользователя дополнительную информацию.

В следующем коде показан простой пример вызывающую `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Изменение параметров фокус

Доступны Навигация зависит от элементов управления фокус, чтобы помочь пользователю понять, какие операции доступны. Android предоставляет `Focusable` свойство, которое можно пометить элементы управления, в частности возможность получать фокус во время перехода.

**C#**

Чтобы предотвратить получение фокуса с элемента управления C#, задайте `Focusable` свойства `false`:

```csharp
label.Focusable = false;
```

**Макета AXML**

В формате XML-файлы набора `android:focusable` атрибут:

```xml
<android:focusable="false" />
```

Можно также контролировать порядок фокуса с `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` атрибутов, обычно задается в макета AXML. Используйте эти атрибуты, чтобы убедиться, что пользователь может легко перемещаться по элементам управления на экране.


## <a name="accessibility-and-localization"></a>Локализации и специальных возможностей

В приведенных выше примерах, описание подсказки и содержимое набор непосредственно отображаемого значения. Желательно использовать значения в **Strings.xml** файла, например это:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

С помощью текста из файла строк ниже приведен в C# и файлы макета AXML:

**C#**

Вместо использования строковых литералов в коде, поиска переведенных значений из строки файлов с помощью `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

В формате XML, такие как атрибуты специальных возможностей `hint` и `contentDescription` можно присвоить идентификатор строки:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

В приложении можно указать несколько переводов язык файла является преимуществом сохранения текста в отдельном файле. См. в разделе [руководство по локализации для Android](~/android/app-fundamentals/localization.md) дополнительные как добавить файлы локализованных строк в проекте приложения.


## <a name="testing-accessibility"></a>Тестирование специальных возможностей

Выполните [эти действия](http://developer.android.com/training/accessibility/testing.html#how-to) для включения talkback; "и" Проводник по сенсорного ввода для проверки специальных возможностей на устройствах Android.

Может потребоваться установить [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) из Google Play, если он не отображается в **параметры > Специальные возможности**.


## <a name="related-links"></a>Связанные ссылки

- [Кросс платформенных специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md)
- [Интерфейсы API Android специальных возможностей](http://developer.android.com/guide/topics/ui/accessibility/index.html)
