---
title: Настройка Visual Studio 2017
description: Эта статья описывает, как настроить Visual Studio 2017 для развертывания Xamarin.iOS. В частности, она описывает настройку установленной версии Xamarin.iOS, панели инструментов iOS и раскрывающегося меню "Платформы решения".
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/16/2018
ms.openlocfilehash: 70633877fb07f52ce4e7a399668be6268942b137
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785958"
---
# <a name="configuring-visual-studio-2017"></a>Настройка Visual Studio 2017

_В этой статье описываются различные параметры конфигурации Xamarin.iOS для Visual Studio._

## <a name="using-matching-xamarinios-versions"></a>Использование соответствующих версий Xamarin.iOS

Среда Visual Studio 2017 должна использовать ту же версию Xamarin.iOS, которая установлена на узле сборки Mac. Для этого должны выполняться указанные ниже требования.

 - Если вы используете Visual Studio 2017, выберите **стабильный** канал обновлений в Visual Studio для Mac.

 - Если вы используете предварительную версию Visual Studio 2017, выберите **альфа**-канал обновлений в Visual Studio для Mac.

> [!NOTE]
> Начиная с [Visual Studio 2017 версии 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) система Visual Studio 2017 автоматически определяет, использует ли узел сборки Mac ту же версию Xamarin.iOS, что и Windows. В случае несовпадения версий Visual Studio 2017 предлагает удаленно установить правильную версию на узле сборки Mac. Дополнительные сведения см. в разделе [Автоматическая подготовка Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) руководства [Связывание с компьютером Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="ios-toolbar"></a>Панель инструментов iOS

Когда проект iOS открыт в Visual Studio 2017, должна отображаться панель инструментов iOS.  По умолчанию она содержит четыре кнопки, которые можно использовать для разработки Xamarin.iOS.

![Панель инструментов iOS Visual Studio 2017](config-options-images/ios-toolbar.png "Панель инструментов iOS Visual Studio 2017")

- **Связать с Mac** — открывает диалоговое окно связывания с компьютером Mac. Включена, если проект iOS открыт в Visual Studio 2017.
- **Показать симулятор iOS** — на узле сборки Mac окно симулятора iOS открывается на переднем плане. Включена, если проект iOS открыт в Visual Studio 2017.
- **Журнал устройств** — открывает окно, в котором можно проверять журналы устройств. Включена, если проект iOS открыт в Visual Studio 2017.
- **Показать IPA-файл на сервере сборки** — открывает на узле сборки Mac окно, показывающее расположение IPA-файла для приложения. Включается после завершения сборки, для которой был создан IPA-файл.

Если эта панель инструментов не отображается, откройте меню **Вид** в Visual Studio 2017 и выберите **Панели инструментов > iOS**:

![Включение панели инструментов для iOS](config-options-images/ios-toolbar-enable.png "Включение панели инструментов для iOS")

## <a name="solution-platforms-drop-down-menu"></a>Раскрывающееся меню "Платформы решения"

Раскрывающееся меню **Платформы решения** позволяет выбрать для следующей сборки целевое физическое устройство или симулятор.

Чтобы это раскрывающееся меню всегда отображалось на стандартной панели инструментов, выполните следующие действия.

- В Visual Studio 2017 щелкните стрелку "вниз" в правом крайнем углу стандартной панели инструментов.
- Выберите пункт **Добавить или удалить кнопки** 
- Убедитесь, что флажок **Платформы решения** установлен:

![Включение раскрывающегося меню "Платформы решения"](config-options-images/solution-platforms-enable.png "Включение раскрывающегося меню \"Платформы решения\"")

При открытии проекта iOS панели инструментов **Стандартная** и **iOS** должны выглядеть как на следующем снимке экрана:

![Панели инструментов "Стандартная" и iOS](config-options-images/toolbars.png "Панели инструментов \"Стандартная\" и iOS")


