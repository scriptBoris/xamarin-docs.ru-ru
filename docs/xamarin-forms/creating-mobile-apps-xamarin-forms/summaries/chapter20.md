---
title: Сводка Глава 20. Асинхронный и файловый ввод-вывод
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 20. Асинхронный и файловый ввод-вывод'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 7d9630840983b36204214927136e0c9efe07d840
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058237"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Сводка Глава 20. Асинхронный и файловый ввод-вывод

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

 Графический пользовательский интерфейс должен реагировать на ввод пользователя события последовательно. Это означает, что вся обработка данных — события должно находиться в одном потоке, который часто называют *основной поток* или *поток пользовательского интерфейса*.

Пользователи ожидают, что графические интерфейсы пользователя реагировать. Это означает, что программа быстрой обработки событий ввода пользователя. Если это невозможно, то обработка необходимо перейти на роль второстепенных потоков выполнения.

Несколько примеров программ в этой книге использовали [ `WebRequest` ](xref:System.Net.WebRequest) класса. В этом классе [ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) метод запускает рабочий поток, который вызывает функцию обратного вызова, после ее завершения. Однако такой функции обратного вызова выполняется в рабочий поток, поэтому необходимо вызвать программу [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) метод для доступа к интерфейсу пользователя.

> [!NOTE]
> Следует использовать программы Xamarin.Forms [ `HttpClient` ](xref:System.Net.Http.HttpClient) вместо [ `WebRequest` ](xref:System.Net.WebRequest) для доступа к файлам через Интернет. `HttpClient` поддерживает асинхронные операции.

Более современный подход к асинхронной обработки на .NET и C#. Это включает в себя [ `Task` ](xref:System.Threading.Tasks.Task) и [ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1) классов и других типов в [ `System.Threading` ](xref:System.Threading) и [ `System.Threading.Tasks` ](xref:System.Threading.Tasks) пространства имен, а также C# 5.0 `async` и `await` ключевые слова. Это, что рассматривается в этой главе.

## <a name="from-callbacks-to-await"></a>Из обратных вызовов для ожидания

`Page` Сам класс содержит три асинхронные методы для отображения полей предупреждений:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Возвращает `Task` объекта
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) Возвращает `Task<bool>` объекта
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) Возвращает `Task<string>` объекта

`Task` Объектов указывают, что эти методы реализуют задач based Asynchronous Pattern, известный как TAP. Эти `Task` быстро возвращаются объекты из метода. `Task<T>` Возвращают значения составляют «обещает», значение типа `TResult` будут доступны после завершения задачи. `Task` Возвращаемое значение указывает асинхронного действия, который будет завершено, однако с значение не возвращается.

Во всех этих случаях `Task` завершена, когда пользователь закрывает окно с предупреждением.  

### <a name="an-alert-with-callbacks"></a>Оповещение с обратными вызовами

[ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) образце показано, как обрабатывать `Task<bool>` возвращают объекты и `Device.BeginInvokeOnMainThread` вызовов с помощью методов обратного вызова.

### <a name="an-alert-with-lambdas"></a>Оповещение с лямбда-выражения

[ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) образце показано, как использование анонимного лямбда-функции для обработки `Task` и `Device.BeginInvokeOnMainThread` вызовов.  

### <a name="an-alert-with-await"></a>Оповещение с помощью await

Более простой подход включает в себя `async` и `await` ключевые слова, представленные в C# 5. [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) в нем демонстрируется их использование.

### <a name="an-alert-with-nothing"></a>Оповещение с nothing

Если асинхронный метод возвращает `Task` вместо `Task<TResult>`, то программа не нужно использовать любой из этих методов, если его не нужно знать, после завершения выполнения асинхронной задачи. [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) в нем демонстрируется это.

### <a name="saving-program-settings-asynchronously"></a>Идет сохранение параметров программы асинхронно

[ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) в нем демонстрируется использование [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) метод `Application` чтобы сохранить параметры программы, как их изменить, не переопределение `OnSleep` метод.

### <a name="a-platform-independent-timer"></a>Таймер независимо от платформы

Можно использовать [ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32)) для создания таймера независимо от платформы. [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) в нем демонстрируется это.

## <a name="file-inputoutput"></a>Ввод/вывод файла

