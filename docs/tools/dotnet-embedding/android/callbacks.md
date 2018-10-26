---
title: Обратные вызовы на устройстве Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c6eaf4dd90b172053b4b87e3427cfe35213c6727
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117165"
---
# <a name="callbacks-on-android"></a>Обратные вызовы на устройстве Android

Для Java из C# немного *рискованным делом*. Именно имеется *шаблон* для обратных вызовов с C# для Java; Однако это гораздо проще, чем хотелось бы.

Мы обсудим три варианта решения этой задачи обратных вызовов, которые лучше всего подходят для Java:

- Абстрактные классы
- интерфейсов,
- Виртуальные методы

## <a name="abstract-classes"></a>Абстрактные классы

Это самый простой маршрута для обратных вызовов, поэтому я бы рекомендовал использовать `abstract` если только вы пытаетесь получить обратного вызова, работа в простейшей форме.

Давайте начнем с C# мы хотели бы Java для реализации класса:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Ниже приведены сведения для выполнения этой работы.

- `[Register]` Создает имя неплохо пакет на языке Java — вы получите имя пакета создан без него.
- Создание подкласса `Java.Lang.Object` сигналы внедрение .NET для запуска класса через генератор Java Xamarin.Android.
- Пустой конструктор: будут нужны для использования из кода Java.
- `(IntPtr, JniHandleOwnership)` конструктор: Xamarin.Android будете использовать его для создания C#-эквивалент объектов Java.
- `[Export]` сигнализирует о Xamarin.Android предоставлять метод для Java. Мы также можно изменить имя метода, так как мир Java, отметок "нравится" для использования методов нижнего регистра.

Далее создадим C# метод для тестирования сценария:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```
`JavaCallbacks` может быть любой класс, чтобы проверить это, при условии, что он является `Java.Lang.Object`.

Теперь выполните внедрение .NET для сборки .NET, для создания AAR. См. в разделе [руководства по началу работы](~/tools/dotnet-embedding/get-started/java/android.md) подробные сведения.

После импорта файла AAR в Android Studio, давайте написать модульный тест:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```
Поэтому мы:

- Реализации `AbstractClass` на языке Java с помощью анонимного типа
- Убедитесь, что наш экземпляр возвращает `"Java"` из Java
- Убедитесь, что наш экземпляр возвращает `"Java"` изC#
- Добавлен `throws Throwable`, так как C# конструкторы отмечены в настоящее время `throws`

Если б мы выполнили этот модульный тест как-является, произойдет сбой с ошибкой такие как:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Чего не хватает вот `Invoker` типа. Это является подклассом `AbstractClass` , который перенаправляет C# вызовы Java. Если объект Java переходит C# мира и их эквиваленты C# тип является абстрактным, а затем Xamarin.Android автоматически ищет C# тип с суффиксом `Invoker` для использования в пределах C# кода.

Xamarin.Android использует это `Invoker` шаблон для проектов Java привязки, помимо прочего.

Вот наша реализация `AbstractClassInvoker`:
```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Есть довольно много на этой странице мы:

- Добавлен класс с суффиксом `Invoker` который наследуется от класса `AbstractClass`
- Добавлен `class_ref` для хранения ссылки JNI к классу Java, который наследуется от наших C# класса
- Добавлен `id_gettext` для хранения ссылки JNI для Java `getText` метод
- Включенные `(IntPtr, JniHandleOwnership)` конструктор
- Реализации `ThresholdType` и `ThresholdClass` Xamarin.Android сведений о по требованию `Invoker`
- `GetText` необходимые для поиска на Java `getText` метод с соответствующей сигнатурой JNI и назовите его
- `Dispose` именно нужно очистить ссылку `class_ref`

После добавления этого класса и создание новых AAR, наши модульного тестирования проходов. Как вы видите, этот шаблон для обратных вызовов не *идеальной*, но возможно.

Дополнительные сведения по взаимодействию с Java см. в разделе удивительно [Xamarin.Android документации](~/android/platform/java-integration/working-with-jni.md) по этой теме.

## <a name="interfaces"></a>интерфейсов,

Интерфейсы являются так же как абстрактные классы, за исключением одного детализации: Xamarin.Android не создает Java для них. Это обусловлено тем, прежде чем внедрение .NET, не во многих сценариях, где будет реализовывать Java C# интерфейс.

Предположим, у нас есть следующие C# интерфейса:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` Отправляет токену внедрение .NET это интерфейс Xamarin.Android, что в противном случае это точно так же, как `abstract` класса.

Так как Xamarin.Android в настоящее время не создают код Java для этого интерфейса, добавьте следующие Java для вашей C# проекта:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Можно поместить файл в любом месте, но не забудьте задать действие построения `AndroidJavaSource`. Это сигнал, чтобы скопировать его в соответствующую папку для компилируется в файле AAR внедрение .NET.

Далее, `Invoker` реализации будут совпадать довольно:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

После создания файла AAR в Android Studio, можно было бы написать следующие передачи модульного теста:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Виртуальные методы

Переопределение `virtual` в Java имеется возможность, но не удобной.

Предположим, у вас есть следующие C# класса:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Если вы следовали `abstract` класс приведенном выше примере, он будет работать за исключением один элемент данных: _Xamarin.Android не поиска `Invoker`_ .

Чтобы устранить эту проблему, измените C# класса `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Это не идеальное решение, но он получает этот сценарий работал. Xamarin.Android будет окрашена `VirtualClassInvoker` и Java можно использовать `@Override` в методе.

## <a name="callbacks-in-the-future"></a>Обратные вызовы в будущем

Существует несколько вещей мы могли бы улучшить эти сценарии:

1. `throws Throwable` на C# конструкторы фиксируется на это [PR](https://github.com/xamarin/java.interop/pull/170).
1. Создать генератор Java в Xamarin.Android поддерживает интерфейсы.
    - Это избавляет от необходимости добавления исходный файл Java с действием сборки `AndroidJavaSource`.
1. Путь для Xamarin.Android для загрузки `Invoker` для виртуальных классов.
    - Это избавляет от необходимости отмечать класс в нашем `virtual` пример `abstract`.
1. Создать `Invoker` автоматически классы для внедрение .NET
    - Это будет сложным, но возможно. Xamarin.Android, уже умеет делать что-то подобное этому, для проектов Java привязки.

Есть много работы, чтобы сделать здесь, но эти усовершенствования внедрение .NET возможны.

## <a name="further-reading"></a>Дополнительные сведения

* [Начало работы в Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Предварительное исследование Android](~/tools/dotnet-embedding/android/index.md)
* [Ограничения внедрение .NET](~/tools/dotnet-embedding/limitations.md)
* [Вносящая вклад в проект с открытым кодом](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
