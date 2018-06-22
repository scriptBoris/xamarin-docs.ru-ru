---
title: Советы по устранению неполадок
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 961f9f38687790343f225d95c74e00e98f594c28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773869"
---
# <a name="troubleshooting-tips"></a>Советы по устранению неполадок


## <a name="getting-diagnostic-information"></a>Получение диагностических сведений

Xamarin.Android имеет несколько мест для поиска при различных ошибок.
Сюда входит следующее.

1.  Диагностические данные MSBuild.
2.  Журналы развертывания устройства.
3.  Выходные данные журнала отладки Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Вывод диагностических сообщений MSBuild

Диагностические MSBuild может содержать дополнительные сведения, относящиеся к построение пакета и может содержать некоторые сведения о развертывании пакета.

Чтобы включить вывод диагностики MSBuild в Visual Studio, выполните следующие действия:

1.  Нажмите кнопку **Сервис > Параметры...**
2.  В представлении дерева в левой части выберите **проекты и решения > Построение и запуск**
3.  В правой панели значение раскрывающегося списка уровень детализации выходных данных сборки MSBuild диагностики
4.  Нажмите кнопку **ОК**.
5.  Выполните очистку и перестройку пакета.
6.  Вывод диагностических сообщений отображается в панели «Вывод».


Чтобы включить вывод диагностических сообщений MSBuild в Visual Studio для Mac/OS X:

1.  Нажмите кнопку **Visual Studio для Mac > Параметры...**
2.  В представлении дерева в левой части выберите **проекты > построения**
3.  На правой панели установите журнала детализации раскрывающийся список для диагностики
4.  Нажмите кнопку **ОК**.
5.  Перезапустите Visual Studio для Mac.
6.  Выполните очистку и перестройку пакета.
7.  Вывод диагностических сообщений отображается в панели ввода ошибок (**представление > дополняет > ошибок** ), нажав кнопку выходные данные построения.




## <a name="device-deployment-logs"></a>Журналы развертывания устройства

Включение ведения журнала развертывания устройства в среде Visual Studio.

1.  **Сервис > Параметры...**>
2.  В представлении дерева в левой части выберите **Xamarin > Параметры Android**
3.  В правой панели, включите [X] **ведение журнала отладки расширения (записывает monodroid.log на рабочий стол)** флажок.
4.  Сообщения журнала записываются в файл monodroid.log на рабочем столе.


Visual Studio для Mac всегда записывает журналы развертывания устройства. Поиск их немного сложнее; *AndroidUtils* файл журнала создается для каждого дня + передаче развертывания, например: **AndroidTools-2012-10-24_12-35-45.log**.

-  В Windows, записываются файлы журналов `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
-  В OS X, файлы журналов записываются в `$HOME/Library/Logs/XamarinStudio-{VERSION}`.




## <a name="android-debug-log-output"></a>Выходные данные журналов отладки Android

Android будет записывать много сообщений для [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin.Android использует свойства системы Android для управления генерацией дополнительные сообщения в журнал Android отладки. Android системные свойства можно задать с помощью *функцию setprop* в течение [мост отладки Android (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Системные свойства считываются во время запуска процесса, а следовательно, должен быть либо установить до запуска приложения или необходимо перезапустить приложение, после изменения свойства системы.



### <a name="xamarinandroid-system-properties"></a>Системные свойства Xamarin.Android

Xamarin.Android поддерживает следующие системные свойства:

-   *Debug.Mono.Debug*: Если непустая строка, это эквивалентно `*mono-debug*`.

-   *Debug.Mono.env*: разделенные вертикальной черты ("*|*") список переменных среды для экспорта во время запуска приложения *перед* моно был инициализирован. Благодаря настройке переменных среды, управляющих моно ведением журнала.

    - *Примечание*: так как значение "*|*"-запятыми, значение должно иметь дополнительный уровень заключения в кавычки, как \` *оболочки adb* \` команды приведет к удалению набор квот.

    - *Примечание*: значения свойств системы Android может быть не более 92 символов.

    - Пример

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *Debug.Mono.log*: разделенных запятой ("*,*") список компонентов, хотя должна вывести дополнительные сообщения в журнал Android отладки. По умолчанию имеет значение nothing. Компоненты:

    -   *все*: печать всех сообщений
    -   *сборка мусора*: сообщений о печати сборки Мусора.
    -   *gref*: печать сообщений выделение и освобождение ссылки (слабой, глобальные).
    -   *lref*: печать сообщений выделения и освобождения локальных ссылок.

    *Примечание*: это *очень* подробных сведений. Не следует включать, если действительно нужно.

-   *Debug.Mono.Trace*: обеспечивает установку [моно--трассировки](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` параметр.



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android не удается разрешить System.ValueTuple

