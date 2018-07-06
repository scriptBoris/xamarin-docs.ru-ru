---
title: Расширенный пример (вручную)
description: В этом документе описывается использование выходных данных xcodebuild как входные данные для цели Sharpie, который содержит информацию об назначение цели Sharpie взгляд изнутри.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c4f7f1e9702fb2ee0f5525343a52e3aacd85d68c
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855264"
---
# <a name="advanced-manual-real-world-example"></a>Расширенный пример (вручную)

**В этом примере используется [POP библиотеки из Facebook](https://github.com/facebook/pop).**

В этом разделе рассматриваются более сложный подход к привязке, где мы будем использовать корпорации Apple `xcodebuild` инструмент, сначала необходимо собрать проект POP, а затем вручную входные данные для цели Sharpie. По сути, в этой статье приводится действия Sharpie цели за кулисами в предыдущем разделе.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Так как библиотека POP имеет проекта Xcode (`pop.xcodeproj`), мы просто используем `xcodebuild` для построения POP. Этот процесс, в свою очередь может создавать файлы заголовков, которые Sharpie цели может потребоваться синтаксический анализ. Вот почему построение прежде, чем привязка является важным. При построении с помощью `xcodebuild` убедитесь, можно передать один и тот же идентификатор пакета SDK и архитектура, требуется передать Sharpie цели (и помните, что цель Sharpie 3.0 обычно это можно сделать для вас!):

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

Будет существовать много выходных данных сборки сведения в консоли как часть `xcodebuild`. Показанной выше, мы видим, что целевой объект «CpHeader» выполнялся которой были скопированы файлы заголовков выходного каталога сборки. Это происходит часто и упрощает привязку: как часть сборки собственной библиотеки, файлы заголовков часто копируются в «открыто» готовых к использованию расположении, к которому можно сделать проще синтаксического анализа для привязки. В этом случае мы знаем, что точки подключения файлы заголовков находятся в `build/Headers` каталога.

Теперь мы готовы для привязки POP. Мы знаем, что мы хотим построить для пакета SDK `iphoneos8.1` с `arm64` архитектура и файлы заголовков, которые нас интересуют только в `build/Headers` под извлечение git POP. Если взглянуть `build/Headers` каталог, мы увидим несколько файлов заголовков:

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Если взглянуть на `POP.h`, мы видим, это файл библиотеки основной заголовок верхнего уровня, который `#import`s другие файлы. По этой причине нам требуется только для передачи `POP.h` для Sharpie цели и clang сделает все остальное в фоновом:

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

Обратите внимание, что мы передали `-scope build/Headers` аргумент Sharpie цели. Так как библиотеки C и Objective-C должен `#import` или `#include` других файлов заголовков, которые являются подробностями реализации библиотеки и интерфейс API для привязки, `-scope` аргумент указывает цель Sharpie игнорировать любые API, который не определен в где-нибудь файл включен в `-scope` каталога.

Вы найдете `-scope` аргумент необязателен часто четко реализованного библиотек, однако явно предоставив никакого вреда.

Кроме того, мы указали `-c -Ibuild/headers`. Во-первых `-c` аргумент указывает Sharpie цели для остановки интерпретации аргументов командной строки и передать все последующие аргументы _непосредственно компилятору clang_. Таким образом `-Ibuild/Headers` включает аргумент компилятора clang, который указывает, что clang для поиска в разделе `build/Headers`, который является места проживания заголовки POP. Без этого аргумента clang не будет знать, где можно найти файлы, `POP.h` является `#import`ing. _Почти все «проблемы» с помощью цели Sharpie сводится решили, что передаваемые clang_.

### <a name="completing-the-binding"></a>Завершение работы привязки

Теперь созданный цели Sharpie `Binding/ApiDefinitions.cs` и `Binding/StructsAndEnums.cs` файлы.

Ниже приведены основные цели Sharpie первый проход в привязке, а в некоторых случаях может быть достаточно. Как уже говорилось тем не менее, разработчик обычно необходимо вручную изменить созданные файлы после завершения задания Sharpie для [устранения неисправностей](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) , не удалось обработать автоматически с помощью средства.

После завершения обновлений эти два файла теперь можно добавлять в проект привязки в Visual Studio для Mac или передаваться напрямую к `btouch` или `bmac` средства для создания окончательного привязки.

Полное описание процесса привязки, см. в разделе наших [инструкции полное Пошаговое руководство](~/ios/platform/binding-objective-c/walkthrough.md).

## <a name="related-links"></a>Связанные ссылки

- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)