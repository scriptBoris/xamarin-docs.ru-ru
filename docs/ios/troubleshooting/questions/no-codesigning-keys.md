---
title: 'Почему сборка iOS завершается ошибкой: нет ключи подписывания кода iPhone допустимым найден в цепочке ключей?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 75f9a78fdc7d15df217378491f016478cde369ff
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351105"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Почему сборка iOS завершается ошибкой: нет ключи подписывания кода iPhone допустимым найден в цепочке ключей?

## <a name="cause-of-the-error"></a>Причина ошибки
Это сообщение об ошибке возникает, когда проект рассматриваемый ищет действительные учетные данные подписи кода, но не можете найти их. Подписывание кода является обязательным для тестирования и развертывания на физических устройствах iOS; а также приложения для Ad-hoc и сохранения сборок. 


### <a name="provisioning-devices"></a>Подготовка устройств
Если вы еще не подготовили устройство iOS, прежде чем, следующее руководство поможет выполнить процесс полное пошаговое: [руководство по подготовке устройства](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Ошибка при использовании симулятора iOS

> [!NOTE]
> Эта проблема устранена в последних версиях Xamarin для Visual Studio. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.


Произошла ошибка в Xamarin.Visual Studio 3.11, вызвавшая проект iOS в шаблоне Xamarin.Forms для добавления codesign Entitlements.plist симулятор сборки; фактически блокирует тестирования с помощью симулятора.

### <a name="how-to-fix"></a>Способы устранения
Вы можете устранить проблемы, удалив `<CodesignEntitlements>` флаг из отладочной сборки в CSPROJ-файл. Это можно сделать следующим образом:

*Предупреждение: Ошибки в CSPROJ-файлов может нарушить работу проекта, поэтому рекомендуется создать резервную копию файлов, прежде чем это.*

1. Щелкните правой кнопкой мыши проект iOS в панели решения и выберите **выгрузить проект**
2. Щелкните правой кнопкой мыши проект еще раз и выберите **изменить CSPROJ [имя_проекта]**
3. Найдите группы PropertyGroup отладки, они должны начинаться с флаги, которые выглядят следующим образом:
   - Отладка: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Выпуск: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. В каждой из сборок, использующих симулятор удалите или закомментируйте следующее свойство: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Перезагрузить проект, и можно будет развернуть в симуляторе.

### <a name="next-steps"></a>Следующие шаги
Дополнительная помощь, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения, ознакомьтесь с разделом [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах связи, предложения, а также как файл новую ошибку, при необходимости. 