Эта ошибка возникает из-за несовместимости с Visual Studio.

- **Visual Studio 2017 г. обновление 1** (версия 15.1 или более ранних версий) совместим только с **System.ValueTuple NuGet 4.3.0** (или более ранних версий).

- **Visual Studio 2017 г. обновление 2** (15.2 или более поздняя версия) совместим только с **System.ValueTuple NuGet 4.3.1** (или более поздней версии).

Выберите правильный System.ValueTuple NuGet, соответствующий установленной системы Visual Studio 2017 г.


## <a name="gc-messages"></a>Сборка Мусора сообщений

Сборка Мусора компонент сообщений можно просмотреть, задав свойство системы debug.mono.log на значение, которое содержит сборки мусора.

GC сообщения создаются каждый раз, когда сборщик Мусора выполняет и предоставляет информацию о том, какая работа сборка Мусора была.

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Дополнительные сведения глобального Каталога, например данные о времени могут быть вызваны параметр `MONO_LOG_LEVEL` переменную среды, чтобы `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

В результате (множество) Дополнительные моно сообщения, включая этих трех последствий:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

В `GC_BRIDGE` сообщение, `num-objects` — число объектов моста, учитывая этот этап, и `num_hash_entries` — число объектов, обработанных в ходе этого вызова в коде моста.

В `GC_MINOR` и `GC_MAJOR` сообщений, `total` — количество времени, во время приостановки мира (потоки не выполняются), пока `bridge` — это объем затраченное время в мост, обработки кода (которая имеет дело с виртуальной Машины Java). В мире *не* приостановлена во время обработки моста.

 *В целом*, чем больше значение `num_hash_entries`, несколько раз, `bridge` займет коллекции и тем больше `total` будет время, затраченное на сбор.



## <a name="global-reference-messages"></a>Сообщения глобальной ссылки

Чтобы включить ведение журнала, глобальной ссылки loggig (GREF) *debug.mono.log* системное свойство должно содержать *gref*, например:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android использует Android глобальной ссылки проводить сопоставление между экземплярами Java и связанных управляемых экземпляров, как при вызове метода Java экземпляр Java, необходимо указать для Java.

К сожалению эмуляторы Android разрешается только 2000 глобальной ссылки существовать одновременно. Оборудование имеет гораздо большего значения 52000 глобальной ссылки. При запуске приложения в эмуляторе, поэтому важно знать, нижний предел может быть проблематично *где* указанный экземпляр из может быть очень полезным.

 *Примечание*: счетчик ссылок на глобальный является внутренним для Xamarin.Android и не выполняет (и не может содержит глобальные ссылок, занят другим собственные библиотеки, которые загружены в процесс. Используйте глобальный счетчик для оценки.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Существует четыре сообщения о последствий.

-  Создание ссылки на глобального: это строки, которые начинаются с *+ g +* и предоставит трассировку стека для создания пути кода.
-  Уничтожение глобальной ссылки: это строки, которые начинаются с *- g-* и позволяет получить трассировку стека для удаления ссылки на глобальные пути кода. Если сборщик Мусора освобождение gref, будет отображено не трассировку стека.
-  Слабая глобального ссылка создания: это строки, которые начинаются с *+ w +* .
-  Слабая глобального ссылка уничтожения: это строки, начинающиеся с *-w-* .


Во всех сообщениях *grefc* значение — число глобальных ссылки, созданные Xamarin.Android, пока *grefwc* значение — это количество слабых ссылок глобального созданных Xamarin.Android. *Обработки* или *obj дескриптор* значение значение дескриптора JNI и символ после " */*" является типом значения дескриптора: */L* для локальных ссылок */G* для глобальной ссылки и */W* для слабых ссылок на глобальный.

В рамках процесса сборки Мусора, ссылки на глобальные (+ g +) преобразуются в слабые ссылки на глобальные (вызывающие a + w +, - g-), запускается GC стороне Java и затем проверяется слабую ссылку на глобальный для просмотра, если они были собраны. Если все еще существует вокруг слабой ссылки создается новый gref (+ g +, -w-), в противном случае уничтожается слабой ссылки (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Экземпляр Java и оболочкой MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Сборка Мусора выполняется...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>Объект все еще существует как дескриптор! = null
## <a name="wref-turned-back-into-a-gref"></a>снова превращается в gref wref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>Объект не существует, как дескриптор == null
## <a name="wref-is-freed-no-new-gref-created"></a>wref — gref освобожденные, новые созданные

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Имеется один «интерес» абсолютно здесь: для целевых объектов под управлением Android, предшествующей 4.0, значение gref равно адрес объекта Java в Android рабочей памяти. (Т. е сборщик Мусора — это не перемещение, умеренная сборщика, и его передачу прямые ссылки на эти объекты.) Таким образом после + g +, + w +, - g-, + g +, - w-результирующий gref будет иметь то же значение, как исходное значение gref последовательности. Это делает grepping через журналы очевиден.

Тем не менее, Android 4.0 имеет скользящего сборщика и больше не передает объекты виртуальной Машины прямой ссылки для Android среды выполнения. Следовательно, после + g +, + w +, - g-, + g +, - w-последовательность, значение gref *будут отличаться*. Если объект нечувствительным несколько глобальных каталогов, он перейдет по несколько значений gref, что затрудняет для определения, где экземпляр был выделен из.

### <a name="querying-programmatically"></a>Запрос программным способом

С помощью запроса можно запросить GREF и WREF счетчики `JniRuntime` объекта.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` — Глобальный счетчик ссылок

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -Счетчик слабых ссылок



