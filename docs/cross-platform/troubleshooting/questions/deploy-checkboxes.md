---
title: Отключены флажки развертывания в Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 35efb00a721062ad3217300f7e3a5430b1bd1560
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528147"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Отключены флажки развертывания в Configuration Manager

С момента Xamarin 3.5, проекты Xamarin.iOS развертываются автоматически при каждом нажатии клавиши **запустить** кнопки панели инструментов или выберите **Отладка > Начать отладку** пункта меню. Вы по-прежнему потребуется задать нужный проект приложения Xamarin.iOS как **запускаемым проектом** перед выполнением любой из этих команд.

По этой причине **развернуть** флажки преднамеренно отключены в Visual Studio Configuration Manager для проектов Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager, отображается флажок «Развернуть» отключена для проекта Xamarin.iOS в Xamarin 3.5")

Это изменение устраняет ошибку, которая может отображаться в более старых версиях Xamarin (версия 3.3 и более ранних версий), если в проект приложения Xamarin.iOS не был задан для развертывания:

![](deploy-checkboxes-images/error.png "Диалоговое окно ошибки: iPhoneApp1 проекта должен быть развернут, прежде чем можно будет запустить. Убедитесь, что проект выбран для развертывания в диспетчере конфигурации решения.")
