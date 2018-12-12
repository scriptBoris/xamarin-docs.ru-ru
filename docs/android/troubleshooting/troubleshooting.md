---
title: Советы по устранению неполадок
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: ccf5d97ff553fd304c4a3af158085d490bb665b7
ms.sourcegitcommit: 2868c968f418cd7cc110f9664f3c3ffb6df1f9af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53267603"
---
# <a name="troubleshooting-tips"></a>Советы по устранению неполадок


## <a name="getting-diagnostic-information"></a>Получение диагностических сведений

Xamarin.Android есть несколько мест для поиска при различных ошибок.
Сюда входит следующее.

1.  Вывод диагностики MSBuild.
2.  Журналы развертывания устройства.
3.  Выходные данные журнала для отладки Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Вывод диагностики MSBuild

Диагностики MSBuild может содержать дополнительные сведения, относящиеся к построение пакета и может содержать некоторые сведения о развертывании пакета.

Чтобы включить вывод диагностики MSBuild в Visual Studio, выполните следующие действия:

1.  Нажмите кнопку **Сервис > Параметры...**
2.  В представлении дерева слева, выберите **проекты и решения > сборка и запуск**
3.  На панели справа равным раскрывающемся списке детализации выходных данных сборки MSBuild диагностики
4.  Нажмите кнопку **ОК**.
5.  Выполните очистку и перестройку пакета.
6.  Диагностические выходные данные отображается в панели «Вывод».


Чтобы включить вывод диагностики MSBuild в Visual Studio для Mac/OS X:

1.  Нажмите кнопку **Visual Studio для Mac > Параметры...**
2.  В представлении дерева слева, выберите **проектов > построения**
3.  На панели справа уровень детализации журнала раскрывающегося списка для диагностики
4.  Нажмите кнопку **ОК**.
5.  Перезапустите Visual Studio для Mac.
6.  Выполните очистку и перестройку пакета.
7.  Диагностические выходные данные отображается на панели ошибок (**представление > панели > ошибки** ), нажав кнопку выходных данных сборки.




## <a name="device-deployment-logs"></a>Журналы развертывания устройств

Включение ведения журнала развертывания устройства в среде Visual Studio.

1.  **Сервис > Параметры...**>
2.  В представлении дерева слева, выберите **Xamarin > Параметры Android**
3.  На панели справа, включить [X] **ведение журнала отладки расширений (файл monodroid.log на рабочий стол)** "флажок".
4.  Сообщения заносятся в файл monodroid.log на рабочем столе.


Visual Studio для Mac всегда записывает журналы развертывания устройства. Поиск их немного сложнее; *AndroidUtils* файл журнала создается для каждого дня + время развертывания происходит, например: **AndroidTools-2012-10-24_12-35-45.log**.

-  В Windows, файлы журналов записываются в `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
-  В OS X, файлы журналов записываются в `$HOME/Library/Logs/XamarinStudio-{VERSION}`.




## <a name="android-debug-log-output"></a>Выходные данные журнала для отладки Android

Android будет записывать много сообщений для [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin.Android использует системные свойства Android для управления созданием дополнительных сообщений в журнал отладки Android. Системные свойства Android можно задать с помощью *setprop* команду в пределах [Android Debug Bridge (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Системные свойства считываются во время запуска процесса, а следовательно, должен быть либо задать прежде чем запустить приложение или приложение необходимо перезапустить после изменения свойства системы.



### <a name="xamarinandroid-system-properties"></a>Системные свойства Xamarin.Android

Xamarin.Android поддерживает следующие системные свойства:

-   *Debug.Mono.Debug*: Если строка не пустой, это эквивалентно `*mono-debug*`.

-   *Debug.Mono.env*: Отделенный вертикальной чертой ("*|*") список переменных среды для экспорта во время запуска приложения *перед* mono была инициализирована. Таким образом, задание переменных среды, управляющих mono ведением журнала.

    - *Примечание*: Так как значение "*|*"-запятыми, значение должно содержать дополнительный уровень заключения в кавычки, как \` *оболочки adb* \` команды приведет к удалению парные кавычки.

    - *Примечание*: Значения свойств системы Android не может превышать 92 символов в длину.

    - Пример

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *Debug.Mono.log*: Разделенный запятыми ("*,*") список компонентов, которые должна вывести дополнительные сообщения в журнал отладки Android. По умолчанию имеет значение nothing. Компоненты:

    -   *все*: Печать всех сообщений
    -   *сборщик мусора*: Печать сообщений о сборки Мусора.
    -   *gref, установленное*: Печать сообщений выделение и освобождение ссылки (слабыми, глобальные).
    -   *lref*: Печать выделение и освобождение сообщений локальной ссылки.

    *Примечание*: это *чрезвычайно* verbose. Не следует включать, если вам действительно нужно.

-   *Debug.Mono.Trace*: Позволяет задавать [mono--трассировки](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` параметр.



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android не удается разрешить System.ValueTuple

