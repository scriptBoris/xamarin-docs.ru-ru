---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Использование библиотек C/C++ с помощью Xamarin
description: 'Visual Studio для Mac можно использовать для построения и интегрировать кода C/C++ для кросс платформенных мобильных приложений для Android и iOS, с помощью Xamarin и C#. В этой статье объясняется, как настроить и отладка проекта C++ в приложении Xamarin.'
author: mikeparker104
ms.author: miparker
ms.date: 12/17/20178
---
# <a name="use-cc-libraries-with-xamarin"></a>Использование библиотек C/C++ с помощью Xamarin

## <a name="overview"></a>Обзор

Xamarin позволяет разработчикам для создания кроссплатформенных собственных мобильных приложений с помощью Visual Studio. Как правило C# привязки используются для предоставления существующие компоненты платформы для разработчиков. Однако бывают случаи, когда потребности приложения Xamarin для работы с существующими базы кода. Иногда команды просто нет времени, бюджета и ресурсы к порту a больших, тщательно протестированного и высокой степенью оптимизации базы кода для C#.

[Visual C++ для кросс платформенной мобильной разработки](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) позволяет C/C++ и C# кода для сборки в качестве части этого же решения, предлагает множество преимуществ, включая унифицированный процесс отладки. Корпорация Microsoft использовала C/C++ и Xamarin таким образом для разработки приложений, таких как [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) и [камеры Pix](https://www.microsoft.com/microsoftpix).

Тем не менее в некоторых случаях имеется желание (или требование) чтобы сохранить существующие средства C/C++ и процессов на месте и библиотеки кода, отделены от приложения, рассматривая библиотеки, как если бы аналогичную сторонних компонентов. В этих случаях задача не только предоставляет нужные элементы для C# , но Управление библиотекой как зависимость. И, само собой, автоматизация как большая часть этого процесса можно.  

Здесь описаны более общий подход, в этом сценарии и рассматривается простой пример.

## <a name="background"></a>Фон

C/C++ считается кросс платформенных язык, но необходимо соблюдать осторожность в убедитесь, что исходный код действительно платформах, используя только C/C++ поддерживает все компиляторы целевой и содержащее незначительной или нулевой платформу условно включены или код, специфичные для компилятора.

В конечном счете код необходимо скомпилировать и успешно выполнена на всех целевых платформах, поэтому это сводится к общность разных платформах (и компиляторы) целевой платформы. Проблемы по-прежнему могут быть вызваны незначительные отличия между компиляторы и поэтому тщательное тестирование (предпочтительно автоматизированное) на каждой целевой платформы становится все более важным.  

## <a name="high-level-approach"></a>Общий подход

На приведенном ниже рисунке представляет четыре этапа подход, используемый для преобразования исходного кода C/C++ в кросс платформенной библиотекой Xamarin, которые являются общими с помощью NuGet и затем используется в приложении Xamarin.Forms.
 

![Общий случай использования C/C++ с помощью Xamarin](images/cpp-steps.jpg)

Ниже приведены четыре этапа:

1.  Компиляция исходного кода C/C++ в собственные библиотеки для конкретных платформ.
2.  Упаковки собственные библиотеки с помощью решения Visual Studio.
3.  Упаковка и отправка пакета NuGet для оболочки .NET.
4.  Использование пакета NuGet из приложения Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Этап 1. Компиляция исходного кода C/C++ в собственные библиотеки для конкретных платформ

Цель этого этапа — для создания собственных библиотек, которые могут быть вызваны C# программы-оболочки. Это может или не могут быть необходимы в зависимости от конкретной ситуации. Многие средства и процессы, которые можно включить в этом распространенном сценарии необходимо выходят за рамки этой статьи. Основные рекомендации, C/C++ codebase синхронизирован с любой собственный код оболочки, достаточно модульное тестирование, хранение и автоматизации сборки. 