## <a name="offline-activation"></a>Автономной активации

Если вы не удается активировать Xamarin.Android в Windows, или не удается установить полную версию Xamarin.Android на Mac OS X, см. в разделе [автономной активации](~/android/get-started/installation/index.md) страницы.



## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>Невозможно обновить до Indie или Business из учетной записи пробной версии

Если вы недавно приобрели Xamarin.Android, ранее работе Xamarin.Android пробную версию, может потребоваться выполнить следующие действия, чтобы получить это изменение лицензии выбираться средствами Visual Studio для Mac или Visual Studio.

-  Закройте Visual Studio для Mac или Visual Studio
-  Удалите все файлы из ~/Library/MonoAndroid на Mac или %PROGRAMDATA%\Mono для Android\License\ для Windows
-  Снова откройте Visual Studio для Mac или Visual Studio и сборка проекта Xamarin.Android


Это должно можно получить работающий. Если продолжает возникать, можно попробовать [автономной активации](~/android/get-started/installation/index.md) для завершения активации рабочей станции.



## <a name="receiving-activation-incomplete-error-message"></a>Получение "неполное сообщение активации

Эта проблема может возникать при использовании Xamarin.Android для Visual Studio. Чтобы устранить эту проблему, отправьте журналы из следующего расположения для *contact@xamarin.com*.

