---
title: Ошибки Xamarin.iOS
description: В этом документе описываются различные ошибки, создаваемые mtouch, средство, используемое для объединения приложений Xamarin.iOS. Ошибки перечислены по коду и полное описание.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 01359d8b5fc402c83006c1c227375557fd2d7149
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122456"
---
# <a name="xamarinios-errors"></a>Ошибки Xamarin.iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mtouch, сообщения об ошибках

Например, параметры среды, отсутствуют средства.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpbugzillaxamarincom"></a>MT0000: Непредвиденная ошибка - заполните отчет об ошибках в http://bugzilla.xamarin.com

Произошла непредвиденная ошибка. Пожалуйста [отчет об ошибках](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с как можно больше информации, включая:

* Полная сборка журналы, с максимальным уровнем детализации (например `-v -v -v -v` в **дополнительные аргументы mtouch**);
* Минимальный тестового случая, воспроизвести ошибку. и
* Все версии сведения

Самый простой способ получить сведения о точной версии является использование **Visual Studio для Mac** меню **о Visual Studio для Mac** элемента, **Показать подробности** кнопки, копирование и вставка сведения для версии (можно использовать **сведения о копировании** кнопки).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: "-devname" был указан без вмешательства конкретного устройства

Это предупреждение создается, если - devname передается mtouch, если действие не зависящий от устройства (-logdev/installdev/killdev/launchdev /-listapps) был запрошен.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: Не удалось выполнить синтаксический анализ переменную среды *.

Эта ошибка происходит при попытке задать ключ указано недопустимое окружение = переменная пара значение. Имеет правильный формат: `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Имя приложения "* .exe" конфликтует с именем сборки (DLL) пакета SDK или продукта.

Имя исполняемого файла сборки и имя приложения не может совпадать с именем любой библиотеки dll в приложении. Измените это имя исполняемого файла.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: Новая логика обуславливающие требует SGen включить слишком.

При включении расширения обуславливающие необходимо также включить сборщик мусора SGen в проект iOS параметры сборки (вкладке "Дополнительно").

Начиная с версии Xamarin.iOS 7.2.1 это требование было снято, новая логика обуславливающие можно включить с помощью Boehm и сборщики мусора SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: Выходной каталог * не существует.

Создайте каталог.

Эта ошибка больше не создается, mtouch автоматически создаст каталог, если он не существует.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: Нет не платформа для разработки *, используйте--платформу = PLAT для указания пакета SDK.

Xamarin.iOS не удается найти папку пакета SDK в расположении, упомянутые в сообщении об ошибке. Убедитесь, что путь указан правильно.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: Корневая сборка * не существует.

Xamarin.iOS не удается найти сборку в расположении, упомянутые в сообщении об ошибке. Убедитесь, что путь указан правильно.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: Необходимо указать один корневой сборки для той # только, найдено: *.

Более одной корневой сборки был передан mtouch, хотя может существовать только один корневой сборки.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Ошибка при загрузке сборки: *.

Произошла ошибка при загрузке сборок, ссылки на сборки корневого. Дополнительные сведения могут предоставляться в выходные данные сборки.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: Не удалось выполнить синтаксический анализ аргументов командной строки: *.

Произошла ошибка при синтаксическом анализе аргументов командной строки. Убедитесь, что они правильные.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * ориентировано на более новой среды выполнения (*), чем поддерживает MonoTouch.

Это предупреждение обычно выводится проект содержит ссылку на библиотеку классов, которое не было создано с помощью Xamarin.iOS BCL.

По аналогии приложения с помощью пакета SDK для .NET 4.0 может не работать в системе, которые поддерживают только .NET 2.0, библиотеку, построенную с помощью .NET 4.0, могут работать на Xamarin.iOS, он может использовать API отсутствует на Xamarin.iOS.

Общее решение заключается в том, чтобы построить библиотеку как библиотеки классов Xamarin.iOS. Это можно сделать, создав новый проект библиотеки классов Xamarin.iOS и добавит все исходные файлы на него. Если у вас исходный код для библиотеки, следует обратиться к поставщику и запросить, что они предоставляют версию их библиотеки, совместимые с Xamarin.iOS.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Неполные данные передаются для выполнения *.

Эта ошибка больше не выводится в текущей версии Xamarin.iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: Поддержка профилирования требуется sgen включить слишком.

SGen (--sgen) необходимо включить профилирование (--профилирование) включен.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: IOS * пакет SDK не поддерживает создание приложений, предназначенных для *.

Это может произойти в следующих случаях:

*  ARMv6 включена и установлен Xcode версии 4.5 или более поздней версии.
*  ARMv7s включена и 4.4 или более ранней версии она установлена.

Убедитесь, что установленная версия Xcode поддерживает выбранных архитектур.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: Недопустимый ABI: *. Поддерживаемые ABI являются: i386, x86_64, armv7, armv7 + llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 и arm64 + llvm.

Передан недопустимый ABI mtouch. Укажите допустимый ABI.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: Параметр * является устаревшим.

Параметр упомянутых mtouch является устаревшим и будет игнорироваться.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: Необходимо указать корневой сборки.

Это необходимо, чтобы указать корневой сборки (обычно основного исполняемого файла) при построении приложения.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Неизвестный аргумент командной строки: *.

Mtouch не распознает аргумент командной строки, упомянутые в сообщении об ошибке.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Только один--[журнала | Установка | kill | запуска] разработки или--[запуска | отладка] sim параметр может использоваться.

Для mtouch, не могут использоваться одновременно несколькими способами:

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Допустимые значения для "\*«,»\*".

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: Не удается скомпилировать с помощью gcc / g ++ (--используйте gcc) при использовании статических регистратор (это значение по умолчанию при компиляции для устройства). Либо удалите используйте gcc флаг или использовать динамические регистратор (--регистратора: динамические).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: Параметры «— неподдерживаемый--enable-универсальные шаблоны в регистратор» и "--регистратора" несовместимы.

Удалить оба варианта `--unsupported--enable-generics-in-registrar` и `--registrar`. Начиная с Xamarin.iOS 7.2.1 регистратора по умолчанию поддерживает универсальные шаблоны.

Эта ошибка больше не отображается (аргумент командной строки `--unsupported--enable-generics-in-registrar` был удален из mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Имя приложения "* .exe" конфликтует с другой пользователь сборки.

Имя исполняемого файла сборки и имя приложения не может совпадать с именем любой библиотеки dll в приложении. Измените это имя исполняемого файла.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: Не удалось найти требуемый файл "*".

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: Версия SDK не был предоставлен. Добавьте `--sdk=X.Y` для указания какой iOS SDK следует использовать для построения приложения.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: Не удалось выполнить синтаксический анализ аргументов командной строки "*": *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Параметры\*«и»\*"несовместимы.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: Не удается включить КРУГОВОЙ (-круговой) при разработке для iOS версии 4.1 или более ранней версии. Отключите КРУГОВОЙ (-круговой: false) или по крайней мере задать цели развертывания iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--enable-repl) поддерживается только в симуляторе (--sim).

REPL: поддерживается только в том случае, если вы создаете для симулятора. Это означает, что при передаче `--enable-repl` для mtouch, необходимо также передать `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: Имя исполняемого файла (\*) и имя приложения (\*) являются различными, это может лишить журналы сбоев начало создаваться должным образом.

Когда symbolicates Xcode (преобразовывает адреса памяти для имен функций и файл/строка чисел) процесс может завершиться ошибкой, если исполняемый файл и приложения имеют разные имена (без расширения).

Чтобы устранить это либо измените «Имя приложения» в параметры приложения сборки/iOS проекта или измените «Имя сборки» в параметрах выходных данных сборки проекта.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Аргументы командной строки "--enable выборку в фоновом режиме" и "--запуска для фоновое получение" требуется "--launchsim" слишком.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: Параметр "--debugtrack" игнорируется, если "--debug" также указан.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Xamarin.iOS проект должен ссылаться на monotouch.dll или Xamarin.iOS.dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: Не может содержать «monotouch.dll» и «Xamarin.iOS.dll», в том же проекте Xamarin.iOS - "\*" является явной ссылки, тогда как "\*" ссылается "*".

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: Не удается запустить * симулятор для * приложения. Включите правильные architecture(s) в проекте iOS параметры сборки (страница "Дополнительно").

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: monotouch.dll — 64-разрядными платформами. Ссылаться на Xamarin.iOS.dll либо не проходить сборку для 64-разрядной архитектуры (ARM64 и/или x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Регистраторов старые (--регистратора: oldstatic | olddynamic) не поддерживаются при указании ссылки на Xamarin.iOS.dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Приложения, предназначенные для ARMv6 не может ссылаться на Xamarin.iOS.dll.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: Не удалось найти сборку "\*«, ссылается»\*".

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: Не может ссылаться на «monotouch.dll» и «Xamarin.iOS.dll».

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: Не используйте ссылку на monotouch.dll или Xamarin.iOS.dll найден. Ссылку на monotouch.dll будут добавляться.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: Сборщик мусора Boehm в настоящее время не поддерживается при ссылке на «Xamarin.iOS.dll». Вместо этого был выбран сборщика мусора SGen.

Сборщик мусора SGen поддерживается с помощью единой системе проектов. Убедитесь, не mtouch дополнительные флаги, указывающие Boehm как сборщик мусора.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim поддерживается только с помощью Xcode 6.0 или более поздней версии.

Установите более новую версию Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--расширение поддерживается только в том случае, если с помощью iOS SDK 8.0 (или более поздней версии).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: Минимальной цели развертывания для приложений единой 5.1.1, текущая цель развертывания: "*". Выберите целевой объект более новые развертывания в проекте iOS параметры приложения.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *.framework поддерживается только в том случае, если целевой объект развертывания 8.0 или более поздней версии. * функции могут работать неправильно.

Указанной платформы не поддерживается в целевой объект развертывания относится к версии iOS. Обновить целевой объект развертывания до более новой версии iOS либо удалить использования указанной платформы из приложения.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin.iOS * требуется Xcode 5.0 или более поздней версии. Текущая версия Xcode (в *) — *.

Установка новой Xcode.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: Команда не указан.

Не указано действие для mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: Не удалось канонизировать пути "*": *

Это внутренняя ошибка. Если вы видите эту ошибку, отправьте ошибки [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: Путь Xcode "*" не существует.

Путь Xcode, передаваемый с помощью `--sdkroot` не существует. Укажите допустимый путь.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: Не удается найти Xcode в расположении по умолчанию (/ Applications/Xcode.app). Установите Xcode или передать пользовательский путь, с помощью--sdkroot <path>.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: Не удается определить путь к Xcode.app из корневой папки sdk "*". Укажите полный путь к пакету Xcode.app.

Путь, передаваемый с помощью `--sdkroot` не содержит допустимое приложение Xcode. Укажите путь к приложению Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Xcode.app "\*" является недопустимым (файл "\*" не существует).

Путь, передаваемый с помощью `--sdkroot` не содержит допустимое приложение Xcode. Укажите путь к приложению Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: Не удалось найти текущий выбранный Xcode в системе: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: Не удалось найти текущий выбранный Xcode в системе. 'xcode-select--print-path» возвращается "*", но этот каталог не существует.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Не Xcode.app (с помощью--sdkroot), в системе Xcode, предоставленное 'xcode-select--print-path': *

Это информационное предупреждение, объясняя Xcode будет использоваться, так как не указаны.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Нет Xcode.app аргументом (--sdkroot или 'xcode-select--print-path»), вместо этого использовать Xcode по умолчанию: *

Это информационное предупреждение, объясняя Xcode будет использоваться, так как не указаны.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: Не удалось найти исполняемый файл в расширении * (CFBundleExecutable запись в своем файле Info.plist.)

Каждый файл Info.plist должен иметь исполняемый файл (с использованием записи CFBundleExecutable), однако нужно автоматически создавать запись во время сборки.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin.iOS поддерживает только внедренные платформы с помощью единой системе проектов.

Xamarin.iOS поддерживает только внедренные платформы при использовании на единый API; Обновите проект для использования на единый API.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin.iOS поддерживает только внедренные платформы при цель развертывания: по крайней мере 8.0 (текущая цель развертывания: * внедренные платформы: *)

Xamarin.iOS поддерживает внедренные платформы только в том случае, когда целевой объект развертывания — по крайней мере 8.0 (поскольку в более ранних версиях iOS не поддерживает внедренные платформы).

Обновите целевой объект развертывания в файле Info.plist проекта до 8.0 или более поздней версии.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Недопустимый сборки регистратора сборки: *

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Недопустимый регистратора: *

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Недопустимое значение для целевой платформы: *.

Недопустимый целевой платформы был передан с помощью аргумента--целевой платформы. Укажите допустимый целевой платформы.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Недопустимая целевая платформа: *. Допустимые целевые платформы являются: *.

Недопустимый целевой платформы был передан с помощью аргумента--целевой платформы. Укажите допустимый целевой платформы.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Неизвестная платформа: *. Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в http://bugzilla.xamarin.com с тестовым случаем.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: Расширения не поддерживаются для платформы "*".

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin.iOS * не поддерживает цель развертывания * (минимальное значение — *). Выберите целевой объект более новые развертывания в файле Info.plist вашего проекта.

Минимальной цели развертывания задается в сообщении об ошибке; Выберите целевой объект более новые развертывания в файле Info.plist проекта.

Если обновление цели развертывания невозможно, используйте более старой версии Xamarin.iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin.iOS * не поддерживает минимальной цели развертывания из * (максимальное значение — *). Выберите целевой объект более старых развертывания в файле Info.plist проекта или обновление до более новой версии Xamarin.iOS.

Xamarin.iOS не поддерживает задание минимальной цели развертывания до более поздней версии, чем версия, созданного для этой конкретной версии Xamarin.iOS.

Выберите целевой объект более старых минимальное развертывания в файле Info.plist проекта или обновление до более новой версии Xamarin.iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Недопустимая архитектура "*" для * проектов. Допустимые архитектур: *

Указан недопустимый архитектуры. Убедитесь, что архитектура является допустимым.

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075: Архитектура не указан (с помощью аргумента--abi). Архитектура является обязательным для * проектов.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076: Проекты WatchOS должно быть расширения.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077: Инкрементные сборки включены с помощью развертывания целевой < 8.0 (в настоящее время *). Эта возможность не поддерживается (полученное приложение не запускается на iOS 9), поэтому цель развертывания устанавливается в 8.0.

Это предупреждение о том, что целевой объект развертывания было присвоено 8.0 для этой сборки, чтобы работы добавочных сборок должным образом.

Инкрементные сборки поддерживаются только в том случае, когда целевой объект развертывания — по крайней мере 8.0 (так как в противном случае полученное приложение не запускается на iOS 9).

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078: Рекомендуемые Xcode версии для Xamarin.iOS * является Xcode * или более поздней версии. Текущая версия Xcode (в *) — *.

Это предупреждение о том, что текущая версия Xcode не рекомендуется версию Xcode для этой версии Xamarin.iOS.

Выполните обновление Xcode для обеспечения оптимальной работы.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: Отключение NewRefCount,--new-refcount:false, является устаревшим.

Это предупреждение о том, что запрос на отключение новый refcount (--новый - refcount:false) было пропущено.

Новая функция refcount теперь обязательна для всех проектов, и это не таким образом можно отключить больше.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: Аргумент командной строки--отчет о сбоях загрузки также требует--загрузки аварийных отчет в.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--enable-repl) поддерживается только в том случае, если компоновка не применяется (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Только для Asm bitcode не поддерживается в watchOS. Используйте либо bitcode--: маркер или--bitcode: полный.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode не поддерживается в симуляторе. Не передавайте--bitcode при сборке для симулятора.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Не используйте ссылку на "*" был найден. Он будет добавлен автоматически.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Целевой платформы (--целевой платформы) должен быть указан при создании для TVOS или WatchOS.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Инкрементные сборки (--fastdev) не поддерживается с Boehm сборщик Мусора. Инкрементные сборки будут отключены.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: Сборщик Мусора должен быть в режиме совместной работой для приложения для watchOS. Удалите аргумент--coop: false для mtouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: Параметр "\*«не может принимать значение»\*" при включении режима совместной работой для сборщика Мусора.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Этой версии Xamarin.iOS требуется * SDK (поставляется с Xcode *). Либо обновление Xcode, чтобы получить обязательные файлы заголовков или задать поведение управляемых компоновщика для связи только пакеты SDK (Чтобы попробовать избежать использования нового API).

Для Xamarin.iOS требуется файлы заголовков, из пакета SDK версии, указанной в сообщении об ошибке для построения приложения. Для устранения этой ошибки рекомендуется обновить Xcode, чтобы получить пакет SDK для, это будет включать все обязательные файлы заголовков. Если у вас несколько версий Xcode установлен, или нужно использовать Xcode в расположении по умолчанию, не забудьте задать правильное расположение Xcode в настройках вашей интегрированной среды разработки.

Вероятность, альтернативным решением является включение управляемых компоновщика. Это приведет к удалению неиспользуемые API включая, в большинстве случаев новый интерфейс API, где находятся файлы заголовков, отсутствует (или является неполным). Тем не менее это не будет работать, если в проекте используется API, которая была введена в новой SDK, отличную от вашей Xcode предоставляет.

Последнего straw решением было бы использовать более старую версию Xamarin.iOS, которая поддерживает пакет SDK проекта требует.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: Не удалось найти «mlaunch».

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: Файлы Aot не может быть скопирован в каталог назначения {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: Не используйте ссылку на Xamarin.iOS.dll найден.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Внутренняя ошибка *. Отправьте отчет об ошибках с тестовым случаем (http://bugzilla.xamarin.com).

Это сообщение об ошибке выводится при сбое Проверка внутренней согласованности в Xamarin.iOS.

Это указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Недопустимая сборке разрабатывать: "*". Отправьте отчет об ошибках с тестовым случаем (http://bugzilla.xamarin.com).

Это сообщение об ошибке выводится при сбое Проверка внутренней согласованности в Xamarin.iOS.

Это всегда ошибки в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: Сборка "*" был указан несколько раз в--аргументы-сборки — на языке ассемблера.

В сообщении об ошибке сборки был указан несколько раз в--аргументы-сборки — на языке ассемблера. Убедитесь, что каждая сборка указывается только один раз.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Сборок\*«и»\*"иметь то же имя целевой ("\*"), но разные целевые объекты ("\*"и" * ").

Упомянутые в сообщении об ошибке сборки имеют конфликтующие целевые объекты сборки.

Пример:

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

В этом примере пытается создать динамической библиотеки и платформу с одной и той же марки (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: Статический объект "\*" содержит более одной сборки ("\*"), но каждый статический объект должен соответствовать только одна сборка.

Упомянутые в сообщении об ошибке сборки компилируются в единый статический объект. Это не допускается, каждая сборка должна быть скомпилирована в другой статический объект.

Пример:

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

В этом примере пытается создать статический объект (`MyBinary`) состоит из двух сборок (`Assembly1.dll` и `Assembly2.dll`), что недопустимо.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: Был указан без построения на языке ассемблера для "*".

При указании сборки построения целевого с помощью `--assembly-build-target`, каждая сборка, в приложении необходимо назначить целевым объектом сборки.

Эта ошибка выдается в том случае, когда сборки в сообщении об ошибке не имеет назначенный целевой построения сборки.

См. в документации `--assembly-build-target` для получения дополнительных сведений.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Имя целевой сборки в сборку "\*" является недопустимым: символ "\*" не допускается.

Имя целевой сборки сборки должен быть допустимым именем файла.

Например этих значений приведет к возникновению этой ошибки:

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

так как `my/path.o` не является допустимым именем файла, из-за символом разделителя каталогов.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Сборок\*"имеют разные пользовательские оптимизации LLVM (\*), что недопустимо, если все они компилируются в единый двоичный файл.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: Сборка разрабатывать "*" не соответствует любой сборки.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: Сборка "{0}" была загружена из путь отличается от указанного пути (указан путь к: {1}, фактический путь: {2}).

Это предупреждение, указывающее, что сборки ссылается приложение была загружена из другого места, чем запрошено.

Это может означать, что приложение ссылается на несколько сборок с тем же именем, но из разных мест, которые могут привести к непредвиденным результатам, (только первая сборка будет использоваться).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Инкрементные сборки были отключены, поскольку эта версия Xamarin.iOS не поддерживает инкрементных сборок в проектах, которые включают привязку независимых библиотек и который компилируется в bitcode.

Инкрементные сборки были отключены, поскольку эта версия Xamarin.iOS не поддерживает инкрементных сборок в проектах, которые включают привязку независимых библиотек и который компилируется в bitcode (проектов tvOS и watchOS).

Не требуется никаких действий со стороны пользователя, это сообщение является исключительно информационным.

Дополнительные сведения см. в разделе номер ошибки[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Это предупреждение больше не отображается.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode работает так, как эта версия Xamarin.iOS не поддерживает watchOS стандартных проектах, использующих LLVM без Включение bitcode.

Bitcode работает автоматически, так как эта версия Xamarin.iOS не поддерживает watchOS стандартных проектах, использующих LLVM без Включение bitcode.

Не требуется никаких действий со стороны пользователя, это сообщение является исключительно информационным.

Дополнительные сведения см. в разделе номер ошибки[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Совместного использования собственного кода был отключен из-за *

Существует несколько причин, по которым совместного использования кода можно отключить.

* так как целевой объект развертывания приложения-контейнера более ранняя, чем iOS 8.0 (он имеет *)).

Машинный код общего доступа требуется iOS 8.0 так, как совместное использование собственного кода реализуется с помощью платформы пользователя, который входит в состав iOS 8.0.

* так как приложения-контейнера включены сборки I18N (*).

Совместное использование машинного кода в настоящее время не поддерживается, если приложения контейнера включены сборки I18N.

* так как приложение контейнера пользовательский XML-файл определения для управляемых компоновщик (*).

Машинный код общего доступа требуется, не поддерживается для проектов, использующих пользовательские XML-файлы определений для управляемых компоновщика.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Совместное использование собственного кода была отключена для расширения "*" так как *.

* так как bitcode параметры отличаются от приложения-контейнера (\*) и расширение (\*).

  Совместное использование собственного кода требует, что bitcode параметры соответствуют между проектами, которые совместно используют код.

* Поскольку-сборки — на языке ассемблера параметры отличаются от приложения-контейнера (\*) и расширение (\*).

  Совместное использование собственного кода требует,-сборки — на языке ассемблера параметры являются идентичными между проектами, которые совместно используют код.

  Это состояние может возникнуть, если инкрементные сборки не либо включены или отключены во всех проектах.

* так как сборки I18N различаются между приложения-контейнера (\*) и расширение (\*).

  Совместное использование машинного кода в настоящее время не поддерживается для расширений, которые включают I18N сборки.

* так как аргументы, компилятор AOT различаются между приложения-контейнера (\*) и расширение (\*).

  Совместного использования собственного кода требует, что аргументы, компилятор AOT не различаются между проектами, которые совместно используют код.

* так как другие аргументы AOT-компилятор различаются между приложения-контейнера (\*) и расширение (\*).

  Совместного использования собственного кода требует, что аргументы, компилятор AOT не различаются между проектами, которые совместно используют код.

  Данное состояние возникает, если «Выполнять все операции 32-разрядное число с плавающей запятой как 64-разрядное число с плавающей запятой» отличается между проектами.

* так как не включено или отключено в приложении контейнера LLVM (\*) и расширение (\*).

  Совместного использования собственного кода требует, что LLVM либо включены или отключены для всех проектов, которые совместно используют код.

* так как параметры компоновщика управляемых различаются между приложения-контейнера (\*) и расширение (\*).

  Совместного использования собственного кода требует, чтобы параметры компоновщика управляемых были идентичны для всех проектов, которые совместно используют код.

* так как пропущенные сборок для управляемых компоновщик различаются между приложения-контейнера (\*) и расширение (\*).

  Совместного использования собственного кода требует, чтобы параметры компоновщика управляемых были идентичны для всех проектов, которые совместно используют код.

* так как у расширения есть пользовательский XML-файл определения для управляемых компоновщик (*).

  Машинный код общего доступа требуется, не поддерживается для проектов, использующих пользовательские XML-файлы определений для управляемых компоновщика.

* так как приложения-контейнера не проходить сборку для ABI * (пока модуль за горами намеченный этот интерфейс ABI).

  Совместного использования собственного кода требует, что для всех архитектур, любое приложение расширение построения для построения приложением контейнера.

  Например: Данное состояние возникает, когда расширение построения для ARM64 + ARMv7, но приложения-контейнера выполняется сборка только для ARM64.

* так как создание приложения-контейнера для ABI \*, который несовместим с ABI расширения (\*).

  Совместное использование собственного кода требуется, что все проекты сборки для тот же интерфейс API.

  Например: Данное состояние возникает, когда расширение построения для ARMv7 + llvm + thumb2, но приложения-контейнера выполняется сборка только для ARMv7 и llvm.

* так как приложения-контейнера ссылается на сборку "\*«from»\*", тогда как расширение ссылается на другой версии из "*".

  Совместное использование собственного кода требует, чтобы все проекты, которые совместно используют код использовали одинаковые версии для всех сборок.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: Рекомендуется ссылаться на динамический символов, с помощью кода (--динамической символ mode = код) Если включен bitcode.

Проекты Xamarin.iOS будет часто ссылаются на собственных символов динамически, это означает, что собственного Компоновщик может удалять таких собственных символов в процессе собственного связывания, поскольку собственного компоновщик не видит, что эти символы используются.

Обычно запрашивает компоновщику хранить такие символы, Xamarin.iOS (с помощью `-u symbol` флаг компоновщика), но при компиляции для bitcode собственного компоновщик не принимает `-u` флаг.

Xamarin.iOS реализовала альтернативное решение: мы создаем дополнительный машинный код, который ссылается на эти символы, и таким образом собственного компоновщик будут видеть, что эти символы используются. Это делается автоматически при компиляции в bitcode.

Если `--dynamic-symbol-mode=linker` передается mtouch, этом альтернативное решение будет отключено, а Xamarin.iOS будет пытаться передать `-u` собственного компоновщика. Это скорее всего приведет к ошибкам собственного компоновщика.

Решение должно быть удаление `--dynamic-symbol-mode=linker` аргумент из дополнительным аргументам mtouch в параметрах сборки проекта.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Недопустимая архитектура: {arch}. 32-разрядных архитектур не поддерживаются, когда цель развертывания: 11 или более поздней версии. Убедитесь, что проект не проходить сборку для 32-разрядной архитектуры.

iOS 11 не содержит поддержки для 32-разрядных приложений, поэтому он не поддерживается для построения для 32-разрядное приложение, если цель развертывания — iOS 11 или более поздней версии.

Изменить целевую архитектуру в параметрах сборки проекта iOS для arm64 либо изменить целевой объект развертывания в файле Info.plist проекта на более раннюю версию iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: Не удается запустить 32-разрядное приложение в симуляторе, который поддерживает только 64-разрядной.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Aot файлов не найден в предполагаемом каталоге «{msymdir}».

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: Исполняемая сборка * не ссылается на *.

Ссылка на не удалось найти сборку платформы (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) в исполняемая сборка.

Обычно это происходит, когда отсутствует код в исполняемый файл проекта, использующего ничего в сборке платформы; для экземпляра пустой метод Main (и никакой другой код) будет отображать эту ошибку:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

С помощью API из сборки платформы устранит ошибку:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: Не удалось задать текущего языка «{язык}» (в соответствии с LANG = {LANG}): {exception}

Это предупреждение, указывающее, что текущий язык не может быть установлена на язык в сообщении об ошибке.

Текущий язык по умолчанию системный язык.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125:-сборки — на языке ассемблера аргумент командной строки учитывается в симуляторе.

Никаких действий не требуется, это сообщение является исключительно информационным.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Инкрементные сборки были отключены, поскольку инкрементные сборки не поддерживаются в симуляторе.

Никаких действий не требуется, это сообщение является исключительно информационным.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Инкрементные сборки были отключены, поскольку эта версия Xamarin.iOS не поддерживает инкрементных сборок в проектах, которые включают в себя более чем одним из поставщиков привязки библиотеки.

Инкрементные сборки отключены автоматически, так как эта версия Xamarin.iOS не всегда сборку проектов с несколькими библиотеками сторонних привязки правильно.

Никаких действий не требуется, это сообщение является исключительно информационным.

Дополнительные сведения см. в разделе номер ошибки[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: Не удалось touch файл "*": *

Сбой при касании в файл (который предназначено для проверки того, что частичного построения выполняются правильно).

Это предупреждение можно игнорировать скорее; случае возникновения проблем зарегистрировать ошибку (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) и будут исследованы.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Проект связанные сообщения об ошибках

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Установщик / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: Не удалось найти приложение в указанный каталог

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: Не удалось создать символических ссылок, были скопированы файлы

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: Не удалось удалить приложение "*". Может потребоваться вручную завершить приложение.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: Не удалось получить список установленных приложений.

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: Не удалось удалить приложение "\*«на устройстве»\*": *-может потребоваться вручную завершить приложение.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: Не удалось установить приложение "\*«на устройстве»\*": *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: Не удалось запустить приложение "\*«на устройстве»\*": *. Приложение по-прежнему можно запустить вручную, щелкнув его.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: Не удалось запустить симулятор

Данная ошибка возвращается, если не удалось запустить симулятор mtouch.   Это может произойти иногда устарел или исчезновения симулятор процессом, выполняемым уже существует.

Можно использовать следующие команды в командной строке Unix для завершения процессов заблокированной симулятор:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: Не удалось скопировать сборку "\*«to»\*": *

Это известная проблема в некоторых версиях Xamarin.iOS.

В этом случае вам попробуйте следующий обходной путь:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Тем не менее, так как эта проблема устранена в последнюю версию Xamarin.iOS, отправьте новую ошибку в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) полная информация о версии и выходные данные журнала сборки.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: Не удалось загрузить сборку "*": *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: Не удалось добавить отсутствующий файл ресурсов: "*"

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: Не удалось выполнить, чтобы получить список приложений на устройстве "*": *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Отслеживание ошибок зависимостей: нет файлов для сравнения. Отправьте отчет об ошибках в http://bugzilla.xamarin.com с тестовым случаем.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с caes теста.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: Не удалось повторно использовать кэшированную версию "*": *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Не удалось создать исполняемый файл "*": *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Не удалось создать исполняемый файл "*": *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: Не удалось создать файл Обратите внимание, ЧТО, поскольку каталог уже существует с тем же именем.

Удалите каталог `NOTICE` из проекта.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: Не удалось создать файл Обратите внимание, ЧТО: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: Приложения не удалось выполнить проверку подписи кода и не могут быть установлены на устройстве "*". Проверьте сертификаты, профили, подготовки и объединить их идентификаторы. Возможно, устройство не является частью выбранного профиля подготовки (ошибка: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: Приложение имеет права, не поддерживает текущий профиль подготовки и не могут быть установлены на устройстве "*". Проверьте журнал устройств iOS, более подробные сведения (ошибка: 0xe8008016).

Это может произойти, если:

* Ваше приложение имеет права, не поддерживает текущий профиль подготовки.
  Возможные решения:
  - Укажите другой профиль подготовки, поддерживающий назначений потребностей приложения.
  - Удаление прав, не поддерживается в текущем профиле подготовки.
* Устройство, которое вы пытаетесь развернуть не включен в профиль подготовки, которую вы используете.
  Возможные решения:
  - Создание нового приложения на основе шаблона в Xcode, выберите того же профиля подготовки и развертывание на одном устройстве. Иногда Xcode может автоматически обновлять профили подготовки с помощью новых устройств (в других случаях Xcode запросит порядок действий).
  -Посетить центр разработчиков iOS и обновить профиль подготовки с новым устройством, а затем Загрузите обновленный профиль подготовки на компьютер.

В большинстве случаев Дополнительные сведения об ошибке будет выводиться в журнал устройств iOS, которые могут помочь диагностировать проблему.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: Не удалось запустить приложение "\*«на устройстве»\*": *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: Не удалось скопировать файл "\*«to»\*": {2}

Не удалось скопировать файл. Сообщение об ошибке из операции копирования имеет дополнительные сведения об ошибке.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: Не удалось скопировать каталог "\*«to»\*": {2}

Не удалось скопировать каталог. Сообщение об ошибке из операции копирования имеет дополнительные сведения об ошибке.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: Не удалось связаться с устройством, чтобы найти приложение "*": *

Произошла ошибка при попытке найти приложение на устройстве.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: Приложение не удалось проверить подпись на устройстве "*". Убедитесь в том, что установлен и не истек срок действия профиля подготовки (ошибка: 0xe8008017).

Устройство отклонил установку приложения, так как не удалось проверить подпись.

Убедитесь, что установлен и не истек срок действия профиля подготовки.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: Не удалось перечислить отчеты о сбоях на устройстве *.

Произошла ошибка при попытке отобразить список отчетов о сбоях на устройстве.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: Не удалось скачать отчет об аварийном завершении * с устройства *.

Произошла ошибка при попытке загрузить отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: Нельзя использовать Xcode 7 + для запуска приложений на устройствах с iOS * (Xcode 7 поддерживает только iOS 6 +).

Не поддерживается использование Xcode 7 + для запуска приложений на устройствах с iOS версии ниже 6.0.

Используйте более старую версию Xcode или коснуться его вручную, чтобы запустить его.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: Спецификация недопустимое устройство: "*". Ожидаемый «ios», «watchos» или «all».

Спецификации устройства передается с помощью параметра--устройства является недопустимым. Допустимые значения: «ios», «watchos» или «all».

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: Не удалось найти приложение в указанном каталоге: *

Путь к приложению, передаваемый--launchdev не существует. Укажите допустимое приложение пакета.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: Запуск приложений на устройстве с помощью идентификатора пакета является устаревшим. Передайте полный путь к пакету для запуска.

Рекомендуется передать путь к приложению, чтобы запустить на устройстве, а не только идентификатор пакета.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: Не удалось запустить приложение "\*«на устройстве»\*" из-за блокировки устройства. Разблокируйте устройство и повторите попытку.

Разблокируйте устройство и повторите попытку.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Этот исполняемый файл приложения может быть слишком большим (* МБ) для выполнения на устройстве. Если был включен bitcode может потребоваться отключить его для разработки приложений, она необходима только для отправки приложений в Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: Не удалось удалить приложение "\*«с устройства»\*": *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: Не может содержать разные версии платформы «{name}»

Это не невозможно связать с разными версиями та же платформа.

Обычно это происходит, когда расширение ссылается на другую версию платформы, чем приложения-контейнера (возможно, с помощью сторонних привязки сборок).

После этого ошибка будет существовать несколько [MT1036](#MT1036) список путей для каждой платформы, различных ошибок.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Состав с Framework «{name}»: {path} (связано с предыдущей ошибкой)

Эта ошибка выдается только совместно с параметром [MT1036](#MT1036). См. в разделе [MT1036](#MT1036) Дополнительные сведения.

### <a name="mt11xx-debug-service"></a>MT11xx: Отладка службы

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: Не удалось запустить приложение

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: Не удается присоединиться к приложению (остановите его): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: Не удалось отсоединить

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: Не удалось отправить пакет: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Тип непредвиденный ответ

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: Не удалось получить список приложений на устройстве: Истекло время ожидания запроса.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Не удалось запустить приложение: *

Проверьте, если ваше устройство заблокировано.

Если вы развертываете в корпоративное приложение или с помощью бесплатной подготовки профиля, возможно, разработчике (Это объясняется <a href="http://stackoverflow.com/a/30726375/183422">здесь</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: Не удалось найти средства разработчика для этого устройства XX (гг).

Требуется несколько операций из mtouch <tt>DeveloperDiskImage.dmg</tt> файла должны присутствовать.   Этот файл является частью Xcode и обычно располагается относительно пакета SDK, который вы используете для сборки, в <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Эта ошибка может произойти либо потому, что у вас нет DeveloperDiskImage.dmg, которая соответствует устройству, вы подключились.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Не удается запустить приложение из-за блокировки устройства. Разблокируйте устройство и повторите попытку.

Проверьте, если ваше устройство заблокировано.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Не удается запустить приложение из-за ограничений безопасности iOS. Убедитесь, что разработчик является доверенным.

Если вы развертываете в корпоративное приложение или с помощью бесплатной подготовки профиля, возможно, разработчике (Это объясняется <a href="http://stackoverflow.com/a/30726375/183422">здесь</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: Приложение запущено успешно, но невозможно ожидания для приложения завершить работу по запросу, так как невозможно обнаружить завершение приложения при запуске, использовании программы gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: симулятора

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: Не удалось загрузить симулятора: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Недопустимый симулятор конфигурации: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Спецификация недопустимый симулятора: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Спецификация недопустимый симулятор "*": среда выполнения не указан.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Спецификация недопустимый симулятор "*": не указан тип устройства.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: Не удалось найти среда выполнения симулятор "*".

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: Не удалось найти тип устройства симулятор "*".

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: Не удалось найти среда выполнения симулятор "*".

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: Не удалось найти тип устройства симулятор "*".

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Спецификация недопустимый симулятора: \*, Неизвестный ключ "\*"

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: Simulator, версия "\*«не поддерживает тип симулятор»\*"

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: Не удалось создать simulator, версия которой введите = * и среды выполнения = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Спецификация недопустимый симулятор для Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Спецификация недопустимый симулятора Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: Указан недопустимый пакет SDK: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: Не удалось найти симулятор UDID "*".

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: Не удалось загрузить набор приложений на "*".

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Идентификатор пакета не найдено в приложении на "*".

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: Не удалось найти симуляторе для "*".

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: Не удалось найти последнюю версию среды выполнения симулятор для устройства "*".

Это обычно указывает на проблему с Xcode.

Возможные решения для устранения этой проблемы:

* Используйте симулятор, один раз в Xcode.
* Передать явное версия пакета SDK, с помощью пакета sdk — <version>.
* Повторно установите Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: Не удалось найти симулятор парном iPhone для симулятора WatchOS "*".

При запуске приложения в симуляторе WatchOS с WatchOS, должно быть парных симулятор iOS также.

Посмотрите симуляторов может быть связан с iOS симуляторов, с помощью пользовательского интерфейса Xcode устройств (устройства -> меню окна).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: Собственная библиотека `*` (\*) было пропущено, так как он не соответствует текущей сборки architecture(s) (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: Не удалось извлечь собственной библиотеки "*" из «+». Убедитесь, что собственная библиотека была правильно встроена в управляемую сборку (если сборка была построена с использованием привязки проекта, собственной библиотеки должны быть включены в проект и его действие при сборке должен быть «ObjcBindingNativeLibrary»).

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: Не удалось распаковать собственная платформа "\*«from»\*". Просмотрите журнал построения для получения дополнительных сведений о команде собственного «zip».

Не удалось распаковать указанной собственного платформы из библиотеки привязки.

Просмотрите журнал, bulid Дополнительные сведения об этом сбое команды собственного «zip».

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: Внедренные платформы "*" в * является недопустимым: он не содержит Info.plist.

Указанной платформы embedded не содержит Info.plist и поэтому не допустимая платформа.

Убедитесь, что платформа является допустимым.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: Библиотека привязки "\*" содержит framework пользователя (\*), но пользователя встроенной платформы требуют iOS 8.0 (текущая цель развертывания: *). Необходимо укажите цель развертывания в файле Info.plist, чтобы по крайней мере 8.0.

Указанная привязка библиотеки входит embedded инфраструктура, но Xamarin.iOS поддерживает только внедренные платформы IOS 8.0 или более поздней версии.

Можно задать целевой объект развертывания в файле Info.plist, чтобы по крайней мере 8.0, чтобы устранить эту ошибку (или не используйте внедренные платформы).

### <a name="mt14xx-crash-reports"></a>MT14xx: Отчеты о сбоях

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: Не удалось открыть службу отчетов о сбоях: AFCConnectionOpen возвращается *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: Не удалось закрыть службу отчетов о сбоях: AFCConnectionClose возвращается *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: Не удалось прочитать сведения о файле, для *: AFCFileInfoOpen возвращается *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: Не удалось прочитать отчет о сбоях: AFCDirectoryOpen (*) возвращаются: *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: Не удалось прочитать отчет о сбоях: AFCFileRefOpen (*) возвращаются: *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: Не удалось прочитать отчет о сбоях: AFCFileRefRead (*) возвращаются: *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: Не удалось перечислить отчеты о сбоях: AFCDirectoryOpen (*) возвращаются: *

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Возможные решения для решения этой проблемы:

* Удалите приложение с устройства и повторите попытку.
* Отключите устройство и снова его.
* Перезагрузка устройства.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: Не Mach-O динамической библиотеки (Неизвестный заголовок "0 x *»): *.

Произошла ошибка при обработке динамической соответствующую библиотеку.

Убедитесь, что динамическая библиотека является допустимым динамическая библиотека Mach-O.

Формат библиотеки можно проверить с помощью `file` команду из терминала:

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: Не статической библиотеки (Неизвестный заголовок "*"): *.

Произошла ошибка при обработке в статическую библиотеку в вопросе.

Убедитесь, что статическая библиотека является допустимым Mach-O статической библиотеки.

Формат библиотеки можно проверить с помощью `file` команду из терминала:

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: Не Mach-O динамической библиотеки (Неизвестный заголовок "0 x *»): *.

Произошла ошибка при обработке динамической соответствующую библиотеку.

Убедитесь, что динамическая библиотека является допустимым динамическая библиотека Mach-O.

Формат библиотеки можно проверить с помощью `file` команду из терминала:

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Неизвестный формат для запись в позиции * в *.

Произошла ошибка при обработке fat архива в вопросе.

Убедитесь, что fat архив является допустимым.

Формат fat архива можно проверить с помощью `file` команду из терминала:

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: Файл типа * не является файлом MachO (*).

Произошла ошибка при обработке MachO рассматриваемый файл.

Убедитесь, что файл является допустимым динамическая библиотека Mach-O.

Формат файла можно проверить с помощью `file` команду из терминала:

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Компоновщика, сообщения об ошибках

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: Не удалось выполнить привязку сборок

Эта ошибка означает, что компоновщик управляемых произошла непредвиденная ошибка, например исключение и не удалось завершить или сохраните сборку обрабатывается. Дополнительные сведения о конкретной ошибки будут входить в журнал построения, например

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

Очень важно отчет об ошибках для таких проблем. В большинстве случаев обходного пути можно указать, пока опубликован внесены соответствующие исправления. Указанные выше сведения важно (вместе с тестовым случаем и/или binairy сборки) для устранения проблемы.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: Не удается разрешить ссылку: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: Параметр "*" будет игнорироваться, так как компоновка отключена

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: Файл определений компоновщика Extra "*" не удалось найти.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Определения из "*" не удалось выполнить синтаксический анализ.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: Не удалось загрузить библиотеку mscorlib.dll из: *. Переустановите Xamarin.iOS.

Как правило, это означает, что возникла проблема с установкой Xamarin.iOS. Попробуйте переустановить Xamarin.iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: Неизвестный HttpMessageHandler `*`. Допустимые значения: HttpClientHandler (по умолчанию), CFNetworkHandler или NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: Неизвестный TlsProvider `*`.  Допустимые значения: по умолчанию или appletls.

Значение, присваиваемое `tls-provider=` не является допустимым поставщиком TLS (Transport Layer Security).

`default` И `appletls` являются единственными допустимыми значениями и оба представляют один и тот же параметр, а также поддерживает SSL/TLS с помощью собственного TLS API Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: Недопустимый HttpMessageHandler `*` для watchOS. Единственным допустимым значением является NSUrlSessionHandler.

Это предупреждение, что происходит потому, что в файле проекта указываются недопустимый HttpMessageHandler.

Более ранних версиях наши средства предварительного просмотра создается по умолчанию недопустимое значение в файле проекта.

Чтобы устранить это предупреждение, откройте файл проекта в текстовом редакторе и удалите все узлы HttpMessageHandler из XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: Недопустимый TlsProvider `legacy` параметр. Единственным допустимым значением `appletls` будет использоваться.

`legacy` Поставщика, который был полностью управляемая SSLv3 / TLSv1 единственным поставщиком, не поставляются с Xamarin.iOS больше. Проекты, которые использовали этот старого поставщика и теперь создавать с помощью новой `appletls` один.

Чтобы устранить это предупреждение, откройте файл проекта в текстовом редакторе и удалить все "MtouchTlsProvider'' узлов из XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: Не удалось обработать XML-описание.

Это означает, что имеется ошибка в [пользовательские XML-файл конфигурации компоновщика](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) вы указали, см. в разделе файла.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: Сборка "\*" есть ссылка из двух разных мест: "\*" и "*".

В сообщении об ошибке сборки загружается из нескольких расположений. Убедитесь в том, что всегда используют ту же версию сборки.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: Не удалось загрузить корневой сборки "*"

Не удалось загрузить корневой сборки. Убедитесь, что путь в сообщении об ошибке ссылается на существующий файл, и является допустимой сборкой .NET.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: Оптимизатор привязки не удалось обработки `...`.

Произошла непредвиденная произошла при попытке оптимизировать созданный код привязки. Элемент, причиной проблемы является именованным в сообщении об ошибке. Чтобы устранить эту проблему, с именем сборки (или содержащий тип или метод с именем) нужно будет предоставляться в [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительных mtouch аргументы**).

Последняя цифра `x` будет:
* `0` для имени сборки;
* `1` для имени типа;
* `3` Имя метода;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: Обработки не удалось удалить ресурсы пользователя `...`.

Произошла непредвиденная произошла при попытке удалить ресурсы пользователя. Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

Ресурсы пользователя, файлы, включенные в сборках (как ресурсы), которые требуется извлечь во время сборки, чтобы создать пакет приложений. В том числе следующее:

* `__monotouch_content_*` и `__monotouch_pages_*` ресурсов; и
* Собственные библиотеки, встроенный в сборку привязки;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: По умолчанию HttpMessageHandler setter сбой обработки `...`.

Произошла непредвиденная произошла при попытке задать значение по умолчанию `HttpMessageHandler` для приложения. Подайте [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Метод удаления кода не удалось обработки `...`.

Произошла непредвиденная произошла при попытке удалить код из BCL, в состав приложения. Подайте [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Sealer сбой обработки `...`.

Произошла непредвиденная произошла при попытке запечатайте типы или методы (окончательная) или devirtualizing некоторые методы. Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Метаданные редуктора не удалось обработки `...`.

Произошла непредвиденная произошла при попытке уменьшить метаданные из приложения. Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: MarkNSObjects сбой обработки `...`.

Произошла непредвиденная произошла при попытке пометить `NSObject` подклассы из приложения. Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](http://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Встраивания сбой обработки `...`.

Произошла непредвиденная произошла при попытке встроенный код из приложения. Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](https://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Смарт-Сохранить преобразование перечисления не удалось обработки `...`.

Произошла непредвиденная произошла при попытке пометить методы преобразования для смарт-перечислений из приложения. Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](https://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: Не удается разрешить ссылку на "\*«, на которую указывает ссылка из метода»\*" в "*".

Недопустимая сборка была обнаружена при обработке метод, упомянутый в сообщении об ошибке.

Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](https://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Произошла ошибка при обработке метода "\*«в сборке»\*": *

Произошла непредвиденная произошла при попытке пометить метод, упомянутый в сообщении об ошибке.

Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](https://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Ошибка при обработке сборки "\*": *

Произошла непредвиденная ошибка при обработке сборки.

Сборка, причиной проблемы называется в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо будет предоставить в [отчет об ошибках](https://bugzilla.xamarin.com) вместе с полная сборка журнала с уровнем детализации включен (т. е. `-v -v -v -v` в **дополнительные аргументы mtouch**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Не удалось связать сборку "{0}" как смешанный режим.

Сборки в смешанном режиме не могут обрабатываться компоновщиком.

См. в разделе https://msdn.microsoft.com/library/x0w2664k.aspx Дополнительные сведения о смешанных сборках.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: AOT, сообщения об ошибках

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Не удалось не сборки AOT "*"

Как правило, это указывает на ошибку в компиляторе AOT. Пожалуйста заполните [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с проектом, который может использоваться для воспроизведения ошибки.

Иногда это можно обойти это, отключив инкрементные сборки в проект iOS параметр сборки (но это по-прежнему ошибки, поэтому отправьте сообщение в любом случае).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002: Ограничение AOT: метод "*" должен быть статическим, так как он дополнен [MonoPInvokeCallback]. См. в разделе [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Это сообщение об ошибке поступает из AOT-компилятор.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: Конфликтующие--параметры отладки и--llvm. Soft отладка отключена.

Отладка не поддерживается, если включена LLVM. Если необходимо выполнить отладку приложения, сначала отключите LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Не удалось не сборки AOT "*", так как он не существует.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: Зависимость "\*«из сборки»\*" не найден. Просмотрите ссылки проекта.

Обычно это происходит, если сборка ссылается на другую версию сборки платформы (как правило, версия .NET 4 библиотеки mscorlib.dll).

Это не поддерживается и может не создавать или выполняться правильно (сборка может использовать API из .NET 4 версии библиотеки mscorlib.dll, версии Xamarin.iOS не поддерживает).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: Не удается вычислить карту зависимости для проекта. Это приведет к медленнее времени сборки, так как Xamarin.iOS не может правильно определить то, что необходимо перестроить (и какие действия не должен быть заново). Просмотрите предыдущие предупреждения, для получения дополнительных сведений.

 Создание или выполняться правильно (сборка может использовать API из .NET 4 версии библиотеки mscorlib.dll, версии Xamarin.iOS не поддерживает).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: Файлы сведений отладки (*.mdb) не будет загружен при включении llvm.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: Поддержка Bitcode требует использования параметров в серверную часть LLVM AOT (--llvm)

Поддержка Bitcode требует использования параметров в серверную часть LLVM AOT (--llvm).

Отключить поддержку Bitcode, либо включить LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Сообщения об ошибках код создания

### <a name="mt40xx-main"></a>MT40xx: Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: Основной шаблон не может быть развернут для `*`.

Произошла ошибка при создании main.m. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: Не удалось скомпилировать созданный код для методов P/Invoke. Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Не удалось скомпилировать созданный код для методов P/Invoke. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx: регистратора

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: Регистратор не может создать подпись для типа `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, как выполнить маршалинг с Objective-C.

Если вы считаете, Xamarin.iOS должен поддерживать соответствующий тип, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: Регистратор найден недопустимый тип `*` в сигнатуре для метода `*`. Взамен рекомендуется использовать `*`.

Это в настоящее время происходит только с одним типом: System.DateTime. Вместо этого используйте эквивалентные Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: Регистратор найден недопустимый тип `*` в сигнатуре для метода `*`: тип реализует INativeObject, но не имеет конструктор, который принимает два (IntPtr, bool) аргументов

Это происходит, когда регистратор возникнуть тип в сигнатуре с параметрами, упомянутых выше. Регистратор может потребоваться создать новые экземпляры типа, и в этом случае требуется конструктор с (IntPtr, bool) подпись - первый аргумент (IntPtr) указывает управляемых дескриптор, а на втором, если вызывающий объект передает владение машинный код Дескриптор (если это значение равно false, «сохранить» будет вызван для объекта).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: Регистратор не может маршалировать возвращаемое значение для типа `*` в сигнатуре для метода `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, как выполнить маршалинг с Objective-C.

Если вы считаете, Xamarin.iOS должен поддерживать соответствующий тип, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: Регистратор не может маршалировать параметр типа `*` в сигнатуре для метода `*`.

Если вы считаете, Xamarin.iOS должен поддерживать соответствующий тип, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: Регистратор не может маршалировать возвращаемое значение для структуры `*` в сигнатуре для метода `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, как выполнить маршалинг с Objective-C.

Если вы считаете, Xamarin.iOS должен поддерживать соответствующий тип, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: Регистратор не может маршалировать параметр типа `*` в сигнатуре для метода `+`.

Тип был найден в экспортированных API, который среда выполнения не знает, как выполнить маршалинг с Objective-C.

Если вы считаете, Xamarin.iOS должен поддерживать соответствующий тип, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: Регистратор не удается получить тип ObjectiveC для управляемого типа `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, как выполнить маршалинг с Objective-C.

Если вы считаете, Xamarin.iOS должен поддерживать соответствующий тип, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: Не удалось скомпилировать код созданный регистратора. Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Не удалось скомпилировать созданный код для регистратора. Журнал сборки будет содержать выходные данные собственного компилятора, объясняющее, почему не компиляции кода.

Это всегда ошибки в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com) с тестовым случаем или проекта.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: Регистратор не может маршалировать выходной параметр типа `*` в сигнатуре для метода `*`.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: Регистратор не может создать подпись для типа `*` в методе `*`.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112: Регистратор найден недопустимый тип `*`. Регистрация универсальных типов с Objective-C не поддерживается и может привести к непредвиденному поведению и/или сбои (для обеспечения обратной совместимости с более ранними версиями Xamarin.iOS можно не учитывать эту ошибку, передав `--unsupported--enable-generics-in-registrar` как дополнительные mtouch Аргумент, на странице параметры сборки проекта iOS. См. в разделе [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) Дополнительные сведения).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: Регистратор найден универсального метода: "\*.\*". Экспорт универсальные методы не поддерживается и приводит к непредвиденному поведению и/или сбои.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Непредвиденная ошибка в регистратор для метода "\*.\*"-отправьте отчет об ошибках в http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: Не удалось зарегистрировать сборку "*": *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: Найти регистратор Несовпадение подписей в методе "*.*"-селектор указывает метод принимает * параметров, а управляемый метод * параметров.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: Не удается зарегистрировать два управляемых типов ("\*«и»\*") с тем же именем собственного ("*").

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: Не удалось зарегистрировать селектор "\*«элемента»\*. *", так как селектор уже зарегистрировано в другой элемент.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: Регистратор найден указан неизвестный тип поля "\*«в поле»\*. *". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: Нельзя использовать GCC / G ++ для компиляции созданного кода от статического регистратора, при использовании платформы учетные записи (файлы заголовков, предоставляемых компанией Apple, используемый во время компиляции требуется Clang). Либо используйте Clang (--компилятора: clang) или динамической регистратор (--регистратора: динамические).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: Нельзя использовать в компилятора Clang *.* Пакет SDK для компиляции сформированного кода от статического регистратора, если отличные от ASCII имена типов ("*") в приложение. Либо используйте GCC / G ++ (--компилятора: gcc | g ++), динамического регистратор (--регистратора: динамические) или более новые SDK.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: Тип параметра с переменным числом аргументов в функции с переменным числом аргументов "*" должен быть System.IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: Недопустимый * на "*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: Регистратор найден недопустимый тип "\*«в сигнатуре для метода»\*": интерфейс должен иметь атрибут протокола, указывая его тип оболочки.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: Не удается зарегистрировать два управляемых протокола ("\*«и»\*") с тем же именем собственного ("*").

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: Не удается зарегистрировать более одного метода интерфейса для метода "\*" (который реализует "\*").

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: Регистратор найден недопустимый универсальный тип параметра "\*«в методе»\*". Универсальный параметр должен иметь ограничения «NSObject».

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: Регистратор найден недопустимый обобщенный тип возвращаемого значения "\*«в методе»\*". Обобщенный тип возвращаемого значения должен быть ограничением «NSObject».

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: Регистратор не удается экспортировать статические методы в универсальных классах ("*").

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: Регистратор не удается экспортировать статические свойства в универсальных классах ("\*.\*").

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: Регистратор найден недопустимый обобщенный тип возвращаемого значения "\*«в свойстве»\*". Тип возвращаемого значения должен иметь ограничение «NSObject».

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: Не удается создать экземпляр типа "*" из Objective-C, так как тип является универсальным. [Исключение времени выполнения]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: Приложение использует "*" платформу, которая не включена в пакет SDK, вы используете для создания приложения для iOS (эта инфраструктура была введена в iOS *, а вы создаете с помощью iOS * пакет SDK.) Выберите новую SDK в параметрах сборки приложения iOS.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: Элемент "\*.\*" имеет атрибут экспорта, который не указывает селектора. Имя является обязательным.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: Регистратор не может маршалировать тип параметра "\*«параметра»\*«в методе»\*. *"

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: Регистратор не может маршалировать тип свойства "\*«свойства»\*. *".

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: Регистратор не может маршалировать тип свойства "\*«свойства»\*. *". Свойства с атрибутом [подключение] должен иметь тип свойства NSObject (или его подкласс NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: Найти регистратор Несовпадение подписей в методе "*.*"-селектор указывает метод с переменным числом аргументов принимает * параметров, а управляемый метод * параметров.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: Не удается зарегистрировать селектор "\*«в элементе»\*", так как Xamarin.iOS неявно регистрирует этот селектор.

Это происходит, когда создание подкласса типом framework и требуется реализовать «сохранить», «выпуск» или «dealloc» метод:

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

Это, однако можно переопределить эти методы, в том случае, если класс не является первой подкласс платформы введите:

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

В этом случае будет переопределять Xamarin.iOS `retain`, `release` и `dealloc` на `MyNSObject` класса, и не было конфликтов.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: Не удалось зарегистрировать тип "*".

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: Класс ObjectiveC "*" не может быть зарегистрировано, он не являются производными от любого известного ObjectiveC класса, (включая NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: Не удается зарегистрировать метод "*", так как он не имеет связанного trampoline. Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Недопустимое перечислимое "*": перечисления с атрибутом [собственный] должен иметь базовый тип перечисления «long» или «ulong».

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: Имя параметра атрибута регистратора в классе\*"("\*") содержит недопустимый символ:"\*"(\*).

Имя класса Objectice C не может содержать пробелы, это означает, что `Register` атрибут на соответствующий управляемый класс не может иметь `Name` либо параметр не может содержать пробелы.

Убедитесь, что `Register` атрибут на управляемый класс, упомянутые в сообщении об ошибке не содержит пробелов.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Обнаружено протокол наследования от протокола JSExport при использовании динамического регистратор. Невозможно экспортировать протоколы JavaScriptCore динамически; необходимо использовать статический регистратор (добавьте "--регистратора: static к дополнительным аргументам mtouch в iOS проекта параметры сборки, чтобы выбрать статический регистратор).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: Регистратор найти универсальный протокол: "*". Экспорт универсальные протоколы, не поддерживается.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: Не удается зарегистрировать метод "\*.\*" так как тип первого параметра ("\*") не соответствует типу категорию ("\*").

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: Не удается зарегистрировать тип "\*" так как свойство типа ("\*") в его категории атрибут не является производным от NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: Не удается зарегистрировать тип "*", так как не задано свойство типа в его атрибут Category.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: Не удается зарегистрировать тип "*" как категория так как он реализует INativeObject или подклассы NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: Не удается зарегистрировать тип "\*" как категория, так как он является универсальным.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: Не удается зарегистрировать метод "\*" как метод категории, так как он является универсальным.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: Не удается зарегистрировать метод "\*«с помощью селектора»\*" как метод категории на "*", так как Objective-C уже имеет реализацию этот селектор.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: Не удается зарегистрировать две категории ("\*«и»\*") с тем же именем собственного ("*").

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: Не удается зарегистрировать метод категории "\*", так как по крайней мере один параметр является обязательным (и его тип должен соответствовать типу категории "\*")

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: Не удается зарегистрировать конструктор * категории * поскольку конструкторы в категории не поддерживаются.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: Не удается зарегистрировать метод "*" как метод категории, так как методы категории должны быть статическими.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Недопустимый символ "\*" (\*) найти в селекторе "\*«for»\*".

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: Регистратор найден неподдерживаемый структуру "\*": все поля в структуре также должны быть структур (поле "\*«с типом»{2}" не является структурой).

Регистратор найти структуру с неподдерживаемые поля.

Все поля в структуре, которая предоставляется Objective-C, также должны быть структуры (не классы).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: Тип "\*" (используется в качестве * {2}) недоступен в ** (она появилась в * *)\* выполните сборку с более новой * пакет SDK (обычно выполняются с помощью самую последнюю версию Xcode.

Регистратор найти тип, который не входит в текущий пакет SDK.

Выполните обновление Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Внутренняя ошибка в регистратор (*). Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: Не удается экспортировать свойство "\*" поскольку селектора "\*" ключевое слово, Objective-C. Используйте другое имя.

Селектор рассматриваемое свойство не является допустимым идентификатором Objective-C.

Используйте допустимый идентификатор Objective-C как селекторов.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: Регистратор не удалось найти тип «System.Void» во всех сборках.

Эта ошибка скорее всего, указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: Не удается зарегистрировать метод "\*", так как подпись содержит тип (\*), не является ссылочным.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: Не удается зарегистрировать метод "\*", так как подпись содержит универсальный тип (\*) с типом универсального аргумента, который не подкласса NSObject (*).

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: Не удается зарегистрировать тип "{управляемых\_имя}" так как его имя Objective-C "{экспортировать\_имя}" — это ключевое слово Objective-C. Используйте другое имя.

Имя Objective-C для соответствующий тип не является допустимым идентификатором Objective-C.

Используйте допустимый идентификатор Objective-C.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: Не удалось создать оболочку P/Invoke для {метод}: {message}

Xamarin.iOS не удается создать функцию-оболочку P/Invoke для упомянутых выше.
Проверьте сообщение ошибки для основную причину.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: Регистратор не удается преобразовать из «{управляемого типа}» в «{собственный тип}» для возвращаемого значения в метод {}.

См. в описании ошибки <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: Недопустимый атрибут BindAs в элементе {элемент}: тип BindAs {type} отличается от типа свойства {type}.

Убедитесь, что тип в атрибуте BindAs совпадает с типом члена, которому он присоединен.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: Регистратор невозможно преобразовать из «{собственный тип}» в «{управляемый тип}» для параметра «{имени}» в методе {метод}.

Регистратор не поддерживает преобразование между упомянутых выше типов.

Это ошибка в Xamarin.iOS, если нужному API, предоставляемые Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ] [ 1].

При запуске в данную коллекцию при разработке проекта привязки для машинной библиотеки, мы открыты для добавления поддержки для нового сочетания типов. Если это так, отправьте запрос на расширение ([http://bugzilla.xamarin.com][2]) с помощью тестового случая и мы оценить его.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: GCC и цепочки инструментов сообщения об ошибках

### <a name="mt51xx-compilation"></a>MT51xx: компиляция

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Отсутствует "*" компилятора. Установите компонент «Средства командной строки» Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: Не удалось собрать файл "*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: Не удалось скомпилировать файл "*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: Не удалось найти ни "\*«и»\*" компилятора. Установите компонент «Средства командной строки» Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: Не удалось скомпилировать файлы "*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx: связывание

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Собственный связывание не удалось. Просмотрите журнал сборки и флаги пользователя, предоставленный gcc: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Собственный связывание не удалось. Просмотрите журнал построения.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Машинный код связывание предупреждение: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Машинный код связывание ошибка: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Собственный связывание не удалось неопределенный символ: *. Убедитесь, что содержатся ссылки на все необходимые платформы, и собственные библиотеки должным образом связаны в.

Это происходит, когда собственный компоновщику не удается найти символ, который используется где-нибудь. Существует несколько причин, что это может произойти:

* Привязка независимых производителей необходима платформа, но привязка не задано в его `[LinkWith]` атрибута. Решения:
  - Если вы являетесь автором стороннего привязки или иметь доступ к источнику, изменить привязки `[LinkWith]` атрибут для включения framework, оно должно:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Если невозможно изменить привязку независимых производителей, вручную можно связать с требуемую платформу, передав `-gcc_flags '-framework SystemFramework'` для `mtouch` (это делается путем изменения дополнительным аргументам mtouch на странице параметры сборки проекта iOS. Помните, что это необходимо сделать для каждой конфигурации проекта).
* В некоторых случаях управляемый привязки представляет собой несколько собственных библиотек, и все должны быть включены в привязках. Это может быть более одной собственной библиотеки в каждый проект привязки, поэтому решение — просто добавьте необходимые собственные библиотеки в проект привязки.</li>
* Управляемые привязки ссылается на собственных символов, которые не существуют в собственной библиотеки.
    Обычно это происходит, когда привязки, существует некоторое время, и машинный код был изменен в течение этого времени, чтобы определенного собственного класса были удалены или переименованы, пока не была обновлена привязки.
* P/Invoke ссылается на символ, не существует. Начиная с версии 7.4 Xamarin.iOS <a href="#MT5214">MT5214</a> ошибка для этого случая (Дополнительные сведения см. в. MT5214).
* Привязка независимых производителей / библиотека создана с помощью C++, но привязка не задано в его `[LinkWith]` атрибута. Это обычно довольно легко распознавать, так как символы имеют искаженное символы C++ (один распространенный пример — `__ZNKSt9exception4whatEv`).
  - Если вы являетесь автором стороннего привязки или иметь доступ к источнику, изменить привязки `[LinkWith]` атрибута `IsCxx` флаг:

            [LinkWith ("mylib.a", IsCxx = true)]

  - Если вы не можете изменить привязку независимых производителей, или вручную связывании с библиотекой стороннего, эквивалентные флаг задается путем передачи <code>-cxx</code> для mtouch (это можно сделать, изменив дополнительным аргументам mtouch на странице параметры сборки проекта iOS . Помните, что это необходимо сделать для каждой конфигурации проекта).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Собственный связывание не удалось неопределенный класс Objective-C: \*. Символ "\*" не удалось найти в любом библиотек и платформ, связанная с вашим приложением.

Это происходит, когда собственный компоновщику не удается найти класс, Objective-C, который используется где-нибудь. Это может произойти по нескольким причинам: так же, как [MT5210](#MT5210) и Кроме того:

* Привязка независимых производителей привязан протокол Objective-C, но не аннотируйте его с помощью <code>[Protocol]</code> атрибута в определение api. Решения:
  - Добавьте отсутствующее `[Protocol]` атрибут:

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Собственный связывание не удалось повторяющегося символа: *.

Это происходит, когда собственный компоновщик обнаруживает повторяющиеся символы между все собственные библиотеки. После этой ошибки может существовать один или несколько [MT5213](#MT5213) ошибки, связанные с расположение для каждого вхождения символа. Возможные причины этой ошибки:

* Той же библиотеке собственного включается в два раза.
* Для определения и те же символы произойти два различных собственных библиотек.
* Собственная библиотека не встроен, а также содержит один символ более одного раза.
  Это можно проверить с помощью следующий набор команд из терминала (Замените i386 x86_64 и armv7 и armv7s/arm64 в соответствии с архитектуры, вы создаете для):

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Существует несколько возможных способов, чтобы устранить эту проблему:

  - Запрос, что поставщик собственной библиотеки, устранить ее и предоставить новую версию.
  - Ее устранить самостоятельно, удалив файлы лишний объект (это работает, только если проблема в том, на самом деле файлы дублированный объект)

            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a

  - Попросите компоновщику удалять неиспользуемый код. Xamarin.iOS сделает это автоматически, если выполняются все из следующих условий:
    - Все привязки сторонних `[LinkWith]` атрибуты включения разработала:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - Не `-gcc_flags` передается mtouch (в поле аргументы mtouch дополнительных параметрах сборки проекта iOS).
    - Можно также попросить компоновщик напрямую, чтобы удалить неиспользуемый код, добавив `-gcc_flags -dead_strip` к дополнительным аргументам mtouch в проект iOS параметры сборки.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Дублировать символ в: * (расположение связанного с предыдущей ошибкой)

Эта ошибка выдается только совместно с параметром [MT5212](#MT5212). См. в разделе [MT5212](#MT5212) Дополнительные сведения.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Собственный связывание не удалось неопределенный символ: *. Была ссылка на этот символ управляемого элемента *. Убедитесь, что все необходимые платформы были на которые имеются ссылки и собственные библиотеки связанной.

Эта ошибка выдается в том случае, когда управляемый код содержит P/Invoke для собственный метод, который не существует. Пример:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Существует несколько возможных решений.

  -  Удалите рассматриваемый P/Invoke из исходного кода.
  -  Включение управляемых компоновщика для всех сборок, (этого в проект iOS параметры сборки, задав «Поведение компоновщика» на «Все сборки»). Это эффективно приведет к удалению всех P/Invoke вы не используете из приложения (автоматически, а не вручную, например предыдущей точки). Недостатком является это сделает сборок симулятора медленнее, что он может нарушить работу приложения, если он используется отражение, — можно найти дополнительные сведения о компоновщик [здесь](~/ios/deploy-test/linker.md) )
  -  Создайте второй собственной библиотеки, который содержит отсутствующие собственных символов. Обратите внимание, что это просто обходной путь (Если вы попробуете вызвать эти функции, приложения произойдет сбой).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: Ссылки на "*" может потребовать дополнительных - framework = XXX или - lXXX инструкции собственного компоновщика

Это предупреждение, указывающее, что было обнаружено P/Invoke для ссылки на соответствующую библиотеку, но приложение не сопоставлен с ним.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Собственный связывание сбой для *. Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка выдается при компоновке выходные данные компилятора AOT.

Эта ошибка скорее всего, указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Возможно собственного связывание не удалось, так как имеет слишком большую длину командной строки компоновщика (* символов).

Собственный связывание не удалось, а возможно, это произошло потому, что компоновщик команду имеет слишком большую длину.

Проекты Xamarin.iOS будет часто ссылаются на собственных символов динамически, это означает, что собственного Компоновщик может удалять таких собственных символов в процессе собственного связывания, поскольку собственного компоновщик не видит, что эти символы используются.

Обычно Xamarin.iOS запросит компоновщику хранить такие символы, с использованием `-u symbol` флаг компоновщика, но при наличии много таких символов, весь командной строки может превышать максимальную длину командной строки, как указано в операционной системе.

Существует несколько возможных источников для таких динамического символов:

* P/Invokes в методы в статически связанные библиотеки (где — имя библиотеки dll `__Internal` в атрибут DllImport `[DllImport ("__Internal")]`).
* Поле ссылки на адресов памяти при статически связанные библиотеки с проектами привязки (`[Field]` атрибутов).
* Классы Objective-C, на которые ссылается статически связанные библиотеки с проектами привязки (при использование инкрементных построений или не используется статический регистратор).

Возможные решения:

* Включение управляемых компоновщика (если это возможно для всех сборок, а не только сборки пакета SDK). Это может удалить достаточно источников для динамического символы, чтобы компоновщик командной строки не превышает максимально.
* Уменьшите количество P/Invoke, ссылки на поля и/или классы Objective-C.
* Перепишите динамического символы, имеют более короткие имена.
* Передайте `-dlsym:false` дополнительных mtouch в качестве аргумента проекта iOS параметры сборки. В этом случае Xamarin.iOS будет создавать собственную ссылку в коде AOT-скомпилированные и не нужно попросить компоновщика, чтобы сохранить этот символ. Тем не менее создает работает только для устройства, и это вызовет ошибки компоновщика, если P/Invoke для функций, которые не существуют в статическую библиотеку.
* Передайте `--dynamic-symbol-mode=code` как дополнительные аргументы mtouch в проект iOS параметры сборки. В этом случае Xamarin.iOS создает дополнительный собственный код, который ссылается на эти символы вместо просить компоновщику сохранить эти символы, с помощью аргументов командной строки. Недостатком этого подхода является, это увеличит размер исполняемого файла немного.
* Включить статический регистратор путем передачи `--registrar:static` дополнительных mtouch в качестве аргумента проекта iOS параметры сборки (для сборок симулятора, поскольку статические регистратор уже используется по умолчанию, для сборок устройств). Статические регистратор создаст код, который ссылается на классы Objective-C статически, поэтому нет необходимости, чтобы запросить компоновщику сохранить такие классы.
* Отключите инкрементные сборки (для сборок устройств). Если инкрементные сборки включены, код, сгенерированный статический регистратор не считалось собственного компоновщиком, что означает, что Xamarin.iOS по-прежнему необходимо попросить компоновщик для сохранения ссылки на классы Objective-C. Таким образом, отключение инкрементные сборки не этой потребности.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: Не может игнорировать динамический символ {символ} (--игнорировать dynamic-symbol = {symbol}), так как он не был обнаружен в качестве динамического символа.

Аргумент командной строки `--ignore-dynamic-symbol=symbol` было передано, но этот символ не является символ, который был распознан как динамический символ, который должен сохраняться вручную.

Существует две основных причины этого:

* Указано неверное имя символа.
    * Не добавляйте в начало символ подчеркивания в имени символа.
    * Символ для классов Objective-C является `OBJC_CLASS_$_<classname>`.
* Символ указан правильно, но он — это символ, который уже сохранен обычным способом (некоторые построения причины параметры точный список динамического символы для изменения).

### <a name="mt53xx-other-tools"></a>MT53xx: Другие средства

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Отсутствует средство «удалить». Установите компонент «Средства командной строки» Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Отсутствует средство «dsymutil». Установите компонент «Средства командной строки» Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: Не удалось создать отладочные символы (каталог dSYM). Просмотрите журнал построения.

Произошла ошибка при выполнении dsymutil в каталоге окончательный .app создать отладочные символы. Просмотрите журнал сборки см. в разделе выходных данных в dsymutil и см. в разделе, как можно исправить ее.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: Не удалось удалить конечный двоичный файл. Просмотрите журнал построения.

Произошла ошибка при работе со средством «лента», чтобы удалить отладочную информацию из приложения.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Отсутствует средство «lipo». Установите компонент «Средства командной строки» Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: Не удалось создать библиотеку fat. Просмотрите журнал построения.

Произошла ошибка при работе со средством «lipo». Просмотрите журнал, сборки, чтобы просмотреть ошибку, о которых сообщает «lipo».

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: Не удалось подписать исполняемый файл. Просмотрите журнал построения.

Произошла ошибка при подписи приложения. Просмотрите журнал, сборки, чтобы просмотреть ошибку, о которых сообщает «codesign».

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mtouch внутренних средств сообщения об ошибках

### <a name="mt600x-stripper"></a>MT600x: Stripper

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: Запущена версия Сесил не поддерживает удаление сборки

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: Не удалось удалить сборку `*`.

Произошла ошибка при чередует управляемого кода (удаление кода IL) из сборок в приложении.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [UnauthorizedAccessException message]

Произошла ошибка безопасности при удаление отладочных символов из приложения.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: MSBuild, сообщения об ошибках

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: Не удалось разрешить IP-адресов узлов для параметров Wi-Fi отладчика.

*Задача MSBuild: DetectDebugNetworkConfigurationTaskBase*

Действия по устранению неполадок:

- Попробуйте выполнить `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (Это позволит вам IP-адресом, а не ошибкой очевидно).
- Попробуйте выполнить «ping \`hostname\`"которой может получить дополнительные сведения, например: `cannot resolve MyHost.local: Unknown host`

В некоторых случаях это проблема «локальной сети» и его можно обращаться путем добавления неизвестный узел `127.0.0.1   MyHost.local` в `/etc/hosts`.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Этот компьютер не имеет ни одного сетевого адаптера. Это необходимо, при отладке или профилировании на устройстве через Wi-Fi.

*Задача MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: Расширение приложения "*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: Расширение приложения "*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: Расширение приложения "*" не соответствует CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: Расширение приложения "\*" имеет недопустимый CFBundleIdentifier (\*), он не начинается с CFBundleIdentifier набор основных приложений (*).

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: Расширение приложения "\*" имеет CFBundleIdentifier (\*), заканчивается недопустимый суффиксом «.key».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: Расширение приложения "*" не соответствует CFBundleShortVersionString.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: Расширение приложения "*" имеет недопустимый Info.plist: он не содержит словарю NSExtension.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: Расширение приложения "*" имеет недопустимый Info.plist: словарь NSExtension не содержит значение NSExtensionPointIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: Расширение WatchKit "*" имеет недопустимый Info.plist: словарь NSExtension не содержит словарю NSExtensionAttributes.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: Расширение WatchKit "*" не имеет ровно один приложении для Apple watch.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: Расширение WatchKit "*" имеет недопустимый Info.plist: UIRequiredDeviceCapabilities должен содержать функцию «watch-помощник по поиску».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: Приложение Контрольные значения "*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: Приложение Контрольные значения "*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: Приложении для Apple Watch "\*" имеет недопустимый CFBundleIdentifier (\*), он не начинается с CFBundleIdentifier набор основных приложений (*).

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: Приложении для Apple Watch "\*" не имеет допустимое значение UIDeviceFamily. Ожидаемый «Контрольные значения (4)», но обнаружен "\* (*)".

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: Приложение Контрольные значения "*" не соответствует CFBundleExecutable

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: Приложении для Apple Watch "\*" имеет недопустимое значение WKCompanionAppBundleIdentifier ("\*"), не соответствует CFBundleIdentifier набор основных приложений ("*").

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: Приложение Контрольные значения "*" имеет недопустимый Info.plist: ключ WKWatchKitApp должен присутствовать и иметь значение «true».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: Приложение Контрольные значения "*" имеет недопустимый Info.plist: LSRequiresIPhoneOS ключ не должен присутствовать.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: Приложение Контрольные значения "*" не содержит расширение просмотра.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: Расширение Контрольные значения "*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: Расширение Контрольные значения "*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: Расширение Контрольные значения "*" не соответствует CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: Расширение Контрольные значения "\*" имеет недопустимый CFBundleIdentifier (\*), он не начинается с CFBundleIdentifier набор основных приложений (*).

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: Расширение Контрольные значения "\*" имеет CFBundleIdentifier (\*), заканчивается недопустимый суффиксом «.key».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: Расширение Контрольные значения "*" имеет недопустимый Info.plist: он не содержит словарю NSExtension.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: Расширение Контрольные значения "*" имеет недопустимый Info.plist: NSExtensionPointIdentifier должно быть «com.apple.watchkit».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: Расширение Контрольные значения "*" имеет недопустимый Info.plist: словарь NSExtension должен содержать NSExtensionAttributes.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: Расширение Контрольные значения "*" имеет недопустимый Info.plist: словарь NSExtensionAttributes должен содержать WKAppBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: Расширение WatchKit "*" имеет недопустимый Info.plist: UIRequiredDeviceCapabilities не должен содержать функцию «watch-помощник по поиску».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: Приложение Контрольные значения "*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: Приложение Контрольные значения "*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: Приложении для Apple Watch "\*" не имеет допустимое значение UIDeviceFamily. Ожидалось "\*«, обнаружено»\* (\*)".

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: Приложение Контрольные значения "*" не соответствует CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: Расширение WatchKit «{extensionName}» имеет недопустимое значение WKAppBundleIdentifier ("\*"), не соответствует CFBundleIdentifier приложение Watch ("\*").

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: Приложение Контрольные значения "*" имеет недопустимый Info.plist: WKCompanionAppBundleIdentifier должна существовать и соответствовать CFBundleIdentifier набор основных приложений.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: Приложение Контрольные значения "*" имеет недопустимый Info.plist: LSRequiresIPhoneOS ключ не должен присутствовать.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: Набора приложений {AppBundlePath} не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Путь Main Info.plist не содержит CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Путь Main Info.plist не содержит CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Путь Main Info.plist не содержит CFBundleSupportedPlatforms.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Путь Main Info.plist не содержит UIDeviceFamily.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Нераспознанный формат: *.

*Задача MSBuild: PropertyListEditorTaskBase*

Где * может быть:

- string
- array
- dict
- bool
- действительные
- целочисленный
- date
- Данные

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Добавить: запись, *, неправильно указанный.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Добавить: запись, *, содержит недопустимый индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Добавить: * запись уже существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Добавить: не удается добавить запись, *, в качестве родительского.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Удалить: не удается удалить запись, *, из родительского элемента.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Удалить: запись, *, содержит недопустимый индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Удалить: запись, *, не существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Импортировать: запись, *, неправильно указанный.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Импортировать: запись, *, содержит недопустимый индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Импортировать: ошибка при чтении файла: *.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Импортировать: не удается добавить запись, *, в качестве родительского.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Слияние: невозможно добавить dict. записей массива

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Слияние: указан, операция должна быть контейнер.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Слияние: запись, *, содержит недопустимый индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Слияние: запись, *, не существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Слияние: ошибка при чтении файла: *.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Задайте: запись, *, неправильно указанный.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Задайте: запись, *, содержит недопустимый индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Задайте: запись, *, не существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Действие редактора Неизвестный PropertyList: *.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Ошибка при загрузке "*": *.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Ошибка при сохранении "*": *.

*Задача MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Сообщения среды выполнения

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Несоответствие версий между собственной среды выполнения Xamarin.iOS и monotouch.dll. Переустановите Xamarin.iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: Не удалось найти метод "\*«в типе»\*".

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: Не удалось найти закрытый универсальный метод "\*«в типе»\*".

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: Не удается создать экземпляр * для собственного объекта 0 x * (типа "*"), так как другой экземпляр уже существует для этого собственный объект (тип *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Тип оболочки "\*«отсутствует его собственного класса ObjectiveC»\*".

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: Не удалось найти селектор "\*«в типе»\*"

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: Не удается получить дескриптор метода для селектора "\*«в типе»\*", так как селектор не соответствует методу

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: Загруженную версию Xamarin.iOS.dll был скомпилирован для * bits, во время процесса * bits. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Это означает, что дает сбой в процессе построения. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: Не удается найти блок в делегата метод преобразования для метода *.*" s параметр #*. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Это означает, что привязка API не была выполнена правильно. Это API, предоставляемый компанией Xamarin, можно зарегистрировать ошибку в наших bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), если привязка независимых производителей, обратитесь к поставщику.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Собственный тип совпадает с числом параметров Xamarin. [iOS | Mac] .dll и архитектура выполнения. Xamarin. [iOS | Mac] .dll был построен для *-бит, пока текущий процесс *-разрядной.

Это означает, что дает сбой в процессе построения. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: Не удается найти делегат для атрибута block преобразования ([DelegateProxy]) для возвращаемого значения для метода *.*. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Xamarin.iOS не удалось найти необходимый метод во время выполнения (для преобразования делегат в блоке).

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата имеет значение null. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата ({2}) указывает тип без поля «Handler». Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата ({2}) поле «Handler» имеет значение null. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата ({2}) поле «Handler» не является делегатом, это *. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: Не удалось преобразовать делегат для блокировки для возвращаемого значения для метода *.*, так как входные данные не делегат, это *. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Ошибка внутренней согласованности. Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: Не удалось найти сборку * в загруженных сборках.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: Не удалось найти модуль с помощью параметр MetadataToken * в сборке *.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Неизвестный тип неявного токена: *.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: Ожидается ссылка на маркер * как *, но это *. Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Требуется свой объект экземпляра для создания закрытого универсального метода для открытого универсального метода: * (Справочник по маркерам: *). Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: Не удалось найти является действительным типом расширения для смарт-перечисления «{smart_type}». Можно зарегистрировать ошибку на https://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