Библиотеки в пошаговой инструкции были созданы с помощью Visual Studio Code с сопутствующей сценарий оболочки. Расширенная версия этого пошагового руководства можно найти в [репозиторий Mobile CAT GitHub](https://github.com/xamarin/mobcat/blob/dev/samples/cppwithxamarin/README.md) , посвященной этой части примера более подробно. Собственные библиотеки которые обрабатываются как зависимость стороннего в этом случае Однако на этом этапе проиллюстрирован для контекста.


Для простоты пошагового руководства предназначено только подмножество архитектур. Для iOS для создания единого fat двоичных из отдельных двоичных файлов конкретной архитектуры используется программа lipo. Android будет использовать динамический двоичные файлы с расширением .so и операций ввода-вывода будет использовать статический fat двоичных с расширением .a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Этап 2. Упаковки собственные библиотеки с помощью решения Visual Studio

Следующий этап заключается в том, программы-оболочки собственные библиотеки, чтобы легко используются из .NET. Это делается с помощью решения Visual Studio с четырьмя проектами. Общий проект содержит общий код. Проекты, предназначенные для каждого из Xamarin.Android, Xamarin.iOS и .NET Standard позволяют библиотеки должно быть указано в виде независимых от платформы.

В программы-оболочки используется[фокус с подменой](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/), "описываемого Betts пол. Это не единственный способ, но он упрощает ссылку на эту библиотеку и позволяет избежать необходимости явно управлять реализации платформы в рамках самого приложения. Фокус по сути является обеспечение, что целевые объекты (.NET Standard, Android, iOS) совместно используют одно пространство имен, имя сборки и структуру класса. Так как NuGet всегда будет предпочитать библиотеку платформы, версии .NET Standard никогда не используется во время выполнения.

Большая часть работы на этом этапе основное внимание уделяется с помощью P/Invoke для вызова методов собственной библиотеки и управление ссылками на базовые объекты. Целью является для предоставления функциональных возможностей библиотеки потребителю абстрагируясь out любой сложности. Разработчикам Xamarin.Forms не обязательно должны иметь практические знания о внутреннем устройстве неуправляемые библиотеки. Все должно быть, как они используют любые другие управляемые C# библиотеки.

В конечном счете выходные данные этого этапа — это набор библиотек .NET, по одному на целевой объект, а также nuspec документ, который содержит сведения, необходимые для создания пакета на следующем шаге.

**Этап 3. Упаковка и передача пакета NuGet для оболочки .NET**

Третий этап создание пакета NuGet с помощью артефактов сборки из предыдущего шага. Результат с этого шага — пакет NuGet, который может использоваться из приложения Xamarin. Пошаговое руководство использует локальный каталог для использования в качестве веб-канал NuGet. В рабочей среде этот шаг следует опубликовать пакет с открытым или частных NuGet веб-канала и должен быть полностью автоматизирован.

**Этап 4. Использование пакета NuGet из приложения Xamarin.Forms**

Последним шагом является ссылаться и использовать пакет NuGet из приложения Xamarin.Forms. Этот сценарий требует настройки NuGet веб-канала в Visual Studio для использования веб-канал, определенный на предыдущем шаге.

После настройки веб-канала пакета необходимо ссылаться на каждый проект в приложении Xamarin.Forms между различными платформами. «Фокус заманить и подменить» предоставляет одинаковые интерфейсы, функциональные возможности собственной библиотеки можно вызывать с помощью кода, определенные в одном месте.

Репозиторий исходного кода содержит [список Дополнительные материалы](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up) , включены статьи о настройке частного NuGet, веб-канала в DevOps в Azure и отправки пакета канала. При этом немного больше времени установки, чем локальный каталог, в среде командной разработки лучше такого рода веб-канала.

## <a name="walk-through"></a>Пошаговое руководство

Инструкции относятся к **Visual Studio для Mac**, но структура работает **Visual Studio 2017** также.

### <a name="prerequisites"></a>Предварительные требования

Для выполнения этой процедуры требуется разработчика:

-   [NuGet командной строки (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

-   [*Visual Studio* *для Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Активный [**учетной записи разработчика Apple**](https://developer.apple.com/) необходим для развертывания приложений на iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Создание собственных библиотек (этап 1)

Функциональные возможности собственной библиотеки основан на примере из [Пошаговое руководство: Создание и использование статической библиотеки (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

В этом пошаговом руководстве пропускает первого этапа создания собственные библиотеки, поскольку библиотеке предоставляется как зависимость независимых производителей в этом сценарии. Предварительно скомпилированные библиотеки собственного включены вместе с [пример кода](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin) или может быть [загрузки](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) напрямую.

### <a name="working-with-the-native-library"></a>Работа с собственной библиотеки

Исходный *MathFuncsLib* пример включает один класс с именем MyMathFuncs со следующим определением: 

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

Дополнительный класс определяет функции-оболочки, позволяющие потребитель .NET для создания, удаления и взаимодействовать с основной собственный класс MyMathFuncs.

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

Он будет эти функции-оболочки, которые используются на [Xamarin](https://visualstudio.microsoft.com/xamarin/) стороне.

## <a name="wrapping-the-native-library-stage-2"></a>Перенос собственной библиотеки (этап 2)

На этом этапе требуется [предварительно скомпилированные библиотеки](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) описано в разделе [выше](https://docs.microsoft.com/xamarin/cross-platform/cpp/index).

### <a name="creating-the-visual-studio-solution"></a>Создание решения Visual Studio

1. В **Visual Studio для Mac**, нажмите кнопку **новый проект** (из *страницу приветствия*) или **новое решение** (из *файл* меню).
2. Из **новый проект** окно, выберите **общий проект** (изнутри *Многоплатформенность > библиотеки*) и нажмите кнопку **Далее**.
3. Обновите следующие поля, а затем нажмите кнопку **создать**:

    - **Имя проекта:** MathFuncs.Shared  
    - **Имя решения:** MathFuncs  
    - **Расположение:** Используйте значение по умолчанию расположение сохранения (или выбора вместо)   
    - **Создание проекта в каталоге решения:** Присвойте этому параметру проверки
4. Из **обозревателе решений**, дважды щелкните **MathFuncs.Shared** проекта и перейдите к **основные параметры**.
5. Удалить **. Общие** из **пространство имен по умолчанию** , ему будет присвоено **MathFuncs** , затем выберите **ОК**.
6. Откройте **MyClass.cs** (созданного с помощью шаблона), затем переименуйте класс и имя файла для **MyMathFuncsWrapper** и изменить пространство имен для **MathFuncs**.
7. **ЩЕЛКНУТЬ при нажатой клавише УПРАВЛЕНИЯ** решение **MathFuncs**, затем выберите **Добавление нового проекта...**  из **добавить** меню.
8. Из **новый проект** окно, выберите **библиотеки .NET Standard** (изнутри *Многоплатформенность > библиотеки*) и нажмите кнопку **Далее**.
9. Выберите **.NET Standard 2.0** и нажмите кнопку **Далее**.
10. Обновите следующие поля, а затем нажмите кнопку **создать**:

    - **Имя проекта:** MathFuncs.Standard  
    - **Расположение:** Используйте тот же сохранить расположении, что общий проект   

11. Из **обозревателе решений**, дважды щелкните **MathFuncs.Standard** проекта.
12. Перейдите к **основные параметры**, затем обновите **пространство имен по умолчанию** для **MathFuncs**.
13. Перейдите к **вывода** параметры, затем обновите **имя сборки** для **MathFuncs** щелкните **ОК**.
14. Перейдите к **компилятора** изменить параметры, **конфигурации** для **выпуска**, задание **отладочная информация** для  **Символы только**.
15. Удалить **Class1.cs/Getting работы** из проекта (если один из них была частью шаблона).
16. **ЩЕЛКНУТЬ при нажатой клавише УПРАВЛЕНИЯ** в проекте **зависимости/референты** папку, нажмите кнопку **изменить ссылки**.
17. Выберите **MathFuncs.Shared** из **проекты** , а затем нажмите кнопку **ОК**.
18. Повторите шаги с 7-17 (игнорирование шаг 9) с помощью следующих конфигураций:

    | **ИМЯ ПРОЕКТА**  | **ИМЯ ШАБЛОНА**   | **МЕНЮ "НОВЫЙ ПРОЕКТ"**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Библиотека классов       | Android > библиотеки      |
    | MathFuncs.iOS     | Привязка библиотеки     | iOS > библиотеки          |

19. Из **обозревателе решений**, дважды щелкните **MathFuncs.Android** проекта, а затем перейдите к **компилятора** параметры.

20. С помощью **конфигурации** присвоено **Отладка**, изменить **определить символы** для включения **Android;**.

21. Изменение **конфигурации** для **выпуска**, затем измените **определить символы** для включения **Android;**.

22. Повторите шаги с 19 – 20 для **MathFuncs.iOS**, редактирования **определить символы** для включения **iOS;** вместо **Android;** в обоих случаях.

23. Построение решения **выпуска** конфигурации (**УПРАВЛЕНИЯ + COMMAND + B**) и проверьте, что все три выходных данных сборки (Android, iOS, .NET Standard) (в папке bin соответствующего проекта) одного и того же имя **MathFuncs.dll**.

На этом этапе решения должны иметь три целевых объектов, каждый для Android, iOS и .NET Standard и общий проект, на который ссылается каждый из трех целевых объектов. Они должны использовать те же по умолчанию пространство имен и выходные данные сборки с тем же именем. Это необходимо для подхода «с подменой», упомянутых ранее.

### <a name="adding-the-native-libraries"></a>Добавление собственных библиотек

Процесс добавления собственных библиотек в решение оболочки немного отличается, Android и iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Собственные ссылки для MathFuncs.Android

1. **CONTROL + ЩЕЛКНИТЕ** на **MathFuncs.Android** проекта, а затем выберите **новую папку** из **добавить** меню, назовите его **libs**.

2. Для каждого **ABI** (двоичный интерфейс приложений), **УПРАВЛЕНИЯ + ЩЕЛЧОК** на **libs** папку, нажмите кнопку ** Новая папка** из **добавить** меню, назовите его после этого соответствующие **ABI**. В этом случае:

    - arm64-v8a
    - armeabi-v7a;
    - x86
    - x86_64  

    > [!NOTE]
    > Более подробные сведения см. в разделе [ЦП и архитектуры](https://developer.android.com/ndk/guides/arch) раздел из [NDK руководство разработчика](https://developer.android.com/ndk/guides/), в частности раздел об адресации [машинного кода в пакеты приложений ](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Проверьте структуру папок:  

    ```
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Добавить соответствующие **.so** библиотеки для каждого из **ABI** папок, основываясь на сопоставлении, следующие:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Для добавления файлов, **УПРАВЛЕНИЯ + ЩЕЛЧОК** на папку, представляющую соответствующих **ABI**, затем выберите **добавить файлы...**  из **добавить** меню. Выберите соответствующую библиотеку (из **PrecompiledLibs** directory) нажмите кнопку **откройте** и нажмите кнопку **ОК** оставить параметр по умолчанию, чтобы *копирования файл в каталог*.

5. Для каждого из **.so** файлы, **УПРАВЛЕНИЯ + ЩЕЛЧОК** выберите **EmbeddedNativeLibrary** вариант **действие при построении** меню.

Теперь **libs** папки должен выглядеть следующим образом:

```bash
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86 
        - libMathFuncs.so
    - x86_64 
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>Собственные ссылки для MathFuncs.iOS

1. **CONTROL + ЩЕЛКНИТЕ** на **MathFuncs.iOS** проекта, а затем выберите **добавить собственную ссылку** из **добавить** меню. 
2. Выберите **libMathFuncs.a** библиотеки (из библиотек или ios, в разделе **PrecompiledLibs** directory) нажмите кнопку **Open** 
3. **CONTROL + ЩЕЛКНИТЕ** на **libMathFuncs** файла (в **собственные ссылки** папку, нажмите кнопку **свойства** из меню  
4. Настройка **собственную ссылку** таким образом, чтобы они проверяются (отображается значок тактов) в **свойства** панели:
        
    - Принудительная загрузка
    - Is C++
    - Интеллектуальная связь 

    > [!NOTE]
    > С помощью типа проекта библиотеки привязки вместе с [собственную ссылку](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) внедряет статическую библиотеку и позволяет ей быть автоматически связываются с приложением Xamarin.iOS, которое ссылается на нее (даже когда он включается с помощью пакета NuGet). 

5. Откройте **ApiDefinition.cs**, удаление шаблонную закомментированном коде (останется только **MathFuncs** пространство имен), затем выполните этот шаг для **Structs.cs** 

    > [!NOTE]
    > Проект библиотеки привязки требуются эти файлы (с *ObjCBindingApiDefinition* и *ObjCBindingCoreSource* действия при сборке) для сборки. Тем не менее мы напишем код, для вызова нашей собственной библиотеки вне эти файлы в виде, могут совместно использоваться цели библиотеки iOS и Android с помощью стандартных P/Invoke.

### <a name="writing-the-managed-library-code"></a>Написание кода управляемой библиотеки

Теперь запишите C# код для вызова собственной библиотеки. Цель — скрыть любой сложности. Потребитель не нужен любой опыт работы внутренних компонентов собственной библиотеки или концепций P/Invoke.  

#### <a name="creating-a-safehandle"></a>Создание SafeHandle

1. **CONTROL + ЩЕЛКНИТЕ** на **MathFuncs.Shared** проекта, а затем выберите **Add File...**  из **добавить** меню. 
2. Выберите **пустой класс** из **новый файл** окна, назовите его **MyMathFuncsSafeHandle** и нажмите кнопку **New**
3. Реализуйте **MyMathFuncsSafeHandle** класса:

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Объект [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) является более предпочтительным для работы с неуправляемыми ресурсами в управляемом коде. Это упрощает массу стереотипного кода, связанные с жизненным циклом критические завершения и объекта. Владелец этого дескриптора впоследствии можно расценивать это как любой другой управляемый ресурс и не получат реализации полного [шаблон уничтожаемых объектов](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose). 

#### <a name="creating-the-internal-wrapper-class"></a>Создание внутренний класс-оболочка

1. Откройте **MyMathFuncsWrapper.cs**, заменить его на внутренний статический класс

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. В этом же файле добавьте в класс следующий условный оператор:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Этот параметр задает **DllName** постоянное значение, основанное на ли сборки библиотеки для **Android** или **iOS**. Это позволяет устранить различные соглашения об именовании, используемые каждой соответствующей платформы, но и тип библиотеки, в этом случае использовать. Android использует динамической библиотеки и поэтому ожидает, что имя файла, включая расширение. Для iOS "*__Internal*" является обязательным, так как мы используем статической библиотеки.

3. Добавьте ссылку на **System.Runtime.InteropServices** в верхней части **MyMathFuncsWrapper.cs** файла

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Добавьте методы создания оболочки для создания и реализации **MyMathFuncs** класса:

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Мы передаем наш константа **DllName** для **DllImport** атрибута вместе с **EntryPoint** явно которой среда выполнения .NET имя вызываемой функции в этой библиотеке. С технической точки зрения, нам не нужно предоставить **EntryPoint** значение, если были идентична неуправляемый наших имен управляемого метода. Если значение не указано, будет использоваться имя управляемого метода как **EntryPoint** вместо этого. Тем не менее лучше указывать явно.  

5. Добавьте методы программы-оболочки, чтобы мы могли работать с **MyMathFuncs** класса, используя следующий код:

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > Для параметров в этом примере мы используем простые типы. Поскольку маршалинга является побитовое копирование-в этом случае он не требует дополнительных действий с нашей стороны. Также Обратите внимание на использование **MyMathFuncsSafeHandle** класса вместо стандартного **IntPtr**. **IntPtr** автоматически сопоставляется с **SafeHandle** в рамках процесса маршалинга.

6. Убедитесь, что готовый **MyMathFuncsWrapper** класс отображается как ниже:

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Завершение работы MyMathFuncsSafeHandle-класс
1. Откройте **MyMathFuncsSafeHandle** класса, перейдите к заполнитель **TODO** комментарий в **ReleaseHandle** метод:
    ```csharp
    // TODO: Release the handle here
    ```
2. Замените **TODO** строки:

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Класс MyMathFuncs

Теперь, когда завершена оболочки, создайте класс MyMathFuncs, который будет управлять ссылку на неуправляемый объект C++ MyMathFuncs.  

1. **CONTROL + ЩЕЛКНИТЕ** на **MathFuncs.Shared** проекта, а затем выберите **Add File...**  из **добавить** меню. 
2. Выберите **пустой класс** из **новый файл** окна, назовите его **MyMathFuncs** и нажмите кнопку **New**
3. Добавьте следующие члены для **MyMathFuncs** класса:

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Реализуйте конструктор для класса, поэтому он создает и сохраняет дескриптор для собственного **MyMathFuncs** объекта при создании экземпляра класса:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Реализуйте **IDisposable** интерфейс, используя следующий код:

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. Реализуйте **MyMathFuncs** методов с помощью **MyMathFuncsWrapper** классом для выполнения реальной работы за кулисами, передавая указатель, мы сохранили на базовый неуправляемый объект. Код должен иметь следующий вид:

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>Создание метаданных nuspec

Чтобы получить библиотеку комплектуется и распространяется через NuGet, решение должно **nuspec** файла. Это определит, какие из полученные сборки будут включены для каждой поддерживаемой платформы.

1.  **CONTROL + ЩЕЛКНИТЕ** решение **MathFuncs**, нажмите кнопку **добавить папку решения** из **добавить** меню, назвав его  **SolutionItems**.
2.  **ЩЕЛКНУТЬ при нажатой клавише УПРАВЛЕНИЯ** на **SolutionItems** папку, затем выберите **новый файл... **  из **добавить** меню.
3.  Выберите **пустой XML-файл** из **новый файл** окна, назовите его **MathFuncs.nuspec** и нажмите кнопку **Новый**.
4.  Обновление **MathFuncs.nuspec** с метаданными основного пакета, которое будет отображаться для **NuGet** потребителя. Пример:


    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    >  См. в разделе [Справочник по файлу nuspec](https://docs.microsoft.com/nuget/reference/nuspec) документ более подробные данные в схему, используемую для этого манифеста.

5. Добавить `<files>` как дочерний элемент элемента `<package>` элемент (непосредственно под `<metadata>`), указывая каждый файл с отдельным `<file>` элемент:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->        

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > При установке пакета в проект, а там, где несколько сборок с тем же именем, указанным, NuGet будет эффективно выберите сборки, наиболее подходящую для данной платформы.

6. Добавить `<file>` элементы для **Android** сборки:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Добавить `<file>` элементы для **iOS** сборки:

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Добавить `<file>` элементы для **netstandard2.0** сборки:

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Проверьте **nuspec** манифеста:

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>
    
        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > Этот файл указывает пути вывода сборки из **выпуска** сборки, поэтому убедитесь, что решение создать, используя эту конфигурацию.

На этом этапе решение содержит 3 сборок .NET и вспомогательного **nuspec** манифеста.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Распространение оболочки .NET с помощью NuGet

Следующим шагом является упаковки и распространения пакета NuGet, поэтому он может быть легко способности, используемый приложением и управляется как зависимость. Упаковки и потребления может быть выполнено в одном решении, но распространение библиотеку с помощью средства NuGet в разделение и позволяет нам управлять их баз кода независимо друг от друга.

### <a name="preparing-a-local-packages-directory"></a>Подготовка каталога локальные пакеты

Самая простая форма веб-канал NuGet это локальный каталог:

1.  В **Finder**, перейдите к удобной папке. Например **/пользователи**.
2.  Выберите **новую папку** из **файл** меню, таких как указание понятного имени **local канала nuget**.

### <a name="creating-the-package"></a>Создание пакета

1.  Задайте **конфигурация сборки** для **выпуска**и выполнить сборки с использованием **COMMAND + B**.
2.  Откройте **терминалов** и укажите путь к папке, содержащей **nuspec** файла.
3.  В **терминалов**, выполнение **пакет nuget** указания команды **nuspec** файл **версии**  (например, 1.0.0) и **OutputDirectory** с помощью папки, созданной на [предыдущего шага](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), то есть ** Local канала nuget**. Пример:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Подтвердите** , **MathFuncs.1.0.0.nupkg** будет создана в **local канала nuget** каталога.

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[НЕОБЯЗАТЕЛЬНО] С помощью частной веб-канала NuGet с помощью DevOps в Azure

Более надежный способ описан в [приступить к работе с пакетами NuGet в Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), который показывает, как для создания закрытого канала и отправки пакета (созданный на предыдущем шаге) для канала.

Это идеальный вариант для этого рабочего процесса, полностью автоматизирована, например с помощью [конвейеры Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Дополнительные сведения см. в разделе [приступить к работе с Azure конвейеры](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Использование оболочки .NET из приложения Xamarin.Forms
Для выполнения данного пошагового руководства, создайте **Xamarin.Forms** опубликованные приложения для использования пакета только в локальную **NuGet** веб-канала.

### <a name="creating-the-xamarinforms-project"></a>Создание **Xamarin.Forms** проекта

1. Откройте новый экземпляр класса **Visual Studio для Mac**. Это можно сделать из **терминалов**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. В **Visual Studio для Mac**, нажмите кнопку **новый проект** (из *страницу приветствия*) или **новое решение** (из *файл* меню).
3. Из **новый проект** окно, выберите **приложение с пустыми формами** (изнутри *Многоплатформенность > приложение*) и нажмите кнопку **Далее**.
4. Обновите следующие поля, а затем нажмите кнопку **Далее**:

    - **Имя приложения:** MathFuncsApp.
    - **Идентификатор организации:** Использовать обратное пространство имен, например, _com. {your_org}_.
    - **Целевые платформы:** Используйте значение по умолчанию (Android и iOS целевые объекты).
    - **Общий код:** Установите .NET Standard (решение «Общая библиотека» становится возможной, но выходит за рамки этого пошагового руководства).

5. Обновите следующие поля, а затем нажмите кнопку **создать**:

    - **Имя проекта:** MathFuncsApp.
    - **Имя решения:** MathFuncsApp.  
    - **Расположение:** Используйте значение по умолчанию расположение сохранения (или выбора альтернативы).

6. В **обозревателе решений**, **УПРАВЛЕНИЯ + ЩЕЛЧОК** на целевом объекте (**MathFuncsApp.Android** или **MathFuncs.iOS**) для начального тестирования, затем Выберите **Назначить запускаемым проектом**.
7. Выберите предпочтительный **устройства** или **симулятор**/**эмулятор**. 
8. Запустите решение (**КОМАНДУ + ВОЗВРАЩАЕМОЕ**) чтобы проверить, что шаблонную **Xamarin.Forms** выполнено построение проекта и работает нормально. 

    > [!NOTE]
    > **iOS** (в частности имитатор) обычно имеют минимальное время сборки/развертывания.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Добавление пакета NuGet локального веб-канал NuGet конфигурации

1. В **Visual Studio**, выберите **предпочтения** (из **Visual Studio** меню).
2. Выберите **источников** из **NuGet** раздела, а затем щелкните **добавить**.
3. Обновите следующие поля, а затем нажмите кнопку **добавить источник**:

    - **Имя:** Введите понятное имя, например, Local-пакеты.  
    - **Расположение:** Укажите **local канала nuget** папки, созданной на [предыдущего шага](#preparing-a-local-packages-directory).

    > [!NOTE]
    > В этом случае нет необходимости для указания **Username** и **пароль**. 

4. Нажмите кнопку **ОК**.

### <a name="referencing-the-package"></a>Указание ссылок на пакет

Повторите перечисленные ниже шаги для каждого проекта (**MathFuncsApp**, **MathFuncsApp.Android**, и **MathFuncsApp.iOS**).

1. **CONTROL + ЩЕЛКНИТЕ** на проект, а затем выберите **добавить пакеты NuGet...**  из **добавить** меню.
2. Поиск **MathFuncs**. 
3. Проверьте **версии** пакета является **1.0.0** и другие сведения отображаются, такие как должным образом **Title** и **описание**, то есть , *MathFuncs* и *образец библиотеки-оболочки C++*. 
4. Выберите **MathFuncs** пакета, а затем щелкните **Add Package**.

### <a name="using-the-library-functions"></a>С помощью функции библиотеки

Теперь, со ссылкой на **MathFuncs** пакет в каждый из проектов, эти функции доступны для C# кода.

1.  Откройте **MainPage.xaml.cs** изнутри **MathFuncsApp** распространенных **Xamarin.Forms**проекта (ссылается обоих **MathFuncsApp.Android** и **MathFuncsApp.iOS**).
2.  Добавить **с помощью** инструкций для **System.Diagnostics** и **MathFuncs** в верхней части файла:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Объявите экземпляр `MyMathFuncs` класс в верхней части `MainPage` класса:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Переопределить `OnAppearing` и `OnDisappearing` методы из `ContentPage` базового класса:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. Обновление `OnAppearing` метод для инициализации `myMathFuncs` переменная объявлена ранее:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Обновление `OnDisappearing` метод для вызова `Dispose` метод `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Реализовать частный метод, именуемый **TestMathFuncs** следующим образом:

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. Наконец, вызовите `TestMathFuncs` в конце `OnAppearing` метод:

    ```csharp
    TestMathFuncs();
    ```

9. Запустите приложение для каждой целевой платформы и проверить выходные данные в **выходные данные приложения** панели выглядит следующим образом:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > При возникновении "*DLLNotFoundException*" при тестирование на платформе Android или ошибку построения для iOS, не забудьте проверить, что архитектура ЦП/эмулятора или симулятора устройства при использовании совместим с подмножество, которое мы решили Поддержка. 

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как создать приложение Xamarin.Forms, которое использует собственные библиотеки посредством общих оболочки .NET распространение с помощью пакета NuGet. Пример, приведенный в этом пошаговом руководстве намеренно очень упрощен, чтобы упростить демонстрируют подход. Реальное приложение будет иметь дело сложностей, таких как обработка исключений, обратные вызовы, маршалинг более сложных типов и связывание с другими библиотеками зависимостей. Следует принимать во внимание — это процесс, который развития кода C++ координируемых и синхронизированы с помощью программы-оболочки и клиентских приложений. Этот процесс может зависеть от одного или обоих этих угроз, являются ли ответственность за одной командой. В любом случае автоматизации — это настоящее преимущество. Ниже приведены некоторые ресурсы, предоставляя дополнительные материалы устраняет некоторые основные понятия, а также соответствующие файлы для загрузки. 

### <a name="downloads"></a>Загрузки

- [Средства командной строки (CLI NuGet)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Примеры

- [Hyperlapse кросс платформенной разработки мобильных приложений на C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++ и Xamarin)](https://blog.xamarin.com/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San Анджелес пример порта](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)

### <a name="further-reading"></a>Дополнительные сведения

[Статьи, относящиеся к содержимому этой записи](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
