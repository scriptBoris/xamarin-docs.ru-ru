---
title: Сводка главе 13. Растровые изображения
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка главе 13. Растровые изображения'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0f9b9e27afd5dbbf52f3653995470136e794f17b
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935203"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Сводка главе 13. Растровые изображения

Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) элемент отображает точечный рисунок. Все платформы Xamarin.Forms поддерживают форматы файлов JPEG, PNG, GIF и BMP.

Растровые изображения в Xamarin.Forms поступать из четырех местах:

- В Интернете в соответствии с URL-адрес
- Внедрены в качестве ресурса в общей библиотеке переносных классов
- Внедренного как ресурс в проектах приложений платформы
- Из любого места, которое позволяет ссылаться на .NET `Stream` объекта, включая `MemoryStream`

Ресурсы точечного рисунка в переносимой библиотеке Классов являются независимыми от платформы, пока ресурсы точечного рисунка в проектах платформы зависят от платформы.

Точечный рисунок, заданный, задав [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) свойство `Image` для объекта типа [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/), абстрактным классом с производными три:

- [`UriImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) для доступа к растрового изображения в Интернете, на основе `Uri` присвоено его [ `Uri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.Uri/) свойство
- [`FileImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/) для доступа к растрового изображения, хранимые в проекте приложения платформы на основе пути файлов и папок его [ `File` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FileImageSource.File/) свойство
- [`StreamImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/) для загрузки точечного рисунка, с помощью .NET `Stream` указанного путем возвращения объекта `Stream` из `Func` присвоено его [ `Stream` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StreamImageSource.Stream/) свойство

Кроме того (и чаще) можно использовать следующие статические методы `ImageSource` класс, которые возвращают `ImageSource` объектов:

- [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) для доступа к растрового изображения в Интернете, на основе `Uri` объекта
- [`ImageSource.FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) для доступа к растрового изображения, хранящиеся в виде внедренного ресурса в PCL, приложения или [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Type/) или [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Reflection.Assembly/) для доступа к растрового изображения в другой сборке источника
- [`ImageSource.FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) для доступа к растрового изображения из проекта приложения платформы
- [`ImageSource.FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) для загрузки точечного рисунка, на основе `Stream` объекта

Отсутствует эквивалент класса из `Image.FromResource` методы. `UriImageSource` Класс может быть полезен, если вам нужно управлять кэшированием. `FileImageSource` Класс полезен в XAML. `StreamImageSource` полезно для асинхронной загрузки объекта `Stream` объектов, тогда как `ImageSource.FromStream` выполняется в синхронном режиме.

## <a name="platform-independent-bitmaps"></a>Точечные рисунки независимо от платформы

[ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) проекта загружает растровый рисунок по Интернету при помощи `ImageSource.FromUri`. `Image` Элементу присваивается `Content` свойство `ContentPage`, поэтому он ограничен размер страницы. Независимо от размера точечного рисунка, ограниченный ресурсами `Image` элемент растягивается по размеру контейнера и точечный рисунок отображается в его максимальный размер в рамках `Image` элемент сохранением пропорции растрового изображения. Области `Image` за пределы растрового изображения будут выделены цветом с [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/).

[ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) пример аналогичен, но просто задает `Source` URL-адрес. Преобразование обрабатывается [ `ImageSourceConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSourceConverter/) класса.

### <a name="fit-and-fill"></a>По размеру и заливки

