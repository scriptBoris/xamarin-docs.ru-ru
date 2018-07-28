---
title: Принципы работы приложения Xamarin.Mac
description: Этот документ содержит ссылки на руководства, описывающие основные понятия, необходимые для понимания при разработке приложений Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: e085cf33615d216e1ce9963254050ef2b623ae40
ms.sourcegitcommit: d0da5ce4158239abd2b36f67550e9b475055766a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320808"
---
# <a name="xamarinmac-application-fundamentals"></a>Принципы работы приложения Xamarin.Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Общие шаблоны и способы](~/mac/app-fundamentals/patterns.md)

На протяжении всего API Apple, предоставляемых через C# некоторые стили и шаблоны отобразились снова и снова. Если у вас есть опыт программирования с помощью Xamarin.iOS, они могут быть вам знакомы. Документация будет часто ссылаться на эти шаблоны и идиомы несколько раз, поэтому наличие основательного понимания их поможет вам разобраться в документации, которую вы найдете.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Основные сведения об API-интерфейсах Mac](~/mac/app-fundamentals/mac-apis.md)

Большую часть времени разработки с помощью Xamarin.Mac вы думаете, читать и писать на C# не столь важно, с помощью базовых интерфейсов Objective-C API. Тем не менее иногда вы будет нужно прочитать документацию по API Apple, перевод ответ на Stack Overflow в решение для решения проблемы или сравнения в существующий образец.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[Консольные приложения](~/mac/app-fundamentals/console.md)

Вы также можете создавать «автономная» консольных приложений, которые обращаются к macOS собственного API-интерфейсов с помощью Xamarin.Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Работа с xib-файлы](~/mac/app-fundamentals/xib.md)

В этой статье рассматривается работа с xib-файлы, созданные в конструктора Interface Builder для создания и поддержки пользовательских интерфейсов для приложения Xamarin.Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.xib меньше пользовательского интерфейса](~/mac/app-fundamentals/xibless-ui.md)

В этой статье описывается создание пользовательского интерфейса приложения непосредственно из кода C# без использования конструктора Interface Builder с .storyboard или .xib файлов.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Работа с изображениями](~/mac/app-fundamentals/image.md)

В этой статье рассматривается работа с изображениями и значками в приложении Xamarin.Mac. Здесь рассматривается создание и обслуживание изображений, необходимых для создания значок приложения и использование изображений в коде C# и конструктора Interface Builder.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Привязка данных и кодирования ключ значение](~/mac/app-fundamentals/databinding.md)

В этой статье рассматривается, используя ключ значение кодирования и наблюдения, чтобы разрешить для привязки данных к элементам пользовательского интерфейса в конструктора Interface Builder ключ значение. При использовании этого метода можно значительно уменьшить объем кода C#, который должен быть написано для приложения Xamarin.Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Работа с базами данных](~/mac/app-fundamentals/databases.md)

В этой статье рассматривается, используя ключ значение кодирования и наблюдения, чтобы разрешить для привязки данных с прямым доступом к базам данных SQLite для элементов пользовательского интерфейса в конструктора Interface Builder ключ значение. Здесь также рассматриваются использование ORM для SQLite.NET для предоставления доступа к данных SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Работа с копирования и вставки](~/mac/app-fundamentals/copy-paste.md)

В этой статье рассматривается работа с монтажного стола для предоставления скопируйте и вставьте его в приложении Xamarin.Mac. Показано, как работать со стандартными типами данных, которые могут совместно использоваться несколькими приложениями и как обеспечить поддержку пользовательских данных в приложении дают.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Песочница приложения Xamarin.Mac](~/mac/app-fundamentals/sandboxing.md)

В этой статье рассматриваются песочница приложения Xamarin.Mac для выпуска в App Store. Он включает в себя все элементы, которые необходимо выполнить в "песочницы": каталоги контейнера, назначения, определенного пользователем разрешения, разделение привилегий и принудительного применения ядра.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Воспроизведение звука с помощью AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

В этой статье показано, как использовать вспомогательный класс для управления воспроизведением звукового использования помощью AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Сообщения об ошибках](~/mac/app-fundamentals/troubleshooting.md)

Иногда мы все застрять при работе над проектом, невозможность получения API для работы того, который мы хотим или в попытке устранить ошибки. Наша цель в Xamarin для для успешной работы в написании мобильных и настольных приложений, и приведены некоторые материалы для ознакомления.
