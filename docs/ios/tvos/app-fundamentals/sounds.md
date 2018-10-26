---
title: Воспроизведение звука в tvOS с помощью AVAudioPlayer в Xamarin
description: В этой статье показано, как использовать вспомогательный класс для управления воспроизведением звукового помощью AVAudioPlayer использования в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: bc1e9febea9529c3fadbacd689404562952276fe
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103648"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Воспроизведение звука в tvOS с помощью AVAudioPlayer в Xamarin

## <a name="about-the-avaudioplayer"></a>О помощью AVAudioPlayer

`AVAudioPlayer` Используется для воспроизведения звуковых данных из памяти или файла. Компания Apple рекомендует использовать этот класс для воспроизведение звука в приложении, если вы не выполняете сети потоковой передачи или потребовать аудио низкую задержку ввода-вывода.

Можно использовать `AVAudioPlayer` для следующих целей:

- Воспроизведение звуков любой продолжительности необязательно выполнения цикла.
- Несколько звук в то же время, с необязательным синхронизации.
- Управлять тома, темп воспроизведения и стерео размещение каждого звуков, воспроизведение.
- Поддерживает функции, такие как Перемотка вперед или назад.
- Получите уровень воспроизведения данных отслеживания использования.

`AVAudioPlayer` поддерживает звуков в любой формат аудио, предоставляемыми iOS, tvOS и OS X такими как `.aif`, `.wav` или `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Воспроизведение звуков в tvOS

Поскольку tvOS поддерживает те же классы аудио элементов, как iOS, см. наш iOS [Игральных звука с помощью AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) документации, полный список воспроизведения звука в приложении Xamarin.tvOS.



## <a name="related-links"></a>Связанные ссылки

- [Справочник по с помощью AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
