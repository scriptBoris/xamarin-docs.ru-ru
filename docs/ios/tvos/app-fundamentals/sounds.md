---
title: Воспроизведение звука в tvOS с помощью AVAudioPlayer в Xamarin
description: В этой статье показано, как использовать вспомогательный класс для управления воспроизведением звукового помощью AVAudioPlayer использования в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 2ce1d4b8564ef9599581aabd6a72ba3af12ec251
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241355"
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
