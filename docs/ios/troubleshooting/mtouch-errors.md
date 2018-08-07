---
title: Ошибки Xamarin.iOS
description: В этом документе описываются различные ошибки, созданные mtouch инструмент, используемый для упаковки приложения Xamarin.iOS. Ошибки перечислены в коде и полное описание.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: e9332ba34f113f56859065c74c24c116a331eceb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789451"
---
# <a name="xamarinios-errors"></a>Ошибки Xamarin.iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: сообщения об ошибках mtouch

Например, параметры среды, средств отсутствует.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpbugzillaxamarincom"></a>MT0000: Непредвиденная ошибка - заполните отчет об ошибках в http://bugzilla.xamarin.com

Произошла непредвиденная ошибка. Проверьте [отчет об ошибке](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) как можно больше информации, включая:

* Полные журналы, построений с максимальная степень подробности (например `-v -v -v -v` в **mtouch дополнительные аргументы**);
* Минимальный тестовый случай, воспроизведите ошибку. и
* Все версии сведения

Чтобы получить сведения о точной версии проще всего использовать **Visual Studio для Mac** меню **о Visual Studio для Mac** элемента, **Показать подробности** кнопки, копирование и вставка версия сведения (можно использовать **сведения о копировании** кнопка).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: "-devname" предоставлен без вмешательства конкретных устройств

Это предупреждение создается, если передается - devname mtouch при вмешательство конкретного устройства (-logdev/installdev/killdev/launchdev /-listapps) был запрошен.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: Не удалось выполнить синтаксический анализ переменной среды \*.

Эта ошибка возникает при попытке задать среды недопустимый ключ = значение переменной пары. Имеет неправильный формат: `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Имя приложения "\* .exe" конфликтует с именем сборки (DLL) пакета SDK или продукта.

Имя исполняемой сборки и имя приложения не может совпадать с именем библиотеки DLL, в приложении. Измените это имя исполняемого файла.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: Новая логика refcounting требует SGen слишком включения.

При включении расширения refcounting необходимо также включить сборщик мусора SGen в проекте iOS параметры построения (вкладка «Дополнительно»).

Начиная с Xamarin.iOS 7.2.1 это требование было снято, можно включить в новой логике refcounting с Боэм и сборщики мусора SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: Выходной каталог \* не существует.

Создайте каталог.

Эта ошибка больше не создается, mtouch автоматически будет создан каталог, если он не существует.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: Отсутствует платформа не devel \*, используйте--платформы = PLAT, чтобы указать пакет SDK.

Xamarin.iOS не удается найти каталог пакета SDK в расположении, упомянутые в сообщении об ошибке. Убедитесь, что путь указан правильно.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: Корневой сборки \* не существует.

Xamarin.iOS не удается найти сборку в месте, упомянутые в сообщении об ошибке. Убедитесь, что путь указан правильно.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: Необходимо указать один корневые сборки только, найдено # сборки: \*.

Более одной корневой сборки был передан mtouch, могут быть только один корневой сборки.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Ошибка при загрузке сборки: \*.

Произошла ошибка при загрузке сборки корневых ссылок на сборки. Дополнительные сведения могут предоставляться в выходные данные построения.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: Не удалось выполнить синтаксический анализ аргументов командной строки: \*.

Произошла ошибка при синтаксическом анализе аргументов командной строки. Убедитесь, что они верны все.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: \* был создан в более поздней выполнения (\*) не поддерживает MonoTouch.

Обычно это предупреждение отображается, поскольку проект имеет ссылку на библиотеку классов, не было создано с помощью Xamarin.iOS BCL.

Так же, как приложения с помощью пакета SDK платформы .NET 4.0 могут не работать в системе только вспомогательные .NET 2.0 библиотеки, созданных с использованием .NET 4.0 могут не работать в Xamarin.iOS, он может использовать API не существует на Xamarin.iOS.

Общие решение заключается в том, чтобы построить библиотеку как Xamarin.iOS библиотека классов. Это можно сделать путем создания нового проекта библиотеки классов Xamarin.iOS и добавление всех исходных файлов. Если у вас исходный код для библиотеки, следует обратиться к поставщику и запрос, что они предоставляют Xamarin.iOS совместимой версии свои библиотеки.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Неполные данные предоставляются для завершения \*.

Эта ошибка больше не указывается в текущей версии Xamarin.iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: Профилирования поддержки требуется sgen слишком включения.

SGen (--sgen) необходимо включить профилирование (--профилирования) включен.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: IOS \* пакет SDK не поддерживает создание приложений для различных версий \*.

Это может произойти в следующих случаях:

*  ARMv6 включен и устанавливается Xcode 4.5 или более поздней версии.
*  ARMv7s включен и устанавливается Xcode 4.4 или более ранней версии.

Убедитесь, что установленная версия Xcode поддерживает выбранных архитектур.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: Недопустимый ABI: \*. Поддерживаемые ABIs являются: i386 x86_64, armv7 armv7 + llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 и arm64 + llvm.

В mtouch был передан недопустимый интерфейс ABI. Укажите допустимый ABI.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: Параметр \* рекомендуется к использованию.

Параметр упомянутых mtouch является устаревшим и будет игнорироваться.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: Можно указать корневой сборки.

Это необходимо, чтобы указать корневой сборки (обычно основного исполняемого файла) при построении приложения.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Неизвестный параметр командной строки: \*.

Mtouch не распознает аргумент командной строки, упомянутые в сообщении об ошибке.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Только один--[журнала | установить | kill | запуска] разработки или--[запуска | отладка] sim параметр может быть использован.

Существует несколько вариантов mtouch, не могут использоваться одновременно.

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Допустимые параметры для "\*«,»\*".

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: Не удается скомпилировать с помощью gcc / g ++ (--используйте gcc) при использовании статического регистратора (это значение по умолчанию, при компиляции для устройства). Либо удалите используйте gcc флаг или использовать динамические регистратора (--регистратора: динамические).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: Параметры «--неподдерживаемые--enable-универсальных типов в регистратора» и "--регистратора" несовместимы.

Удалите оба варианта `--unsupported--enable-generics-in-registrar` и `--registrar`. Начиная с Xamarin.iOS 7.2.1 регистратора по умолчанию поддерживает универсальные шаблоны.

Эта ошибка больше не отображается (аргумент командной строки `--unsupported--enable-generics-in-registrar` был удален из mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Имя приложения "\* .exe" конфликтует с другой пользователь сборки.

Имя исполняемой сборки и имя приложения не может совпадать с именем библиотеки DLL, в приложении. Измените это имя исполняемого файла.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: Не удалось найти требуемый файл "\*".

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: Версия пакета SDK не был предоставлен. Добавьте `--sdk=X.Y` для указания какие iOS SDK следует использовать для построения приложения.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: Не удалось выполнить синтаксический анализ аргументов командной строки "\*": \*

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Options'\*«и»\*"несовместимы.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: Не удается включить КРУГОВОЙ (-круговой) при разработке для iOS 4.1 или более ранней версии. Отключите КРУГОВОЙ (-круговой: false) или по крайней мере равно цели развертывания iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--enable repl) поддерживается только в симуляторе (--sim).

REPL поддерживается только в том случае, если вы создаете для симулятора. Это означает, что при передаче `--enable-repl` для mtouch, необходимо также передать `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: Имя исполняемого файла (\*) и имя приложения (\*) являются различными, это может препятствовать журналы аварийных начало symbolicated должным образом.

Когда symbolicates Xcode (преобразовывает адреса памяти для имен функций и номеров строки файла) процесс может завершиться ошибкой, если исполняемый файл и приложения имеют различные имена (без расширения).

