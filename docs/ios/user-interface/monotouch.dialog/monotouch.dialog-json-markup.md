---
title: Разметка Json MonoTouch.Dialog
description: В этом документе описывается синтаксис JSON, который может использоваться для создания пользовательского интерфейса Xamarin.iOS с помощью MonoTouch.Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: d084094ab52e317fbb42f6b8c8c553d9d6158251
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106875"
---
# <a name="monotouchdialog-json-markup"></a>Разметка Json MonoTouch.Dialog

На этой странице описываются разметка Json, принят в MonoTouch.Dialog [JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

Сообщите нам, начните с примером. Ниже приведен полный файл Json, который может быть передан в JsonElement.

```csharp
{     
  "title": "Json Sample",
  "sections": [ 
      {
          "header": "Booleans",
          "footer": "Slider or image-based",
          "id": "first-section",
          "elements": [
              { 
                  "type" : "boolean",
                  "caption" : "Demo of a Boolean",
                  "value"   : true
              }, {
                  "type": "boolean",
                  "caption" : "Boolean using images",
                  "value"   : false,
                  "on"      : "favorite.png",
                  "off"     : "~/favorited.png"
              }, {
                      "type": "root",
                      "title": "Tap for nested controller",
                      "sections": [ {
                         "header": "Nested view!",
                         "elements": [
                           {
                             "type": "boolean",
                             "caption": "Just a boolean",
                             "id": "the-boolean",
                             "value": false
                           },
                           {
                             "type": "string",
                             "caption": "Welcome to the nested controller"
                           }
                         ]
                       }
                     ]
                   }
          ]
      }, {
          "header": "Entries",
          "elements" : [
              {
                  "type": "entry",
                  "caption": "Username",
                  "value": "",
                  "placeholder": "Your account username"
              }
          ]
      }
  ]
}
```

Приведенная выше разметка создает следующие пользовательского интерфейса:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "Пользовательскому Интерфейсу, созданному в данной разметке")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Каждый элемент в дереве может содержать свойство `"id"`. Вполне возможно, во время выполнения для ссылки на отдельные разделы или элементы, с помощью индексатора JsonElement. Пример:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Корневой элемент синтаксиса

Корневой элемент содержит следующие значения:

-  `title`
-  `sections` (необязательно)


Корневой элемент может использоваться в теле раздела как элемент для создания вложенных контроллера. В этом случае дополнительное свойство `"type"` должно иметь значение `"root"`

 <a name="url" />


### <a name="url"></a>url

Если `"url"` свойство имеет значение, если пользователь нажимает на этого RootElement, код будет запрашивать файл по указанному url и сделает содержимое новые сведения, отображаемые. Это можно использовать для создания расширения пользовательского интерфейса с сервера, в зависимости от того, пользователь касается.

 <a name="group" />


### <a name="group"></a>группа

Если задано, это задает groupname для корневого элемента. Имена групп используются для выбора сводки, отображается в качестве значения параметра корневой элемент из одного из вложенных элементов в элементе. Это значение флажка или значение типа "переключатель".

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Определяет элемент переключатель, который выбран в вложенных элементов

 <a name="title" />


### <a name="title"></a>заголовок

Если имеется, он будет заголовок, используемый для RootElement

 <a name="type" />


### <a name="type"></a>type

Должно быть присвоено `"root"` при это отображается в разделе (Это позволяет вложить контроллеров).

 <a name="sections" />


### <a name="sections"></a>разделы

Это массив Json с отдельными разделами

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Синтаксис раздела

Раздел содержит:

-  `header` (необязательно)
-  `footer` (необязательно)
-  Массив `elements`


 <a name="header" />


### <a name="header"></a>заголовок

Если он имеется, текст заголовка отображается как заголовок раздела.

 <a name="footer" />


### <a name="footer"></a>Нижний колонтитул

Если он имеется, нижний колонтитул отображается в нижней части раздела.

 <a name="elements" />


### <a name="elements"></a>элементы

Это массив элементов. Каждый элемент должен содержать по крайней мере один ключ `"type"` ключ, используемый для определения типа создаваемого элемента.
Некоторые элементы имеют некоторые общие свойства как `"caption"` и `"value"`. Это список поддерживаемых элементов:

-  `string` элементы (оба с и без стиля)
-  `entry` строки ("Обычный" или "пароль")
-  `boolean` значения (с помощью коммутаторов или изображения)


