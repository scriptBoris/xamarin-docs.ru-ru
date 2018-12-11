---
title: Подключение предварительных версий Visual Studio 2019 Preview в Windows и macOS
description: Это руководство содержит инструкции по использованию предварительной версии Visual Studio 2019 Preview в Windows для создания приложений для iOS с использованием предварительной версии Visual Studio 2019 Preview для Mac в macOS для размещения сборок.
ms.prod: xamarin
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/04/2018
ms.openlocfilehash: 1eeb79737bd712da64ce34a6b4fe83ce2823e3eb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899367"
---
# <a name="visual-studio-2019-preview-pairing"></a>Связывание экземпляров Visual Studio 2019 Preview

![Предварительный просмотр](~/media/shared/preview.png)

[Visual Studio 2019 Preview для Mac](https://docs.microsoft.com/visualstudio/mac/install-preview) — первая версия Visual Studio, в которой одновременная установка полностью поддерживается в macOS. При этом у разработчиков приложений для iOS в Windows складывается новая ситуация: если на их узле сборки Mac есть и Visual Studio 2017 для Mac (версии 7.x), и Visual Studio 2019 Preview для Mac (версия 8.0), какая из них работает в качестве узла сборки для их установки в Windows?

_По умолчанию_ именно стабильный выпуск &ndash; Visual Studio 2017 для Mac (версия 7.7) &ndash; будет использоваться с Visual Studio в Windows независимо от того, используете ли вы Visual Studio 2017 или Visual Studio 2019 Preview в Windows.

Для полноценного тестирования Visual Studio 2019 Preview в Windows и macOS:

1. Установите и используйте версию Visual Studio 2019 Preview (версия 16.0) на компьютере с Windows.
2. Установите Visual Studio 2019 Preview для Mac (версия 8.0) на своем Mac.
3. В **Visual Studio 2019 для Mac**:

    1. Перейдите в раздел **Visual Studio > Проверка обновлений**.

    2. В окне **Обновление Visual Studio** выберите **предварительную версию Xamarin** в канале обновления. Появится такое предупреждение:

    > [!WARNING]
    > Попробуйте последние сборки Visual Studio 2019 Preview для Mac сборок с новейшими версиями среды выполнения Mono и Xamarin SDK. **Установка сборок из этого канала приведет к нарушению работы Visual Studio 2017 для Mac.** Используйте этот канал только в том случае, если вы создаете приложения Xamarin в операционной системе Windows с помощью выпусков Visual Studio 2019 Preview.

    В. Нажмите клавишу **Переключить канал** , чтобы включить узел сборки предварительной версии.

4. Используйте инструкции по [связыванию с компьютером Mac для разработки Xamarin.iOS](index.md) и [советы по устранению неполадок](troubleshooting.md) (при необходимости).