-  Расположение журнала: **% LocalAppData %\\Xamarin\\журналы**




## <a name="receiving-error-retrieving-update-information-error-message"></a>Получает сообщение «Ошибка при получении сведений об обновлениях»

Время от времени обновления завершится со следующей ошибки, которой часто случается при поиске обновлений:

Большую часть времени, эта ошибка может быть решена просто ведения журнала из вашей учетной записи Xamarin, и затем снова ведения журнала.

Для этого найдите вашей платформе ниже и следуйте инструкциям:

**На Mac:**
1. Откройте Visual Studio для Mac
2. Выберите Visual Studio для Mac > учетной записи...
3. Нажмите кнопку выхода
4. Нажмите кнопку входа
5. Введите свои учетные данные
6. Проверка наличия обновлений

**На ПК с помощью Visual Studo:**
1. Открытие Visual Studio
2. Выберите Инструменты > учетной записи Xamarin
3. Нажмите кнопку выхода
4. Нажмите кнопку входа
5. Введите свои учетные данные
6. Проверка наличия обновлений

Если эта ошибка продолжает появляться, обратитесь по электронной почте **contact@xamarin.com**.




## <a name="android-debug-logs"></a>Журналы отладки Android

[Android отладочный журналы](~/android/deploy-test/debugging/android-debug-log.md) может предоставить дополнительную информацию о любой ошибки времени выполнения, что вы видите.



## <a name="floating-point-performance-is-terrible"></a>С плавающей запятой производительности ужасных!

Кроме того «Мое приложение запускает 10 раз быстрее с отладочной сборки, чем с построения выпуска!»

Xamarin.Android поддерживает несколько устройств ABIs: *armeabi*, *armeabi v7a*, и *x86*. ABIs устройства могут быть указаны в **свойства проекта > вкладка "приложение" > Поддерживаемые архитектуры**.

Отладочные построения использовать пакет Android, который предоставляет все ABIs и таким образом будет использовать быстрый ABI для целевого устройства.

Построения выпуска будут включены только ABIs, выбранного на вкладке свойств проекта. Можно выбрать более одного.

*armeabi* ABI по умолчанию, а имеет широкая поддержка устройств. *Тем не менее*, armeabi не поддерживает Многопроцессорный устройств и оборудования с плавающей запятой, amont другие вещи. Следовательно приложения, использующие среду выполнения версии armeabi будет привязан к одного ядра и будет использовать реализацию soft-float. Они могут влиять на значительно более низкую производительность приложения.

Если вашему приложению требуется довольно производительности с плавающей запятой (например, игры), следует включить *armeabi v7a* ABI. Требуется поддерживают только *armeabi v7a* среды выполнения, хотя это означает, что старые устройства, которые поддерживают только *armeabi* не сможет запустить приложение.



## <a name="could-not-locate-android-sdk"></a>Не удалось найти пакет SDK для Android

2 загрузки доступны из Google для Android SDK для Windows.
Если установщик .exe, он будет записывать разделы реестра, которые сообщают Xamarin.Android, где она установлена. Если выбрать этот ZIP-файл и распакуйте его, Xamarin.Android не знает, где искать пакета SDK. Можно определить Xamarin.Android, где пакета SDK в Visual Studio последовательно выбрав пункты **Сервис > Параметры > Xamarin > Параметры Android**:

