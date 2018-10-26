---
title: PhotoKit в Xamarin.iOS
description: В этом документе описывается PhotoKit, обсуждение модели объектов, как выполнить запрос модели данных и сохранения изменений в библиотеке фотографий.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: d59cac9403a244ce553d84e0590b8a9c3d4d2f30
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102715"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit в Xamarin.iOS

PhotoKit — это новая платформа, которая позволяет приложениям запрашивать библиотека изображений системы и создать настраиваемые пользовательские интерфейсы для просмотра и изменения его содержимого. Она включает множество классов, представляющих изображения и видео, а также коллекции средств, таких как диски и папки.

## <a name="model-objects"></a>Объекты модели

PhotoKit представляет этих средств в новые, он вызывает объекты модели. Модель объектов, представляющих фотографии и видео, сами имеют тип `PHAsset`. Объект `PHAsset` содержит метаданные, такие как тип носителя ресурса и дата его создания.
Аналогичным образом `PHAssetCollection` и `PHCollectionList` классы содержат метаданные о коллекции активов и списки коллекции соответственно. Коллекции активов — группы ресурсов, таких как все фотографии и видео для определенного года. Аналогичным образом списки коллекции представляют собой группы коллекций ресурсов, например фотографии и видео, сгруппированные по годам.

## <a name="querying-model-data"></a>Запрос данных модели

PhotoKit позволяет легко запрашивать данные модели через широкий набор методов выборки. Например, чтобы получить все образы, следует вызвать `PFAsset.Fetch`, передав `PHAssetMediaType.Image` тип носителя.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

`PHFetchResult` Экземпляра будет содержать все `PFAsset` экземпляров, представляющих изображения. Для получения изображений, сами используйте `PHImageManager` (или версии, которая кэширования `PHCachingImageManager`) для создания запроса для образа путем вызова `RequestImageForAsset`. Например, следующий код извлекает изображения для каждого ресурса в `PHFetchResult` для отображения в ячейки представления коллекций:


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Это приводит к сетке образов, как показано ниже:

![](photokit-images/image4.png "Отображение сетки образов выполняющегося приложения")
 
## <a name="saving-changes-to-the-photo-library"></a>Сохранение изменений в библиотеке фотографий

Это способ обработки запроса и чтения данных. Вы также можете записать изменения обратно в библиотеку. Так как несколько заинтересованных приложения способны взаимодействовать с библиотекой фотографий системы, вы можете зарегистрировать наблюдателя для получения уведомлений об изменениях, с помощью PhotoLibraryObserver. Затем при поступлении изменения, чтобы обновление приложения соответствующим образом. Например Вот простой реализации для перезагрузки выше представления коллекции:

    class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
    {
        readonly PhotosViewController controller;
        public PhotoLibraryObserver (PhotosViewController controller)
        
        {
            this.controller = controller;
        }
    
        public override void PhotoLibraryDidChange (PHChange changeInstance)
        {
            DispatchQueue.MainQueue.DispatchAsync (() => {
            var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
            controller.fetchResults = changes.FetchResultAfterChanges;
            controller.CollectionView.ReloadData ();
            });
        }
    }
    
Чтобы фактически записи изменений обратно из приложения, создайте запрос на изменение. Каждый из классов модели имеет класс связанные с ним изменения запроса. Например можно изменить PHAsset, создав PHAssetChangeRequest. Приведены шаги для изменения, которые записаны в библиотеке фотографий и отправляемых наблюдателям отличается от приведенного выше.

-   Выполните операцию редактирования.
-   Сохраните данные фильтрованного изображения к экземпляру PHContentEditingOutput.
-   Сделайте запрос на изменение для публикации изменения формы редактирования выходные данные.

Ниже приведен пример, который записывает изменение изображения, которое применяет фильтр noir образ основных компонентов:

    void ApplyNoirFilter (object sender, EventArgs e)
    {
            
            Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {
            
        // perform the editing operation, which applies a noir filter in this case
            var image = CIImage.FromUrl (input.FullSizeImageUrl);
            image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
            var noir = new CIPhotoEffectNoir {
                Image = image
            };
            var ciContext = CIContext.FromOptions (null);
            var output = noir.OutputImage;
            var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
            imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
            var editingOutput = new PHContentEditingOutput(input);
            var adjustmentData = new PHAdjustmentData();
            var data = uiImage.AsJPEG();
            NSError error;
            data.Save(editingOutput.RenderedContentUrl, false, out error);
            editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
            PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
                PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
                request.ContentEditingOutput = editingOutput;
          },
          (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
      });
    }
    
Когда пользователь выбирает кнопку, применения фильтра:

![](photokit-images/image5.png "Пример фильтра")
 
И благодаря PHPhotoLibraryChangeObserver, это изменение отражается в представлении коллекции, когда пользователь переходит назад:

![](photokit-images/image6.png "Это изменение отражается в представлении коллекции, когда пользователь переходит назад")
