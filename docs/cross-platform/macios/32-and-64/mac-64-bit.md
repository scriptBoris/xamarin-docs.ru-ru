---
title: Обновление приложений Xamarin.Mac единой на 64-разрядную версию
description: В этом руководстве описывается, как обновить приложения Xamarin.Mac для 64-разрядных целевых. Он также предоставляет примеры различных типов ошибок, которые могут возникнуть при внесении этого изменения.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 3667f129cb566f71e9afd2969af13fe5b9682882
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109384"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Обновление приложений Xamarin.Mac единой на 64-разрядную версию

Начиная с января 2018 г., компания Apple требует что новые [отправки в Mac App Store предназначенных для 64-разрядных](https://developer.apple.com/news/?id=06282017a). Приложения, уже доступные в Mac App Store необходимо обновить целевой объект 64-разрядных с июня 2018 г.

**Файл** > **New** шаблон проекта Xamarin.Mac создает 64-разрядных приложений по умолчанию, поэтому все недавно созданные приложения уже 64-разрядными платформами и не требует изменения.

## <a name="targeting-64-bit"></a>Предназначенных для 64-разрядной

1. Откройте **параметры проекта** окно автоматически выполняется приложение Xamarin.Mac:

   ![Контекстное меню для проекта](mac-64-bit-images/1-contextual_menu-vsmac.png "контекстное меню для проекта")

2. Выберите **сборки Mac** и задайте **Поддерживаемые архитектуры** для **x86\_64**:

   [![Установка поддерживаемых архитектур x86_64](mac-64-bit-images/2-project_options-vsmac.png "присвоить x86_64 Поддерживаемые архитектуры")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Если приложение имеет любых внешних зависимостей, например собственные ссылки или проектов привязки, обновите их к целевому объекту 64-разрядной.

### <a name="errors"></a>Ошибки

При первом построении и запуске приложения с поддержкой 64-разрядной, могут возникнуть ошибки ссылку от проблем, clang или среды выполнения. Эти ошибки могут возникать, если сторонние зависимости, например, собственные ссылки в Xamarin.Mac или проекты привязки или вручную загружать системных платформ — не были обновлены на 64-разрядную версию.

> [!TIP]
> Преобразование проекта в 64-разрядных существенных изменений и косвенно позволит обнаружить различные ошибки программирования. В частности его может изменить размер и выравнивание структур данных, которые повлияют на подписей p/invoke и машинный код, связанный в проекте. Просмотрите все предупреждения сборки, учитывая и тестирования приложения throughly после этого для перехвата возможных проблем.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Пример ошибки, полученные из динамически скомпонованные сторонних зависимостей, не предназначенных для 64-разрядных:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Эта ошибка может следовать во время выполнения, `dlopen` возвращение `IntPtr.Zero` вместо ожидаемого дескриптора.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Пример ошибки, полученные из статически компонуемые сторонних зависимостей, не предназначенных для 64-разрядных:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Для успешного построения и запуска, обновите эти зависимости на 64-разрядную версию и перекомпилировать приложение.

