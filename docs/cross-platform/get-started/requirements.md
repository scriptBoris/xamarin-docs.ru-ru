---
title: Требования к системе
description: Этот документ перечисляет требования к системе для создания приложений с помощью Xamarin на компьютерах Windows и Mac. Он также содержит ссылки на инструкции по установке.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 87c3a189851db66e630095eae31434a590050154
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292276"
---
# <a name="system-requirements"></a>Требования к системе

Продукты Xamarin связаны с пакетами SDK для платформ, начиная от Apple или Google и до iOS или Android, поэтому требования к системе также соответствуют их условиям. На этой странице описывается совместимость систем для платформы Xamarin и указываются рекомендуемые среды разработки и версии пакетов SDK.

Дополнительные сведения о получении программного обеспечения и необходимых пакетов SDK см. в разделе [Инструкции по установке](#installation-instructions).

## <a name="development-environments"></a>Среды разработки

В этой таблице показано, какие платформы можно создать, используя различные комбинации ОС и инструментов разработки:

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> При разработке для iOS на компьютерах Windows требуется [доступный по сети компьютер Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md), необходимый для удаленной компиляции и отладки. Это условие также нужно выполнить, если среда Visual Studio запущена на виртуальной машине Windows на компьютере Mac.

## <a name="macos-requirements"></a>Требования к macOS

Чтобы использовать компьютер Mac для разработки в Xamarin, требуются следующие версии программного обеспечения и пакетов SDK. Проверьте версию операционной системы и следуйте инструкциям для [установщика Xamarin](#installation-instructions).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode можно установить (и обновить) с веб-сайта [developer.apple.com](https://developer.apple.com/xcode/download/) или с помощью Mac App Store.

### <a name="testing--debugging-on-macos"></a>Тестирование и отладка в macOS

- В целях тестирования и отладки мобильные приложения Xamarin можно развертывать на физических устройствах через USB (приложения Apple Watch сначала развертываются на парном iPhone).
- Приложения Xamarin.Mac тестируются непосредственно на компьютере разработки.

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> Будущий выпуск Xamarin.Mac 4.8 будет поддерживать только macOS 10.9 или более поздней версии.
> Предыдущие версии Xamarin.Mac поддерживали macOS 10.7 или более поздней версии, но в этих версиях macOS нет достаточной инфраструктуры TLS для поддержки TLS 1.2. Для macOS 10.7 или macOS 10.8 используйте Xamarin.Mac 4.6 или более ранней версии.

## <a name="windows-requirements"></a>Требования к Windows

Чтобы использовать компьютер Windows для разработки в Xamarin, требуются следующие версии программного обеспечения и пакетов SDK.
Проверьте версию операционной системы (и убедитесь, что вы не используете *Express*-выпуск Visual Studio, в противном случае рекомендуется выполнить обновление до выпуска *Community*).
В установщике Visual Studio 2017 есть параметр для автоматической установки Xamarin (**разработка мобильных приложений на .NET**).

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
> - Xamarin для Visual Studio поддерживает все выпуски Visual Studio 2017 (Community, Professional и Enterprise).
> - Для разработки приложений Xamarin.Forms для универсальной платформы Windows (UWP) требуется Windows 10 с Visual Studio 2017.

### <a name="testing--debugging-on-windows"></a>Тестирование и отладка в Windows

В целях тестирования и отладки мобильные приложения Xamarin можно развертывать на физических устройствах через USB или по беспроводному соединению (устройства iOS должны быть подключены к компьютеру Mac, а не к компьютеру с Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>Инструкции по установке

Последний выпуск Xamarin для macOS можно скачать на веб-странице [xamarin.com/download](http://xamarin.com/download). В Windows следуйте инструкциям по установке [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Полный список текущих версий продукта можно найти на [странице текущих выпусков](http://developer.xamarin.com/releases/current/). На этой странице также приводятся версии отдельных продуктов (и ссылки на заметки о выпуске) для альфа- и бета-каналов.

Конкретные инструкции по [установке](~/cross-platform/get-started/installation/index.md) для каждой платформы можно найти здесь:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Кроме того, доступны дополнительные сведения о [поддерживаемых платформах и требованиях к Xamarin.Forms](~/get-started/installation.md).

## <a name="related-links"></a>Связанные ссылки

- [Загрузить Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Текущие выпуски](https://developer.xamarin.com/releases/current/)