Элементы строки можно использовать как кнопки путем предоставления метода для вызова, когда пользователь нажимает на ячейки или периферийного устройства

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Подготовка к просмотру элементов

Элементы визуализации основаны на C# StringElement и StyledStringElement и их можно отображать информацию разными способами и можно визуализировать их разными способами. Самый простой элементы могут создаваться следующим образом:

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

При этом будут отображены простую строку с все значения по умолчанию: шрифт, фон, цвет текста и оформления. Можно подключить действия к этим элементам и сделать их ведут себя как кнопки, задав `"ontap"` свойство или `"onaccessorytap"` свойства:

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

Выше вызовет метод «ShowPhotos» в классе «Acme.PhotoLibrary». `"onaccessorytap"` Аналогичен предыдущему, но он будет вызван только если пользователь нажимает на аксессуары вместо коснувшись ячейки. Чтобы включить эту функцию, необходимо также задать периферийного устройства:

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Подготовка к просмотру элементов можно отобразить две строки за один раз, один заголовок и другое значение. О том, как подготавливаются к просмотру эти строки зависит от стиля, это можно сделать с помощью `"style"` свойство. Значение по умолчанию будет отображаться заголовок слева, а значение справа. Стиль для получения дополнительных сведений см. в разделе. Цвета кодируются с помощью символа «#», следуют шестнадцатеричные числа, представляющие значения красного, зеленого, синего и может быть альфа-значения. Краткая форма (3 или 4 шестнадцатеричных цифр), которое представляет значения RGB или RGBA можно закодировать содержимое. Или длинной форме (6 или 8 цифр), которые представляют значения RGB или RGBA. Сокращенная версия является сокращением для записи же шестнадцатеричную цифру дважды. Поэтому константа «#1bc» результате красный = 0x11, green = 0xbb и синий = 0xcc. Если отсутствует значение альфа, цвета непрозрачен. Ниже представлено несколько примеров.

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>Периферийных устройств

Определяет, какие периферийного устройства для отображения в элементе визуализации, возможными значениями являются:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Если значение не указано, отображается не периферийных устройств

 <a name="background" />


### <a name="background"></a>фон

Свойство background задает цвет фона для ячейки. Значение равно либо URL-адрес изображения (в этом случае загрузчик async изображение будет вызываться и фон будет обновлена после будет загружен образ) или это может быть цвет, указанный с помощью синтаксиса цвет.

 <a name="caption" />


### <a name="caption"></a>Заголовок

Строки, отображаемый в элементе визуализации основной. Шрифт и цвет можно настроить, задав `"textcolor"` и `"font"` свойства. Стиль отрисовки определяется `"style"` свойство.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>цвет и detailcolor

Цвет, используемый для основного текста или подробный текст.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>detailfont и шрифт

Шрифт, используемый для заголовок или текст сведений. Формат спецификации шрифта является имя шрифта, при необходимости следуют дефис и размера.
Ниже приведены спецификации шрифтов.

-  «Helvetica»
-  «Helvetica-14»


 <a name="linebreak" />


### <a name="linebreak"></a>LineBreak

Определяет, как разбить строки. Допустимые значения:

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Оба `character-wrap` и `word-wrap` может использоваться вместе с `"lines"` равным нулю, чтобы включить элемент отрисовки в многострочный элемент.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>OnTap и onaccessorytap

Эти свойства должно указывать имя статического метода в приложении, который принимает объект в качестве параметра. При создании иерархии с помощью методов JsonDialog.FromFile или JsonDialog.FromJson можно передать значение необязательно объекта. Значение этого объекта затем передается в методы. Это можно использовать, чтобы передать некоторый контекст статическому методу. Пример:

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />


### <a name="lines"></a>линии

Если это равно нулю, это сделает Авторазмер элемент зависимости от строк, содержащихся содержимого. Чтобы это работало, необходимо также задать `"linebreak"` свойства `"character-wrap"` или `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>стиль

Стиль определяет, какие стиль ячейки, который будет использоваться для отображения содержимого и они соответствуют значениям перечисления UITableViewCellStyle.
Допустимые значения:

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : текст с подзаголовок.


 <a name="subtitle" />


### <a name="subtitle"></a>Подзаголовок

Значение, используемое для подзаголовка. Это ярлык для установки стиля `"subtitle"` и задайте `"value"` строковое значение.
Это делает с одна запись.

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

Цвет, используемый для текста.

 <a name="value" />


### <a name="value"></a>value

Вторичное значение будет отображаться в элементе визуализации. Макет этого зависит от `"style"` параметр. Шрифт и цвет можно настроить, задав `"detailfont"` и `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Логическими элементами

