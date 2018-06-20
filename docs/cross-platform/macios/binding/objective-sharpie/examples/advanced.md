---
title: Расширенный пример (вручную)
description: В этом документе описывается использование выходных данных xcodebuild как входные данные для цели Sharpie, предоставляющий понять назначение за кулисами Sharpie цель.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 811b783d33a20e23a7e807861e19355a1c372b84
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209405"
---
# <a name="advanced-manual-real-world-example"></a>Расширенный пример (вручную)

**В этом примере используется [библиотеки POP от Facebook](https://github.com/facebook/pop).**

В этом разделе рассматриваются более сложные подход к привязке, где используется Apple `xcodebuild` инструмент, сначала выполните построение проекта POP и вывести входные данные для цели Sharpie вручную. По сути они охватывают действия Sharpie цель за кулисами в предыдущем разделе.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Поскольку библиотека POP есть проект Xcode (`pop.xcodeproj`), мы просто используем `xcodebuild` для построения POP. Этот процесс, в свою очередь может создавать файлы заголовков, Sharpie цель может потребоваться выполнить синтаксический анализ. Именно поэтому построение перед важно привязки. При построении через `xcodebuild` обеспечить передается архитектура и идентификатор пакета SDK, необходимо передать Sharpie цель (и обратите внимание, что цель Sharpie 3.0 обычно это можно сделать для вас!):

```
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

Будет большой объем выходных данных сборки сведения в консоли в рамках `xcodebuild`. Показанной выше, можно увидеть выполнение целевой объект «CpHeader» которой заголовочные файлы были скопированы в выходной каталог сборки. Это часто происходит и упрощает привязки: как часть построения собственной библиотеки, файлы заголовков часто копируются в «открыто» использовать папку, это можно сделать проще анализа для привязки. В этом случае мы знаем, что файлы заголовков POP находятся в `build/Headers` каталога.

Теперь мы готовы для привязки POP. Мы знаем, что мы хотим построить для пакета SDK `iphoneos8.1` с `arm64` архитектуры и в нем имеются файлы заголовков, которые мы стремимся `build/Headers` под извлечение POP git. Если мы посмотрим `build/Headers` каталог, мы рассмотрим несколько файлов заголовков:

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Если взглянуть на `POP.h`, мы видим основной верхнего уровня заголовка библиотеки в файл, который является `#import`s другие файлы. По этой причине нам требуется только для передачи `POP.h` для Sharpie цель и clang будет выполнять rest в фоновом:

```
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

Обратите внимание, что мы передан `-scope build/Headers` аргумент Sharpie к цели деятельности организации. Так как библиотеки C и Objective-C должен `#import` или `#include` других файлов заголовков, которые являются подробностями реализации библиотеки и интерфейс API для привязки, `-scope` аргумент сообщает Sharpie цель игнорировать любого API, который не определен в где-нибудь файл включен в `-scope` каталога.

Вы найдете `-scope` аргумент необязателен часто четко реализовано библиотек, однако явного предоставления вреда.

Кроме того, мы указали `-c -Ibuild/headers`. Во-первых `-c` аргумент сообщает Sharpie цель остановить интерпретации аргументов командной строки и передать все последующие аргументы _компилятору clang_. Таким образом `-Ibuild/Headers` включает аргумент компилятора clang, который указывает, что clang для поиска в списке `build/Headers`, являющееся проживания POP заголовки. Без этого аргумента clang не будет знать, где находятся файлы, `POP.h` — `#import`ing. _Почти все «проблемы» с помощью Sharpie цель сводится для выяснения дальнейших для передачи clang_.

### <a name="completing-the-binding"></a>Завершение работы привязки

Теперь создана цели Sharpie `Binding/ApiDefinitions.cs` и `Binding/StructsAndEnums.cs` файлов.

Это основные цели Sharpie первый проход на привязку, и в некоторых случаях может быть достаточно. Как уже говорилось выше тем не менее, разработчик обычно потребуется вручную изменить созданные файлы после завершения Sharpie цель для [устранить все проблемы,](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) , не может быть обработано автоматически с помощью средства.

После завершения обновления эти два файла, теперь могут добавляться в проект привязки в Visual Studio для Mac или передаваться напрямую к `btouch` или `bmac` средства для создания окончательного привязки.

Подробное описание процесса привязки, см. в разделе нашей [полное Пошаговое руководство инструкции](~/ios/platform/binding-objective-c/walkthrough.md).

