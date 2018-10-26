---
title: Введение в iOS 11
description: Этот документ содержит ссылки на различные руководства, описывающие возможности iOS 11, включая ARKit, CoreML, MapKit, PDFKit, SiriKit, framework концепции и многое другое.
ms.prod: xamarin
ms.assetid: 22C38EA6-6DA9-4B92-B41B-814E589033F6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2017
ms.openlocfilehash: 5e75a7872081d82c289db0312bbc7d84bce77b4e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116723"
---
# <a name="introduction-to-ios-11"></a>Введение в iOS 11

_Попробуйте новый iOS 11 API-интерфейсов с помощью Xamarin._

> [!NOTE]
> Перед началом работы ознакомьтесь с [Приступая к работе](get-started.md) страницы инструкции о том, как установить Xcode 9.

![Пример ARKit](images/arkit.png) ![Размещение объектов AR](images/arkit2.png) ![Пример CoreML](images/coreml.png) ![Пример MapKit](images/mapkit.png) ![Пример машинного зрения прямоугольников](images/vision1.png) ![Пример машинного зрения лиц](images/vision2.png) ![Пример перетаскивания](images/drag-drop.png) ![Пример перетаскивания](images/drag-drop2.png) ![Пример SiriKit](images/sirikit.png)

iOS 11 включает в себя множество новых компонентов и усовершенствований для разнообразных платформ:

## <a name="preparing-your-app-for-ios-11updating-your-appindexmd"></a>[Подготовка приложений для iOS 11](updating-your-app/index.md)

Apple представила архитектуру обновления, новые визуальные изменения и процесс обновленные iTunes Connect для iOS 11. Используйте это руководство, чтобы убедиться в том, что приложение Xamarin.iOS подготовлена для нового выпуска.

## <a name="arkitarkitindexmd"></a>[ARKit](arkit/index.md)

ARKit Открытие дополненная реальность на устройства iOS, в котором пользователям взаимодействовать с внешним миром через камеры устройства.
С помощью Xamarin, можно также использовать [ARKit с UrhoSharp](arkit/urhosharp.md).

## <a name="coremlcoremlmd"></a>[CoreML](coreml.md)

Модели машинного обучения можно интегрировать в приложения iOS 11 с CoreML. Платформа CoreML предоставляет простой API для включения в проекты приложений, чтобы разрешить проблемы для анализа существующих моделей прямо в приложение.

## <a name="corenfccorenfcmd"></a>[CoreNFC](corenfc.md)

iPhone 7 и более новых устройствах может считывать теги рядом с полем связи действия (NFC), для включения приложений для обнаружения с тегами продуктов, мест или вещи в мире вокруг них.

## <a name="drag-and-dropdrag-and-dropmd"></a>[Перетаскивание](drag-and-drop.md)

Framework перетаскивания обеспечивает поддержку всего операций ввода-вывода для перемещения данных по сенсорного ввода. На устройстве iPad можно перетащить как внутри, так и между различные приложения; Хотя на iPhone, можно перетаскивать только в пределах одного приложения. Предусмотрена поддержка для разных вариантов настройки, включая сложные типы данных, анимации и обработки мультисенсорные жесты.

## <a name="mapkitmapkitmd"></a>[MapKit](mapkit.md)

MapKit имеет ряд улучшений, включая поддержку автоматического маркера, группирование и добавление компас к представлению.

## <a name="pdfkit"></a>PDFKit

PDFKit теперь доступна в iOS 11, перевод PDF создания и изменения в приложения возможности.

## <a name="sirikitsirikitmd"></a>[SiriKit](sirikit.md)

Siri теперь поддерживает еще больше взаимодействия, включая списки и примечания и другие усовершенствования, такие как имена альтернативные приложений.

## <a name="visionvisionmd"></a>[Vision](vision.md)

Предоставляет разнообразные изображения функций обработки и анализа на устройства iOS, включая обнаружение лиц и распознавания, моделей CoreML, новые интерфейсы API обнаружения штрихкода, текст и период обнаружения и общими объект обнаружения и отслеживания.

## <a name="samples"></a>Примеры

Мы сотрудничаем с C# [примеры](https://developer.xamarin.com/samples/ios/iOS11/) для начала работы:

* [Пример ARKit](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
* [ARKit размещение объектов](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
* [ARKit и UrhoSharp](arkit/urhosharp.md)
* [Пример CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreML)
* [Образец распознавания образов CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition)
* [CoreML с Azure пользовательской модели](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
* [Пример модуля чтения CoreNFC тега](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
* [Перетаскивание таблицы представления](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView)
* [Перетаскивание представления коллекции](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
* [Перетаскивание пользовательское представление](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCustomView)
* [DragBoard Drag & Drop образца](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropDragBoard)
* [Пример MapKit](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample)
* [Пример SiriKit](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
* [Обновленный пример framework фотографий](https://developer.xamarin.com/samples/monotouch/ios11/SamplePhotoApp/)
* [Концепции и CoreML пример](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision)
* [Образец обнаружения прямоугольники компьютерного зрения](https://developer.xamarin.com/samples/monotouch/ios11/VisionRects)
* [Образец обнаружения лиц компьютерного зрения](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces)
* [Пример PDKFit мини-приложения](https://developer.xamarin.com/samples/monotouch/ios11/PDFAnnotationWidgetsAdvanced)
* [Пример PDFKit водяного знака](https://developer.xamarin.com/samples/monotouch/ios11/PDFDocumentWatermark)

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения о iOS 11 зайдите [новые возможности в iOS 11](https://developer.apple.com/ios/) документации.


## <a name="related-links"></a>Связанные ссылки

- [Новые возможности в iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Образцы Xamarin iOS 11](https://developer.xamarin.com/samples/ios/iOS11/)