[![Расположение пакета SDK для Android в Xamarin Android параметры](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>Интегрированная среда разработки не отображает целевое устройство

Иногда будет пытаться развертывания приложения на устройство, однако устройство, которое вы хотите развернуть на не отображается в диалоговом окне Выбор устройства. Это может произойти, когда мост отладки Android решает переходите отпуска.

Чтобы определить проблему, найдите [adb программы](~/android/deploy-test/debugging/android-debug-log.md), затем запустите:

```shell
adb devices
```

Если устройство не отображается, необходимо перезапустить сервер мост отладки Android, чтобы можно было найти устройства:

```shell
adb kill-server
adb start-server
```

Программное обеспечение для синхронизации HTC может помешать **adb start-server** работать должным образом. Если **adb start-server** команды не печатается исходящий порт, к которому он начинается, закройте программное обеспечение для синхронизации HTC и повторите перезапуск сервера adb.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Не удается запустить исполняемый файл указанной задачи «keytool»

Это означает, что путь не содержит каталог, где находится каталог bin пакета SDK для Java. Проверьте, что вы выполнили шаги из [установки](~/android/get-started/installation/index.md) руководства.


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe или aresgen.exe завершился с кодом 1

Для решения этой проблемы, перейти в Visual Studio и изменить уровень детализации MSBuild, чтобы сделать это, выберите: **Сервис > Параметры > проект** и **решения > построения** и **запуска > Уровень детализации выходных данных построения проекта MSBuild** и это значение равно **обычный**.

Перестроить и проверьте область вывода Visual Studio, который должен содержать ошибки переполнения.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Не хватает места хранения на устройстве для развертывания пакета

Это происходит, если не запустить эмулятор из среды Visual Studio. При запуске эмулятора вне Visual Studio, необходимо передать `-partition-size 512` параметры, например

```shell
emulator -partition-size 512 -avd MonoDroid
```

Убедитесь, т. е. При использовании имени правильный симулятор [имя, используемое при настройке имитатор](~/android/get-started/installation/windows.md#device).


## <a name="installfailedinvalidapk-when-installing-a-package"></a>Установка\_сбой\_НЕДОПУСТИМЫЙ\_APK при установке пакета

Имена пакета Android *должен* содержать точку ("*.*"). Измените имя пакета, чтобы он содержал периодом.

-   В среде Visual Studio:
    -   Щелкните правой кнопкой мыши проект > Свойства
    -   Перейдите на вкладку Android манифеста в левой части экрана.
    -   Обновите поле имя пакета.
        -   Если вы видите сообщение &ldquo;AndroidManifest.xml не найден. Щелкните, чтобы добавить его. &rdquo;, щелкните ссылку, а затем обновите поле имя пакета.
-   В среде Visual Studio для Mac:
    -   Щелкните правой кнопкой мыши проект > Параметры.
    -   Перейдите к построению и раздел приложения Android.
    -   Измените поле имя пакета будет содержать ".".




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>Установка\_сбой\_отсутствует\_SHARED\_БИБЛИОТЕКИ при установке пакета

— «Общей библиотеки» в данном контексте *не* собственного общей библиотеки (*libfoo.so*) файла; он вместо этого — это библиотека, необходимо отдельно установить на целевом устройстве, например карты Google.

Пакет Android указывает, какие общей библиотеки требуются с `<uses-library/>` элемента. Если *необходимые* библиотеки не установлен на целевом устройстве (например `//uses-library/@android:required` — *true*, используемого по умолчанию), то произойдет сбой установки пакета с *УСТАНОВИТЬ\_ Не удалось выполнить\_отсутствует\_SHARED\_БИБЛИОТЕКИ*.

Чтобы определить, какие общей библиотеки необходимы, просмотрите *создан*
**AndroidManifest.xml** файла (например **obj\\отладки\\android \\AndroidManifest.xml**) и выполните поиск `<uses-library/>` элементов. `<uses-library/>` элементы могут быть добавлены вручную в своем проекте **свойства\\AndroidManifest.xml** файла и через [UsesLibraryAttribute настраиваемого атрибута](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/).

Например, при добавлении ссылки на сборку *Mono.Android.GoogleMaps.dll* добавит неявно `<uses-library/>` для карты Google общей библиотеки.



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>Установка\_сбой\_обновление\_НЕСОВМЕСТИМЫЕ при установке пакета

Пакеты Android имеют три требования.

-   Они должны содержать "." (см. предыдущие записи)
-   Они должны иметь уникальное строковое имя пакета (поэтому tld обратного соглашению, в приложение Android имена, например com.android.chrome для приложения Chrome)
-   При обновлении пакетов, пакет должен иметь тот же ключ подписывания.

Таким образом рассмотрим следующую ситуацию:

1.  Построение и развертывание приложения в качестве приложения отладки
2.  Изменить ключ подписывания, например для использования в качестве приложения выпуска (или из-за вас не устраивает предоставляемых по умолчанию отладки ключ подписывания)
3.  Установка приложения без его удаления сначала, например Отладка > Начать без отладки в Visual Studio


В этом случае установка пакета завершится ошибкой с установкой\_сбой\_обновление\_НЕСОВМЕСТИМЫЕ ошибку, так как имя пакета не были изменены при подписывании ключ был. [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md) также будет содержать сообщение, подобное:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Чтобы устранить эту ошибку, полностью удалите приложение с устройства перед повторной установкой.


## <a name="installfaileduidchanged-when-installing-a-package"></a>Установка\_сбой\_UID\_ИЗМЕНЕНО при установке пакета

При установке пакета Android, ей назначается *идентификатор пользователя* (UID).
*Иногда*, в целях в настоящее время неизвестно при установке через уже установленного приложения, установка завершится ошибкой с `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Чтобы обойти эту проблему, *полностью удалить* пакета Android по установке приложения с графическим интерфейсом пользователя целевой объект Android или с помощью `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**НЕ используйте** `adb uninstall -k`, как это будет *Сохранить* данные приложений и таким образом сохранить конфликтующие UID на целевом устройстве.



## <a name="release-apps-fail-to-launch-on-device"></a>Выпуск приложения не сможет запуститься на устройстве

Android журнал отладки выход будет содержать сообщение, подобное:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

В этом случае существует две возможные причины:

1.  Apk-файл не предоставляет ABI, которая поддерживает целевое устройство.
    Например apk-файл содержит только двоичные файлы armeabi v7a и целевое устройство поддерживает только armeabi.

2.  [Android ошибки](http://code.google.com/p/android/issues/detail?id=21670). Если это так, удалите приложение, межплатформенных пальцы и переустановите приложение.

Для исправления (1), измените параметры и свойства проекта и [добавить поддержку необходимые ABI в список поддерживаемых ABIs](~/android/app-fundamentals/cpu-architectures.md). Чтобы определить, какие ABI, необходимо добавить, выполните следующую команду adb от целевого устройства:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

Выход будет содержать основной (и необязательно вторичная) ABIs.

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>Для проекта не задано свойство OutPath &ldquo;MyApp.csproj&rdquo;

Обычно это означает, у вас есть компьютера с HP и переменной среды &ldquo;платформы&rdquo; установил на что-либо как MCD или HPD. Это конфликтует со свойством платформы MSBuild, который обычно имеет значение &ldquo;любой ЦП&rdquo; или &ldquo;x86&rdquo;. Необходимо будет удалить эту переменную среды с компьютера перед MSBuild может функционировать:

-   Панель управления > Система > Дополнительно > переменные среды

Перезапустите Visual Studio или Visual Studio для Mac и попытки перестроить. Что теперь должен работать соответствующим образом.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject не может быть приведен к...

Xamarin.Android 4.x не маршалировать правильно вложенных универсальных типов должным образом. Например, рассмотрим следующий C\# код с использованием [SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


Проблема заключается в том, что Xamarin.Android неправильно маршалирует вложенных универсальных типов. `List<IDictionary<string, object>>` Маршалируется в [java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/), но `ArrayList` содержащего `mono.android.runtime.JavaObject` экземпляров (Справочник по какой `Dictionary<string, object>` экземпляров) вместо, реализующую [java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/), что может привести следующее исключение:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

Обходной путь заключается в использовании указанных [типы коллекций Java](~/android/internals/api-design.md) вместо `System.Collections.Generic` типы для &ldquo;внутреннего&rdquo; типов. Это приведет к соответствующих типов Java при маршалинге экземпляров. (Следующий код намного сложнее, чем требуется, чтобы сократить время существования gref. Он может упрощена, чтобы изменить исходный код с помощью `s/List/JavaList/g` и `s/Dictionary/JavaDictionary/g` Если время существования gref не беспокойтесь.)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Эта проблема будет устранена в будущих версиях](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).


## <a name="unexpected-nullreferenceexceptions"></a>Непредвиденная исключений NullReferenceException

Иногда [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md) будет упомянуть исключений NullReferenceException, &ldquo;не может быть&rdquo; или поступать из Mono для кода Android во время выполнения незадолго до отключается приложения:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

или

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Это происходит, когда среда выполнения Android решает отменить процесс, который может произойти по различным причинам, включая обращения GREF предел целевого объекта или действия &ldquo;неправильный&rdquo; с JNI.

Для просмотра, если это так, в журнале Android отладки сообщение от процесса, аналогичного тому:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Прервана из-за переполнения глобальной ссылки

Слой JNI для Android среды выполнения поддерживает только ограниченное число ссылок на объекты JNI недействителен в любой момент времени. При превышении этого ограничения, прерывание действия.

GREF (*глобальной ссылки*) ограничение равно 2000 ссылки в эмуляторе и ~ 52000 ссылается на оборудовании.

Известно, что вы используете для создания слишком много GREFs при появлении сообщения, такие как это в Android журнал отладки:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

По достижении предельного количества GREF печатается сообщение из следующего:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


В приведенном выше примере (который, кстати, поставляется вместе с [ошибки 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) проблемы является то, что слишком много Android.Graphics.Point экземпляры создаются; см. раздел [комментарий \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) список исправлений для этой конкретной ошибки.

Как правило, является решение для поиска подходящего имеет слишком много экземпляров выделенной &ndash; Android.Graphics.Point в дамп выше &ndash; найдите, где они создаются в исходном коде и удалите их соответствующим образом (, чтобы их Сокращено время существования Java-Object). Это не всегда подходит (\#685215 является многопоточным, поэтому тривиальные решение позволяет избежать вызова Dispose), но это в первую очередь необходимо учитывать.

Можно включить [GREF входа](~/android/troubleshooting/index.md) создания GREFs и сколько существует.


## <a name="abort-due-to-jni-type-mismatch"></a>Прервана из-за несоответствия типа JNI

Если сводный вручную код JNI возможна, типы могут не соответствовать правильно, например при попытке вызвать `java.lang.Runnable.run` на тип, который не реализует `java.lang.Runnable`. В этом случае будет сообщение следующего вида в Android журнал отладки:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Поддержка динамического кода

### <a name="dynamic-code-does-not-compile"></a>Динамический код не компилируется.

Для использования C\# динамическими в приложение или библиотека, необходимо добавить в проект System.Core.dll, Microsoft.CSharp.dll и Mono.CSharp.dll.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>В сборке выпуска MissingMethodException справедливо для динамического кода во время выполнения.

-   Вполне вероятно, что проект приложения не имеет ссылки на библиотеку System.Core.dll, Microsoft.CSharp.dll или Mono.CSharp.dll. Убедитесь, что существуют ссылки на эти сборки.

    -   Имейте в виду, динамического кода всегда затраты. Если требуется эффективный код, рекомендуется не использовать динамический код.

-   В первой версии preview эти сборки были исключены, если кодом приложения явно используются типы в каждой сборке. Для решения этой проблемы см. [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Проекты, построенные с AOT + LLVM аварийного завершения на x86 устройств

При развертывании приложения, созданного с [AOT + LLVM](~/android/deploy-test/release-prep/index.md) x86 86 устройств, может появиться сообщение об ошибке "исключение" следующим образом:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Это известная проблема в [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). Это решение подходит для отключения LLVM.