В большинстве случаев .NET [ `System.IO` ](xref:System.IO) пространство имен было источник поддержки файлового ввода-вывода. Несмотря на то, что некоторые методы в этом пространстве имен поддерживают асинхронные операции, большинство — нет. Пространство имен также поддерживает несколько обычных вызовов методов, которые выполняют функции ввода-вывода файлов.

### <a name="good-news-and-bad-news"></a>Хорошие новости и плохие новости

Всех платформах, поддерживаемых Xamarin.Forms поддержки приложения локальное хранилище &mdash; хранилища, закрытых для приложения.

Библиотеки Xamarin.iOS и Xamarin.Android включают в себя версию .NET, Xamarin явно специально созданные для этих двух платформ. К ним относятся классы из `System.IO` , можно использовать для выполнения файлового ввода-вывода с локальным хранилищем приложения в этих двух платформ.

Тем не менее если вы ищете эти `System.IO` классы в Xamarin.Forms PCL, вы не найдете их. Проблема в том, Microsoft в корне файлового ввода-вывода для API среды выполнения Windows. Программы, предназначенные для Windows 8.1, Windows Phone 8.1 и универсальной платформы Windows не используют `System.IO` для файлового ввода-вывода.

Это означает, что необходимо использовать [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (впервые было рассказано в [ **Глава 9. Вызовы API конкретных платформ** ](chapter09.md) для реализации файлового ввода-вывода.

> [!NOTE]
> Переносимые библиотеки классов были заменены библиотеки .NET Standard 2.0 и .NET Standard 2.0 поддерживает [ `System.IO` ](xref:System.IO) типы для всех платформ Xamarin.Forms. Нет необходимости использовать `DependencyService` для выполнения большинства задач файлового ввода-вывода. См. в разделе [обработка файлов в Xamarin.Forms](~/xamarin-forms/app-fundamentals/files.md) для более современный подход к файлового ввода-вывода.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Первый снимок при кросс платформенных файлового ввода-вывода

[ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) образец определяет [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) интерфейс для файлового ввода-вывода и реализации этого интерфейса на всех платформах. Тем не менее реализации среды выполнения Windows не работают с методы в этом интерфейсе, так как методы ввода-вывода файла среды выполнения Windows являются асинхронными.

### <a name="accommodating-windows-runtime-file-io"></a>При размещении среды выполнения Windows файлового ввода-вывода

Программы, работающие в среде выполнения Windows используются классы в [ `Windows.Storage` ](/uwp/api/Windows.Storage) и [ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams) пространства имен для файлового ввода-вывода, включая приложения локального хранилища. Поскольку корпорация Майкрософт определила, что любая операция, которая требует от более чем 50 миллисекунд должны выполняться асинхронно, чтобы избежать блокирования потока пользовательского интерфейса, эти методы ввода-вывода файла большей части являются асинхронными.

Код, демонстрирующий этот новый подход будет в библиотеке, чтобы он может использоваться другими приложениями.

## <a name="platform-specific-libraries"></a>Библиотеки для конкретных платформ

Это следует хранить в библиотеках повторно используемого кода. Это гораздо более надежной, в тех случаях, когда различные части повторно используемого кода являются для совершенно разных операционных систем.

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) решение демонстрирует один из подходов. Это решение содержит семь различных проектов:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), обычный PCL Xamarin.Forms
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), библиотека классов iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), библиотеки классов Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), библиотеку классов универсальных Windows
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), общий проект для кода, который является общим для всех платформ Windows

Все проекты для отдельных платформ (за исключением элемента **Xamarin.FormsBook.Platform.WinRT**) содержат ссылки на **Xamarin.FormsBook.Platform**. Три проекта Windows иметь ссылку на **Xamarin.FormsBook.Platform.WinRT**.

Все проекты содержат статический `Toolkit.Init` метод, чтобы гарантировать, что библиотека загружается, если он ссылается напрямую не проект в решение приложения Xamarin.Forms.

**Xamarin.FormsBook.Platform** проект содержит новый [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) интерфейс. Все методы теперь имеют имена с `Async` суффиксы и возврат `Task` объектов.

**Xamarin.FormsBook.Platform.WinRT** проект содержит [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) класс для среды выполнения Windows.