Эта ошибка возникает из-за несовместимости с помощью Visual Studio.

- **Visual Studio 2017 с обновлением 1** (версия 15.1 или более ранней версии) совместима только с **System.ValueTuple NuGet 4.3.0** (или более ранней версии).

- **Visual Studio 2017 с обновлением 2** (версия 15.2 или более поздней версии) совместима только с **System.ValueTuple NuGet 4.3.1** (или более поздней версии).

Выберите правильный System.ValueTuple NuGet, которая соответствует вашей установки Visual Studio 2017.


## <a name="gc-messages"></a>Сборщик Мусора сообщений

Сообщения компонента глобального Каталога можно просмотреть, задав системное свойство debug.mono.log значение, которое содержит сборки мусора.

Сборщик Мусора сообщения создаются в том случае, если сборщик Мусора выполняет и предоставляет сведения о том, какая работа сборка Мусора была:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Дополнительные сведения о сборке Мусора, такие как сведения о времени может формироваться путем настройки `MONO_LOG_LEVEL` переменную среды, чтобы `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

В результате (и даже) Дополнительные Mono сообщения, включая этих трех последствий:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

В `GC_BRIDGE` сообщение, `num-objects` — количество объектов моста, рассматривает этот этап и `num_hash_entries` — количество объектов, обработанных при данном вызове кода моста.

В `GC_MINOR` и `GC_MAJOR` сообщений, `total` — это количество времени, во время приостановки мира (потоки не выполняются), хотя `bridge` — количество времени, затраченное в bridge, обработки кода (что имеет дело с виртуальной Машиной Java). Мир *не* приостановлена в процессе обработки моста.

 *В целом*, чем больше значение `num_hash_entries`, больше времени, `bridge` вступят в коллекции и тем больше `total` будет время, затраченное на сбор.



## <a name="global-reference-messages"></a>Сообщений глобальной ссылки

Чтобы включить ведение журнала, глобальной ссылки loggig (gref, Установленное) *debug.mono.log* системное свойство должно содержать *gref, установленное*, например:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android использует Android глобальные ссылки для обеспечения сопоставления между экземплярами Java и связанных управляемых экземпляров, как при вызове метода Java экземпляр Java необходимо указать для Java.

Эмуляторы Android, к сожалению, разрешить только 2000 глобальные ссылки должны существовать во время. Оборудование ограничен гораздо выше 52000 глобальной ссылки. Нижний предел может быть проблематичным, когда выполнение приложения в эмуляторе, поэтому важно знать, *где* правильных экземпляр из может быть очень полезно.

 *Примечание*: глобальный счетчик является внутренним для Xamarin.Android и не учитывает (и не может включают глобальные ссылки, выполняемое другие собственные библиотеки, загружаемые в процесс. Используйте глобальный счетчик для оценки.

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

Существует четыре сообщения последствий:

-  Создание глобальной ссылки: это строки, начинающиеся с *+ g +* и предоставит трассировку стека для создания пути кода.
-  Уничтожение глобальной ссылки: это строки, начинающиеся с *- g-* и может предоставлять трассировку стека для удаления ссылки на глобальные пути кода. Если сборщик Мусора освобождение gref, установленное, будет предоставляться не выполняется трассировка стека.
-  Слабая глобального ссылка создания: это строки, начинающиеся с *+ w +* .
-  Слабая глобального ссылка уничтожения: это строки, начинающиеся с *-w-* .


Во всех сообщениях *grefc* значение является счетчиком глобальные ссылки, создаваемые в Xamarin.Android, хотя *grefwc* значение является счетчиком слабые глобальные ссылки, создаваемые в Xamarin.Android. *Обрабатывать* или *obj дескриптор* значение равно значение дескриптора JNI, а знак после " */*" является типом значения дескриптора: */L* для ссылки на местную, */G* для глобального ссылок и */W* для слабых ссылок на глобальный.

В рамках процесса сборки Мусора, глобальные ссылки (+ g +) преобразуются в слабые ссылки на глобальные (причиной "+" w + и - g-), запускается сборка Мусора на стороне Java и затем слабую ссылку на глобальный проверяется, чтобы см. в разделе, если они были собраны. Если все еще существует, новый gref, установленное создается вокруг слабые ссылки (+ g +, -w-), в противном случае уничтожается слабые ссылки (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Экземпляр Java и инкапсулируется MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Сборка Мусора выполняется...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>Объект существует, как дескриптор! = null
## <a name="wref-turned-back-into-a-gref"></a>wref преобразуются обратно в gref, установленное

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>Объект не существует, как дескриптор == null
## <a name="wref-is-freed-no-new-gref-created"></a>wref является освобожденные, новые gref, установленное создан

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Имеется один «интересный» абсолютно здесь: для целевых объектов под управлением Android, предшествующей 4.0, gref, установленное значение равно на адрес объекта Java в памяти среды выполнения Android. (То есть такое сборщик Мусора не перемещение, умеренная, сборщик, и его прочь прямые ссылки на эти объекты.) Таким образом после + g +, + w +, - g-, + g +, - w-последовательности, полученный gref, установленное будет иметь то же значение, что исходное значение gref, установленное. В результате grepping журналах довольно просто.

Тем не менее, Android 4.0 имеет скользящего сборщика и больше не передает прямые ссылки на среды выполнения Android объекты виртуальной Машины. Следовательно, после + g +, + w +, - g-, + g +, - w-последовательности, gref, установленное значение *будет отличаться*. Если объект выдерживает его, несколько глобальных каталогов, вы перейдете по несколько значений gref, установленное, что усложняет для определения, где экземпляр был выделен из.

### <a name="querying-programmatically"></a>Запрос программным способом

Вы можете запрашивать количество gref, Установленное и WREF, запросив `JniRuntime` объекта.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` — Глобальный счетчик ссылок

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -Счетчик слабых ссылок