Логическими элементами следует выбрать тип `"bool"`, может содержать `"caption"` для отображения и `"value"` задано значение true или false. Если `"on"` и `"off"` свойств, они считаются образов. Образы разрешаются относительно текущего рабочего каталога в приложении. Если вы хотите ссылаться на файлы относительно пакета, можно использовать `"~"` как ярлык для представления каталога пакета приложения. Например `"~/favorite.png"` будет favorite.png, содержащийся в файле пакета. Пример:

```csharp
{ 
    "type" : "boolean",
    "caption" : "Demo of a Boolean",
    "value"   : true
},

{
    "type": "boolean",
    "caption" : "Boolean using images",
    "value"   : false,
    "on"      : "favorite.png",
    "off"     : "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>type

Тип можно задать либо `"boolean"` или `"checkbox"`. Если присвоено логическое значение будет использоваться UISlider или изображений (если оба `"on"` и `"off"` заданы). Если значение checkbox, будет использоваться флажок. `"group"` Свойство может использоваться для маркировки логический элемент как относящиеся к определенной группе. Это полезно в том случае, если содержащая корневой также имеет `"group"` свойство в качестве привилегированного пользователя будет обобщите результаты с количеством все логические значения (или флажки), которые принадлежат к одной группе.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Элементы записи

Чтобы разрешить пользователю вводить данные, используйте элементы записи. Используется тип для элементов записи `"entry"` или `"password"`. `"caption"` Задано значение текста для отображения справа и `"value"` присваивается начальное значение присвоено значение записи. `"placeholder"` Используется для отображения подсказку пользователю для пустой записи (он показан затенены). Далее приводятся некоторые примеры.

```csharp
{
        "type": "entry",
        "caption": "Username",
        "value": "",
        "placeholder": "Your account username"
}, {
        "type": "password",
        "caption": "Password",
        "value": "",
        "placeholder": "You password"
}, {
        "type": "entry",
        "caption": "Zip Code",
        "value": "01010",
        "placeholder": "your zip code",
        "keyboard": "numbers"
}, {
        "type": "entry",
        "return-key": "route",
        "caption": "Entry with 'route'",
        "placeholder": "captialization all + no corrections",
        "capitalization": "all",
        "autocorrect": "no"
}
```

 <a name="autocorrect" />


### <a name="autocorrect"></a>автозамены

Определяет стиль автоматического исправления, используемый для записи. Возможные значения: true или false (или строки `"yes"` и `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>написание прописными буквами

Чтобы использовать для записи задан стиль регистра символов. Допустимые значения:

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>Заголовок

Заголовок, используемый для ввода

 <a name="keyboard" />


### <a name="keyboard"></a>клавиатура

Тип клавиатуры, используемый для ввода данных. Допустимые значения:

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>Заполнитель

Пояснительный текст, который отображается, если операция имеет пустое значение.

 <a name="return-key" />


### <a name="return-key"></a>ключ Return

Метка, используемая для возвращаемого значения ключа. Допустимые значения:

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>value

Начальное значение для записи

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>Элементы переключателей

Переключатель элементы имеют тип `"radio"`. Элемент выбран, выбранные платформой `radioselected` свойство в содержащий его элемент корневой.
Кроме того если оно задано для `"group"` принадлежит свойство, данный переключатель в эту группу.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Дата и время элементы

Типы элементов `"datetime"`, `"date"` и `"time"` используются для отображения даты с временем, значения даты или времени. Эти элементы принимают в качестве параметров заголовок и значение. Значения могут записываться в любом формате, поддерживаемом функцией .NET DateTime.Parse. Пример

```csharp
"header": "Dates and Times",
"elements": [
        {
                "type": "datetime",
                "caption": "Date and Time",
                "value": "Sat, 01 Nov 2008 19:35:00 GMT"
        }, {
                "type": "date",
                "caption": "Date",
                "value": "10/10"
        }, {
                "type": "time",
                "caption": "Time",
                "value": "11:23"
                }                       
]
```

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>HTML и веб-элемент

Можно создать ячейки, шифрованию будет внедрена UIWebView, который выполняет визуализацию содержимого указанный URL-адрес, локальных или удаленных с помощью `"html"` типа. Только два свойства для данного элемента, `"caption"` и `"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
