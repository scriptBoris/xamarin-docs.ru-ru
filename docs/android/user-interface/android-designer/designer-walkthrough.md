---
title: С помощью конструктора Android
description: В этом разделе приведен пример Xamarin.Android конструктора. Демонстрирует создание пользовательского интерфейса для приложения браузера небольшие цветные; Этот пользовательский интерфейс создается полностью в конструкторе.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 8d1dc410d5336d9c2505a18720cc7f734e838c39
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798636"
---
# <a name="using-the-android-designer"></a>С помощью конструктора Android

_В этом разделе приведен пример Xamarin.Android конструктора. Демонстрирует создание пользовательского интерфейса для приложения браузера небольшие цветные; Этот пользовательский интерфейс создается полностью в конструкторе._


## <a name="overview"></a>Обзор

Android пользовательские интерфейсы могут создаваться декларативно с помощью XML-файлов или программным путем написания кода. Конструктор Xamarin.Android позволяет разработчикам создавать и изменять декларативный макеты визуально, без необходимости работать с трудоемкость ручного редактирования XML-файлов. Конструктор также предоставляет в реальном времени обратной связи, который позволяет разработчикам оценить изменения пользовательского интерфейса без необходимости повторного развертывания приложения на устройстве или эмуляторе. Это может ускорить разработку пользовательского интерфейса Android невероятно. В этой статье представлено пошаговое руководство, которое показывает, как использовать конструктор Xamarin.Android для визуального создания пользовательского интерфейса.


## <a name="walkthrough"></a>Пошаговое руководство

Цель данного руководства является использование конструктора Android для создания пользовательского интерфейса для приложения браузера примере цвет, выводит список цветов, их имена и их значения RGB. Мы рассмотрим способы добавления мини-приложения в рабочую область конструирования, а также как визуально создавать эти мини-приложения. После этого объясняется, как изменить мини-приложения интерактивном режиме в рабочей области конструктора или с помощью конструктора **Pad свойство**. Наконец мы рассмотрим, как будет выглядеть наш разработки при запуске приложения.

Давайте начнем!


### <a name="creating-a-new-project"></a>Создание нового проекта

Первым шагом является создание нового проекта Xamarin.Android.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Запустите Visual Studio и нажмите кнопку **новый проект...**  выберите **Visual C\# > Android > приложения Android (Xamarin)** шаблона:

[![Пустое приложение Android](designer-walkthrough-images/vs/01-android-app-sml.w157.png)](designer-walkthrough-images/vs/01-android-app.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Запустите Visual Studio для Mac и нажмите кнопку **новое решение...** . Выберите **приложения Android** шаблона и нажмите кнопку **Далее**:

[![Пустое приложение Android](designer-walkthrough-images/xs/01-android-app-sml.png)](designer-walkthrough-images/xs/01-android-app.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Название нового приложения **DesignerWalkthrough** и нажмите кнопку **ОК**.

[![Имя приложения](designer-walkthrough-images/vs/02-name-app-sml.png)](designer-walkthrough-images/vs/02-name-app.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Название нового приложения **DesignerWalkthrough**. В разделе **целевых платформ**выберите **самые актуальные и наибольший** и нажмите кнопку **Далее**:

[![Имя приложения](designer-walkthrough-images/xs/02-designer-walkthrough-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough.png#lightbox)

На следующем экране диалоговое окно, нажмите кнопку **создать**.

-----



### <a name="adding-a-layout"></a>Добавление макета

Давайте создадим **LinearLayout** мы воспользуемся для хранения элементов интерфейса нашей пользователя.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

В Visual Studio щелкните правой кнопкой мыши **ресурсы и макет** в **обозревателе решений** и выберите **Добавить > новый элемент...** . В **Добавление нового элемента** диалогового окна выберите **Android макета**. Назовите файл **ListItem.axml** и нажмите кнопку **добавить**:

[![Новый макет](designer-walkthrough-images/vs/03-new-layout-sml.w157.png)](designer-walkthrough-images/vs/03-new-layout.w157.png#lightbox)

Новый **ListItem** макет отображается в конструкторе:

[![Представление конструктора](designer-walkthrough-images/vs/04-designer-view-sml.png)](designer-walkthrough-images/vs/04-designer-view.png#lightbox)

Нажмите кнопку **источника** вкладку в нижней части конструктора, чтобы просмотреть источник XML для этой структуры:

[![Конструктор XML](designer-walkthrough-images/vs/05-designer-xml-sml.png)](designer-walkthrough-images/vs/05-designer-xml.png#lightbox)

Из **представление** меню, нажмите кнопку **другие окна > Структура документа** Открытие **Структура документа**. **Структура документа** показывает, что макет в настоящее время содержит один **LinearLayout** мини-приложения:

[![Структура документа](designer-walkthrough-images/vs/06-document-outline-sml.png)](designer-walkthrough-images/vs/06-document-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Щелкните правой кнопкой мыши в Visual Studio для Mac **ресурсы и макет** в **решения** рукописного ввода и выберите **Добавить > новый файл...** . В **новый файл** диалогового окна выберите **Android > макета**. Назовите файл **ListItem** и нажмите кнопку **New**:

[![Новый макет](designer-walkthrough-images/xs/03-new-layout-sml.png)](designer-walkthrough-images/xs/03-new-layout.png#lightbox)

Новый **ListItem** макет отображается в конструкторе:

[![Представление конструктора](designer-walkthrough-images/xs/04-designer-view-sml.png)](designer-walkthrough-images/xs/04-designer-view.png#lightbox)

Нажмите кнопку **источника** вкладку в нижней части конструктора, чтобы просмотреть источник XML для этой структуры. При нажатии кнопки **Структура документа** вкладке в правой части показывает, что макет в настоящее время содержит один **LinearLayout** мини-приложения:

[![Конструктор XML](designer-walkthrough-images/xs/05-designer-xml-sml.png)](designer-walkthrough-images/xs/05-designer-xml.png#lightbox)

-----



### <a name="creating-the-list-item-user-interface"></a>Создание списка элементов пользовательского интерфейса

Далее мы собираемся создавать интерфейс пользователя для приложения браузера цвета.
Нажмите кнопку **конструктор** вкладку, чтобы вернуться в область конструктора.
В **элементов**, прокрутите вниз до **изображений & мультимедиа** раздела и их каждого элемента, пока не найдете *ImageView*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Найдите ImageView](designer-walkthrough-images/vs/07-locate-imageview-sml.png)](designer-walkthrough-images/vs/07-locate-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Найдите ImageView](designer-walkthrough-images/xs/06-locate-imageview-sml.png)](designer-walkthrough-images/xs/06-locate-imageview.png#lightbox)

-----

Кроме того, можно ввести *ImageView* в строку поиска, чтобы найти `ImageView` мини-приложения:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Поиск ImageView](designer-walkthrough-images/vs/08-imageview-search-sml.png)](designer-walkthrough-images/vs/08-imageview-search.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Поиск ImageView](designer-walkthrough-images/xs/07-imageview-search-sml.png)](designer-walkthrough-images/xs/07-imageview-search.png#lightbox)

-----

Перетащите этот `ImageView` на поверхность разработки:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ImageView на холсте](designer-walkthrough-images/vs/09-imageview-on-canvas-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![ImageView на холсте](designer-walkthrough-images/xs/08-imageview-on-canvas-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas.png#lightbox)

-----

Это `ImageView` будет использоваться для отображения в нашем приложении браузера цветовой палитры цветов.

Затем перетащите `LinearLayout (Vertical)` мини-приложение из **элементов** в конструктор. Обратите внимание, что синий контур указывает границы добавленного `LinearLayout`и **Структура документа** показывают, что он располагается непосредственно под `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Синий контур](designer-walkthrough-images/vs/10-blue-outline-sml.png)](designer-walkthrough-images/vs/10-blue-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Синий контур](designer-walkthrough-images/xs/10-blue-outline-sml.png)](designer-walkthrough-images/xs/10-blue-outline.png#lightbox)

-----

При выборе `ImageView` в конструкторе перемещает синий контур вокруг `ImageView`, в **Структура документа**, выделение перемещается `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Выберите ImageView](designer-walkthrough-images/vs/11-select-imageview-sml.png)](designer-walkthrough-images/vs/11-select-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Выберите ImageView](designer-walkthrough-images/xs/11-select-imageview-sml.png)](designer-walkthrough-images/xs/11-select-imageview.png#lightbox)

-----

Затем перетащите `Text (Large)` мини-приложение из **элементов** в только что добавленную `LinearLayout`. Обратите внимание, что конструктор использует зеленый выделяет для указания, где будут вставлены новые мини-приложения:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Выделяет зеленый](designer-walkthrough-images/vs/12-green-highlight-sml.png)](designer-walkthrough-images/vs/12-green-highlight.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Выделяет зеленый](designer-walkthrough-images/xs/12-green-highlight-sml.png)](designer-walkthrough-images/xs/12-green-highlight.png#lightbox)

-----

Добавьте `Text (Small)` мини-приложения ниже `Text (Large)` мини-приложения:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Добавление небольшого мини-приложения](designer-walkthrough-images/vs/13-add-small-text-sml.png)](designer-walkthrough-images/vs/13-add-small-text.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Добавление небольшого мини-приложения](designer-walkthrough-images/xs/13-add-small-text-sml.png)](designer-walkthrough-images/xs/13-add-small-text.png#lightbox)

-----

На этом этапе конструктор должна напоминать следующий снимок экрана.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Макет конструктора](designer-walkthrough-images/vs/14-raw-layout-sml.png)](designer-walkthrough-images/vs/14-raw-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Макет конструктора](designer-walkthrough-images/xs/14-raw-layout-sml.png)](designer-walkthrough-images/xs/14-raw-layout.png#lightbox)

-----

Если два `textView` мини-приложения, не находящихся внутри `linearLayout1`, их можно перетаскивать `linearLayout1` в **Структура документа** и разместите их, поэтому они появляются, как показано на предыдущем снимке экрана (с отступом под `linearLayout1`).



### <a name="arranging-the-user-interface"></a>Размещение пользовательского интерфейса

Изменим пользовательский Интерфейс для отображения `ImageView` в левой части экрана, с двумя `TextView` мини-приложения с накоплением справа от `ImageView`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Выберите `ImageView`.

2.  В **окно свойств**, нажмите кнопку **по категориям** сортировки значок и прокрутите вниз до **макет - группе ViewGroup** раздела.

3.  Изменение `layout_width` параметру `wrap_content`:

![Задать содержимое wrap](designer-walkthrough-images/vs/15-wrap-content.png "набор переноса содержимого")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  С `ImageView` , нажмите кнопку **свойства** вкладки.

2.  Непосредственно под **свойства** щелкните **макета**.

3.  Прокрутите вниз до **группе ViewGroup** и измените `Width` параметру `wrap_content`:

[![Набор переноса содержимого](designer-walkthrough-images/xs/15-wrap-content-sml.png)](designer-walkthrough-images/xs/15-wrap-content.png#lightbox)

-----

Другой способ изменения `Width` параметр предназначен для щелкните треугольник с правой стороны мини-приложение, чтобы включить или отключить его значения ширины для `wrap_content`:

[![Перетащите поле, чтобы задать ширину](designer-walkthrough-images/xs/16-width-arrow-sml.png)](designer-walkthrough-images/xs/16-width-arrow.png#lightbox)

Повторное нажатие треугольника возвращает `Width` параметру `match_parent`.

Теперь, переключитесь в **Структура документа** и выберите корневой `LinearLayout`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Выберите корневой LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Выберите корневой LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout.png#lightbox)

-----
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

С корнем `LinearLayout` выбран, вернуться к **свойства** окно, нажмите кнопку **по алфавиту** сортировки значок и прокрутки, пока не найдете `orientation`. Изменение `orientation` параметру `horizontal`:

![Горизонтальная ориентация](designer-walkthrough-images/vs/17-horizontal-orientation.png "горизонтальная ориентация:")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

С корнем `LinearLayout` выбран, вернуться к **свойства** и нажмите кнопку **мини-приложение**. Изменение `Orientation` параметру `horizontal`:

[![Горизонтальная ориентация:](designer-walkthrough-images/xs/18-horizontal-orientation-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation.png#lightbox)

-----

На этом этапе конструктор должна напоминать следующий снимок экрана.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Макет конструктора](designer-walkthrough-images/vs/18-designer-layout-sml.png)](designer-walkthrough-images/vs/18-designer-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Макет конструктора](designer-walkthrough-images/xs/19-designer-layout-sml.png)](designer-walkthrough-images/xs/19-designer-layout.png#lightbox)

-----


### <a name="modifying-the-spacing"></a>Изменение интервала

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Затем мы изменим параметры внешних и внутренних полей в пользовательском Интерфейсе, чтобы предоставить дополнительное место между мини-приложений. Выберите `ImageView`, нажмите кнопку **по категориям** значок поиска в **свойства** окно и прокрутите вниз до **макета** раздела. Изменение `Min Height` для `70dp`, `Min Width` для `50dp`и `padding` для `10dp`. Это относится отступ вокруг всех сторон `ImageView` и elongates его по вертикали:

[![Задать заполнения](designer-walkthrough-images/vs/19-padding-widths-sml.png)](designer-walkthrough-images/vs/19-padding-widths.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Затем мы изменим параметры внешних и внутренних полей в пользовательском Интерфейсе, чтобы предоставить дополнительное место между мини-приложений. Выберите `ImageView` и нажмите кнопку **макета** в разделе **свойства**. Изменение `Padding` для `10dp`, `Min Width` для `50dp`и `Min Height` для `70dp`. Это относится отступ вокруг всех сторон `ImageView` и elongates его по вертикали:

[![Задать заполнения](designer-walkthrough-images/xs/20-padding-widths-sml.png)](designer-walkthrough-images/xs/20-padding-widths.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Нижней, левой, правой, а верхнего отступа параметры можно задать независимо друг от друга, введя значения в `paddingBottom`, `paddingLeft`, `paddingRight`, и `paddingTop` соответственно.
Например, задать `paddingLeft` на `5dp` и `paddingBottom`, `paddingRight`, и `paddingTop` поля `10dp`:

[![Параметры пользовательского заполнения](designer-walkthrough-images/vs/20-custom-padding-sml.png)](designer-walkthrough-images/vs/20-custom-padding.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Верхней, правой, нижней и левое заполнение параметров могут быть заданы независимо, введя значения в `Top`, `Right`, `Bottom`, и `Left` заполнения поля, соответственно. Например, задать `Left` значение для заполнения `5dp` и `Top`, `Right`, и `Bottom` значений для заполнения `10dp`. Обратите внимание, что `Padding` теперь содержится список разделенных запятыми из следующих значений:

[![Параметры пользовательского заполнения](designer-walkthrough-images/xs/21-custom-padding-sml.png)](designer-walkthrough-images/xs/21-custom-padding.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

После этого настроить положение `LinearLayout` мини-приложения, содержащего две `TextView` мини-приложения. В **Структура документа**выберите `linearLayout1`. В **свойства** прокрутите вниз до **макет - группе ViewGroup** раздела. Задать `layout_marginBottom`, `layout_marginLeft`, `layout_marginRight`, и `layout_marginTop` для `5dp`, `5dp`, `0dp`, и `5dp` соответственно:

[![Установка полей](designer-walkthrough-images/vs/21-margins-sml.png)](designer-walkthrough-images/vs/21-margins.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

После этого настроить положение `LinearLayout` мини-приложения, содержащего две `TextView` мини-приложения. В **Структура документа**выберите `linearLayout1`. В **свойства** выберите **макета** вкладки. Прокрутите вниз до **группе ViewGroup** статьи и задайте `Left`, `Top`, `Right`, и `Bottom` границы для `5dp`, `5dp`, `0dp`, и `5dp` соответственно:

[![Установка полей](designer-walkthrough-images/xs/22-margins-sml.png)](designer-walkthrough-images/xs/22-margins.png#lightbox)

-----



### <a name="removing-the-default-image"></a>Удаление образа по умолчанию

Так как мы используем `ImageView` для отображения цветов (вместо изображения), удалим добавлены с помощью шаблона источник изображения по умолчанию.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Выберите `ImageView`.

2.  В **свойства**, найти `src` поля.

3.  Очистить `src` параметр пуст:

![Удалить параметр src ImageView](designer-walkthrough-images/vs/22-clear-img-src.png "отменить параметр src ImageView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1.  Выберите `ImageView`.

2.  Нажмите кнопку **мини-приложение** в разделе **свойства**.

3.  Очистить `Src` параметр пуст:

[![Удалить параметр src ImageView](designer-walkthrough-images/xs/23-clear-src-sml.png)](designer-walkthrough-images/xs/23-clear-src.png#lightbox)

-----


### <a name="adding-a-listview-container"></a>Добавление контейнера ListView

Теперь, когда **ListItem** макет определен, мы добавим `ListView` на основной макет. Это `ListView` будет содержать список **элементов ListItem**.
В **элементов**, найдите `ListView` мини-приложение и перетащите его на поверхность разработки. `ListView` В конструкторе будут пустыми, за исключением синие линии, в которых указаны его границы, при этом. Можно просмотреть **Структура документа** и убедитесь, что **ListView** был правильно добавлен:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Новый ListView](designer-walkthrough-images/vs/23-new-listview.png "новый ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Новый ListView](designer-walkthrough-images/xs/24-new-listview-sml.png)](designer-walkthrough-images/xs/24-new-listview.png#lightbox)

-----

По умолчанию `ListView` получает `Id` значение `@+id/listView1`.
Откройте **мини-приложение** в разделе **свойства** и измените `Id` для `@+id/myListView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Переименуйте идентификатор в myListView](designer-walkthrough-images/vs/24-change-id.png "переименования идентификатор myListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Переименуйте идентификатор в myListView](designer-walkthrough-images/xs/25-change-id-sml.png)](designer-walkthrough-images/xs/25-change-id.png#lightbox)

-----

На этом этапе наш пользовательский интерфейс готова к использованию.



### <a name="running-the-application"></a>Запуск приложения


Откройте **MainActivity.cs** и замените код следующим:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity (Label = "DesignerWalkthrough", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem> ();
        ListView listView;

        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);

            SetContentView (Resource.Layout.Main);
            listView = FindViewById<ListView> (Resource.Id.myListView);

            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.DarkRed,
                                               ColorName = "Dark Red", Code = "8B0000" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.SlateBlue,
                                               ColorName = "Slate Blue", Code = "6A5ACD" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.ForestGreen,
                                               ColorName = "Forest Green", Code = "228B22" });

            listView.Adapter = new ColorAdapter (this, colorItems);
        }
    }
    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView (int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.ListItem, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

В этом коде используется настраиваемый `ListView` адаптера для загрузки сведений о цветах и отображать данные в пользовательском Интерфейсе, который мы только что создали. Чтобы пример был коротким, цвет шрифта, жестко запрограммированы в список, но адаптер может быть изменено для извлечения информации о цвете из источника данных или вычислять его на лету. Дополнительные сведения о `ListView` адаптеров, в разделе [представлениях ListView и адаптеры](~/android/user-interface/layouts/list-view/index.md).

Выполните сборку и запуск приложения. Снимке экрана ниже приведен пример того, как приложение отображается при выполнении на устройстве.

[![Окончательного снимка экрана](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)



## <a name="summary"></a>Сводка

В этой статье мы рассмотрены способы использования конструктора Xamarin.Android в Visual Studio для Mac для создания пользовательского интерфейса. Далее мы было показано, как создать интерфейс для одного элемента в списке.
Попутно мы рассматривали том, как добавить мини-приложения и разместить их визуально, а также назначения ресурсов и задать различные свойства для этих мини-приложения. В заключение мы показано, как интерфейс, созданный в конструкторе выполняется в пример приложения.