Чтобы устранить это либо измените «Имя приложения» в параметрах приложения сборки/iOS проекта или изменение «Имя сборки» в параметрах выходные данные построения проекта.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Аргументы командной строки "— Включить выборку в фоновом режиме" и "--запуска для фонового извлечения" требуется "--launchsim" слишком.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: Параметр "--debugtrack" игнорируется, если "--debug" также указан.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Xamarin.iOS проект должен ссылаться на monotouch.dll или Xamarin.iOS.dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: В том же проекте Xamarin.iOS - не может содержать «monotouch.dll» и «Xamarin.iOS.dll» "\*" — Прямая ссылка на, а "\*" ссылается "\*".

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: Не удается запустить \* имитатор для \* приложения. Включите правильный architecture(s) в проекте iOS параметры сборки (страница «Дополнительно»).

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: monotouch.dll — 64-разрядными платформами. Ссылки Xamarin.iOS.dll либо не создавайте для архитектуры x 64 (ARM64 или x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Регистраторов старый (--регистратора: oldstatic | olddynamic) не поддерживаются при ссылке на Xamarin.iOS.dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Приложения, предназначенные для ARMv6 не может ссылаться на Xamarin.iOS.dll.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: Не удалось найти сборку "\*«, на которую указывает»\*".

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: Не может ссылаться на «monotouch.dll» и «Xamarin.iOS.dll».

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: Отсутствует ссылка на monotouch.dll или Xamarin.iOS.dll найден. Ссылку на monotouch.dll будет добавлен.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: Сборщик мусора Боэм в настоящее время не поддерживается при ссылке на «Xamarin.iOS.dll». Вместо этого было выбрано SGen сборщик мусора.

Только сборщик мусора SGen поддерживается с единой проектами. Убедитесь, не mtouch дополнительные флаги, определяющие Боэм сборщиком мусора.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim поддерживается только с помощью Xcode 6.0 или более поздней версии.

Установите более новую версию Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--расширение поддерживается только при использовании iOS SDK версии 8.0 (или более поздней версии).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: Цели минимальное развертывания для приложений единой 5.1.1, текущей цели развертывания "\*". Выберите целевой объект более новые развертывания в параметры приложения iOS проекта.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: \*.framework поддерживается только в том случае, если целевой объект развертывания версии 8.0 или более поздней версии. \* функции могут работать неправильно.

Указанный framework не поддерживается в целевой объект развертывания ссылается на версию iOS. Цель развертывания обновления до более новой версии iOS либо удалите использовании платформы, указанной в приложении.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin.iOS \* требуется Xcode 5.0 или более поздней версии. Текущая версия Xcode (в \*) — \*.

Установите новую Xcode.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: Команда не указана.

Никаких действий не указан для mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: Не удалось канонизировать пути "\*": \*

Это внутренняя ошибка. Если вы видите эту ошибку, проверьте файл ошибку [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: Путь Xcode "\*" не существует.

Путь Xcode, передаваемый с помощью `--sdkroot` не существует. Укажите допустимый путь.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: Не удается найти Xcode в расположении по умолчанию (/ Applications/Xcode.app). Установите Xcode или передать пользовательский путь, используя--sdkroot <path>.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: Не удается определить путь Xcode.app от корня пакета sdk "\*". Укажите полный путь к пакету Xcode.app.

Путь, передаваемый с помощью `--sdkroot` не соответствует допустимому приложению Xcode. Укажите путь к приложению Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Xcode.app "\*" является недопустимым (файл "\*" не существует).

Путь, передаваемый с помощью `--sdkroot` не соответствует допустимому приложению Xcode. Укажите путь к приложению Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: Не удалось найти выбранный Xcode в системе: \*

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: Не удалось найти выбранный Xcode в системе. «xcode выберите--печати путь» вернул "\*", однако этот каталог не существует.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Не Xcode.app (с использованием--sdkroot), с помощью системы Xcode, сообщаемые «xcode выберите--путь печати»: \*

Это информационное предупреждение, объясняющий Xcode будет использовать, так как ничего не было указано.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Нет Xcode.app указано (используя--sdkroot или «xcode выберите--печати путь»), вместо него используется значение по умолчанию Xcode: \*

Это информационное предупреждение, объясняющий Xcode будет использовать, так как ничего не было указано.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: Не удается найти исполняемый файл в расширении \* (CFBundleExecutable запись в своем файле Info.plist.)

Каждый Info.plist должен иметь исполняемый файл (с помощью CFBundleExecutable входа), однако запись должны создаваться автоматически во время построения.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin.iOS поддерживает только внедренные платформы с единой проектов.

Xamarin.iOS поддерживает только внедренные платформы при использовании API единой; Выполните обновление проекта для использования в единой API.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin.iOS поддерживает только внедренные платформы при цель развертывания по крайней мере 8.0 (текущее значение целевого развертывания: \* внедренных платформ: \*)

Xamarin.iOS поддерживает только внедренные платформы после цели развертывания по крайней мере 8.0 (так как в более ранних версиях iOS не поддерживает внедренные платформы).

Обновите цель развертывания в файле Info.plist проекта до версии 8.0 или более поздней версии.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Недопустимый сборки регистратора сборки: \*

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Недопустимый регистратора: \*

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Недопустимое значение для требуемой версии .NET framework: \*.

Недопустимый целевой платформы был передан с помощью аргумента--целевой платформы. Укажите допустимый целевой платформы.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Недопустимая целевая платформа: \*. Допустимые целевые платформы являются: \*.

Недопустимый целевой платформы был передан с помощью аргумента--целевой платформы. Укажите допустимый целевой платформы.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Неизвестная платформа: \*. Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в http://bugzilla.xamarin.com с тестовым случаем.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: Расширения не поддерживаются для платформы "\*".

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin.iOS \* не поддерживает развертывание конечного объекта \* (минимальное значение — \*). Выберите целевой объект более новые развертывания в файле Info.plist проекта.

Цель развертывания минимальное задается в сообщении об ошибке; Выберите целевой объект более новые развертывания в файле Info.plist проекта.

Если цель развертывания обновления невозможно, используйте более старой версии Xamarin.iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin.iOS \* не поддерживает минимальное развертывания целевым объектом \* (максимальное значение — \*). Выберите целевой объект более старые развертывания в файле Info.plist проекта или обновление до более новой версии Xamarin.iOS.

Xamarin.iOS не поддерживает установку на более позднюю версию, чем версия данной конкретной версии Xamarin.iOS был построен для цели минимальное развертывания.

Выберите целевой объект более старые минимальное развертывания в файле Info.plist проекта или обновление до более новой версии Xamarin.iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Недопустимая архитектура "\*" для \* проектов. Допустимые архитектуры: \*

Указан недопустимый архитектуры. Убедитесь, что архитектура является допустимым.

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075: Архитектура не указан (с помощью аргумента--abi). Архитектура является обязательным для \* проектов.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076: WatchOS проектов должен быть расширений.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077: Добавочные построения включены с помощью развертывания целевой < 8.0 (в настоящее время \*). Это не поддерживается (полученное приложение не будет запускаться на iOS 9), поэтому цель развертывания будет присвоено 8.0.

Это предупреждение о том, что целевой объект развертывания было установлено до 8.0 для этой сборки, чтобы работы добавочных сборок должным образом.

Добавочные построения поддерживаются только в том случае, если цель развертывания по крайней мере 8.0 (так как в противном случае конечного приложения не запускаются на iOS 9).

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078: Рекомендуется Xcode версию для Xamarin.iOS \* — Xcode \* или более поздней версии. Текущая версия Xcode (в \*) — \*.

Это предупреждение о том, что текущая версия Xcode не рекомендуется версию для этой версии Xamarin.iOS Xcode.

Обновите Xcode для обеспечения оптимальной работы.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: Отключение NewRefCount,--new-refcount:false, является устаревшим.

Это предупреждение о том, что запрос на отключение нового счетчика ссылок (--новый - refcount:false) было пропущено.

Новая функция refcount теперь обязателен для всех проектов, и поэтому он не можно отключить больше.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: Аргумент командной строки — отчет о сбоях загрузки также требует--загрузки-аварийного завершения отчет в.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--enable repl) поддерживается только в том случае, если не используется компоновка (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Bitcode только для ассемблерного кода не поддерживается в watchOS. Используйте любой--bitcode: маркер или--bitcode: полный.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode не поддерживается в симуляторе. Не передавайте--bitcode при построении для симулятора.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Отсутствует ссылка на ' \*' был найден. Он будет добавлен автоматически.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Целевой платформы (--целевой платформы) должен быть указан при создании решения для TVOS или WatchOS.

Это обычно указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Добавочные построения (--fastdev) не поддерживается с Боэм GC. Добавочные построения будут отключены.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: Сборщик Мусора должен быть в режиме совместной для watchOS приложений. Удалите аргумент mtouch coop: false.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: Параметр "\*«не может принимать значение»\*" при включении режима совместной сборщику мусора.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Эта версия Xamarin.iOS требует \* пакет SDK (поставляется с Xcode \*). Либо обновите Xcode обязательные файлы заголовков или задаваемого поведения управляемых компоновщика связи Framework SDK только (чтобы Старайтесь избегать новых API).

Xamarin.iOS требует файлы заголовков, из пакета SDK версии, указанной в сообщении об ошибке для построения приложения. Для устранения этой ошибки рекомендуется обновить Xcode, чтобы получить необходимые SDK, это будет включать все необходимые файлы заголовков. Если установлено несколько версий Xcode установлен, или хотите использовать Xcode в нестандартное расположение, не забудьте установить правильное расположение Xcode в настройках вашей интегрированной среды разработки.

Вероятность, альтернативное решение: чтобы включить управляемые компоновщика. Это приведет к удалению неиспользуемых API включая, в большинстве случаев новый API, где находятся файлы заголовка, отсутствует (или является неполным). Однако это не будет работать, если проект использует API, который был введен в новой SDK, отличной от вашей Xcode предоставляет.

Последние straw решением было бы использовать более старую версию Xamarin.iOS, версии, поддерживающей SDK проекта требуется.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: Не удалось найти «mlaunch».

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: Aot файлов не может быть скопирован в целевой каталог {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: Отсутствует ссылка на Xamarin.iOS.dll найден.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Внутренняя ошибка \*. Отправьте отчет об ошибках с тестовым случаем (http://bugzilla.xamarin.com).

Это сообщение об ошибке появляется в том случае, когда проверка внутренней согласованности в Xamarin.iOS завершается неудачно.

Это указывает на ошибку в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Недопустимая сборка собран: "\*". Отправьте отчет об ошибках с тестовым случаем (http://bugzilla.xamarin.com).

Это сообщение об ошибке появляется в том случае, когда проверка внутренней согласованности в Xamarin.iOS завершается неудачно.

Это значение всегда равно ошибки в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с тестовым случаем.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: Сборка "\*" указан несколько раз в--аргументы целевой объект построения сборки.

Сборки, упомянутые в сообщении об ошибке указан несколько раз в--аргументы целевой объект построения сборки. Убедитесь, что каждая сборка упоминается только один раз.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Сборок\*«и»\*"имеют одинаковое имя целевого ("\*"), но разные целевые объекты ("\*"и" \* ").

Сборки, упомянутые в сообщении об ошибке имеют конфликтующие целевых объектов построения.

Пример:

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

В этом примере пытается создать динамическую библиотеку и платформу с разных производителей (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: Статический объект "\*" содержит более одной сборки ("\*"), но каждого статического объекта должен соответствовать только одна сборка.

Упомянутые в сообщении об ошибке сборки компилируются в один статический объект. Это не допускается, каждая сборка должна быть откомпилирована в другой статический объект.

Пример:

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

В этом примере пытается построить статический объект (`MyBinary`) состоит из двух сборок (`Assembly1.dll` и `Assembly2.dll`), который не является допустимым.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: Нет целевых объектов построения сборки был указан для "\*".

При указании сборки построения целевого с помощью `--assembly-build-target`, каждая сборка в приложении должен иметь назначенный целевой объект сборки.

Эта ошибка возникает в том случае, если сборки, упомянутые в сообщении об ошибке не имеет назначенный быть собран сборки.

См. в документации `--assembly-build-target` для получения дополнительных сведений.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Имя целевой сборки в сборку "\*" является недопустимым: символ "\*" не допускается.

Имя целевой сборки сборки должно быть допустимым именем файла.

Например эти значения приведет к возникновению этой ошибки:

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

Поскольку `my/path.o` не является допустимым именем файла, из-за символом разделителя каталогов.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Сборок\*"имеют разные пользовательские оптимизации LLVM (\*), что не допускается, если все они компилируются в единый двоичный файл.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: Сборка собран "\*" не соответствует любой сборки.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: Сборка "{0}" была загружена из путь, отличный по указанному пути (указанный путь: {1}, фактический путь: {2}).

Это предупреждение о том, что сборки ссылается приложение была загружена из другого места, чем запрошено.

Это может означать, что приложение ссылается на несколько сборок с тем же именем, но из различных мест, которые могут привести к непредвиденным результатам, (только первая сборка будет использоваться).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Добавочные построения были отключены, поскольку эта версия Xamarin.iOS не поддерживает добавочные построения в проектах, содержащих привязки сторонних библиотек и которая компилируется в bitcode.

Добавочные построения были отключены, поскольку эта версия Xamarin.iOS не поддерживает добавочные построения в проектах, содержащих привязки сторонних библиотек и который компилируется в bitcode (проекты tvOS watchOS).

Не требуется никаких действий со стороны пользователя, данное сообщение является исключительно для информационных целей.

Дополнительные сведения см. номер ошибки[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Это предупреждение больше не отображается.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode был включен, так как эта версия Xamarin.iOS не поддерживает построение watchOS проектов с помощью LLVM без включения bitcode.

Bitcode был включен автоматически, так как эта версия Xamarin.iOS не поддерживает построение watchOS проектов с помощью LLVM без включения bitcode.

Не требуется никаких действий со стороны пользователя, данное сообщение является исключительно для информационных целей.

Дополнительные сведения см. номер ошибки[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Совместное использование собственного кода была отключена, поскольку \*

Существует несколько причин, по которым можно отключить для совместного использования кода:

* поскольку целевой объект развертывания приложения контейнера является более ранней, чем iOS версии 8.0 (он имеет \*)).

Машинный код для управления доступом требуется iOS 8.0 так как машинный код для управления доступом реализуется с помощью платформы пользователя, которая впервые появилась в iOS 8.0.

* так как приложение контейнера включает сборки I18N (\*).

Машинный код для управления доступом в настоящее время не поддерживается, если приложение контейнера включает I18N сборки.

* Поскольку приложение контейнера имеет пользовательский XML-файл определения для управляемых компоновщика (\*).

Машинный код для управления доступом требуется не поддерживается для проектов, использующих пользовательские XML-файлы определений для управляемых компоновщика.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Машинный код для управления доступом был отключен для расширения "\*" из-за \*.

* Параметры bitcode различием между приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом требуется соответствие параметров bitcode между проектами, которые совместно использовать код.

* Поскольку сборки путь построения параметры отличаются друг от друга приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом требуется сборки путь построения параметры ничем не отличаются от тех проектах, совместно использовать код.

  Это состояние может возникнуть, если Инкрементные построения не либо включены или отключены во всех проектах.

* Поскольку сборки I18N отличаются друг от друга приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом в настоящее время не поддерживается для расширений, которые включают I18N сборки.

* так как аргументы для компилятора AOT отличаются друг от друга приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом требует, что аргументы для компилятора AOT отличается от проектов, совместно использующих код.

* Поскольку другие аргументы для компилятора AOT отличаются друг от друга приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом требует, что аргументы для компилятора AOT отличается от проектов, совместно использующих код.

  Такая ситуация возникает, если «Выполнять все операции в 32-разрядное число с плавающей запятой как 64-разрядное число с плавающей запятой» различается в разных проектах.

* так как не включено или отключено в приложении контейнера LLVM (\*) и расширение (\*).

  Машинный код для управления доступом требуется LLVM является либо включен или отключен для всех проектов, совместно использовать код.

* Поскольку параметры компоновщика управляемого отличаются друг от друга приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом требует, что параметры компоновщика управляемого идентичны для всех проектов, совместно использовать код.

* из-за пропущенных сборок для управляемых компоновщика отличаются друг от друга приложения контейнера (\*) и расширение (\*).

  Машинный код для управления доступом требует, что параметры компоновщика управляемого идентичны для всех проектов, совместно использовать код.

* так как у расширения есть пользовательский XML-файл определения для управляемых компоновщика (\*).

  Машинный код для управления доступом требуется не поддерживается для проектов, использующих пользовательские XML-файлы определений для управляемых компоновщика.

* так как приложение контейнера не была выполнена сборка для ABI \* (пока модуль выполняется построение для этого интерфейса ABI).

  Машинный код для управления доступом требуется, что сборка выполняется для всех архитектур, любой модуль приложения создает для приложения контейнера.

  Например: это состояние возникает, когда модуль создает для ARM64 + ARMv7, но приложение контейнера выполняется построение только для ARM64.

* Поскольку выполняется построение приложения контейнера для ABI \*, который несовместим с ABI расширения (\*).

  Машинный код для управления доступом требуется, что все проекты сборки для точного же API.

  Например: это состояние возникает, когда модуль создает ARMv7 + llvm + thumb2, но приложение контейнера выполняется построение только для ARMv7 + llvm.

* поскольку контейнер приложение ссылается на сборку "\*«с»\*", а модуль ссылается на другую версию из "\*".

  Машинный код для управления доступом требуется, что все проекты, которые совместно использовать код использовать те же версии для всех сборок.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: Рекомендуется ссылаться на динамические символов, с помощью кода (--режим для обозначения динамической = код) при включении bitcode.

Xamarin.iOS проекты будут часто ссылаются на собственный символы динамически, это означает, что собственного компоновщика может удалить такие машинных символов в процессе собственного связывания, так как собственный компоновщик не отображается, что эти символы используются.

Обычно Xamarin.iOS попросит собственные компоновщику сохранять такие символы (с помощью `-u symbol` флаг компоновщика), но если компиляции для bitcode собственного компоновщик не допускает `-u` флаг.

Xamarin.iOS внедрила имеется и альтернативное решение: мы создаем лишние машинного кода, который ссылается на эти символы, и таким образом собственного компоновщика будут видеть, что эти символы используются. Это делается автоматически при компиляции в bitcode.

Если `--dynamic-symbol-mode=linker` передается mtouch, этом альтернативное решение будет отключено, а Xamarin.iOS попытается передать `-u` собственного компоновщику. Это приведет к скорее ошибках компоновщика в машинном коде.

Решением является удаление `--dynamic-symbol-mode=linker` аргумент из дополнительных mtouch аргументы в параметрах сборки проекта.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Недопустимая архитектура: {arch}. 32-разрядных архитектур не поддерживаются при цель развертывания 11 или более поздней версии. Убедитесь, что проект не построен для 32-разрядной архитектуры.

iOS 11 не содержит поддержки для 32-разрядных приложений, поэтому не поддерживается для создания приложений для 32-разрядного приложения, когда цель развертывания iOS 11 или более поздней версии.

Изменить целевую архитектуру в параметрах сборки проекта iOS для arm64 либо изменить цель развертывания в файле Info.plist проекта на более раннюю версию iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: Не удается запустить 32-разрядное приложение в имитаторе, который поддерживает только 64-разрядной.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Aot файлов не удалось найти в предполагаемом каталоге {msymdir}.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: Исполняемая сборка \* не ссылается на \*.

Ни одна ссылка не удалось найти сборку платформы (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) в исполняемую сборку.

Обычно это происходит, где нет кода в проект исполняемого файла, использующего ничего из сборки платформы; для экземпляра пустой метод Main (и никакой другой код) будет показывать ошибки:

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

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: Не удалось задать текущего языка {lang} (согласно LANG = {LANG}): {исключение}

Это предупреждение, о том, что не удалось задать текущего языка с языком в сообщении об ошибке.

Текущий язык по умолчанию языка системы.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: Сборки путь построения аргумента командной строки учитывается в симуляторе.

Никаких действий не требуется, это сообщение является исключительно для информационных целей.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Добавочные построения были отключены, поскольку добавочные построения не поддерживаются в симуляторе.

Никаких действий не требуется, это сообщение является исключительно для информационных целей.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Добавочные построения были отключены, поскольку эта версия Xamarin.iOS не поддерживает добавочные построения в проекты, которые включают привязки более чем одному сторонних библиотек.

Добавочные построения отключены автоматически, так как эта версия Xamarin.iOS не всегда построении проектов с помощью нескольких библиотек сторонних привязки правильно.

Никаких действий не требуется, это сообщение является исключительно для информационных целей.

Дополнительные сведения см. номер ошибки[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: Не удалось касание файла "\*": \*

Произошла ошибка при касании файла (что позволяет убедиться, что частичного построения выполняются правильно).

Это предупреждение можно игнорировать скорее; в случае затруднений зарегистрировать ошибку (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) и будут исследованы.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Проект связанные сообщения об ошибках

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Установщик / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: Не удалось найти приложение в указанном каталоге

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: Не удалось создать символьных ссылок, были скопированы файлы

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: Не удалось удалить приложение "\*". Может потребоваться вручную завершить приложение.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: Не удалось получить список установленных приложений.

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: Не удалось удалить приложение "\*«на устройстве»\*": \*-может потребоваться вручную завершить приложение.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: Не удалось установить приложение "\*«на устройстве»\*": \*.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: Не удалось запустить приложение "\*«на устройстве»\*": \*. Приложение по-прежнему можно запустить вручную, нажав на нем.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: Не удается запустить имитатор

Эта ошибка возникает в том случае, если не удается запустить имитатор mtouch.   Это может произойти иногда устаревших или исчезновения симулятор процесс, выполняющийся уже существует.

Для завершения процессов постоянный симулятор можно использовать следующие команды в командной строке Unix:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: Не удалось скопировать сборку "\*«to»\*": \*

Это известная проблема в определенных версиях Xamarin.iOS.

В этом случае вам, попробуйте следующее решение.

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Тем не менее, поскольку эта проблема устранена в последней версии Xamarin.iOS, проверьте файл новую ошибку в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с полной версией информации и выходные данные журнала построения.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: Не удалось загрузить сборку "\*": \*

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: Не удалось добавить отсутствующий файл ресурсов: "\*"

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: Не удалось вывести список приложений на устройстве "\*": \*

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Слежения ошибка: нет файлов для сравнения. Отправьте отчет об ошибках в http://bugzilla.xamarin.com с тестовым случаем.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с caes теста.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: Не удалось повторно использовать кэшированная версия "\*": \*.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Не удалось создать исполняемый файл "\*": \*

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Не удалось создать исполняемый файл "\*": \*

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: Не удалось создать файл уведомления, так как каталог с тем же именем уже существует.

Удалите каталог `NOTICE` из проекта.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: Не удалось создать файл Обратите внимание: \*.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: Приложение сбой проверки подписи кода и не могут быть установлены на устройстве "\*". Проверьте сертификаты, профили, подготовки и объединить идентификаторы. Возможно, устройство не является частью выбранного профиля подготовки (ошибка: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: Приложения имеет права, не поддерживается для текущего профиля подготовки и не могут быть установлены на устройстве "\*". Для получения дополнительных сведений проверьте журнал устройства iOS (ошибка: 0xe8008016).

Это может произойти, если:

* Ваше приложение имеет права, текущий профиль подготовки не поддерживает.
  Возможные решения:
  - Укажите другой профиль подготовки, поддерживающий прав потребности вашего приложения.
  - Удаление прав, не поддерживается в текущем профиле подготовки.
* Устройство, которое вы пытаетесь развернуть, не включается в профиле подготовки, которую вы используете.
  Возможные решения:
  - Создание нового приложения из шаблона в Xcode, выберите того же профиля подготовки и развертывания на одном устройстве. Иногда Xcode можно автоматически обновлять профили подготовки с нового устройства (в других случаях Xcode запросит что делать).
  -Перейдите к центру разработки для iOS и обновить профиль подготовки с нового устройства, а затем Загрузите обновленный профиль подготовки на компьютер.

В большинстве случаев журнала устройства iOS будут выведены Дополнительные сведения об ошибке, которые могут помочь диагностировать проблему.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: Не удалось запустить приложение "\*«на устройстве»\*": \*

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: Не удалось скопировать файл "\*«to»\*": {2}

Не удается скопировать файл. Сообщение об ошибке из операции копирования содержит дополнительные сведения об ошибке.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: Не удалось скопировать каталог "\*«to»\*": {2}

Не удалось скопировать каталог. Сообщение об ошибке из операции копирования содержит дополнительные сведения об ошибке.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: Не удалось связаться с устройством, чтобы найти приложение "\*": \*

Произошла ошибка при попытке найти приложение на устройстве.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: Приложение не удалось проверить подпись на устройстве "\*". Убедитесь в том, что установлен и не истек срок действия профиля подготовки (ошибка: 0xe8008017).

Устройство отклонил установку приложения, поскольку не удается проверить подпись.

Убедитесь, что установлен профиль подготовки и не истек.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: Не удалось перечислить отчеты о сбоях на устройстве \*.

Произошла ошибка при попытке получить список отчетов аварийного завершения на устройстве.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: Не удалось загрузить отчет об аварийном завершении \* с устройства \*.

Произошла ошибка при попытке загрузить отчеты об аварийном завершении с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: Для запуска приложений на устройствах с iOS нельзя использовать Xcode 7 + \* (Xcode 7 поддерживает только iOS 6 +).

Используется для запуска приложений на устройствах с версией iOS ниже 6.0 Xcode 7 + невозможна.

Используйте более старой версии Xcode или коснуться приложения вручную, чтобы запустить его.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: Спецификация недопустимые: "\*". Ожидаемый «ios», «watchos» или «все».

Переданный спецификации устройства с помощью--устройство является недопустимым. Допустимые значения: «ios», «watchos» или «все».

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: Не удалось найти приложение в указанном каталоге: \*

Путь к приложению, передаваемый--launchdev не существует. Укажите набор допустимых приложений.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: Запуск приложений на устройстве, с помощью идентификатора пакета является устаревшим. Передайте полный путь к пакету для запуска.

Рекомендуется передать путь в приложение для запуска на устройстве, а не идентификатор набора.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: Не удалось запустить приложение "\*«на устройстве»\*", так как устройство заблокировано. Разблокируйте устройство и повторите попытку.

Разблокируйте устройство и повторите попытку.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Этот исполняемый файл приложения может быть слишком большим (\* МБ) для выполнения на устройстве. Если был включен bitcode может потребоваться отключить ее для разработки, требуется только для отправки приложения в Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: Не удалось удалить приложение "\*«с устройства»\*": \*

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: Не может содержать разные версии framework «{name}»

Связать с разными версиями такая же платформа невозможна.

Обычно это происходит, когда расширение ссылается на другую версию платформы, чем приложение контейнера (возможно, с помощью сторонних привязки сборок).

После этого ошибка будет существовать несколько [MT1036](#MT1036) перечисления путей для каждой платформы различных ошибок.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Включенные из Framework «{name}»: {путь} (связано с предыдущей ошибкой)

Эта ошибка возникает только вместе с [MT1036](#MT1036). См. в разделе [MT1036](#MT1036) для получения дополнительной информации.

### <a name="mt11xx-debug-service"></a>MT11xx: Отладка службы

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: Не удалось запустить приложение

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: Не удалось присоединить к приложению (kill его): \*

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: Не удалось отсоединить

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: Не удалось отправить пакет: \*

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Тип неожиданный ответ

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: Не удалось получить список приложений на устройстве: Истекло время ожидания запроса.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Не удается запустить приложение: \*

Проверьте правильность блокировки устройства.

Если вы развертываете корпоративного приложения или с помощью свободного подготовительный профиль, может потребоваться разработчике (Это объясняется <a href="http://stackoverflow.com/a/30726375/183422">здесь</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: Не удалось найти инструменты разработчика для этого устройства XX (гг).

Требуется несколько операций из mtouch <tt>DeveloperDiskImage.dmg</tt> файл должен присутствовать.   Этот файл является частью Xcode и обычно располагается относительно пакета SDK, который используется для построения, в <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Эта ошибка может произойти либо у вас DeveloperDiskImage.dmg, которая соответствует устройству, вы подключились.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Не удается запустить приложение, так как устройство заблокировано. Разблокируйте устройство и повторите попытку.

Проверьте правильность блокировки устройства.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Не удается запустить приложение из-за ограничений безопасности операций ввода-вывода. Убедитесь, что разработчик является доверенным.

Если вы развертываете корпоративного приложения или с помощью свободного подготовительный профиль, может потребоваться разработчике (Это объясняется <a href="http://stackoverflow.com/a/30726375/183422">здесь</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: Приложение успешно запущен, но невозможно дождаться выхода по запросу, так как не удается обнаружить завершение приложения при запуске программы с помощью gdbserver приложению.

### <a name="mt12xx-simulator"></a>MT12xx: симулятор

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: Не удалось загрузить имитатор: \*

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Недопустимый симулятор конфигурации: \*

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Спецификация недопустимый симулятор: \*

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Спецификация недопустимый симулятор "\*": среда выполнения не указан.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Спецификация недопустимый симулятор "\*": не указан тип устройства.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: Не удалось найти среду выполнения имитатора "\*".

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: Не удалось найти тип устройства симулятор "\*".

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: Не удалось найти среду выполнения имитатора "\*".

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: Не удалось найти тип устройства симулятор "\*".

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Спецификация недопустимый симулятор: \*, Неизвестный ключ "\*"

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: Версия симулятора "\*«не поддерживает тип симулятор»\*"

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: Не удалось создать simulator, версия которой введите = \* и выполнения = \*.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Спецификация недопустимый имитатор для Xcode 4: \*

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Спецификация недопустимый имитатор для Xcode 5: \*

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: Указан недопустимый пакет SDK: \*

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: Не удалось найти симулятор UDID "\*".

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: Не удалось загрузить пакет приложений на "\*".

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Идентификатор пакета не найден в приложения в "\*".

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: Не удалось найти имитатор для "\*".

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: Не удалось найти последнюю версию среды выполнения имитатор для устройства "\*".

Это обычно указывает на проблему с Xcode.

Действия, которые можно попытаться устранить эту ошибку:

* Используйте симулятор раз в Xcode.
* Передайте явную версию пакета SDK, с помощью пакета sdk — <version>.
* Переустановите Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: Не удалось найти имитатор парной iPhone для симулятора WatchOS "\*".

При запуске WatchOS приложения в симуляторе WatchOS, должен быть соединен симулятор iOS также.

Посмотрите симуляторов может быть связан с iOS с помощью пользовательского интерфейса в Xcode устройств симуляторов (меню окна -> устройства).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: Собственная библиотека `*` (\*) было пропущено, так как он не соответствует текущей сборки architecture(s) (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: Не удалось извлечь собственной библиотеки "\*" из «+». Убедитесь, что собственная библиотека была правильно встроена в управляемую сборку (если сборка была построена с использованием привязки проекта, собственной библиотеки, которые должны быть включены в проект и его действие при построении должно быть «ObjcBindingNativeLibrary»).

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: Не удалось распаковать собственная платформа "\*«с»\*". Просмотрите журнал сборки Дополнительные сведения с помощью команды собственного «zip».

Не удалось распаковать указанного собственная платформа из библиотеки привязки.

Просмотрите журнал bulid Дополнительные сведения об этом сбое с помощью команды собственного «zip».

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: Внедренные платформа "\*" в \* недопустим: не содержит Info.plist.

Указанный внедренный framework не содержит Info.plist и поэтому не допустимым framework.

Убедитесь, что платформа является допустимым.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: Библиотека привязки "\*" содержит framework пользователя (\*), но пользователя встроенной платформы требуют iOS 8.0 (текущий цель развертывания \*). Задайте цели развертывания в файле Info.plist, чтобы по крайней мере 8.0.

Указанная привязка библиотека содержит внедренные framework, но Xamarin.iOS поддерживает только внедренные платформы IOS 8.0 или более поздней версии.

Укажите цель развертывания можно задать в файле Info.plist, чтобы по крайней мере 8.0, чтобы устранить эту ошибку (или не использовать внедренные платформы).

### <a name="mt14xx-crash-reports"></a>MT14xx: Отчеты о сбоях

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: Не удалось открыть службу отчетов аварийного завершения: AFCConnectionOpen возвращается \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: Не удалось закрыть аварийного завершения службы отчетов: AFCConnectionClose возвращается \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: Не удалось прочитать файл сведений для \*: AFCFileInfoOpen возвращается \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: Не удалось прочитать отчет о сбоях: AFCDirectoryOpen (\*) вернул: \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: Не удалось прочитать отчет о сбоях: AFCFileRefOpen (\*) вернул: \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: Не удалось прочитать отчет о сбоях: AFCFileRefRead (\*) вернул: \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: Не удается получить список отчетов о сбоях: AFCDirectoryOpen (\*) вернул: \*

Произошла ошибка при попытке доступа к отчеты о сбоях с устройства.

Аспекты, чтобы попытаться устранить эту проблему.

* Удалите приложение с устройства и повторите попытку.
* Отключить устройство и подключите его повторно.
* Перезагрузите устройство.
* Перезагрузите Mac.
* Синхронизация устройства с iTunes (при этом будут удалены все отчеты о сбоях с устройства).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: Не соответствие-O динамическую библиотеку (Неизвестный заголовок "0 x \*»): \*.

Произошла ошибка при обработке динамической библиотеки в вопросе.

Убедитесь, что динамическая библиотека является допустимой библиотекой динамической соответствие-O.

Формат библиотеки можно проверить с помощью `file` из терминала:

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: Не статической библиотеки (Неизвестный заголовок "\*"): \*.

Произошла ошибка при обработке статической библиотеки в вопросе.

Убедитесь, что статическая библиотека является допустимым соответствие-O статической библиотеки.

Формат библиотеки можно проверить с помощью `file` из терминала:

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: Не соответствие-O динамическую библиотеку (Неизвестный заголовок "0 x \*»): \*.

Произошла ошибка при обработке динамической библиотеки в вопросе.

Убедитесь, что динамическая библиотека является допустимой библиотекой динамической соответствие-O.

Формат библиотеки можно проверить с помощью `file` из терминала:

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Неизвестный формат fat записи позиции \* в \*.

Произошла ошибка при обработке fat в архив.

Убедитесь, что fat архив является допустимым.

Формат fat архива можно проверить с помощью `file` из терминала:

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: Файл типа \* не является файлом MachO (\*).

Произошла ошибка при обработке файла MachO в вопросе.

Убедитесь, что файл является допустимой библиотекой динамической соответствие-O.

Формат файла можно проверить с помощью `file` из терминала:

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Сообщения об ошибках компоновщика

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: Не удалось выполнить привязку сборок

Эта ошибка означает, что управляемого компоновщика произошла непредвиденная ошибка, например исключение и не удалось завершить или сохраните сборку обрабатывается. Дополнительные сведения о конкретной ошибки будет частью журнал сборки, например

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

Очень важно отчет об ошибке для таких проблем. В большинстве случаев решения этой проблемы может быть указано до публикации соответствующие исправления. Указанные выше сведения важно (вместе с тестовым случаем или binairy сборки) для устранения проблемы.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: Не удается разрешить ссылку: \*

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: Параметр "\*" будет игнорироваться, так как компоновка отключена

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: Файл определения компоновщика дополнительный "\*" не найден.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Определения "\*" не удалось выполнить синтаксический анализ.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: Не удалось загрузить библиотеку mscorlib.dll из: \*. Переустановите Xamarin.iOS.

Это обычно указывает на наличие проблемы с установкой, Xamarin.iOS. Попробуйте переустановить Xamarin.iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: Неизвестный HttpMessageHandler `*`. Допустимые значения: HttpClientHandler (по умолчанию), CFNetworkHandler или NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: Неизвестный TlsProvider `*`.  Допустимые значения: по умолчанию или appletls.

Значение, заданное `tls-provider=` не является допустимым поставщиком TLS (Безопасность на уровне транспорта).

`default` И `appletls` являются единственными допустимыми значениями и оба представляют один и тот же параметр, а также поддерживает SSL/TLS с помощью собственного API TLS Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: Недопустимый HttpMessageHandler `*` для watchOS. Единственным допустимым значением является NSUrlSessionHandler.

Это предупреждение возникает, если файл проекта указывает недопустимый HttpMessageHandler.

Предыдущие версии средств нашей предварительной версии, созданных по умолчанию недопустимое значение в файле проекта.

Чтобы устранить это предупреждение, откройте файл проекта в текстовом редакторе и удалите все узлы HttpMessageHandler из XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: Недопустимый TlsProvider `legacy` параметр. Единственным допустимым значением `appletls` будет использоваться.

`legacy` Поставщика, который был полностью управляемая SSLv3 / TLSv1 единственным поставщиком, не входит в состав Xamarin.iOS больше. Проекты, которые использовали этот старого поставщика и теперь построить с помощью более новой версии `appletls` один.

Чтобы устранить это предупреждение, откройте файл проекта в текстовом редакторе и удалить все ' MtouchTlsProvider'' узлов из XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: Не удалось обработать XML-описание.

Это означает, что имеется ошибка в [пользовательские XML-файл конфигурации компоновщика](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) вы указали, просмотрите файл.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: Сборка "\*" ссылка из двух разных мест: "\*" и "\*".

Упомянутые в сообщении об ошибке сборка загружается из нескольких мест. Убедитесь в том, что всегда используют ту же версию сборки.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: Не удалось загрузить корневой сборки "\*"

Не удалось загрузить корневой сборки. Убедитесь, что путь в сообщении об ошибке ссылается на существующий файл, и является допустимой сборкой .NET.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: Оптимизатор привязки сбой обработки `...`.

Что-либо неожиданное произошла при попытке оптимизации созданный код. Элемент, причиной проблемы является именованным в сообщении об ошибке. Чтобы устранить эту проблему, с именем сборки (или содержащий тип или метод с именем) будет необходимо указать в [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **дополнительных mtouch аргументы**).

Последняя цифра `x` будет:
* `0` для имени сборки;
* `1` для имени типа;
* `3` Имя метода;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: Удаление пользователей ресурсов не удалось выполнить обработку `...`.

Что-либо неожиданное произошла при попытке удаления пользователей ресурсов. Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо предоставить в [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

Ресурсы пользователя — это файлы, включенные в сборки (как ресурсы), которые требуется извлечь, во время построения, чтобы создать пакет приложения. В том числе следующее:

* `__monotouch_content_*` и `__monotouch_pages_*` ресурсов; и
* Собственные библиотеки, внедренных в сборку привязки;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: По умолчанию HttpMessageHandler setter сбой обработки `...`.

Что-либо неожиданное произошла при попытке установить значение по умолчанию `HttpMessageHandler` для приложения. Отправьте [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Метод удаления кода не удалось обработки `...`.

Что-либо неожиданное произошла при попытке удалить код из BCL доставки с приложением. Отправьте [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Sealer ошибка при обработке `...`.

Что-либо неожиданное произошла при попытке запечатайте типы или методы (окончательная) или devirtualizing некоторые методы. Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо предоставить в [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Метаданные редуктора сбой обработки `...`.

Что-либо неожиданное произошла при попытке уменьшить метаданные из приложения. Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо предоставить в [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: MarkNSObjects ошибка при обработке `...`.

При попытке пометить неожиданного произошла `NSObject` подклассов из приложения. Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо предоставить в [отчет ошибок](http://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Встраивания ошибка при обработке `...`.

Непредвиденная произошла при попытке встроенного кода в приложении. Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему сборки необходимо будет предоставляться в [отчет ошибок](https://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Смарт-Сохранить преобразование перечисления не удалось обработки `...`.

Что-либо неожиданное произошла при попытке пометить методы преобразования для смарт-перечислений из приложения. Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему сборки необходимо будет предоставляться в [отчет ошибок](https://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: Не удается разрешить ссылку "\*«, на которую указывает ссылка из метода»\*" в "\*".

Недопустимая ссылка на сборку была обнаружена при обработке метода, упомянутые в сообщении об ошибке.

Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо предоставить в [отчет ошибок](https://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Ошибка при обработке метода "\*«в сборке»\*": \*

Что-либо неожиданное произошла при попытке пометить метод, который упоминается в сообщении об ошибке.

Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему, сборка будет необходимо предоставить в [отчет ошибок](https://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Ошибка при обработке сборки "\*": \*

Произошла непредвиденная ошибка при обработке сборки.

Сборка, причиной проблемы является имя в сообщении об ошибке. Чтобы устранить эту проблему сборки необходимо будет предоставляться в [отчет ошибок](https://bugzilla.xamarin.com) вместе с полная сборка журнала с помощью детализации включен (т. е. `-v -v -v -v` в **mtouch дополнительные аргументы**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Не удалось связать сборки "{0}" как смешанный режим.

Сборки смешанного режима не могут обрабатываться компоновщиком.

В разделе https://msdn.microsoft.com/library/x0w2664k.aspx Дополнительные сведения о смешанных сборках.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Сообщения об ошибках AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Не удалось AOT сборки "\*"

Это обычно указывает на ошибку в компиляторе AOT. Проверьте файл ошибку [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) с проектом, который может использоваться для воспроизведения ошибки.

Иногда это можно обойти, отключив добавочные построения в проекте iOS построение-параметр (но она по-прежнему ошибку, поэтому отправьте сообщение все равно).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002: Ограничение AOT: метод "\*" должен быть статическим, так как он объявлен с [MonoPInvokeCallback]. В разделе [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Это сообщение об ошибке поступает из компилятора AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: Конфликтующие--параметры отладки и--llvm. Отладка программной отключена.

Отладка не поддерживается, если включен LLVM. Если необходимо выполнить отладку приложения, сначала отключите LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Не удалось AOT сборки ' \*', так как он не существует.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: Зависимость "\*«из сборки»\*" не найден. Проверьте ссылки на проект.

Обычно это происходит, если сборка ссылается на другую версию сборки платформы (обычно .NET 4 версия mscorlib.dll).

Это не поддерживается и может не построения или выполняться правильно (сборка может использовать API .NET 4 версии mscorlib.dll, версия Xamarin.iOS, у которого нет).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: Не удается вычислить схему зависимости для проекта. Это приведет к увеличивается время построения, поскольку Xamarin.iOS не может правильно определить то, что необходимо перестроить (и что не нужно перестроить). Проверьте предыдущие предупреждения, для получения дополнительных сведений.

 сборка или выполняться правильно (сборка может использовать API .NET 4 версии mscorlib.dll, версия Xamarin.iOS, у которого нет).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: Файлы сведений отладки (\*.mdb) не будет загружен при включении llvm.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: Поддержка Bitcode требует использования серверной части LLVM AOT (--llvm)

Поддержка Bitcode требует использования серверной части LLVM AOT (--llvm).

Отключите поддержку Bitcode или включить LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Сообщения об ошибках код создания

### <a name="mt40xx-main"></a>MT40xx: Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: Основного шаблона не может быть развернут для `*`.

Произошла ошибка при создании main.m. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: Ошибка при компиляции созданного кода для методов P/Invoke. Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Сбой при компиляции созданного кода для методов P/Invoke. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx: регистратора

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: Регистратор не удалось создать подпись для типа `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, каким образом следует маршалировать из цели-C.

Если вы считаете, Xamarin.iOS должен поддерживать тип в вопросе, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: Регистратора найден недопустимый тип `*` в сигнатуре метода `*`. Взамен рекомендуется использовать `*`.

Это в настоящее время происходит только с одним типом: System.DateTime. Вместо этого используйте эквивалентные Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: Регистратора найден недопустимый тип `*` в сигнатуре метода `*`: тип реализует INativeObject, но не имеет конструктора, который принимает два (IntPtr bool) аргументов

Это происходит, когда обнаруживают тип в сигнатуре с параметрами, упомянутых в регистратор. Регистратор может потребоваться для создания новых экземпляров типа, и в этом случае необходимо конструктор (IntPtr, bool) подпись - первый аргумент (IntPtr) задает управляемого дескриптора, а для второго, если вызывающий объект передает владение собственный Дескриптор (если это значение равно false, «сохранить» будет вызван для объекта).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: Регистратор не может маршалировать возвращаемое значение для типа `*` в сигнатуре метода `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, каким образом следует маршалировать из цели-C.

Если вы считаете, Xamarin.iOS должен поддерживать тип в вопросе, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: Регистратор не может маршалировать параметр типа `*` в сигнатуре метода `*`.

Если вы считаете, Xamarin.iOS должен поддерживать тип в вопросе, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: Регистратор не может маршалировать возвращаемое значение для структуры `*` в сигнатуре метода `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, каким образом следует маршалировать из цели-C.

Если вы считаете, Xamarin.iOS должен поддерживать тип в вопросе, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: Регистратор не может маршалировать параметр типа `*` в сигнатуре метода `+`.

Тип был найден в экспортированных API, который среда выполнения не знает, каким образом следует маршалировать из цели-C.

Если вы считаете, Xamarin.iOS должен поддерживать тип в вопросе, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: Регистратор не удается получить тип ObjectiveC для управляемого типа `*`.

Тип был найден в экспортированных API, который среда выполнения не знает, каким образом следует маршалировать из цели-C.

Если вы считаете, Xamarin.iOS должен поддерживать тип в вопросе, отправьте запрос на расширение в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: Не удалось скомпилировать код созданный регистратора. Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Сбой при компиляции созданного кода для регистратора. Журнал сборки будет содержать выходные данные собственного компилятора, объясняющий, почему не компиляции кода.

Это значение всегда равно ошибки в Xamarin.iOS; Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com) с тестовым случаем или проекта.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: Регистратор не может маршалировать выходной параметр типа `*` в сигнатуре метода `*`.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: Регистратор не удалось создать подпись для типа `*` в методе `*`.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112: Регистратора найден недопустимый тип `*`. Регистрация универсальных типов в Objective-C не поддерживается и может привести к непредвиденному поведению и сбоям (для обратной совместимости с более ранними версиями Xamarin.iOS можно не учитывать эту ошибку путем передачи `--unsupported--enable-generics-in-registrar` как дополнительные mtouch Аргумент, на странице Параметры построения проекта iOS. В разделе [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) для получения дополнительной информации).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: Регистратора найти универсальный метод: "\*.\*". Экспорт универсальные методы не поддерживается и может привести к непредвиденному поведению и сбоям.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Непредвиденная ошибка при регистрации для метода "\*.\*"-отправьте отчет об ошибках в http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: Не удалось зарегистрировать сборку "\*": \*

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: Найти регистратора Несовпадение подписей в методе "*.*"-селектор указывает метод принимает \* параметров, а управляемый метод \* параметров.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: Не удается зарегистрировать два управляемого типа ("\*«и»\*") с тем же именем native ("\*").

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: Не удалось зарегистрировать селектор "\*«элемента»\*. \*", так как селектор уже зарегистрирован в другой элемент.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: Регистратора найден неизвестный тип поля "\*«в поле»\*. \*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: Невозможно использовать GCC / G ++ для компиляции созданного кода из статических регистратора, при использовании framework учетных записей (файлы заголовков, компании Apple, используемые во время компиляции требует Clang). Либо используйте Clang (--компилятора: clang) или динамической регистратора (--регистратора: динамические).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: Нельзя использовать Clang компилятор, входящий в *.* Пакет SDK для компиляции созданного кода из статических регистратора, когда не ASCII имена типов ("\*") присутствуют в приложении. Либо используйте GCC / G ++ (--компилятора: gcc | g ++), динамической регистрации (--регистратора: динамические) или более новой SDK.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: Тип параметра с переменным числом аргументов в функции с переменным числом аргументов "\*" должен быть System.IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: Недопустимый \* на "\*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: Регистратора найден недопустимый тип "\*«в сигнатуре метода»\*": интерфейс должен иметь атрибут протокола указанием типа оболочки.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: Не удается зарегистрировать два управляемых протокола ("\*«и»\*") с тем же именем native ("\*").

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: Не удается зарегистрировать более одного метода интерфейса для метода "\*" (который реализует "\*").

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: Регистратора найден недопустимый универсальный тип параметра "\*«в методе»\*". Универсальный параметр должен иметь ограничение «NSObject».

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: Регистратора найден недопустимый универсальный тип возвращаемого значения "\*«в методе»\*". Обобщенный тип возвращаемого значения должен иметь ограничение «NSObject».

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: Регистратор не удается экспортировать статические методы в универсальных классах ("\*").

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: Регистратор не удается экспортировать статических свойств в универсальных классах ("\*.\*").

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: Регистратора найден недопустимый универсальный тип возвращаемого значения "\*«в свойстве»\*". Тип возвращаемого значения должен иметь ограничение «NSObject».

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: Не удается создать экземпляр типа "\*" из Objective-C, так как тип является универсальным. [Исключение времени выполнения]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: Приложение использует "\*" платформу, которая не включена в пакет SDK, которые вы используете для создания приложений iOS (эта платформа была введена в iOS \*, а вы создаете с iOS \* пакет SDK.) Выберите новую SDK в приложения iOS параметры сборки.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: Элемент "\*.\*" имеет атрибут экспорта, который не указывает селектора. Селектор не требуется.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: Регистратор не может маршалировать тип параметра "\*«для параметра»\*«в методе»\*. \*"

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: Регистратор не может маршалировать тип свойства "\*«для свойства»\*. \*".

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: Регистратор не может маршалировать тип свойства "\*«для свойства»\*. \*". Свойства с атрибутом [подключение] должны иметь тип свойства NSObject (или подкласс NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: Найти регистратора Несовпадение подписей в методе "*.*"-селектор указывает метод с переменным числом аргументов принимает \* параметров, а управляемый метод \* параметров.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: Не удается зарегистрировать селектор "\*«в элементе»\*", так как Xamarin.iOS неявно регистрирует этот селектор.

Это происходит, когда создание подкласса типа framework, а попытка реализовать «сохранить», «выпуск» или «dealloc» метод:

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

Это, однако можно переопределить эти методы, если класс не является первой подкласс платформы введите:

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

В этом случае будут переопределены Xamarin.iOS `retain`, `release` и `dealloc` на `MyNSObject` класса, и не было конфликтов.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: Не удалось зарегистрировать тип "\*".

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: Класс ObjectiveC "\*" не может быть зарегистрировано, он не подключен к производными класса все известные ObjectiveC (включая NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: Не удается зарегистрировать метод ' \*', так как он не имеет связанного trampoline. Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Недопустимое перечислимое "\*": перечисления атрибутом [машинного кода] должен иметь базовый тип перечисления «long» или «ulong».

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: Имя параметра регистратора атрибут в классе\*"("\*") содержит недопустимый символ:"\*"(\*).

Имя класса Objectice C не может содержать пробелы, это означает, что `Register` на соответствующий управляемый класс не может иметь `Name` либо параметр не может содержать пробелы.

Убедитесь, что `Register` атрибут для управляемого класса, упомянутые в сообщении об ошибке не содержит пробелов.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Обнаружено протокола, наследующим JSExport протокола при использовании динамического регистратора. Не удается экспортировать протоколы JavaScriptCore динамически; необходимо использовать статический регистратора (Добавление "--регистратора: static к аргументам дополнительных mtouch в проекте iOS параметры сборки для выбора регистратора статический).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: Регистратора найти универсальный протокол: "\*". Экспорт универсального протоколов не поддерживается.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: Не удается зарегистрировать метод "\*.\*" так как тип первого параметра ("\*") не соответствует типу категории ("\*").

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: Не удается зарегистрировать тип "\*" так как свойство типа ("\*") в его категория атрибута не является производным от NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: Не удается зарегистрировать тип "\*", так как не задано свойство Type в атрибуте его категории.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: Не удается зарегистрировать тип "\*" как категория, так как он реализует INativeObject или подклассы NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: Не удается зарегистрировать тип "\*" как категория, так как он является универсальным.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: Не удается зарегистрировать метод "\*" как метод категории, так как он является универсальным.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: Не удается зарегистрировать метод "\*«с помощью селектора»\*" как метод категории на "\*", так как Objective-C уже имеет реализацию этот селектор.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: Не удается зарегистрировать две категории ("\*«и»\*") с тем же именем native ("\*").

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: Не удается зарегистрировать метод категории "\*", так как по крайней мере один параметр обязателен (и его тип должен соответствовать типу категории "\*")

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: Не удается зарегистрировать конструктор \* в категории \* поскольку конструкторы в категории не поддерживаются.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: Не удается зарегистрировать метод "\*" как метод категории так, как категория методы должны быть статическими.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Недопустимый символ "\*" (\*) найден в селекторе "\*«for»\*".

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: Регистратора найден неподдерживаемый структуры "\*": все поля структуры должны иметь структуры (поле "\*«с типом»{2}" не является структурой).

Регистратор найти структуру с поля не поддерживается.

Все поля в структуре, который предоставляется Objective-C также должны быть структуры (не классы).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: Тип "\*" (используется в качестве \* {2}) недоступен в \*\* (появился в \* \*)\* выполните сборку с более новой \* пакет SDK (обычно выполняются с использованием самой последней версии Xcode.

Регистратор найти тип, который не входит в текущий пакет SDK.

Обновите Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Внутренняя ошибка в регистраторе (\*). Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: Не удается экспортировать свойство "\*" поскольку селектора "\*" — это ключевое слово C цель. Используйте другое имя.

Селектор для рассматриваемой свойства не является допустимым идентификатором C цель.

Используйте допустимый идентификатор Objective-C как селекторов.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: Регистратор не удалось найти тип «System.Void» в любом из указанных сборок.

Скорее всего, это ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: Не удается зарегистрировать метод "\*", так как подпись содержит тип (\*), не является ссылочным.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: Не удается зарегистрировать метод "\*", так как подпись содержит универсальный тип (\*) с типом универсального аргумента, который не подкласса NSObject (\*).

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: Не удается зарегистрировать тип "{управляемых\_имя}" из-за его имя Objective-C "{экспортировать\_имя}" — это ключевое слово Objective-C. Используйте другое имя.

Имя Objective-C для рассматриваемого типа не является допустимым идентификатором C к цели деятельности организации.

Используйте допустимый идентификатор Objective-C.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: Не удалось создать оболочку P/Invoke для {метода}: {message}

Не удалось создать функцию-оболочку P/Invoke для упомянутых Xamarin.iOS.
Проверьте сообщение отчета об ошибке на основную причину.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: Регистратора невозможно преобразовать из «{управляемый тип}» в «{собственный тип}» для возвращаемого значения в метод {}.

См. в описании ошибки <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: Недопустимый атрибут BindAs на член {}: {type} BindAs тип отличается от типа свойства {type}.

Убедитесь, что типа в атрибуте BindAs соответствует тип члена, которому он присоединен.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: Регистратора невозможно преобразовать из «{собственный тип}» в «{управляемый тип}» для параметра «{имени}» в методе {метод}.

Регистратор не поддерживает преобразование между типами, упомянутых выше.

Это ошибка в Xamarin.iOS, если рассматриваемого API, предоставленные Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ] [ 1].

Если возникли это при разработке проекта привязки для собственной библиотеки, мы можем открыть, чтобы добавить поддержку нового сочетания типов. Если это так, отправьте запрос на расширение ([http://bugzilla.xamarin.com][2]) с тестового случая и мы оценить его.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: GCC и инструментов сообщения об ошибках

### <a name="mt51xx-compilation"></a>MT51xx: компиляция

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Отсутствует "\*" компилятора. Установите компонент «Средства командной строки» Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: Не удалось собрать файл "\*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: Не удалось скомпилировать файл "\*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: Не удалось найти ни "\*«и»\*" компилятора. Установите компонент «Средства командной строки» Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: Не удалось скомпилировать файлы "\*". Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx: связывание

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Собственный связывания не удалось. Просмотрите журнал сборки и флаги пользователя, предоставленный gcc: \*

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Собственный связывания не удалось. Просмотрите журнал сборки.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Машинный код связывание предупреждение: \*

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Машинный код Ошибка связывания: \*

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Собственный связывания не удалось неопределенный символ: \*. Убедитесь, что сослались все необходимые платформы и собственные библиотеки должным образом связаны в.

Это происходит, когда собственный компоновщику не удается найти символ, который упоминается в любом месте. Существует несколько причин, что это может происходить:

* Привязка независимых требует платформу, но привязка не задано в ее `[LinkWith]` атрибута. Решения.
  - Если вы являетесь автором привязки сторонних разработчиков или иметь доступ к источнику, измените свойство привязки `[LinkWith]` атрибут, чтобы включить платформу, он должен:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Если не удается изменить привязку сторонних разработчиков, вручную можно связать с требуемую платформу, передав `-gcc_flags '-framework SystemFramework'` для `mtouch` (это выполняется путем изменения mtouch дополнительные аргументы на странице Параметры построения проекта iOS. Помните, что это необходимо сделать для каждой конфигурации проекта).
* В некоторых случаях управляемый привязки состоит из нескольких библиотек неуправляемого кода, и все должны быть включены в привязках. Существует возможность могут иметь более одного собственной библиотеки в каждый проект привязки, и решением является просто добавьте необходимые собственные библиотеки в проект привязки.</li>
* Управляемый привязка-это машинных символов, которые не существуют в собственной библиотеки.
    Обычно это происходит, когда привязки выступает в течение некоторого времени, машинный код был изменен в течение этого времени, чтобы определенного собственного класса были удалены или переименованы во время привязки не были обновлены.
* P/Invoke ссылается на символ, не существует. Начиная с Xamarin.iOS 7.4 <a href="#MT5214">MT5214</a> ошибка для этого случая (MT5214 более подробные сведения).
* Привязка независимых / библиотека была построена с использованием C++, но привязка не задано в ее `[LinkWith]` атрибута. Это обычно является довольно легко распознавать, поскольку символы, символы искаженное C++ (типичный пример — `__ZNKSt9exception4whatEv`).
  - Если вы являетесь автором привязки сторонних разработчиков или иметь доступ к источнику, измените свойство привязки `[LinkWith]` атрибут, задаваемый `IsCxx` флаг:

            [LinkWith ("mylib.a", IsCxx = true)]

  - Если не удается изменить привязку независимых производителей или вручную связывание с библиотекой сторонних разработчиков, эквивалентный флаг задается путем передачи <code>-cxx</code> для mtouch (это можно сделать, изменяя mtouch дополнительные аргументы, на странице Параметры построения проекта iOS . Помните, что это необходимо сделать для каждой конфигурации проекта).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Собственный связывания не удалось неопределенный класс Objective-C: \*. Символ "\*" не удалось найти в любом библиотек и платформ, связанных с приложением.

Это происходит, когда собственный компоновщику не удается найти класс Objective-C, который упоминается в любом месте. Это может произойти по нескольким причинам: аналогичны [MT5210](#MT5210) и Кроме того:

* Привязка независимых привязан протокол Objective-C, но не аннотируйте его <code>[Protocol]</code> атрибута в его определении api. Решения.
  - Добавление недостающих `[Protocol]` атрибута:

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Собственный связывания не удалось повторяющихся символов: \*.

Это происходит, когда собственный компоновщик обнаруживает повторяющиеся символы между собственные библиотеки. За этой ошибкой могут существовать один или несколько [MT5213](#MT5213) ошибки, связанные с расположение для каждого вхождения символа. Возможные причины этой ошибки:

* Одна и та же библиотека собственного входит в два раза.
* Для определения символов, же произойти два различных собственных библиотек.
* Собственная библиотека не встроен и содержит тот же символ более одного раза.
  Это можно проверить с помощью следующий набор команд из терминала (Замените i386 x86_64/armv7/armv7s/arm64 согласно архитектуры, для которого выполняется сборка для):

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Существует несколько способов для устранения этой проблемы:

  - Попросите предоставить новую версию поставщика собственной библиотеки и ее устранению.
  - Устранить неполадку самостоятельно, удаление файлов лишний объект (это работает, только если проблема заключается в действительности файлы дублированный объект)

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

  - Попросите компоновщика, чтобы удалить неиспользуемый код. Xamarin.iOS делает это автоматически при выполнении всех следующих условий:
    - Все привязки сторонних `[LinkWith]` включения SmartLink атрибуты:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - Не `-gcc_flags` передается mtouch (в поле аргументы mtouch Дополнительные параметры сборки iOS проекта).
    - Можно также попросить компоновщик непосредственно, чтобы удалить неиспользуемый код, добавив `-gcc_flags -dead_strip` на mtouch дополнительные аргументы в проекте iOS параметров сборки.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Повторяющийся символ в: \* (расположение связанного с предыдущей ошибкой)

Эта ошибка возникает только вместе с [MT5212](#MT5212). См. в разделе [MT5212](#MT5212) для получения дополнительной информации.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Собственный связывания не удалось неопределенный символ: \*. Была ссылка на этот символ управляемые члены \*. Убедитесь, что все необходимые платформы была упоминаемого и собственные библиотеки связаны.

Эта ошибка возникает, когда управляемый код содержит собственный метод, который не существует P/Invoke. Пример:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Существует несколько возможных решений.

  -  Удалите рассматриваемый P/Invoke из исходного кода.
  -  Включение управляемого компоновщика для всех сборок (это делается в проекте iOS параметры построения, задав «Поведение компоновщика» значение «Все сборки»). Все методы P/Invoke не используют фактически будут удалены из приложения (автоматически, а не вручную, например предыдущей точки). Недостатком такого подхода — это сделает сборками симулятор медленнее, что он может нарушить работу приложения, если она использует отражение — можно найти дополнительные сведения о компоновщик [здесь](~/ios/deploy-test/linker.md) )
  -  Создайте второй собственного библиотеку, содержащую отсутствующие машинных символов. Обратите внимание, что это просто обходной путь (Если вы столкнулись с целью эти функции вызываются, приложение произойдет сбой).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: Ссылки на "\*" может потребоваться дополнительный - framework = XXX или - lXXX инструкции в машинном коде компоновщик

Это предупреждение, указывающее, была обнаружена P/Invoke для ссылки на библиотеку в вопросе, что приложение не связывания с ней.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Собственный связывания сбой для \*. Отправьте отчет об ошибках в http://bugzilla.xamarin.com

Эта ошибка возникает при компоновке выходные данные компилятора AOT.

Скорее всего, это ошибка указывает на ошибку в Xamarin.iOS. Отправьте отчет об ошибках в [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Возможно собственного связывания завершилась ошибкой, поскольку командная строка компоновщика слишком длинное (\* символов).

Собственный связывания сбой и возможно, это произошло потому, что компоновщик команду имеет слишком большую длину.

Xamarin.iOS проекты будут часто ссылаются на собственный символы динамически, это означает, что собственного компоновщика может удалить такие машинных символов в процессе собственного связывания, так как собственный компоновщик не отображается, что эти символы используются.

Обычно Xamarin.iOS запросит собственного компоновщику сохранять такие символы, с использованием `-u symbol` флаг компоновщика, но при наличии большого числа таких символов, вся командной строки может превышать максимальную длину командной строки в соответствии с операционной системой.

Существует несколько возможных источников для таких динамических символов.

* Методы P/Invoke в методы статически связанные библиотеки (где — имя библиотеки dll `__Internal` в атрибуте DllImport `[DllImport ("__Internal")]`).
* Поле ссылки на участках памяти статически связанные библиотеки из привязки проекты (`[Field]` атрибутов).
* Классы Objective-C, на которые ссылается статически связанные библиотеки из привязки проектов (при использовании добавочные построения или не с помощью статических регистратора).

Возможные решения:

* Включение управляемого компоновщика (если это возможно для всех сборок, а не только сборки пакета SDK). Это может удалить достаточно источников для динамического символы, чтобы компоновщик командной строки не превышает максимально.
* Сократите число P/Invoke, ссылки на поля или Objective-C-классы.
* Перепишите динамического символы могут иметь более короткие имена.
* Передайте `-dlsym:false` дополнительных mtouch в качестве аргумента проекта iOS параметров сборки. Этот параметр Xamarin.iOS будет создавать собственную ссылку в AOT-скомпилированном коде и не нужно будет задать компоновщика, чтобы сохранить этот символ. Тем не менее это работает только для устройства строит и вызовет ошибки компоновщика Если P/Invoke для функций, которые не существуют в статическую библиотеку.
* Передайте `--dynamic-symbol-mode=code` параметры построения mtouch дополнительных аргументов в проекте iOS. Этот параметр Xamarin.iOS будет создавать дополнительные машинного кода, который ссылается на эти символы, а не запросом собственного компоновщику сохранять эти символы с помощью аргументов командной строки. Недостатком этого подхода является довольно, увеличится размер исполняемого файла.
* Включение статических регистратора, передав `--registrar:static` дополнительных mtouch в качестве аргумента проекта iOS параметры сборки (для сборок симулятор, так как статические регистратора уже значение по умолчанию для сборок устройств). Статические регистратора создаст код, который ссылается на классы Objective-C статически, поэтому нет необходимости попросить собственного компоновщику сохранять такие классы.
* Отключите добавочные построения (для сборок устройств). При включении добавочные построения код, сгенерированный статических регистратора не будет считаться компоновщиком собственный, что означает, что Xamarin.iOS по-прежнему должно попросите компоновщика для сохранения ссылки на классы Objective-C. Таким образом отключение добавочные построения не позволит этому требованию.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: Нельзя не учитывать динамический символ {символ} (--игнорировать динамической символом = {символ}), так как он не был обнаружен в качестве символа динамического.

Аргумент командной строки `--ignore-dynamic-symbol=symbol` было передано, но этот символ не является символ, который был распознан как динамический символ, который должен сохраняться вручную.

Существует две основных причины этого:

* Указано неверное имя символа.
    * Не вставляет символ подчеркивания в имени символа.
    * Символ для классов Objective-C `OBJC_CLASS_$_<classname>`.
* Символ задан правильно, но это символ, который уже сохранен обычными способами (некоторые построения причины параметры точный список динамических символов для изменения).

### <a name="mt53xx-other-tools"></a>MT53xx: Другие средства

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Отсутствует средство «удалить». Установите компонент «Средства командной строки» Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Отсутствует средство «dsymutil». Установите компонент «Средства командной строки» Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: Не удалось создать символы отладки (dSYM directory). Просмотрите журнал сборки.

Произошла ошибка при выполнении dsymutil в каталоге окончательного .app создание символов отладки. Просмотрите журнал сборки, чтобы показать выходные данные в dsymutil и просмотреть, как исправлена.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: Не удалось удалить конечный двоичный файл. Просмотрите журнал сборки.

Произошла ошибка при работе со средством «лента», чтобы удалить сведения об отладке из приложения.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Отсутствует средство «lipo». Установите компонент «Средства командной строки» Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: Не удалось создать библиотеку файловой системой fat. Просмотрите журнал сборки.

Произошла ошибка при работе со средством «lipo». Просмотрите журнал сборки, чтобы увидеть ошибку «lipo».

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: Не удалось подписать исполняемый файл. Просмотрите журнал сборки.

Произошла ошибка при подписи приложения. Просмотрите журнал сборки, чтобы увидеть ошибку «разработки».

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mtouch внутренние средства сообщения об ошибках

### <a name="mt600x-stripper"></a>MT600x: Stripper

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: Запущена версия Cecil не поддерживает удаление сборки

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: Не удалось удалить сборку `*`.

Произошла ошибка при чередует управляемого кода (с удалением IL-код) из сборок в приложении.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [UnauthorizedAccessException message]

Произошла ошибка безопасности при чередует отладочных символов из приложения.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Сообщения об ошибках MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: Не удалось разрешить узел IP-адресов для параметров отладчика Wi-Fi.

*Задача MSBuild: DetectDebugNetworkConfigurationTaskBase*

Действия по устранению неполадок:

- Попробуйте выполнить `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (который будет предоставлять IP-адреса, а не ошибкой очевидно).
- Попробуйте выполнить «ping \`hostname\`» которой может получить дополнительные сведения, например: `cannot resolve MyHost.local: Unknown host`

В некоторых случаях это проблема «локальной сети» и ее можно устранить путем добавления неизвестный узел `127.0.0.1   MyHost.local` в `/etc/hosts`.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Этот компьютер не имеет ни одного сетевого адаптера. Это требуется при отладке или профилирования на устройстве по Wi-Fi.

*Задача MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: Расширение приложения "\*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: Расширение приложения "\*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: Расширение приложения "\*" не соответствует CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: Расширение приложения "\*" имеет недопустимый CFBundleIdentifier (\*), он не начинается с CFBundleIdentifier набор основных приложений (\*).

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: Расширение приложения "\*" имеет CFBundleIdentifier (\*), заканчивается Недопустимый суффикс «.key».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: Расширение приложения "\*" не соответствует CFBundleShortVersionString.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: Расширение приложения "\*" имеет недопустимый Info.plist: он не содержит словарю NSExtension.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: Расширение приложения "\*" имеет недопустимый Info.plist: NSExtension словарь не содержит значение NSExtensionPointIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: WatchKit расширение "\*" имеет недопустимый Info.plist: NSExtension словарь не содержит словарю NSExtensionAttributes.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: WatchKit расширение "\*" не имеет ровно одно приложение контрольных значений.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: WatchKit расширение "\*" имеет недопустимый Info.plist: UIRequiredDeviceCapabilities должен содержать функцию «Контрольные значения компаньона».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: Приложение Контрольные значения "\*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: Приложение Контрольные значения "\*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: Приложение Контрольные значения "\*" имеет недопустимый CFBundleIdentifier (\*), он не начинается с CFBundleIdentifier набор основных приложений (\*).

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: Приложение Контрольные значения "\*" не имеет допустимого значения UIDeviceFamily. Ожидаемый «Контрольные значения (4)», но было найдено "\* (\*)".

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: Приложение Контрольные значения "\*" не соответствует CFBundleExecutable

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: Приложение Контрольные значения "\*" имеет недопустимое значение WKCompanionAppBundleIdentifier ("\*"), не соответствует CFBundleIdentifier комплект основного приложения ("\*").

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: Приложение Контрольные значения "\*" имеет недопустимый Info.plist: WKWatchKitApp ключа должно присутствовать и иметь значение «true».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: Приложение Контрольные значения "\*" имеет недопустимый Info.plist: LSRequiresIPhoneOS ключ не должен присутствовать.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: Приложение Контрольные значения "\*" не содержит расширение контрольных значений.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: Расширение Контрольные значения "\*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: Расширение Контрольные значения "\*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: Расширение Контрольные значения "\*" не соответствует CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: Расширение Контрольные значения "\*" имеет недопустимый CFBundleIdentifier (\*), он не начинается с CFBundleIdentifier набор основных приложений (\*).

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: Расширение Контрольные значения "\*" имеет CFBundleIdentifier (\*), заканчивается Недопустимый суффикс «.key».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: Расширение Контрольные значения "\*" имеет недопустимый Info.plist: он не содержит словарю NSExtension.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: Расширение Контрольные значения "\*" имеет недопустимый Info.plist: NSExtensionPointIdentifier должен быть «com.apple.watchkit».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: Расширение Контрольные значения "\*" имеет недопустимый Info.plist: словарь NSExtension должен содержать NSExtensionAttributes.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: Расширение Контрольные значения "\*" имеет недопустимый Info.plist: словарь NSExtensionAttributes должен содержать WKAppBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: WatchKit расширение "\*" имеет недопустимый Info.plist: UIRequiredDeviceCapabilities не должно содержать функцию «Контрольные значения компаньона».

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: Приложение Контрольные значения "\*" не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: Приложение Контрольные значения "\*" не соответствует CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: Приложение Контрольные значения "\*" не имеет допустимого значения UIDeviceFamily. Ожидалось "\*«, обнаружено»\* (\*)".

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: Приложение Контрольные значения "\*" не соответствует CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: WatchKit расширение «{extensionName}» имеет недопустимое значение WKAppBundleIdentifier ("\*"), не соответствует CFBundleIdentifier приложение Watch ("\*").

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: Приложение Контрольные значения "\*" имеет недопустимый Info.plist: WKCompanionAppBundleIdentifier должен существовать и должен соответствовать CFBundleIdentifier пакета основным приложением.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: Приложение Контрольные значения "\*" имеет недопустимый Info.plist: LSRequiresIPhoneOS ключ не должен присутствовать.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: Набора приложений {AppBundlePath} не содержит Info.plist.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Info.plist основной путь не указывает CFBundleIdentifier.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Info.plist основной путь не указывает CFBundleExecutable.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Info.plist основной путь не указывает CFBundleSupportedPlatforms.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Info.plist основной путь не указывает UIDeviceFamily.

*Задача MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Нераспознанный формат: \*.

*Задача MSBuild: PropertyListEditorTaskBase*

Где \* может быть:

- string
- array
- dict
- bool
- действительные
- целочисленный
- date
- Данные

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Добавить: запись, \*, неправильно указанный.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Добавить: запись, \*, содержит неправильный индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Добавить: \* запись уже существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Добавить: не удается добавить запись в \*, в качестве родительского.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Удалить: не удается удалить запись, \*, из родительского элемента.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Удалить: запись, \*, содержит неправильный индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Удалить: запись, \*, не существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Импорт: запись, \*, неправильно указанный.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Импорт: запись, \*, содержит неправильный индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Импорт: ошибка при чтении файла: \*.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Импорт: не удается добавить запись в \*, в качестве родительского.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Слияния: не удается добавить элементы массива в dict.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Слияния: указанный элемент должен быть контейнером.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Объединить: операция, \*, содержит неправильный индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Объединить: операция, \*, не существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Слияния: ошибка при чтении файла: \*.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Задать: операция, \*, неправильно указанный.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Задать: операция, \*, содержит неправильный индекс массива.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Задать: операция, \*, не существует.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Действие редактор Неизвестный PropertyList: \*.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Ошибка при загрузке "\*": \*.

*Задача MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Ошибка при сохранении "\*": \*.

*Задача MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Сообщения об ошибках среды выполнения

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Несовпадение версий среды выполнения собственного Xamarin.iOS и monotouch.dll. Переустановите Xamarin.iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: Не удалось найти метод "\*«в типе»\*".

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: Не удалось найти закрытый универсальный метод "\*«в типе»\*".

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: Не удается создать экземпляр \* для собственного объекта 0 x \* (типа "\*"), так как другой экземпляр уже существует для этого собственного объекта (типа \*).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Тип оболочки "\*«отсутствует его собственного класса ObjectiveC»\*".

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: Не удалось найти селектор "\*«в типе»\*"

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: Не удается получить дескриптор метода для селектора "\*«в типе»\*", так как селектор не соответствует методу

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: Загруженная версия Xamarin.iOS.dll был скомпилирован для \* bits во время процесса \* bits. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Это означает, что дает сбой в процессе построения. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: Не удается найти блок в делегата метод преобразования для метода *.*" s параметр #\*. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Это означает, что привязка API не была выполнена правильно. Если это интерфейс API, предоставляемые Xamarin, пожалуйста зарегистрировать ошибку в нашем bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), если привязка независимых производителей, обратитесь к поставщику.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Размер собственный тип несоответствие Xamarin. [iOS | Mac] .dll и архитектура выполнения. Xamarin. [iOS | DLL-файл Mac] был построен для \*-бит, пока текущий процесс \*-бит.

Это означает, что дает сбой в процессе построения. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: Не удается найти делегат для атрибута block преобразования ([DelegateProxy]) для возвращаемого значения для метода *.*. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Xamarin.iOS не удалось найти обязательный метод во время выполнения (для преобразования делегат в блоке).

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата имеет значение null. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата ({2}) указывает тип без поля «Обработчик». Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата ({2}) поле «Обработчик» имеет значение null. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: Недопустимый DelegateProxyAttribute для возвращаемого значения для метода *.*: Тип_делегата ({2}) поле «Обработчик» не является делегатом, это \*. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: Не удается преобразовать в делегат блока для возвращаемого значения для метода *.*, так как входные данные не делегат, это \*. Можно зарегистрировать ошибку на http://bugzilla.xamarin.com.

Недопустимый атрибут DelegateProxy для метода в вопросе.

Это обычно указывает на ошибку в Xamarin.iOS; можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Ошибка внутренней согласованности. Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: Не удалось найти сборку \* в загруженных сборках.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: Не удалось найти модуль с параметр MetadataToken \* в сборке \*.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Неизвестный тип неявного токена: \*.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: Ожидается ссылка на токен \* быть \*, но это \*. Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Для создания закрытого универсального метода для открытого универсального метода требуется экземпляр объекта: \* (токен ссылка: \*). Отправьте отчет об ошибках в http://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: Не удалось найти является действительным типом расширения смарт-перечисление {smart_type}. Можно зарегистрировать ошибку на https://bugzilla.xamarin.com.

Это указывает на ошибку в Xamarin.iOS. Можно зарегистрировать ошибку на [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
