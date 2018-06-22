---
title: Обратные вызовы на Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3f18516643c00dc67fe533ecab00e1f415eb5c46
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922826"
---
# <a name="callbacks-on-android"></a>Обратные вызовы на Android

Вызов для Java из C# вызывает *рискованным делом*. Иными словами — *шаблон* для обратных вызовов с C# для Java; Однако это гораздо проще, чем нужно.

Описание трех параметров для выполнения обратных вызовов, наиболее подходящих для Java.

- Абстрактные классы
- интерфейсов,
- Виртуальные методы

## <a name="abstract-classes"></a>Абстрактные классы

Это простой маршрут для обратных вызовов, поэтому я рекомендую с помощью `abstract` , если только вы пытаетесь получить обратного вызова, работа в простейшей форме.

Начнем с класса C#, мы хотели бы Java для реализации:

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

Ниже приведены сведения, чтобы обеспечить работоспособность.

- `[Register]` Создает имя пакета для работы с низким приоритетом в Java — вы получите имя пакета создан без него.
- Создание подклассов `Java.Lang.Object` запуск класса с помощью генератора Java Xamarin.Android сигналов для внедрения .NET.
- Пустой конструктор: будут нужны для использования из кода Java.
- `(IntPtr, JniHandleOwnership)` конструктор: Xamarin.Android будут использовать для создания C#-эквивалент объектов Java.
- `[Export]` сообщает Xamarin.Android представление метода для Java. Мы также можно изменить имя метода, поскольку Java world хочет использовать методы в нижнем регистре.

Далее создадим метод C# для тестирования сценария:

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
`JavaCallbacks` может быть любой класс, чтобы проверить это, как будет `Java.Lang.Object`.

Теперь запустите .NET внедрения в сборку .NET для создания AAR. В разделе [руководство по началу работы](~/tools/dotnet-embedding/get-started/java/android.md) подробные сведения.

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

- Реализации `AbstractClass` в Java с помощью анонимного типа
- Убедившись, что наш экземпляр возвращает `"Java"` из Java
- Убедившись, что наш экземпляр возвращает `"Java"` из C#
- Добавлен `throws Throwable`, поскольку в настоящее время отмечаются конструкторы C# `throws`

Если мы запустили это модульного теста в виде-является, произойдет сбой с ошибкой такие как:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Что такое отсутствующие вот `Invoker` типа. Это является подклассом `AbstractClass` , пересылающий C# вызывает Java. Если объект Java вводит world C# и эквивалентный тип C# является абстрактным, а затем Xamarin.Android автоматически ищет тип C# с суффиксом `Invoker` для использования в коде C#.

Xamarin.Android использует это `Invoker` шаблон для проектов Java привязки, кроме всего прочего.

Вот реализацию `AbstractClassInvoker`:
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

Есть довольно много переходом здесь мы:

- Добавить класс с суффиксом `Invoker` который наследуется от класса `AbstractClass`
- Добавлен `class_ref` для хранения JNI ссылка на класс Java который наследуется от класса нами класса C#
- Добавлен `id_gettext` для хранения ссылки на Java JNI `getText` метод
- Включенные `(IntPtr, JniHandleOwnership)` конструктор
- Реализован `ThresholdType` и `ThresholdClass` Xamarin.Android для получения сведений о по требованию `Invoker`
- `GetText` требуется выполнить поиск на Java `getText` метод с соответствующей сигнатурой JNI и назовите его
- `Dispose` требуется только для очистки ссылку на `class_ref`

После добавления этого класса и создания новых AAR, нашей модульного теста передает. Как вы видите этот шаблон для обратных вызовов не *идеальный*, но возможно.

Сведения о взаимодействии Java см. в разделе передовые [Xamarin.Android документации](~/android/platform/java-integration/working-with-jni.md) по этой теме.

## <a name="interfaces"></a>интерфейсов,

Интерфейсы являются так же как абстрактные классы, за исключением один элемент данных: Xamarin.Android не создает Java для них. Это потому, что до внедрения .NET, не будут множество сценариев, где Java будет реализовывать интерфейс C#.

Предположим, что у нас есть следующий интерфейс C#:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` сигнал внедрения .NET интерфейс Xamarin.Android, что в противном случае это точно таким же, как `abstract` класса.

Поскольку Xamarin.Android не создаст в настоящее время код Java для этого интерфейса, добавьте следующие Java в проект C#:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Поместите файл в любом месте, но убедитесь в том задать действие построения `AndroidJavaSource`. Это даст сигнал внедрения .NET, чтобы скопировать его в соответствующую папку на компилируется в файле AAR.

Далее, `Invoker` реализация будет немного отличается:

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

После создания файла AAR, в Android Studio, можно написать следующие передача модульного тестирования:

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

Переопределение `virtual` в Java — это возможно, но не прекрасные возможности.

Предположим, что имеется следующий класс C#.

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

Если вы следовали `abstract` приведенном выше примере класс, будет работать так, за исключением один элемент данных: _Xamarin.Android не уточняющего запроса `Invoker`_ .

Чтобы устранить эту проблему, измените класс C# для быть `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Это не идеальное решение, но он получает рабочий этот сценарий. Получат Xamarin.Android `VirtualClassInvoker` и Java можно использовать `@Override` в методе.

## <a name="callbacks-in-the-future"></a>Обратные вызовы в будущем

Существует несколько вещей, мы может улучшить эти сценарии:

1. `throws Throwable` в C# конструкторы фиксируется в данном [PR](https://github.com/xamarin/java.interop/pull/170).
1. Сделайте генератор Java в Xamarin.Android поддерживает интерфейсы.
    - Это устраняет необходимость добавления исходный файл Java с помощью действия построения `AndroidJavaSource`.
1. Сделать способ Xamarin.Android для загрузки `Invoker` для виртуальных классов.
    - Это избавляет от необходимости пометить класс в нашем `virtual` пример `abstract`.
1. Создать `Invoker` классы для внедрения .NET автоматически
    - Это будет сложной, но возможно. Xamarin.Android уже выполняет похожим образом для привязки проектов Java.

Много работы здесь, но для внедрения .NET эти усовершенствования стали возможны.

## <a name="further-reading"></a>Дополнительные сведения

* [Приступая к работе в Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Предварительное исследование Android](~/tools/dotnet-embedding/android/index.md)
* [Ограничения внедрения .NET](~/tools/dotnet-embedding/limitations.md)
* [Вклад в проект с открытым исходным кодом](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
