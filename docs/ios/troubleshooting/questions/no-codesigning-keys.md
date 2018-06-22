---
title: 'Почему моя сборки iOS не с: не допустимым iPhone ключей подписывания найден код в цепочке ключей?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5334e3009906896644caa47c715f912fa379c627
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776694"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Почему моя сборки iOS не с: не допустимым iPhone ключей подписывания найден код в цепочке ключей?

## <a name="cause-of-the-error"></a>Причина ошибки
Это сообщение об ошибке возникает, когда проект рассматриваемой Ищет допустимые учетные данные подписи кода, но не удается найти их. Подписывание кода является обязательным для тестирования и развертывания на физических устройствах iOS; а также Ad-hoc & приложения хранилища сборки. 


### <a name="provisioning-devices"></a>Подготовка устройства
Если еще не подготовки устройства iOS до следующее руководство поможет выполнить процесс полное пошаговое: [руководство подготовки устройства](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Ошибки при использовании симулятор iOS

> [!NOTE]
> Эта проблема устранена в последних версиях Xamarin для Visual Studio. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, проверьте файл [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.


Произошла ошибка в Xamarin.Visual Studio 3.11, вызвавшая проект iOS в шаблоне Xamarin.Forms, чтобы добавить разработки Entitlements.plist в имитаторе сборки; эффективно блокирует тестирования с помощью симулятора.

### <a name="how-to-fix"></a>Сведения по устранению
Вы можете устранения этой проблемы, удалив `<CodesignEntitlements>` флаг из отладочной сборки в CSPROJ-файл. Это можно сделать следующим образом:

*Предупреждение: Ошибки в CSPROJ-файлов может нарушить работу проекта, поэтому рекомендуется создать резервную копию файлов, прежде чем пытаться это.*

1. Щелкните правой кнопкой мыши проект iOS в области решения и выберите **выгрузить проект**
2. Щелкните правой кнопкой мыши проект еще раз и выберите **изменить CSPROJ [имя_проекта]**
3. Найдите группы PropertyGroup отладки, они должны начинаться с флаги, которые выглядят следующим образом:
   - Отладка: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Г. `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. В каждой из сборок, использующих симулятора удалите или закомментируйте следующее свойство: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Перезагрузить проект, и можно развернуть на симуляторе.

### <a name="next-steps"></a>Следующие шаги
Для получения дополнительной помощи, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения см. в разделе [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах контактов, предложения, а также как файл новую ошибку, при необходимости. 
