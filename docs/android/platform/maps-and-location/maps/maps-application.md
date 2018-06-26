---
title: Запуск приложения сопоставления
description: Как запустить приложение встроенные карты из Xamarin.Android приложения.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: d15b6e544f58f03272c711236b579ca568e09539
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935026"
---
# <a name="launching-the-maps-application"></a>Запуск приложения сопоставления

Самый простой способ работы с картами Xamarin.Android заключается в возможности использования приложения встроенные карты, показано ниже:

[![Снимок экрана: пример встроенные карты Google приложения](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

При использовании приложения maps карты не будет частью приложения. Вместо этого запустите приложение карты и загрузить сопоставление извне приложения. Ниже рассматривается Xamarin.Android можно использовать для запуска maps, аналогичный приведенному выше.


## <a name="creating-the-intent"></a>Создание, назначение

Работа с картами, так же легко, как создание целью с соответствующим URI, при выборе режима ActionView и вызов метода StartActivity приложения. Например следующий код запускает приложение карты по центру в заданной широты и долготы:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Данный пример кода является все, что требуется для запуска карты, показанный на предыдущем снимке экрана. Помимо указания широты и долготы, схему URI для карт поддерживает несколько параметров.


## <a name="geo-uri-scheme"></a>Схема Geo URI

Приведенный выше код geo схемы используется для создания URI. Эта схема URI поддерживает несколько форматов, указанных ниже:

-   `geo:latitude,longitude` &ndash; Открывает центр в lat/lon приложение карты. 

-   `geo:latitude,longitude?z=zoom` &ndash; Открытие карты центр в lat/lon приложения, а в масштабе заданного уровня. Уровень масштаба в диапазоне от 1 до 23: 1 экранов весь земной поверхности и 23 является ближайшим уровень масштаба.

-   `geo:0,0?q=my+street+address` &ndash; Открывает приложение maps нужный почтовый адрес. 

-   `geo:0,0?q=business+near+city` &ndash; Открывает приложение карты и отображает результаты поиска заметками. 


Версии URI, принимающие запросов (а именно почтовый адрес или поиска условиями) использовать Google geocoder службы для извлечения в расположение, которое отображается на карте. Например, URI `geo:0,0?q=coop+Cambridge` приводит к схеме, показано ниже:

[![Снимок экрана примера Отображение карты Google с условия поиска](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Дополнительные сведения о geo схем URI. в разделе [Показать на карте расположение](http://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Улица представления

В дополнение к схеме geo Android поддерживает загрузки улицы представления с целью. Ниже приведен пример улицы представления приложения, запускаемого из Xamarin.Android:

[![Снимок экрана примера улицы представления](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Чтобы открыть представление данных улицы, используйте `google.streetview` схема URI, как показано в следующем коде:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Схема URI google.streetview выше имеет следующий вид:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Как видите, существует несколько параметров, которые поддерживаются, указанных ниже:

-   `lat` &ndash; Широта расположение, которое будет отображаться в представлении данных улицы.

-   `lng` &ndash; Долгота расположение, которое будет отображаться в представлении данных улицы.

-   `pitch` &ndash; Угол панорамы улицы вид, от центра в градусах, где прямых вниз на 90 градусов и -90 градусов — вверх.

-   `yaw` &ndash; Центр представления из панорамы улицы представления по часовой стрелке измеряется в градусах из Северной.

-   `zoom` &ndash; Увеличить коэффициент панорамы улицы представление, где 1.0 = Обычный масштаб, 2.0 = увеличенной 2 x 3.0 = увеличенной 4 x и т. д.

-   `mz` &ndash; Уровень масштаба карты, которая будет использоваться при переходе к maps приложения в представлении данных улицы.


Работа с встроенной схемы приложения или представления, улица простой способ быстро добавить поддержку сопоставления. Тем не менее Android Maps API предлагает более точно контролировать процесс сопоставления.
