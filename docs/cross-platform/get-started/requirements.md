---
title: Требования к системе
description: Этот документ перечисляет требования к системе для создания приложений с помощью Xamarin на компьютерах Windows и Mac. Он также содержит ссылки на инструкции по установке.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: asb3993
ms.author: amburns
ms.date: 08/28/2017
ms.openlocfilehash: 04db2fe4e3385c55ecf653b002b909f16e99a101
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780728"
---
# <a name="system-requirements"></a>Требования к системе

_Предварительные требования к использованию Xamarin_

Продукты Xamarin связаны с пакетами SDK для платформ, начиная от Apple или Google и до iOS или Android, поэтому требования к системе также соответствуют их условиям. На этой странице описывается совместимость систем для платформы Xamarin и указываются рекомендуемые среды разработки и версии пакетов SDK.

- [Среды разработки](#devenv)
- [Требования к macOS](#mac)
- [Требования к Windows](#windows)

Дополнительные сведения о получении программного обеспечения и необходимых пакетов SDK см. в разделе [Инструкции по установке](#install).

<a name="devenv" />

## <a name="development-environments"></a>Среды разработки

В этой таблице показано, какие платформы можно создать, используя различные комбинации ОС и инструментов разработки:

[!include[](~/cross-platform/includes/development-environment.md)]


> [!NOTE]
> При разработке для iOS на компьютерах Windows требуется [доступный по сети компьютер Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md), необходимый для удаленной компиляции и отладки. Это условие также нужно выполнить, если среда Visual Studio запущена на виртуальной машине Windows на компьютере Mac.

<a name="mac" />

## <a name="macos-requirements"></a>Требования к macOS

Чтобы использовать компьютер Mac для разработки в Xamarin, требуются следующие версии программного обеспечения и пакетов SDK. Проверьте версию операционной системы и следуйте инструкциям для [установщика Xamarin](#install).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode можно установить (и обновить) с веб-сайта [developer.apple.com](https://developer.apple.com/xcode/download/) или с помощью Mac App Store.

### <a name="testing--debugging-on-macos"></a>Тестирование и отладка в macOS

В целях тестирования и отладки мобильные приложения Xamarin можно развертывать на физических устройствах через USB (приложения Xamarin.Mac можно тестировать непосредственно на компьютере разработки, а приложения Apple Watch сначала развертываются на парном iPhone).

[!include[](~/cross-platform/includes/macos-testing.md)]


<a name="windows" />

## <a name="windows-requirements"></a>Требования к Windows

Чтобы использовать компьютер Windows для разработки в Xamarin, требуются следующие версии программного обеспечения и пакетов SDK.
Проверьте версию операционной системы (и убедитесь, что вы не используете *Express*-выпуск Visual Studio, в противном случае рекомендуется выполнить обновление до выпуска *Community*).
В установщиках Visual Studio 2015 и 2017 доступна автоматическая установка Xamarin.

[!include[](~/cross-platform/includes/windows-requirements.md)]


> [!NOTE]
>
>* Xamarin для Visual Studio поддерживает любой выпуск Visual Studio 2015 или 2017 (Community, Professional и Enterprise).
>
>* Для разработки приложений Xamarin.Forms для универсальной платформы Windows (UWP) требуется Windows 10 с Visual Studio 2015 или 2017.


### <a name="testing--debugging-on-windows"></a>Тестирование и отладка в Windows

В целях тестирования и отладки мобильные приложения Xamarin можно развертывать на физических устройствах через USB (устройства iOS должны быть подключены к компьютеру Mac, а не к компьютеру с Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]


> [!NOTE]
>
>* [Файлы для скачивания эмулятора Windows Phone 8.1](https://www.microsoft.com/download/details.aspx?id=43719).
>* Эмулятор Windows Phone 10 входит в пакет SDK для UWP для Visual Studio 2015.

<a name="install" />

## <a name="installation-instructions"></a>Инструкции по установке

Последний выпуск Xamarin для macOS можно скачать на веб-странице [xamarin.com/download](http://xamarin.com/download). В Windows следуйте инструкциям по установке [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Полный список текущих версий продукта можно найти на [странице текущих выпусков](http://developer.xamarin.com/releases/current/). На этой странице также приводятся версии отдельных продуктов (и ссылки на заметки о выпуске) для альфа- и бета-каналов.

Конкретные инструкции по [установке](~/cross-platform/get-started/installation/index.md) для каждой платформы можно найти здесь:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Кроме того, доступны дополнительные сведения о [поддерживаемых платформах и требованиях к Xamarin.Forms](~/xamarin-forms/get-started/installation.md).


## <a name="related-links"></a>Связанные ссылки

- [Загрузить Xamarin](https://xamarin.com/download/)
- [Текущие выпуски](https://developer.xamarin.com/releases/current/)