Можно управлять как растровое изображение растягивается, задав [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) свойство `Image` к одному из следующих членов [ `Aspect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect/) перечисления:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): учитывает пропорции (по умолчанию)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): заполняет область, не сохраняет пропорции
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): заполняет область, но учитывает пропорции, выполнена Обрезка части точечного рисунка

### <a name="embedded-resources"></a>Внедренные ресурсы

Можно добавить к файлу точечного рисунка, PCL или папки в переносимой библиотеке Классов. Присвойте ему **действие при построении** из **EmbeddedResource**. [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) образце показано, как использовать `ImageSource.FromResource` для загрузки файла. Имя ресурса, передаваемые методу состоит из имени сборки, за которым следует точка, за которым следует имя необязательная папка и точкой, а затем имя файла.

Наборы программы `VerticalOptions` и `HorizontalOptions` свойства `Image` для `LayoutOptions.Center`, что делает `Image` элемент без ограничений. `Image` И растрового изображения имеют одинаковый размер:

- В iOS и Android `Image` является размер растрового изображения в пикселях. Имеется однозначное сопоставление между пикселов точечного рисунка и точек.
- На платформах среды выполнения Windows `Image` — это размер пикселей растрового изображения в аппаратно независимых единицах. На большинстве устройств каждый пиксел точечный рисунок занимает несколько точек.

[ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) пример помещает `Image` в вертикальном `StackLayout` в XAML. Расширения разметки с именем [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) позволяет ссылаться на внедренный ресурс в XAML. Этот класс только загружает ресурсы из сборки, в котором она размещается, поэтому он не может быть помещен в библиотеку.

### <a name="more-on-sizing"></a>Дополнительные сведения об изменения размера

Часто желательно, чтобы точечные рисунки размером согласованно среди всех платформ.
Поэкспериментировав с **StackedBitmap**, можно задать `WidthRequest` на `Image` элемент в вертикальном `StackLayout` обеспечение согласованности между платформами, но размер может уменьшаться размер только при использовании этого метода.

Можно также задать `HeightRequest` чтобы сделать образ размером согласованное на платформах, но ограниченное ширину растрового изображения будет ограничить гибкость использования этой технологии. Для изображения в вертикальном `StackLayout`, `HeightRequest` следует избегать.

Лучше всего начать с растровым изображением шире, чем phone ширину в аппаратно независимых единицах и задайте `WidthRequest` до требуемой ширины в аппаратно независимых единицах. Это показано в [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) образца.

[ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) отображает седьмой главе Льюиса Кэрролла *Алисы приключения в Страна чудес* с исходной иллюстрациями, Джон Tenniel:

[![Тройной снимок mad сторона чая](images/ch13fg16-small.png "Mad текст книги чая сторона Hatters")](images/ch13fg16-large.png#lightbox "Mad текст книги Hatters сторона чая")

### <a name="browsing-and-waiting"></a>Обзор и ожидания

[ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) образец позволяет пользователю просматривать через готовые образы, хранящиеся на веб-сайте Xamarin. Она использует .NET `WebRequest` класса, чтобы скачать JSON-файл со списком точечных рисунков.

Программа использует [ `ActivityIndicator` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) для указания, что что-то происходит. При загрузке каждого растрового изображения, доступной только для чтения [ `IsLoading` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.IsLoading/) свойство `Image` является `true`. `IsLoading` Резервируется привязываемые свойства, поэтому `PropertyChanged` событие при изменении этого свойства. Программа присоединяет обработчик к этому событию и использует текущее значение параметра `IsLoaded` присвоить [ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) свойство `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Потоковая передача точечных рисунков

`ImageSource.FromStream` Метод создает `ImageSource` на основе .NET `Stream`. Метод должен быть передан `Func` объект, который возвращает `Stream` объекта.

### <a name="accessing-the-streams"></a>Доступ к потоки

[ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) образце показано, как использовать `ImaageSource.FromStream` метод для загрузки хранятся в виде внедренного ресурса точечного рисунка и для загрузки точечного рисунка в Интернете.

### <a name="generating-bitmaps-at-run-time"></a>Создание растровых изображений во время выполнения

Все платформы Xamarin.Forms поддерживают несжатых формат файла BMP, которую можно легко создавать в коде и затем сохранить в `MemoryStream`. Этот прием позволяет алгоритмически Создание растровых изображений во время выполнения, реализованное в [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) в класс **Xamrin.FormsBook.Toolkit** библиотеки.

«Самообслуживания» [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) в нем демонстрируется использование `BmpMaker` для создания растрового изображения с помощью градиента образа.

## <a name="platform-specific-bitmaps"></a>Растровые изображения для конкретных платформ

Все платформы Xamarin.Forms Разрешить сохранение растровых изображений в сборки платформы приложений. При получении приложением Xamarin.Forms, эти точечные рисунки платформы имеют тип [ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/). Они используются для:

- [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) свойство [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)
- [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) свойство [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)
- [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) свойство `Button`

Сборки платформы уже содержат точечные рисунки, значки и экраны-заставки:

- В проекте iOS в **ресурсы** папки
- В проекте Android в подпапках **ресурсы** папки
- В проектах Windows в **активы** папку (несмотря на то, что к этой папке на платформах Windows не ограничивают растровые изображения)

[ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) примере используется код для отображения значка из проектов приложений для платформы.

### <a name="bitmap-resolutions"></a>Разрешение растрового изображения

Все платформы позволяют хранить несколько версий точечных рисунков для разрешений на другое устройство. Во время выполнения правильную версию загружается в зависимости от разрешения экрана устройства.

В iOS эти точечные рисунки, различаются по суффикс к имени файла:

- Без суффикса для устройств 160 точек на ДЮЙМ (1 пиксель в аппаратно независимая единица)
- "@2x" суффикс для устройств 320 точек на ДЮЙМ (2 точек в DIU)
- "@3x" суффикс для устройств 480 точек на ДЮЙМ (3 точек в DIU)

Растровое изображение, предназначенное для отображения как квадрат 1 дюйм будет существовать в трех версиях:

- MyImage.jpg 160 точек square
- MyImage@2x.jpg 320 пикселов в квадрат
- MyImage@3x.jpg 480 точек square

Программа ссылаетесь на дополнительное этим растровым изображением, как MyImage.jpg, но правильную версию извлекается во время выполнения на основе разрешения экрана. При отсутствии ограничений точечный рисунок всегда будет отображаться на 160 аппаратно независимых единицах.

Для Android, битовые карты хранятся в различных подпапках **ресурсы** папку:

- drawable-ldpi (низкий точек на ДЮЙМ) для устройств 120 точек на ДЮЙМ (0,75 точек в DIU)
- drawable-mdpi (средний) для устройств 160 точек на ДЮЙМ (1 пиксель в DIU)
- drawable-hdpi (высокий) для устройств 240 точек на ДЮЙМ (1,5 точек в DIU)
- drawable-xhdpi (очень высокое) для устройств 320 точек на ДЮЙМ (2 точек в DIU)
- drawable-xxhdpi (очень очень высокий уровень) для устройств 480 точек на ДЮЙМ (3 точек в DIU)
- drawable-xxxhdpi (три дополнительных Удачи) для устройств 640 точек на ДЮЙМ (4 точек в DIU)

Для точечного рисунка, который предназначен для отображения на одной квадратный дюйм в различных версиях растровое изображение будет тем же именем, но другого размера и хранящиеся в этих папках:

- drawable-ldpi/MyImage.jpg на 120 пикселей ниже square
- drawable-mdpi/MyImage.jpg 160 точек square
- drawable-hdpi/MyImage.jpg в квадрат составляет 240 пикселей
- drawable-xhdpi/MyImage.jpg 320 пикселов в квадрат
- drawable-xxhdpi/MyImage.jpg 480 точек square
- drawable-xxxhdpi/MyImage.jpg в квадрат 640 пикселей

Точечный рисунок всегда будет отображаться на 160 аппаратно независимых единицах. (Стандартный шаблон решения Xamarin.Forms включает только hdpi, xhdpi и xxhdpi папок.)

Проекты среды выполнения Windows поддерживают Битовая карта, состоящий из коэффициент масштабирования в пикселях на аппаратно независимые единицы в процентах, например схему именования:

- MyImage.scale-200.jpg 320 пикселов в квадрат

Только некоторые процентные показатели являются допустимыми. Примеры программ, эта книга включают в себя только образы с **масштаб 200** суффиксы, но текущее решение шаблоны Xamarin.Forms, которые включают **масштаб 100**, **масштабирования 125**, **масштабирования до 150**, и **масштаба 400**.

При добавлении точечных рисунков в проекты платформы **действие при построении** должно быть:

- iOS: **BundleResource**
- Android — **AndroidResource**
- Среда выполнения Windows: **содержимого**

[ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) пример создает два объекта аналогичный кнопке, состоящий из `Image` элементов при помощи `TapGestureRecognizer` установлен. Предполагается, что объекты были квадрат 1 дюйм. `Source` Свойство `Image` задается с помощью `OnPlatform` и `On` объектов для ссылки на потенциально различные имена файлов на платформах. Растровые изображения включают значения указывают их размер в пикселях, чтобы можно было видеть, какой размер растрового изображения извлекается и подготовке к просмотру.

### <a name="toolbars-and-their-icons"></a>Панели инструментов и соответствующие значки

Одним из основных применений точечных рисунков, специфические для платформы является панели инструментов Xamarin.Forms, которое создается с помощью добавления [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) объектов [ `ToolbarItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.ToolbarItems/) определяется коллекции`Page`. `ToobarItem` является производным от [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) от которого он наследует некоторые свойства.

Наиболее важным `ToolbarItem` свойства являются:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) для текста, который может присутствовать в зависимости от платформы и `Order`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) типа `FileImageSource` для изображения, которое может отображаться в зависимости от платформы и `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) типа [ `ToolbarItemOrder` ](xref:Xamarin.Forms.ToolbarItemOrder), перечисление с тремя членами [ `Default` ](xref:Xamarin.Forms.ToolbarItemOrder.Default), [ `Primary` ](xref:Xamarin.Forms.ToolbarItemOrder.Primary), и [ `Secondary` ](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

Количество `Primary` элементы должны быть ограничены трем или четырем. Следует включить `Text` для всех элементов. Для большинства платформ, только `Primary` элементы, требующие `Icon` , но требуется Windows 8.1 `Icon` для всех элементов. Значки должны иметь 32 аппаратно независимых единицах square. `FileImageSource` Тип указывает, что они зависят от платформы.

`ToolbarItem` Активируется [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) событий шифрованию, очень похоже на `Button`. `ToolbarItem` также поддерживает [ `Command` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Command/) и [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.CommandParameter/) свойства часто используются в связи с MVVM. (См. в разделе [Глава 18, MVVM](chapter18.md)).

IOS и Android требовать, чтобы страница, на которой отображается панель инструментов [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) или страницы был переход по `NavigationPage`. [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) программировать наборы `MainPage` свойство его `App` класс [ `NavigationPage` конструктор](https://developer.xamarin.com/api/constructor/Xamarin.Forms.NavigationPage.NavigationPage/p/Xamarin.Forms.Page/) с `ContentPage` аргумент и демонстрирует построение и обработчиком событий панели инструментов.

### <a name="button-images"></a>Изображения кнопки

Также можно использовать точечные рисунки с платформой для задания [ `Image` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Image/) свойство `Button` к растровому изображению квадрата 32 аппаратно независимых единицах, как показано [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) образец.



## <a name="related-links"></a>Связанные ссылки

- [Глава 13 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Примеры в главе 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Работа с образами](~/xamarin-forms/user-interface/images.md)