## <a name="android-debug-logs"></a>Журналы отладки Android

[Android отладочный журналы](~/android/deploy-test/debugging/android-debug-log.md) могут предоставлять дополнительный контекст о любой ошибки времени выполнения, вы видите.



## <a name="floating-point-performance-is-terrible"></a>С плавающей запятой производительности ужасно!

Кроме того «Мое приложение запускает 10 раз быстрее с отладочную сборку, чем с сборки выпуска!»

Xamarin.Android поддерживает несколько устройств ABI: *armeabi*, *armeabi-v7a*, и *x86*. ABI устройства должно лежать в пределах **свойства проекта > вкладка "приложения" > Поддерживаемые архитектуры**.

Отладочные сборки используйте пакет Android, который предоставляет все ABI и таким образом будет использовать самый быстрый интерфейс ABI для целевого устройства.

Сборки выпуска будет включать только ABI, выбранного на вкладке свойств проекта. Можно выбрать более одного.

*armeabi* используется по умолчанию ABI и имеет самую широкую поддержку устройств. *Тем не менее*, armeabi не поддерживает устройства с несколькими ЦП и оборудования с плавающей запятой, amont прочего. Следовательно приложения, использование среды выполнения выпуска armeabi будут привязаны к единственному ядру и будет использоваться реализация soft-float. Оба варианта может привести к значительно более низкую производительность для вашего приложения.

Если вашему приложению требуется неплохую производительность с плавающей запятой (например, игры), то следует включить *armeabi-v7a* ABI. Вы можете поддерживать только *armeabi-v7a* среды выполнения, на то, что это означает, что старые устройства, которые поддерживают только *armeabi* не сможет запустить приложение.



## <a name="could-not-locate-android-sdk"></a>Не удалось найти пакет SDK для Android

