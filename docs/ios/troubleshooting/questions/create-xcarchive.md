---
title: Имеется возможность создания xcarchive-архива из Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 952777b3178b82657c8f64d5882c532c188c7a79
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107239"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>Имеется возможность создания xcarchive-архива из Visual Studio?

## <a name="for-xamarin-4"></a>Для Xamarin 4

По состоянию на Xamarin 4.x, стало возможным создание `.xcarchive` из Windows, задав `ArchiveOnBuild` свойства `true`. Например, с помощью `MSBuild` в командной строке:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive` Будут помещены в `$HOME/Library/Developer/Xcode/Archives` каталог на узле сборки Mac, Xcode и Xamarin Studio поиска для отображения созданные ранее архивы.

См. в разделе, это [блога Xamarin форумы](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) для некоторых brief Дополнительные примечания о `ArchiveOnBuild` свойство. См. в документации [Xamarin.iOS командной строки основан на Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) более подробные сведения о `ServerAddress` и `ServerUser` свойства.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Для Xamarin 3 и более ранних версий

Расширение Visual Studio 3.x Xamarin не предоставляет механизм для создания `.xcarchive` архивов. С другой стороны, логика, используемая для создания `.xcarchive` архивирует в Xamarin Studio для Mac [описан здесь](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), поэтому, возможно, для создания собственных `.xcarchive` вручную в том случае, если необходимо.

Но стоит отметить, что не требуется `.xcarchive` для отправки в App Store. Вы можете отправить файл IPA, до тех пор, пока она подписана с профилем распространения App Store (не Ad Hoc профиль распространения).

На самом деле, вы можете даже просто заархивировать `.app` пакета (который подписан профиль распространения App Store) и отправить их в `.zip` файл в app Store.

В любом случае загрузчик приложений можно использовать для отправки приложений (а не Xcode).

