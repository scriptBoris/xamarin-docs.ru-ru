---
title: С помощью конструктора Xamarin.Android
description: В этой статье приведено пошаговое описание конструктора Xamarin.Android. Он демонстрирует создание пользовательского интерфейса для приложения браузера небольшой цвет; Этот пользовательский интерфейс создается полностью в конструкторе.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1174fe5cb417d4977fd6519086e6c4942e74c10b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120278"
---
# <a name="using-the-xamarinandroid-designer"></a>С помощью конструктора Xamarin.Android

_В этой статье приведено пошаговое описание конструктора Xamarin.Android. Он демонстрирует создание пользовательского интерфейса для приложения браузера небольшой цвет; Этот пользовательский интерфейс создается полностью в конструкторе._


## <a name="overview"></a>Обзор

Можно создать Android пользовательские интерфейсы декларативно, с помощью XML-файлы, или программным путем написания кода. Конструктор Xamarin.Android позволяет разработчикам создавать и изменять декларативной макеты визуально, не требуя ручного редактирования XML-файлов. Конструктор также предоставляет в реальном времени обратной связи, которое позволяет разработчику оценить изменения пользовательского интерфейса без необходимости повторного развертывания приложения на устройстве или эмуляторе. Эти функции конструктора может значительно ускорить разработки Android пользовательского интерфейса.
В этой статье показано, как использовать конструктор Xamarin.Android, чтобы визуально создавать пользовательский интерфейс.

## <a name="walkthrough"></a>Пошаговое руководство

Цель этого пошагового руководства — использование Android Designer для создания пользовательского интерфейса для приложения браузера примере цвет. Приложение браузера цвет выводит список цветов, их имена и их значения RGB. Вы узнаете, как добавить мини-приложения для **конструктора** а также как создавать визуально эти мини-приложения. После этого вы научитесь изменять мини-приложения интерактивно on **конструктора** или с помощью конструктора **свойства** области. Наконец вы увидите, как выглядит структура при запуске приложения на устройстве или эмуляторе.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Создание нового проекта

Первым шагом является создание нового проекта Xamarin.Android. Запустите Visual Studio, нажмите кнопку **новый проект...** и выберите **Visual C\# > Android > приложение Android (Xamarin)** шаблона.
Назовите новое приложение **DesignerWalkthrough** и нажмите кнопку **ОК**.

