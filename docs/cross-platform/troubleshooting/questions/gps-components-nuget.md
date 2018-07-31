---
title: Унификация Google Play служб компонентов и NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 3f5c5f75ae1c7a44537afa59ff4a15d54b1df50b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351487"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Унификация Google Play служб компонентов и NuGet

## <a name="history"></a>Журнал

Раньше несколько компонентов служб Google-Play и пакеты NuGet:

-   Сервисы Google Play (Froyo)
-   Сервисы Google Play (Gingerbread)
-   Сервисы Google Play (ICS)
-   Сервисы Google Play (JellyBean)
-   Сервисы Google Play (KitKat)

Фактически только поставляется два .jar Google файлы службы Google Play:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

О расхождении существовали, так как наш инструментарий не сказали правильно `aapt.exe` ресурс максимальный уровень API был использоваться для заданного приложения. Это означало, мы получили ошибок компиляции, если мы пытались с использованием привязки служб Google Play (KitKat) на более низкий уровень API, например Gingerbread.

## <a name="unifying-google-play-services"></a>Унификация службы Google Play

В более поздних версиях Xamarin.Android, мы теперь указать `aapt.exe` версии максимального использования ресурсов для использования, поэтому эта проблема не исчезнет для нас.

Это означает, что нет особого смысла иметь отдельные пакеты для Gingerbread/ICS/JellyBean/KitKat (тем не менее мы по-прежнему нужна отдельные привязки для Froyo так как это разные JAR-файл полностью).

Чтобы упростить для разработчиков, мы теперь единой наших компонентов и NuGet пакеты на две части:

-   Сервисы Google Play (Froyo) (привязывает `google-play-services-froyo.jar`)
-   Сервисы Google Play (привязывает `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Какой из них следует использовать?

В практически в каждом случае следует использовать службы Google Play. Единственная причина для использования пакета (Froyo) — Если вы активно используете Froyo. Единственная причина, что существует этот отдельный JAR-файл из Google — поскольку Froyo на такой небольшой процент устройств, сами по себе решили прекращена поддержка, поэтому этот JAR-файл является моментальным снимком зафиксировано, не поддерживается, служб Google Play.

### <a name="note-about-gingerbread"></a>Примечание о Gingerbread

Gingerbread не фрагмент по умолчанию поддерживают, и по этой причине некоторые классы в привязке будет невозможно использовать в приложении на устройстве Gingerbread во время выполнения. Классы, такие как `MapFragment` не будут работать на Gingerbread и вместо этого следует использовать вариант их поддержки `SupportMapFragment`. Он работает для разработчика, чтобы узнать, какие использовать. Эта несовместимость регистрируется с Google в своей документации служб Google Play.

### <a name="what-happens-to-the-old-componentsnugets"></a>Что происходит с старые компоненты/NuGet?

Так как они больше не нужны, у нас есть отключено/Delisted следующие компоненты и пакеты NuGet:

-   Сервисы Google Play (Gingerbread)
-   Сервисы Google Play (JellyBean)
-   Сервисы Google Play (KitKat)

Существующий _служб Google Play (ICS)_ /Nuget компонента был переименован в _сервисы Google Play_ и будет храниться в актуальном состоянии в дальнейшем. Использовать эту следует обновить все проекты, ссылки на один из пакетов отключено/Delisted.

Отключенные компоненты по-прежнему существует и должен быть для проектов, которые они все еще указывает ссылка, чтобы избежать нарушения их работоспособности, которые могут быть восстановлены. Аналогичным образом delisted пакеты NuGet по-прежнему существует и может быть восстановлена. Они не обновляются в дальнейшем.
