---
title: Вопросы и ответы
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d2d0af5a69920f392eba1cd655f1d8ab74971be6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107512"
---
# <a name="frequently-asked-questions"></a>Вопросы и ответы

## <a name="general-questions"></a>Общие вопросы

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[Можно ли использовать виртуальную машину Mac с Xamarin?](mac-vm.md)
Да, но только на оборудовании Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Как перейти на использование более ранней версии Xcode?](downgrade-xcode.md)
В этом руководстве приведены ссылки на доступ к предыдущей версии Xcode, а также последнюю версию.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Где можно задать свои расположения пакета SDK для iOS?](ios-sdk.md)
Для большинства пользователей они устанавливаются их в соответствующие расположения автоматически. В этом руководстве перечислены расположения SDK по умолчанию и как изменить их при необходимости.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Как включить параметры разработчика после обновления iOS?](update-developer-options.md)
Ошибка операций ввода-вывода может привести к параметры разработчика исчезает после обновления версии iOS, такая ситуация возникает при переключении на iOS 8.x. В этом руководстве описывается, как можно повторно включать параметры.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[Расположение пользователя не работает в iOS 8](ios8-user-location.md)
В этом руководстве рассказывается, как изменение info.plist, чтобы включить расположения пользователя в iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Где найти dSYM-файл, чтобы выразить символами журналы с данными об аварийном завершении iOS?](symbolicate-ios-crash.md)
В этом руководстве описываются основные шаги symbolicating журналы сбоев операций ввода-вывода для облегчения диагностики сбоев. Также содержит ссылки на дополнительные ресурсы для более сложных методов символизацией & сведения по анализу журналов о сбоях iOS.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Как задать переменные среды выполнения Mono для проектов iOS в Xamarin Studio?](xs-mono-runtime.md)
Если вам нужно задать переменные среды любой среде выполнения для Mono, их можно задать **параметры проекта > выполнить > Общие** страницы.

## <a name="publishing-questions"></a>Вопросы публикации

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Ошибка при отправке в App Store: «Недопустимый пакет — не допускается для внедрения в bitcode параметры обнаружения в отправки»](invalid-bundle-bitcode.md)

Отправка приложения, _требуют_ bitcode, таких как приложения для watchOS и tvOS, должны выполняться с помощью Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[Можно изменить путь к выходной IPA-файл?](ipa-output-path.md)
Начиная с 7 цикла Xamarin можно использовать настраиваемые целевые объекты MSBuild для достижения этой цели.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Копирование выходных IPA-файлов в папку сброса TFS?](ipa-tfs.md)
Да, в этом руководстве описывается как.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[Можно добавить файлы или удалить файлы из файла IPA-ФАЙЛ после его сборки в Visual Studio?](modify-ipa.md)
Да, возможно, но обычно потребует повторной подписи `.app` пакета после внесения изменений. Обратите внимание, что изменение `.ipa` файл не используется при нормальном использовании. В этой статье предоставляется исключительно в информационных целях.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[Имеется возможность создания xcarchive-архива из Visual Studio?](create-xcarchive.md)
По состоянию на Xamarin 4 стало возможным создание `.xcarchive` из Windows, задав `ArchiveOnBuild` свойства `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Почему отправка приложения завершается ошибкой "Обнаружены запрещенные пути ( "iTunesMetadata.plist" ) в..."?](itunesmetadata-disallowed-paths.md)
Эта ошибка является результатом изменения в процесс проверки Apple App Store. Этот возникла следующая ошибка _не_ относятся к конкретной версии Xamarin, установки, поэтому для снижения будет _не_ справки. Это руководство по представлены ссылки на дополнительные сведения о том, как устранить проблему.


## <a name="diagnosing-specific-error-messages"></a>Диагностика сообщения об ошибках

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Ошибка конструктора iOS RegisterServicePort](error-registerserviceport.md)
Ошибки, связанные с `RegisterServicePort` и аналогичные сообщения об ошибках как выше обычно представляют собой проблему шпионского ПО и вредоносных программ на компьютере. В этом руководстве объясняется, подтверждающее, диагностики и сведения об удалении программ-шпионов и вредоносных программ.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Почему сборка iOS завершается ошибкой "В цепочке ключей не удалось найти допустимые ключи подписывания кода iPhone"?](no-codesigning-keys.md)
Это сообщение об ошибке возникает, когда проект рассматриваемый ищет действительные учетные данные подписи кода, но не можете найти их. Подписывание кода является обязательным для тестирования и развертывания на физических устройствах iOS; а также приложения для Ad-hoc и сохранения сборок.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Почему приложение iOS 9 завершается ошибкой "System.Exception: не удалось маршалировать объект Objective-C"?](exception-marshal-obj-c.md)
Изменения API в iOS 9 требуют, что конструктор обратного вызова можно использовать при вызов неуправляемого кода, в качестве базового интерфейса API теперь ожидает ее.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Ошибка выполнения: не удалось найти или загрузить сборку mscorlib.dll](error-mscorlib-not-found.md)
Эта проблема возникает при *скрытые* `.monotouch-32` и `.monotouch-64` папки отсутствуют `.xcarchive` для подписи или создания IPA, вызвавшая ошибку среды выполнения.

## <a name="deprecated"></a>Устарело

> [!IMPORTANT]
> Перечисленные ниже статьи применяются к проблемам, которые были устранены в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Размер IPA-файла составляет 0 байт](ipa-zero-bytes.md)
Отсутствуют некоторые известные проблемы в предыдущих версиях Xamarin, которое может привести к IPA-файл на Windows 0 байт.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Ошибка IBTool: не удалось завершить операцию.](error-ibtool.md)
Apple [фиксированной](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) это `ibtool` об ошибке в Xcode 6.1.1, поэтому обновление до Xcode 6.1.1 или более поздней версии — самый простой исправления.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Ошибка MT1009: не удалось скопировать сборку](error-mt1009.md)
Это влияет на пользователей, выполняющих Xamarin.iOS 7.2.6. Проблема возникает из-за необходимости более высокие привилегии при установке Xamarin.iOS с помощью другой учетной записи разрешения для файлов затем основной учетной записи разработчика.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe вернул...](exception-amddevicenotificationsubscribe.md)
Это сообщение может появиться в окно сообщения об ошибке при первом запуске Visual Studio для Mac или в `mtbserver.log` файл. Обратите внимание, что это ошибка редко. Если Visual Studio не удается подключиться к узлу сборки Mac, существуют другие ошибки, которые чаще всего, появятся в `mtbserver.log` файл.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe не найден, rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Эта ошибка может возникать в `Mac Server Log` в Visual Studio.