Доступны 2 файлы для скачивания из Google для Android Windows SDK для.
Если установщик .exe, он будет записывать разделы реестра, которые сообщают Xamarin.Android, где он был установлен. Если вы выберите ZIP-файл и распакуйте его, Xamarin.Android не знает, где искать SDK. Чтобы узнать Xamarin.Android, где находится пакет SDK в Visual Studio, выбрав **Сервис > Параметры > Xamarin > Параметры Android**:

[![Расположение пакета SDK для Android в параметрах Xamarin Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE не отображает целевое устройство

Иногда будет пытаться развернуть приложение для устройства, но устройство, которое вы хотите развернуть на не отображается в диалоговом окне Выбор устройства. Это может произойти, когда мост отладки Android решает отдохнуть.

Чтобы определить проблему, найдите [программы adb](~/android/deploy-test/debugging/android-debug-log.md), затем запустите:

```shell
adb devices
```

Если устройство не отображается, необходимо перезапустить сервер Android Debug Bridge так, что устройство можно найти:

```shell
adb kill-server
adb start-server
```

Программное обеспечение для синхронизации HTC может препятствовать **start сервер adb** работать должным образом. Если **start сервер adb** команды не распечатать какой порт, он начинается, закройте программное обеспечение для синхронизации HTC и повторите перезапускается сервер adb.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Не удается запустить указанный исполняемый файл задачи «keytool»

Это означает, что путь не содержит каталог, где находится каталог bin пакета SDK для Java. Проверьте, что вы выполнили эти действия из [установки](~/android/get-started/installation/index.md) руководства.


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe или aresgen.exe завершил работу с кодом 1

Для решения этой проблемы, перейти в Visual Studio и изменить уровень детализации MSBuild, чтобы сделать это, выберите: **Сервис > Параметры > проекта** и **решений > построения** и **запуска > уровень детализации выходных данных сборки проекта MSBuild** и это значение равно **обычный**.

Перестроить и проверьте области вывода Visual Studio, которое должно содержать полные сведения об ошибке.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Не хватает места хранения на устройстве для развертывания пакета

Это происходит, когда не запустить эмулятор из среды Visual Studio. При запуске эмулятора вне Visual Studio, необходимо передать `-partition-size 512` параметры, например

```shell
emulator -partition-size 512 -avd MonoDroid
```

Убедитесь, т. е. При использовании имени правильный симулятор [имя, используемое при настройке имитатор](~/android/get-started/installation/windows.md#device).


## <a name="installfailedinvalidapk-when-installing-a-package"></a>Установка\_FAILED\_НЕДОПУСТИМЫЙ\_APK, при установке пакета

Имена пакетов Android *необходимо* , содержат точку ("*.*"). Измените имя пакета, чтобы оно содержит точку.

-   В Visual Studio:
    -   Щелкните правой кнопкой мыши проект > Свойства
    -   Перейдите на вкладку манифеста Android, в левой части.
    -   Обновите поле имени пакета.
        -   Если вы видите сообщение &ldquo;AndroidManifest.xml не найден. Щелкните, чтобы добавить его. &rdquo;, щелкните ссылку и затем обновите поле имени пакета.
-   В Visual Studio для Mac:
    -   Щелкните правой кнопкой мыши проект > Параметры.
    -   Перейти к сборке и раздел приложения Android.
    -   Изменить поле имени пакета должен содержать ".".




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>Установка\_FAILED\_MISSING\_SHARED\_БИБЛИОТЕКИ при установке пакета

«Общей библиотеки» в данном контексте является *не* общую библиотеку машинного кода (*libfoo.so*) файла; это вместо библиотеки, который должен устанавливаться отдельно на целевом устройстве, например Google Maps.

Пакет Android указывает, какие общие библиотеки требуются с `<uses-library/>` элемент. Если *требуется* библиотека отсутствует на целевом устройстве (например `//uses-library/@android:required` — *true*, используемого по умолчанию), то произойдет сбой установки пакета с *УСТАНОВИТЬ\_ Не удалось\_MISSING\_SHARED\_БИБЛИОТЕКИ*.

Чтобы узнать, какие общие библиотеки являются обязательными, найдите *создан*
**AndroidManifest.xml** файла (например **obj\\Отладка\\android \\AndroidManifest.xml**) и найдите `<uses-library/>` элементов. `<uses-library/>` элементы можно добавить вручную в вашем проекте **свойства\\AndroidManifest.xml** файла и через [UsesLibraryAttribute настраиваемого атрибута](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/).

Например, при добавлении ссылки на сборку *Mono.Android.GoogleMaps.dll* неявно добавляется `<uses-library/>` для общей библиотеки Google Maps.



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>Установка\_FAILED\_обновления\_НЕСОВМЕСТИМЫЕ при установке пакета

Android пакеты имеют три требования:

-   Они должны содержать "." (см. предыдущие записи)
-   Они должны иметь уникальное строковое имя пакета (таким образом соглашение tld обратном порядке, в приложении Android имена, например com.android.chrome для приложения Chrome)
-   При обновлении пакетов, пакет должен иметь тот же ключ подписи.

Таким образом рассмотрим следующую ситуацию:

1.  Сборка и развертывание приложения как приложения отладки
2.  Изменился ключ подписывания, например на использование как выпуска приложения (или так, как вам не нравятся предоставляемых по умолчанию отладке ключ подписывания)
3.  Установите приложение, не удаляя его, во-первых, например Отладка > Запуск без отладки в Visual Studio


В этом случае установка пакета завершится ошибкой с установкой\_FAILED\_обновления\_НЕСОВМЕСТИМЫЕ ошибку, так как имя пакета не изменили при подписывании клавиша выполняла. [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md) также будет содержать сообщение, подобное:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Чтобы устранить эту ошибку, полностью удалите приложение с устройства перед повторной установкой.


## <a name="installfaileduidchanged-when-installing-a-package"></a>Установка\_FAILED\_UID\_ИЗМЕНЕНО при установке пакета

При установке пакета Android, оно назначается *идентификатор пользователя* (UID).
*Иногда*, по соображениям настоящее время неизвестно, при установке через уже установленного приложения, установка завершится ошибкой с `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Чтобы обойти эту проблему, *полностью удалить* пакета Android, либо путем установки приложения из графического пользовательского интерфейса Android целевого объекта, или с помощью `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**НЕ используйте** `adb uninstall -k`, как это будет *Сохранить* данные приложений и таким образом сохранить конфликтующие UID на целевом устройстве.



## <a name="release-apps-fail-to-launch-on-device"></a>Выпуск приложений не запущен на устройстве

Выходной Android журнал отладки будет содержать сообщение, подобное:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Если Да, существует две возможные причины:

1.  Apk-файла не предоставляет ABI, который поддерживает целевое устройство.
    Например apk-файла содержит только двоичные файлы для armeabi-v7a, и целевое устройство поддерживает только armeabi.

2.  [Android ошибки](http://code.google.com/p/android/issues/detail?id=21670). Если это так, удалите приложение, cross пальцы и переустановите приложение.

Чтобы устранить проблему (1), изменить параметры и свойства проекта и [добавлена поддержка требуется интерфейс ABI в список поддерживаемых ABI](~/android/app-fundamentals/cpu-architectures.md). Чтобы определить, какие ABI, необходимо добавить, выполните следующую команду adb от целевого устройства:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

Выходные данные будут содержать основной (и необязательно вторичная) ABI.

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>Для проекта не задано свойство OutPath &ldquo;MyApp.csproj&rdquo;

Это обычно означает, что у вас есть компьютера с HP и переменную среды &ldquo;платформы&rdquo; было присвоено что-то образом MCD или HPD. Эта лицензия конфликтует со свойством платформа MSBuild, который обычно имеет значение &ldquo;любой ЦП&rdquo; или &ldquo;x86&rdquo;. Необходимо будет удалить эту переменную среды с компьютера, для того чтобы использовать MSBuild можно:

-   Панель управления > Система > Дополнительно > переменные среды

Перезапустите Visual Studio или Visual Studio для Mac и попытки перестроить. Что теперь должен работать соответствующим образом.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject не может быть приведен к...

Xamarin.Android 4.x не маршалинга правильно вложенных универсальных типов должным образом. Например, рассмотрим следующие C\# кода, используя [SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


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


Проблема в том, что Xamarin.Android неправильно маршалирует вложенных универсальных типов. `List<IDictionary<string, object>>` Который маршалируется [java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/), но `ArrayList` — содержащий `mono.android.runtime.JavaObject` экземпляров (которой `Dictionary<string, object>` экземпляров) вместо, реализующую [java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/), что может привести следующее исключение:

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

Решение — использовать предоставленный [типы коллекций Java](~/android/internals/api-design.md) вместо `System.Collections.Generic` типы для &ldquo;внутреннее&rdquo; типов. Это приведет к соответствующих типов Java при маршалинге экземпляров. (Приведенный ниже сложнее, чем необходимо, чтобы сократить время существования gref, установленное. Он может быть упрощен до изменения исходного кода с помощью `s/List/JavaList/g` и `s/Dictionary/JavaDictionary/g` Если gref, установленное время существования не придется волноваться.)

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

[Это будет исправлено в будущем выпуске](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).


## <a name="unexpected-nullreferenceexceptions"></a>Непредвиденным исключениям NullReferenceException

Иногда [Android журнал отладки](~/android/deploy-test/debugging/android-debug-log.md) будем рассказывать о исключений NullReferenceException, &ldquo;невозможны,&rdquo; или берутся Mono для кода среды выполнения Android незадолго до из строя приложений:

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

Это может произойти, когда решает отменить процесс, который может происходить по ряду причин, включая gref, Установленное предельного масштаба целевого объекта или действия среды выполнения Android &ldquo;неправильный&rdquo; с JNI.

Чтобы увидеть, если это так, в журнале Android отладка сообщение от процесс аналогичен:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Прервать из-за нехватки глобальной ссылки

Уровень среды выполнения Android JNI поддерживает только ограниченный набор ссылок на объекты JNI функционировал в любой момент времени. При превышении этого предела, прервать вещи.

Gref, Установленное (*глобальной ссылки*) это значение равно 2000 ссылки в эмуляторе и ~ 52000 ссылается на оборудовании.

Вы знаете, что вы начинаете создавать слишком много GREFs при появлении сообщения, такие как это, в журнал отладки Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

По достижении gref, Установленное ограничение, выводится сообщение, например следующие:

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


В приведенном выше примере (который, между прочим, поставляется вместе с [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) проблемы является то, что слишком много Android.Graphics.Point, создаются экземпляры; см. в разделе [комментарий \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) список исправлений для этой конкретной ошибки.

Как правило, полезное решение должно найти какой тип имеет слишком много экземпляров выделенных &ndash; Android.Graphics.Point в дампе выше &ndash; найдите, где созданные в исходный код и удалите их соответствующим образом (таким образом, чтобы их Сокращено время существования Java-Object). Этот способ не всегда подходит (\#685215 является многопоточным, поэтому тривиальные решение позволяет избежать вызова Dispose), но это первое, что необходимо учитывать.

Вы можете включить [gref, Установленное ведение журнала](~/android/troubleshooting/index.md) при создании GREFs и сколько существует.


## <a name="abort-due-to-jni-type-mismatch"></a>Прервать из-за несоответствия типа JNI

Если наката руки JNI кода не исключено, что типы не будет соответствовать правильно, например при попытке вызвать `java.lang.Runnable.run` на тип, который не реализует `java.lang.Runnable`. В этом случае будет существовать сообщение следующего вида в журнал отладки Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Поддержка динамического кода

### <a name="dynamic-code-does-not-compile"></a>Динамический код не компилируется.

Чтобы использовать C\# dynamic в приложении или библиотеке, необходимо добавить в проект System.Core.dll, Microsoft.CSharp.dll и Mono.CSharp.dll.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>В окончательном построении MissingMethodException происходит для динамического кода во время выполнения.

-   Вполне вероятно, что проект приложения не содержит ссылки на System.Core.dll, Microsoft.CSharp.dll или Mono.CSharp.dll. Убедитесь, что существуют ссылки на эти сборки.

    -   Имейте в виду, динамического кода всегда затраты. Если требуется эффективный код, рекомендуется не использовать динамического кода.

-   В первой предварительной версии эти сборки были исключены, если типы в каждой сборке явным образом не используются кодом приложения. Ознакомьтесь с решение этой проблемы: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Проекты, построенные с помощью AOT + LLVM аварийного завершения на x86 устройств

При развертывании приложения, созданного с помощью [AOT + LLVM](~/android/deploy-test/release-prep/index.md) x86 устройств, может появиться сообщение об ошибке исключения, аналогичную следующей:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Это известная проблема в [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). Обойти это можно отключить LLVM.