[![Пустое приложение Android](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

В **нового приложения Android** диалоговом окне выберите **пустое приложение** и нажмите кнопку **ОК**:

[![Выбор шаблона пустого приложения Android](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>Добавив макет

Следующим шагом является создание **LinearLayout** , будет содержать пользователя элементы интерфейса. Щелкните правой кнопкой мыши **ресурсы/макета** в **обозревателе решений** и выберите **Добавить > новый элемент...** . В **Добавление нового элемента** диалоговом окне выберите **макет Android**. Назовите файл **list_item** и нажмите кнопку **добавить**:

[![Новый макет](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

Новый **list_item** макет отображается в конструкторе. Обратите внимание на то, что отображаются две панели &ndash; *конструктора* для **list_item** видна в левой области, пока его источник XML отображается на правой панели. Вы можете переключить позиции **конструктора** и **источника** областей, нажав кнопку **области подкачки** значок, расположенный между двумя областями:

[![Представление конструктора](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Из **представление** меню, щелкните **Other Windows > Структура документа** открыть **Структура документа**. **Структура документа** показывает, что макет в настоящее время содержит один **LinearLayout** мини-приложения:

[![Структура документа](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

Следующим шагом является создание пользовательского интерфейса для приложения браузера цвета в рамках этого `LinearLayout`.

### <a name="creating-the-list-item-user-interface"></a>Создание пользовательского интерфейса элемента списка

Если **элементов** панель не отображается, нажмите кнопку **элементов** вкладка в левой части. В **элементов**, прокрутите вниз до раздела **изображения и мультимедиа** раздела и прокрутите вниз, Дополнительно, пока не найдете нужный `ImageView`:

[![Найдите ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

Кроме того, можно ввести *ImageView* в строке поиска, чтобы найти `ImageView`:

[![ImageView поиска](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Перетащите этот `ImageView` в область конструктора (это `ImageView` будет использоваться для отображения в приложении браузера цвет цветовой палитры):

[![ImageView на холсте](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Затем перетащите `LinearLayout (Vertical)` мини-приложения из **элементов** в конструктор. Обратите внимание, что синий контур указывает границы добавленного `LinearLayout`. **Структура документа** показывает, что он является дочерним элементом `LinearLayout`, расположенный в области `imageView1 (ImageView)`:

[![Синий контур](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

При выборе `ImageView` в конструкторе перемещает синий контур, который нужно окружить `ImageView`. Кроме того, выделение перемещается `imageView1 (ImageView)` в **Структура документа**:

[![Выберите ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Затем перетащите `Text (Large)` мини-приложения из **элементов** в добавленный `LinearLayout`. Обратите внимание на то, что конструктор использует зеленый выделяет для указания, куда будут помещены новые мини-приложения:

[![Выделяет зеленый](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Затем добавьте `Text (Small)` ниже мини-приложение `Text (Large)` мини-приложения:

[![Добавить небольшие текстовые мини-приложение](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

На этом этапе в область конструктора должен иметь формат снимке экрана ниже:

[![Макет конструктора](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Если два `textView` мини-приложения не внутри `linearLayout1`, можно перетащить их `linearLayout1` в **Структура документа** и располагать их, чтобы они отображались, как показано на предыдущем снимке экрана (с отступом под `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Размещение пользовательского интерфейса

Следующим шагом является изменение пользовательского интерфейса для отображения `ImageView` в левой части, с двумя `TextView` мини-приложения с накоплением справа от `ImageView`.

1.  Выберите `ImageView`.

2.  В **окно "Свойства"**, введите *ширины* при поиске поле и найдите **ширину макета**.

3.  Изменение **ширину макета** присвоить `wrap_content`:

![Содержимое набора переноса по словам](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Еще одним способом изменения `Width` параметр предназначен для щелкните треугольник в правой части мини-приложение, чтобы переключить его значения ширины для `wrap_content`:

![Перетащите, чтобы задать ширину](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Щелкнув треугольник снова возвращает `Width` значение `match_parent`. Перейдите к **Структура документа** области и выберите корневой `LinearLayout`:

[![Выберите корень LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

С корнем `LinearLayout` выбран, вернитесь к **свойства** области введите *ориентации* в поиск поле и найдите **ориентации** параметр. Изменение **ориентации** для `horizontal`:

![Выбор горизонтальной ориентации](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

На этом этапе в область конструктора должно выглядеть как следующий снимок экрана.
Обратите внимание, что `TextView` мини-приложения были перемещены в правом углу `ImageView`:

[![Макет конструктора](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Изменение интервала

Следующий шаг — изменить параметры Отбивки и внутренних полей в пользовательском Интерфейсе для предоставления дополнительного пространства между мини-приложений. Выберите `ImageView` в рабочей области конструирования. В **свойства** области введите `min` в поле поиска. Введите `70dp` для **Минимальная высота** и `50dp` для **мин. ширина**:

[![Значение высоты и ширины символов](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

В **свойства** области введите `padding` для поиска и введите `10dp` для **Padding**. Эти `minHeight`, `minWidth` и `padding` параметры добавляйте отступ вокруг всех сторонах `ImageView` и растянуть его по вертикали. Обратите внимание на то, что XML-ФАЙЛ макета изменяется по мере введите следующие значения:

[![Установить отступ](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

Нижней, левой, правой и верхней параметры внутренних полей можно задать независимо друг от друга, введя значения в **Padding нижней**, **заполнение слева**, **заполнение справа**и  **Заполнение верхней** соответственно.
Например, задать **заполнение слева** поле `5dp` и **Padding нижней**, **заполнение справа**, и **Top, Padding** поля Чтобы `10dp`:

[![Параметры настраиваемых полей](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Затем настройте расположение `LinearLayout` мини-приложения, содержащего две `TextView` мини-приложения. В **Структура документа**выберите `linearLayout1`. В **свойства** окно, введите `margin` в поле поиска. Задайте **макета полей нижней**, **макета Margin-Left**, и **макета Margin-Top** для `5dp`. Задайте **макета полей справа** для `0dp`:

[![Установка полей](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Удаление образа по умолчанию

Так как `ImageView` используется для отображения цветов (а не изображений), следующим шагом является удаление источник изображения по умолчанию, добавлены с помощью шаблона.

1.  Выберите `ImageView` на **конструктора**.

2.  В **свойства**, введите *src* в поле поиска.

3.  Щелкните маленький квадрат справа от **Src** свойство задание и выберите **Сброс**:

[![Отключите этот параметр src ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

При этом удаляются `android:src="@android:drawable/ic_menu_gallery"` из источника XML для этого `ImageView`.

### <a name="adding-a-listview-container"></a>Добавление контейнера ListView

Теперь, когда **list_item** макет определен, следующим шагом является добавление `ListView` к макету Main. Это `ListView` будет содержать список **list_item**. 

В **обозревателе решений**откройте **Resources/layout/activity_main.axml**. В **элементов**, найдите `ListView` мини-приложение и перетащите его на **конструктора**. `ListView` В конструкторе будет оставаться пустой, за исключением синие линии, в которых указаны его границу, когда он выбран. Можно просмотреть **Структура документа** чтобы убедиться, что **ListView** был правильно добавлен:

[![Новый ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

По умолчанию `ListView` присваивается `Id` значение `@+id/listView1`.
Хотя `listView1` по-прежнему выбран в **Структура документа**откройте **свойства** панели щелкните **Упорядочить по**и выберите **категории**.
Откройте **Main**, найдите **идентификатор** свойство и измените его значение на `@+id/myListView`:

[![Переименуйте идентификатор в myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

На этом этапе пользовательский интерфейс готов к использованию.

### <a name="running-the-application"></a>Запуск приложения

Откройте **MainActivity.cs** и замените его код на следующий:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
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
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
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

Этот код использует пользовательский `ListView` адаптер, чтобы загрузить сведения о цвете и для отображения этих данных в пользовательском Интерфейсе, который был только что создали. Для простоты в этом примере короткий, сведения о цвете жестко закодирована в списке, но адаптер может быть изменено для извлечения информации о цвете из источника данных или вычислять его на ходу. Дополнительные сведения о `ListView` адаптеров, см. в разделе [ListView](~/android/user-interface/layouts/list-view/index.md).

Выполните сборку и запуск приложения. Ниже приведен пример того, как оно отображается при запуске на устройстве:

[![Окончательного снимка экрана](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Создание нового проекта

Первым шагом является создание нового проекта Xamarin.Android.

Запустите Visual Studio для Mac и нажмите кнопку **новый проект...** . Выберите **приложение Android** шаблона и нажмите кнопку **Далее**:

[![Пустое приложение Android](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Назовите новое приложение **DesignerWalkthrough**. В разделе **целевых платформ**выберите **последнее и лучшее** и нажмите кнопку **Далее**:

[![Имя приложения](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

В следующем диалоговом окне, щелкните **создать**.

### <a name="adding-a-layout"></a>Добавив макет

Следующим шагом является создание **LinearLayout** , будет содержать пользователя элементы интерфейса.

В Visual Studio для Mac, щелкните правой кнопкой мыши **ресурсы/макета** в **решение** рукописного ввода и выберите **Добавить > новый файл...** . В **новый файл** диалоговом окне выберите **Android > макет**. Назовите файл **list_item** и нажмите кнопку **New**:

[![Новый макет](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Добавив этот файл, новый **list_item** макет отображается на **конструктора** (Если вы видите сообщение, *этот проект содержит ресурсы, которые не были успешно скомпилированы может повлиять на визуализацию*, нажмите кнопку **сборки > собрать все** для сборки проекта):

[![Представление конструктора](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Нажмите кнопку **источника** вкладку в нижней части конструктора, чтобы просмотреть источник XML для данного макета. При нажатии кнопки **Структура документа** вкладке в правой части, он показывает, что макет в настоящее время содержит один **LinearLayout** мини-приложения:

[![Конструктора XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

Следующим шагом является создание пользовательского интерфейса для приложения браузера цвета.

### <a name="creating-the-list-item-user-interface"></a>Создание пользовательского интерфейса элемента списка

Нажмите кнопку **конструктор** вкладку в нижней части экрана, чтобы вернуться к **поверхности конструктора**. В **элементов** панели справа, прокрутите вниз до раздела **изображения и мультимедиа** разделе и найдите `ImageView`:

[![Найдите ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

Кроме того, можно ввести *ImageView* в строке поиска, чтобы найти `ImageView`:

[![ImageView поиска](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Перетащите этот `ImageView` на **конструктора** (это `ImageView` будет использоваться для отображения в приложении браузера цвет цветовой палитры):

[![ImageView на холсте](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Затем перетащите `LinearLayout (Vertical)` мини-приложения из **элементов** в **конструктора**. Обратите внимание, что синий контур указывает границы добавленного `LinearLayout`. **Структура документа** показывает, что он является дочерним элементом `LinearLayout`, расположенный под `imageView1 (ImageView)`:

[![Синий контур](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

При выборе `ImageView` в конструкторе перемещает синий контур, который нужно окружить `ImageView`. Кроме того, выделение перемещается `imageView1 (ImageView)` в **Структура документа**:

[![Выберите ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Затем перетащите `Text (Large)` мини-приложения из **элементов** в добавленный `LinearLayout`. Обратите внимание, что при перетаскивании указателя мыши на **конструктора**, выделяются вставки новое мини-приложение.
`Text (Large)` Мини-приложения, которые должны быть расположены внутри `linearLayout1` как показано ниже:

[![Добавление мини-приложения крупного текста](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Затем добавьте `Text (Small)` ниже мини-приложение `Text (Large)` мини-приложения. На этом этапе **конструктора** должны напоминать следующий снимок экрана:

[![Добавить небольшие текстовые мини-приложение](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Если два `textView` мини-приложения не внутри `linearLayout1`, можно перетащить их `linearLayout1` в **Структура документа** и расположите их таким образом, чтобы они отображаются, как показано на предыдущем снимке экрана (с отступом под `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Размещение пользовательского интерфейса

Следующим шагом является изменение пользовательского интерфейса для отображения `ImageView` в левой части, с двумя `TextView` мини-приложения с накоплением справа от `ImageView`.

1.  С помощью `ImageView` , щелкните **свойства** вкладки.

2.  Непосредственно под **свойства** щелкните **макета**.

3.  Прокрутите вниз до раздела **ViewGroup** и измените `Width` присвоить `wrap_content`:

[![Содержимое набора переноса по словам](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Еще одним способом изменения `Width` параметр предназначен для щелкните треугольник в правой части мини-приложение, чтобы переключить его значения ширины для `wrap_content`:

[![Перетащите, чтобы задать ширину](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Щелкнув треугольник снова возвращает `Width` значение `match_parent`. Перейдите к **Структура документа** области и выберите корневой `LinearLayout`:

[![Выберите корень LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

С корнем `LinearLayout` выбран, вернитесь к **свойства** вкладку и нажмите кнопку **мини-приложение**. Изменение `Orientation` присвоить `horizontal` как показано ниже. На этом этапе **конструктора** должен напоминать снимок экрана ниже. Обратите внимание, что `TextView` мини-приложения были перемещены в правом углу `ImageView`:

[![Выбор горизонтальной ориентации](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>Изменение интервала

Следующий шаг — изменить параметры Отбивки и внутренних полей в пользовательском Интерфейсе для предоставления дополнительного пространства между мини-приложений. Выберите `ImageView` и нажмите кнопку **макета** открыть, выбрав вкладку **свойства**. Изменение `Min Width` для `50dp`, `Min Height` для `70dp`и `Padding` для `10dp`.
Это относится Отбивки вокруг всех сторонах `ImageView` и elongates его по вертикали:

[![Установить отступ](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

Верхней, правой, нижней и левого дополнения параметры могут быть заданы независимо, введя значения в `Top`, `Right`, `Bottom`, и `Left` заполнения поля, соответственно. Например, задать `Left` значение для заполнения `5dp` и `Top`, `Right`, и `Bottom` значений для заполнения `10dp`. Обратите внимание, что `Padding` изменяет параметр в список с разделителями запятыми из следующих значений:

[![Параметры настраиваемых полей](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Затем настройте расположение `LinearLayout` мини-приложения, содержащего две `TextView` мини-приложения. В **Структура документа**выберите `linearLayout1`. В **свойства** области выберите **макета** вкладки. Прокрутите вниз до раздела **ViewGroup** разделе и задайте `Left`, `Top`, `Right`, и `Bottom` полей для `5dp`, `5dp`, `0dp`, и `5dp` соответственно:

[![Установка полей](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Удаление образа по умолчанию

Так как `ImageView` используется для отображения цветов (а не изображений), следующим шагом является удаление источник изображения по умолчанию, добавлены с помощью шаблона.

1.  Выберите `ImageView`.

2.  Нажмите кнопку **мини-приложение** открыть, выбрав вкладку **свойства**.

3.  Очистить `Src` параметр пуст:

[![Отключите этот параметр src ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

При этом удаляются `android:src="@android:drawable/ic_menu_gallery"` из источника XML для этого `ImageView`.

### <a name="adding-a-listview-container"></a>Добавление контейнера ListView

Теперь, когда **list_item** макет определен, следующим шагом является добавление `ListView` к макету Main. Это `ListView` будет содержать список **list_item**. 

В **обозревателе решений**откройте **Resources/layout/Main.axml**.
Нажмите кнопку `Button` мини-приложения (если таковые имеются) и удалите его. В **элементов**, найдите `ListView` мини-приложение и перетащите его на **конструктора**.
`ListView` В конструкторе будет оставаться пустой, за исключением синие линии, в которых указаны его границу, когда он выбран. Можно просмотреть **Структура документа** чтобы убедиться, что **ListView** был правильно добавлен:

[![Новый ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

По умолчанию `ListView` присваивается `Id` значение `@+id/listView1`.
Хотя `listView1` по-прежнему выбран в **Структура документа**откройте **свойства** панели щелкните **Упорядочить по**и выберите **категории**.
Откройте **Main**, найдите **идентификатор** свойство и измените его значение на `@+id/myListView`:

[![Переименуйте идентификатор в myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

На этом этапе пользовательский интерфейс готов к использованию.

### <a name="running-the-application"></a>Запуск приложения

Откройте **MainActivity.cs** и замените его код на следующий:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
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
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
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

Этот код использует пользовательский `ListView` адаптер, чтобы загрузить сведения о цвете и для отображения этих данных в пользовательском Интерфейсе, который был только что создали. Для простоты в этом примере короткий, сведения о цвете жестко закодирована в списке, но адаптер может быть изменено для извлечения информации о цвете из источника данных или вычислять его на ходу. Дополнительные сведения о `ListView` адаптеров, см. в разделе [ListView](~/android/user-interface/layouts/list-view/index.md).

Выполните сборку и запуск приложения. Ниже приведен пример того, как оно отображается при запуске на устройстве:

[![Окончательного снимка экрана](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>Сводка

В этой статье приведены пошаговые инструкции по процесс с помощью конструктора Xamarin.Android в Visual Studio для создания пользовательского интерфейса для базовое приложение.
Вы узнали, как создать интерфейс для одного элемента в списке, и оно показано, как добавить мини-приложения и разместить их визуально.
Он также объяснил, как назначать ресурсы и затем задать различные свойства для этих мини-приложений.