**Xamarin.FormsBook.Platform.iOS** проект содержит [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) класс для iOS. Эти методы теперь должен быть асинхронным. Некоторые методы используют асинхронные версии методов, определенных в `StreamWriter` и `StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String)) и [ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync). Другие служат для преобразования результат, чтобы `Task` с помощью [ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*) метод.

**Xamarin.FormsBook.Platform.Android** проект содержит аналогичное [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) класс для Android.

**Xamarin.FormsBook.Platform** проект также содержит [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) класс, который упрощает использование `DependencyService` объекта.

Чтобы использовать эти библиотеки, решение приложений должна включать все проекты в **Xamarin.FormsBook.Platform** решения и каждый из проектов приложений должны иметь ссылку на соответствующей библиотеке в  **Xamarin.FormsBook.Platform**.

[ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) решении показано, как использовать **Xamarin.FormsBook.Platform** библиотеки. Каждый из проектов должен вызывать `Toolkit.Init`. В приложении используется асинхронный файловый ввод-вывод функции.

### <a name="keeping-it-in-the-background"></a>Сохранение его в фоновом режиме

Методы в библиотеках, которые выполняют вызовы к нескольким асинхронным методам &mdash; например `WriteFileAsync` и `ReadFileASync` методы в среде выполнения Windows [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) класса &mdash; можно сделать несколько эффективнее с помощью [ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) метод, чтобы избежать переключение обратно в поток пользовательского интерфейса.

### <a name="dont-block-the-ui-thread"></a>Не блокировать поток пользовательского интерфейса.

Иногда это желание избежать использования `ContinueWith` или `await` с помощью [ `Result` ](xref:System.Threading.Tasks.Task`1.Result) свойства для методов. Этого следует избегать для его можно блокировать поток пользовательского интерфейса или даже зависание приложения.

## <a name="your-own-awaitable-methods"></a>Собственные awaitable методы

Асинхронного выполнения некоторых кода, передавая его к одному из [ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action)) методы. Вы можете вызвать `Task.Run` в асинхронный метод, который обрабатывает некоторые служебные данные.

Различные `Task.Run` шаблоны, описаны ниже.

### <a name="the-basic-mandelbrot-set"></a>Основные Мандельброта

Для рисования Мандельброта, задайте в режиме реального времени, [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека имеет [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) структуру, аналогичную в `System.Numerics` пространство имен.

[ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) образца `CalculateMandeblotAsync` метод в файле кода, который вычисляет базовый черно-белые Мандельброта и использует [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)к помещению точечного рисунка.

### <a name="marking-progress"></a>Пометка хода выполнения

Чтобы сообщать о ходе выполнения из асинхронного метода можно создать экземпляр [ `Progress<T>` ](xref:System.Progress`1) класса и определить ваш асинхронный метод, чтобы аргумент типа [ `IProgress<T>` ](xref:System.IProgress`1). Это показано в [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) образца.

### <a name="cancelling-the-job"></a>Отмена задания

Можно также написать асинхронный метод поддерживала отмену. Начните с класс с именем [ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource). [ `Token` ](xref:System.Threading.CancellationTokenSource.Token) Свойство является значением типа [ `CancellationToken` ](xref:System.Threading.CancellationToken). Эти данные передаются в функцию асинхронной. Программа вызывает [ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel) метод `CancellationTokenSource` (обычно в ответ на действие пользователя) для отмены асинхронной функции.

Асинхронный метод может периодически проверять [ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested) свойство `CancellationToken` и выход в случае свойство является `true`, или просто вызвать [ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) метод в тогда метод заканчивается [ `OperationCancelledException` ](xref:System.OperationCanceledException).

[ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) образце демонстрируется использование вызовов функции.

### <a name="an-mvvm-mandelbrot"></a>Мандельброта MVVM

[ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) пример имеет расширенный пользовательский интерфейс, и он основан на основном [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) и [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)классы:

[![Тройной снимок F X Мандельброта](images/ch20fg13-small.png "Мандельброта MVVM")](images/ch20fg13-large.png#lightbox "Мандельброта MVVM")

## <a name="back-to-the-web"></a>Обратно в Интернете

[ `WebRequest` ](xref:System.Net.WebRequest) Класс, используемый в некоторых примерах используется устаревший асинхронный протокол называется модели асинхронного программирования или APM. Такой класс можно преобразовать в современных TAP протокола, с помощью одного из `FromAsync` методы в [ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1) класса. [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) в нем демонстрируется это.



## <a name="related-links"></a>Связанные ссылки

- [Глава 20 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Глава 20-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Работа с файлами](~/xamarin-forms/app-fundamentals/files.md)
