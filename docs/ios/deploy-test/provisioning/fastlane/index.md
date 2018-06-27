---
title: Общие сведения о Fastlane для iOS
description: Это руководство описывает разнообразные инструменты Fastlane, которые можно использовать для подписывания кода приложений iOS. Оно показывает, как обновить, установить и использовать средства fastlane.
ms.prod: xamarin
ms.assetid: 8202C57D-22FF-4224-A5B1-AAEF12B7C106
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: ffb7e0a088bcd227f45b97229f089ef59d4d6608
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785493"
---
# <a name="introduction-to-fastlane-for-ios"></a>Общие сведения о Fastlane для iOS

Fastlane — это проект с открытым кодом, призванный упростить запутанную и часто трудоемкую процедуру выпуска приложений для iOS и Android. Он состоит из несколько служебных программ, каждая из которых охватывает определенный аспект выпуска приложений:

- [deliver](https://github.com/fastlane/fastlane/tree/master/deliver#readme) — контролирует снимки экрана, метаданные и пакеты приложений и отправляет их в iTunes Connect.
- [produce](https://github.com/fastlane/fastlane/tree/master/produce#readme) — создает приложение в iTunes Connect и на портале Developer (это часто называется AppID). Также включает поддержку для групп приложений и служб приложений.
- [pem](https://github.com/fastlane/fastlane/tree/master/pem#readme) — создает и контролирует профили подготовки Push-уведомлений.
- [gym](https://github.com/fastlane/fastlane/tree/master/gym#readme) — может использоваться для сборки и подписания приложения iOS. (Приложения Xamarin уже используют MSBuild для сборки, подписывания и архиваций приложений.)
- [cert](https://github.com/fastlane/fastlane/tree/master/cert#readme) — создает и обслуживает сертификаты для подписи 
- [sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme) — создает и обслуживает профили подготовки
- [match](https://github.com/fastlane/fastlane/tree/master/match#readme) — создает и обслуживает сертификаты и профили, а также сохраняет их в репозитории Git для синхронизации между членами команды разработчиков.

Работать с Fastlane можно по-разному: с помощью команд терминала, файлов или переменных среды для сборок непрерывной интеграции. 

Это руководство рассматривает настройку устройства для разработки приложений iOS. Основное внимание уделяется служебным программам **cert**, **sigh** и **match**. 

Руководство позволяет быстро освоить распространение приложений и рассказывает, как полностью автоматизировать этот процесс на сервере непрерывной интеграции. При этом следует отметить, что Fastlane — это сторонний разработчик, создающий инструменты для работы с проектами Xcode, а значит, некоторые средства или команды, такие как `fastlane init`, могут не работать должным образом с файлами CSPROJ. Дополнительные сведения об использовании Fastlane, вспомогательных инструментах и выпуске приложений для Android с помощью Fastlane: [https://fastlane.tools/](https://fastlane.tools/).

<a name="Installation" />

## <a name="installation"></a>Установка

1. На компьютере с macOS должны быть установлены средства командной строки Xcode. Чтобы установить их, выполните в терминале команду `xcode-select --install`. Если они уже установлены, отобразится следующая ошибка:

    ```bash
    error: command line tools are already installed, use "Software Update" to install updates
    ```

2. Скачайте средства Fastlane из [https://download.fastlane.tools](https://download.fastlane.tools). 

    > [!NOTE]
    > Инструменты Fastlane можно установить с помощью Homebrew, используя `brew cask install fastlane`, или с помощью Rubygems (2.0 и более поздние версии), используя `sudo gem install fastlane –NV`. Однако именно установщик обеспечивает создание всех необходимых зависимостей. 

3. Установите Fastlane, распаковав файл и дважды щелкнув исполняемый файл `install`. Если возникнет ошибка с указанием, что файл невозможно открыть, так как он получен от неизвестного разработчика, нажмите кнопку "OK" и выполните следующие действия:
    - Щелкните исполняемый файл `install`, удерживая клавишу CTRL. Откроется следующее диалоговое окно:

      ![](images/fastlane-image12.png "Диалоговое окно установки")
    
    - Нажмите кнопку "OK", чтобы начать установку инструментов Fastlane

4. В терминале откроется диалоговое окно, показанное ниже. Нажмите клавишу `y`:

  ![](images/fastlane-image13.png "Окно терминала")
 
4. Перед первым использованием Fastlane выполните команду `which fastlane`. Путь должен выглядеть следующим образом: 

    ```bash
    /Users/[user]/.fastlane/bin
    ```

5. Если путь совпадает с указанным выше, все готово к работе.

     Если нет, сделайте следующее. В macOS откройте `.bash_profile` (скрытый текстовый файл в основном каталоге) с помощью следующей команды:

    ```bash
    open ~/.bash_profile
    ```

6. Добавьте следующую переменную среды PATH и сохраните файл: 

    ```bash
    export PATH="$HOME/.fastlane/bin:$PATH"
    ```

7.  Запустите `which fastlane` еще раз и проверьте, что путь выглядит так: `/Users/[user]/.fastlane/bin`


## <a name="updating-fastlane"></a>Обновление Fastlane

Fastlane — это активно развивающийся проект с открытым исходным кодом, и в нем регулярно публикуются новые выпуски. Когда доступна новая версия, система сообщает об этом при выполнении любой команды Fastlane:

[![](images/fastlane-image0.png "Запрос на обновление Fastlane")](images/fastlane-image0.png#lightbox)


Чтобы установить обновление для Fastlane, загрузите последнюю версию пакета [отсюда](https://download.fastlane.tools), а затем дважды щелкните пакет установки:

[![](images/fastlane-image0a.png "Запуск пакета установки")](images/fastlane-image0a.png#lightbox)


## <a name="contents"></a>Описание

Эта серия руководств рассказывает о некоторых инструментах, используемых в Fastlane для подписывания кода приложений iOS при подготовке к их разработке или распространению. На данный момент охвачены следующие средства:

- [cert](~/ios/deploy-test/provisioning/fastlane/cert.md)
- [sigh](~/ios/deploy-test/provisioning/fastlane/sigh.md)
- [match](~/ios/deploy-test/provisioning/fastlane/match.md)

Служебные программы cert и sigh предназначены для создания и обслуживания сертификатов для подписи и профилей подготовки на локальном компьютере. Служебная программа match предлагает дополнительные возможности. Она позволяет создавать сертификаты и профили подготовки, работать с ними и сохранять их в репозитории Git, обеспечивая доступ для всех членов команды разработчиков. Прочтите каждый раздел и узнайте, как работают и как использовать эти средства.

## <a name="using-fastlane-tools-with-xamarin"></a>Использование инструментов Fastlane с Xamarin

Создав удостоверение подписывания и профили подготовки с помощью Fastlane, вы сможете легко настроить параметры подписывания пакетов в Visual Studio для Mac при условии, что сертификаты и закрытые ключи хранятся в цепочке ключей macOS, а профили подготовки — в папке `~/Library/MobileDevice/Provisioning Profiles`.

Чтобы задать параметры подписывания кода для приложения Xamarin.iOS, щелкните правой кнопкой мыши имя проекта, выберите **"Параметры проекта" > "Сборка"> "Подписывание пакета iOS"** и задайте удостоверение подписывания и профиль подготовки явным образом, как показано ниже:

[![](images/fastlane-image11.png "Явная установка удостоверения подписывания и профиля подготовки")](images/fastlane-image11.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [Документация по fastlane](https://fastlane.tools/)
- [Документация по подписыванию кода с помощью Fastlane](https://docs.fastlane.tools/codesigning/getting-started/)
- [Руководство по подписыванию кода](https://codesigning.guide/)
