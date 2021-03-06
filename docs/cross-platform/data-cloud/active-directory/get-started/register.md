---
title: Шаг 1. Регистрация приложения для использования Azure Active Directory
description: В этом документе описывается регистрация приложения Azure в Azure Active Directory, чтобы могли безопасно доступны с мобильными клиентами.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 7f1e743eea81edc0aa45b49f6acb6a9fd461bc80
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780673"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Шаг 1. Регистрация приложения для использования Azure Active Directory

1. Перейдите к [windowsazure.com](https://manage.windowsazure.com) и выполните вход в учетную запись Майкрософт или учетной записи организации на портале Azure. Если у вас нет подписки Azure, можно получить пробную версию из [azure.com](http://www.azure.com)

2. После входа в систему, перейдите к **Active Directory** раздел (1) и выберите каталог, в которой вы хотите зарегистрировать приложение (2)

  [ ![](register-images/01.-active-directory-in-azure-portal-sml.jpg "раздел и выберите каталог, в которой вы хотите зарегистрировать приложение")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Нажмите кнопку **добавить** для создания нового приложения, затем выберите **добавить приложение, разрабатываемое моей организацией**

  [ ![](register-images/02.-add-new-application-sml.jpg "Добавьте приложение, разрабатываемое моей организацией")](register-images/02.-add-new-application.jpg#lightbox)

4. На следующем экране имя приложения (например) XAM-DEMO).
  Необходимо выбрать **собственное клиентское приложение** в качестве типа приложения.

  ![](register-images/03.-app-name.jpg "Убедитесь, что выберите собственное клиентское приложение в качестве типа приложения")

5. На последнем экране введите **URI перенаправления* , уникальный для вашего приложения, как он будет возвращать этот URI после завершения проверки подлинности.

  ![](register-images/04.-app-redirect.jpg "На последнем экране Укажите URI перенаправления, уникальный для вашего приложения, как он будет возвращать этот URI после завершения проверки подлинности")

6. После создания приложения, перейдите к **Настройка** вкладки. Запишите **идентификатор клиента** которой мы будем использовать в приложении более поздней версии. Кроме того на этом экране можно предоставляете приложений для мобильных устройств доступ к Active Directory или добавить другое приложение, например веб-API или Office 365, который может использоваться с мобильными приложениями, после завершения проверки подлинности.

    ![](register-images/05.-configure.jpg "Кроме того на этом экране можно предоставляете приложений для мобильных устройств доступ к Active Directory или добавить другое приложение, например веб-API или Office 365")



## <a name="related-links"></a>Связанные ссылки

- [Образец Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
