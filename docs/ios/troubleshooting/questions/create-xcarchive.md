---
title: Имеется возможность создания архива .xcarchive из Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 23af3bf277ab68ffe93df2e1ee8ee64afc01828d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776811"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>Имеется возможность создания архива .xcarchive из Visual Studio?

## <a name="for-xamarin-4"></a>Для Xamarin 4

Начиная с Xamarin 4.x, стало возможным создание `.xcarchive` из Windows, задав `ArchiveOnBuild` свойства `true`. Например, с помощью `MSBuild` в командной строке:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive` Будут помещены в `$HOME/Library/Developer/Xcode/Archives` каталог на узле сборки Mac, Xcode и Xamarin Studio поиск для отображения ранее созданного архива.

См. в этом [учет форумы Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) для некоторых brief Дополнительные примечания о `ArchiveOnBuild` свойство. См. в документации [Xamarin.iOS командной строке создается в Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) Дополнительные сведения о `ServerAddress` и `ServerUser` свойства.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Для Xamarin 3 и более ранних версий

Расширение Visual Studio 3.x Xamarin предоставляет механизм для создания `.xcarchive` архивов. С другой стороны, логику, используемую для создания `.xcarchive` архивирует в Xamarin Studio на Mac [описан в данном разделе](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), поэтому, возможно, для создания собственных `.xcarchive` вручную, если необходимо.

Но Обратите внимание, что нет необходимости `.xcarchive` для отправки в магазин приложений. Вы можете отправить файл IPA, при условии, что он подписан с использованием профиля хранилища распространения приложения (не Ad Hoc профиль распространения).

На самом деле, даже просто ZIP- `.app` пакета (который подписывается с профилем приложения хранения распространителя) и отправлять их `.zip` файл в магазине приложений.

В любом случае приложение загрузчика приложений можно использовать для отправки приложений (а не Xcode).

