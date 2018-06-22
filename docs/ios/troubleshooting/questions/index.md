---
title: Вопросы и ответы
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: f8264c48b3b4679d45d5603b5637df0016cd3d17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780239"
---
# <a name="frequently-asked-questions"></a>Вопросы и ответы

## <a name="general-questions"></a>Общие вопросы

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[Можно ли использовать виртуальную машину Mac с Xamarin?](mac-vm.md)
Да, но только на оборудовании Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Как перейти на использование более ранней версии Xcode?](downgrade-xcode.md)
В этом руководстве приводятся ссылки на доступ к предыдущей версии Xcode, а также последнюю версию.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Где можно задать свои расположения пакета SDK для iOS?](ios-sdk.md)
Для большинства пользователей они устанавливаются в соответствующие расположения автоматически. В этом руководстве перечислены расположения пакета SDK по умолчанию и как изменить их при необходимости.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Как включить параметры разработчика после обновления iOS?](update-developer-options.md)
Ошибка в операций ввода-вывода может привести к параметров разработчика исчезает после обновления версии iOS, это было обнаружено при переключении на iOS 8.x. В этом руководстве описывается, как можно повторно включать параметры.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[Расположение пользователя не работает в iOS 8](ios8-user-location.md)
В этом руководстве рассказывается, как изменение info.plist, чтобы включить папку пользователя в iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Где найти dSYM-файл, чтобы выразить символами журналы с данными об аварийном завершении iOS?](symbolicate-ios-crash.md)
В этом руководстве описаны основные шаги для symbolicating журналы аварийного завершения операций ввода-вывода для диагностики сбоев. Также приводятся ссылки на дополнительные ресурсы для более сложных методов symbolication & сведения по анализу журналов аварийного завершения операций ввода-вывода.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Как задать переменные среды выполнения Mono для проектов iOS в Xamarin Studio?](xs-mono-runtime.md)
Если необходимо задать переменные среды любой среде выполнения для Mono, они могут задаваться в **параметры проекта > запустить > Общие** страницы.

## <a name="publishing-questions"></a>Вопросы публикации

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Ошибка при отправке в магазин приложений: «Недопустимый пакет - параметры, не может быть внедрен в bitcode обнаруживаются отправки»](invalid-bundle-bitcode.md)

Отправка приложения, _требуют_ bitcode, например приложений watchOS и tvOS должно производиться с Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[Можно изменить путь выходного файла IPA](ipa-output-path.md)
Начиная с 7 цикла Xamarin можно использовать настраиваемые целевые объекты MSBuild с этой целью.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Как скопировать выходные файлы IPA транзитный каталог TFS?](ipa-tfs.md)
Да, в этом руководстве описаны как.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[Можно добавить файлы или удалите файлы из файла IPA после его создания в Visual Studio?](modify-ipa.md)
Да, возможно, но обычно потребует повторной подписи `.app` пакета после внесения изменений. Обратите внимание, что изменение `.ipa` файл не используется при нормальном использовании. В этой статье предоставляется исключительно в информационных целях.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[Имеется возможность создания архива .xcarchive из Visual Studio?](create-xcarchive.md)
Начиная с Xamarin 4 стало возможным создание `.xcarchive` из Windows, задав `ArchiveOnBuild` свойства `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Почему отправка приложения завершается ошибкой "Обнаружены запрещенные пути ( "iTunesMetadata.plist" ) в..."?](itunesmetadata-disallowed-paths.md)
Эта ошибка является результатом изменений в процесс проверки Apple App Store. Это конкретная ошибка _не_ отношение к конкретной версии Xamarin установки, поэтому понижения будет _не_ справки. Это руководство по представлены ссылки на дополнительные сведения о том, как устранить ее.


## <a name="diagnosing-specific-error-messages"></a>Диагностика специальные сообщения об ошибках

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Ошибка конструктора iOS RegisterServicePort](error-registerserviceport.md)
Ошибки, связанные с `RegisterServicePort` и аналогичные сообщения об ошибках как выше обычно являются проблемы с шпионского ПО или вредоносных программ на компьютере. В этом руководстве рассматривается подтверждения диагностики и сведения об удалении программ-шпионов и вредоносных программ.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Почему сборка iOS завершается ошибкой "В цепочке ключей не удалось найти допустимые ключи подписывания кода iPhone"?](no-codesigning-keys.md)
Это сообщение об ошибке возникает, когда проект рассматриваемой Ищет допустимые учетные данные подписи кода, но не удается найти их. Подписывание кода является обязательным для тестирования и развертывания на физических устройствах iOS; а также Ad-hoc & приложения хранилища сборки.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Почему приложение iOS 9 завершается ошибкой "System.Exception: не удалось маршалировать объект Objective-C"?](exception-marshal-obj-c.md)
Изменения API в iOS 9 потребовать использования конструктора обратного вызова при вызов неуправляемого кода, в качестве базового интерфейса API теперь ожидает ее.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Ошибка выполнения: не удалось найти или загрузить сборку mscorlib.dll](error-mscorlib-not-found.md)
Эта проблема возникает при *скрытые* `.monotouch-32` и `.monotouch-64` папки отсутствуют `.xcarchive` для подписи или создание IPA, вызвавшая ошибку времени выполнения.

## <a name="deprecated"></a>Рекомендуется использовать

> [!IMPORTANT]
> В следующей статье применяются к проблемам, которые будут устранены в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, проверьте файл [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Размер IPA-файла составляет 0 байт](ipa-zero-bytes.md)
Произошли некоторые известные проблемы в предыдущих версиях Xamarin, после чего файл IPA в Windows как 0 байт.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Ошибка IBTool: не удалось завершить операцию.](error-ibtool.md)
Apple [фиксированной](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) это `ibtool` простой исправление является ошибка в Xcode 6.1.1, поэтому обновление для Xcode 6.1.1 или более поздней версии.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Ошибка MT1009: не удалось скопировать сборку](error-mt1009.md)
Это влияет на пользователей, работающих в Xamarin.iOS 7.2.6. Эта проблема вызвана разрешения файла требуется более высокий уровень разрешений при Xamarin.iOS устанавливается вместе с другой учетной записью выберите основной учетной записи разработчика.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe вернул...](exception-amddevicenotificationsubscribe.md)
Это сообщение может появиться в окно сообщения об ошибке при первом запуске Visual Studio для Mac или в `mtbserver.log` файла. Обратите внимание, что возникла проблема редко. Если Visual Studio удается подключиться к узлу построения Mac, есть другие ошибки, которые, скорее всего, появятся в `mtbserver.log` файла.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe не найден, rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Эта ошибка может возникать в `Mac Server Log` в Visual Studio.
