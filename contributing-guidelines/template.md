---
title: Заголовок этой статьи
description: Хорошая аннотация к этой статье. Хранящееся здесь содержимое система документации Майкрософт возвращает в результатах поиска, поэтому лучше указать исчерпывающее описание назначения и содержимого этого файла документа
keywords: keyword, list, for, internal, search
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: eac7d316b65b57231d2fc25ef6b859293650d3f8
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "53709618"
---
# <a name="metadata-and-markdown-template"></a>Шаблон метаданных и разметки Markdown

Этот шаблон содержит примеры синтаксиса Markdown, а также указания по настройке метаданных. Чтобы использовать шаблон наиболее эффективно, следует просматривать как **необработанную разметку Markdown**, так и **подготовленное к просмотру представление** (например, в необработанной разметке Markdown виден блок метаданных, а в подготовленном к просмотру представлении — нет).

При создании файла Markdown следует скопировать этот шаблон в новый файл, заполнить метаданные, как указано ниже, задать заголовок H1 в соответствии с названием статьи и удалить содержимое.

## <a name="metadata"></a>Метаданные

Ниже показан полный блок метаданных с рядом заметок.

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

Ключевые замечания:

- Между двоеточием (:) и значением элемента метаданных **должен** быть пробел.
- Если необязательный элемент метаданных не имеет значения, удалите его (не оставляйте его пустым и не используйте "н/д").
- Двоеточия в значении (например, названии) нарушают работу средства анализа метаданных. В этом случае заключите название в двойные кавычки (например, `title: "Writing .NET Core console apps: An advanced step-by-step guide"`).
- Поле **title**. Это заголовок, который будет отображаться в результатах поиска. Он не обязательно должен совпадать с названием в заголовке H1 и должен содержать не более 60 символов.
- Поле **author**, **manager**, **ms.reviewer**. Поле author должно содержать **имя пользователя GitHub**, принадлежащее автору, а не его псевдоним.  Поля manager и ms.reviewer, напротив, должны содержать псевдонимы Майкрософт. В поле ms.reviewer указывается имя руководителя проекта или разработчика, связанного со статьей или компонентом.
- Поле **ms.assetid**. Это глобальный уникальный идентификатор статьи, который используется для внутреннего отслеживания, например для бизнес-аналитики. При создании файла Markdown идентификатор GUID можно получить на сайте [https://www.guidgenerator.com](https://www.guidgenerator.com).

## <a name="basic-markdown-gfm-and-special-characters"></a>Базовая разметка Markdown, GFM и специальные символы

Поддерживаются все возможности базовой разметки Markdown и GitHub Flavored Markdown (GFM). Дополнительные сведения см. в следующих ресурсах.

- [Базовый синтаксис Markdown](https://daringfireball.net/projects/markdown/syntax)
- [DocFX Flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html).
- [Документация GFM](https://guides.github.com/features/mastering-markdown)

Для форматирования в Markdown используются специальные символы, такие как \*, \` и \#. Если вы хотите включить в содержимое один из этих символов, нужно выполнить одно из двух действий:

- поставить обратную косую черту перед специальным символом для его экранирования (например, `\*` для символа \*);
- использовать [код сущности HTML](http://www.ascii.cl/htmlcodes.htm) для символа (например, `&#42;` для &#42;).

## <a name="file-name"></a>Имя файла

В отношении имен файлов действуют указанные ниже правила.
* Содержат только строчные буквы, цифры и дефисы.
* Пробелы и знаки препинания недопустимы. Для разделения слов и чисел в именах файлов используйте дефисы.
* Для обозначения действий используйте глаголы, например develop, buy, build, troubleshoot. Не используйте суффикс -ing.
* Не используйте служебные слова, такие как a, and, the, in, or и т. д.
* Необходимо использовать формат Markdown и расширение имени файла .md.
* Имена файлов не должны быть слишком длинными. Они являются частью URL-адресов статей.



## <a name="headings"></a>Заголовки

Прописные буквы следует использовать как в предложениях. Всегда начинайте с прописной буквы:
- первое слово заголовка;
- Слово после двоеточия в заголовке или названии (например, How to: Sort an array (Практическое руководство. Сортировка массива).

Заголовки следует оформлять в стиле atx, то есть использовать для указания заголовка 1–6 символов решетки (#) в начале строки, что соответствует уровням заголовков HTML с H1 по H6. Выше приведены примеры заголовков первого и второго уровней.

В статье **должен** быть только один заголовок первого уровня (H1), который будет отображаться на странице как название.

Если заголовок заканчивается символом `#`, для его правильного отображения в конце нужно добавить еще один символ `#`. Например, `# Async Programming in F# #`.

На основе заголовков второго уровня будет сформировано оглавление, которое приводится в разделе "Содержимое статьи" под названием статьи.

### <a name="third-level-heading"></a>Заголовок третьего уровня
#### <a name="fourth-level-heading"></a>Заголовок четвертого уровня
##### <a name="fifth-level-heading"></a>Заголовок пятого уровня
###### <a name="sixth-level-heading"></a>Заголовок шестого уровня

## <a name="text-styling"></a>Стиль текста

*Курсив* — используйте для имен файлов, папок и путей (если из-за большой длины они выносятся в отдельные строки), новых терминов, URL-адресов (кроме случаев, когда они отображаются как ссылки, что является вариантом по умолчанию).

**Полужирный** — используйте для элементов пользовательского интерфейса.

## <a name="links"></a>связей

### <a name="internal-links"></a>Внутренние ссылки

Чтобы создать ссылку на заголовок в том же файле Markdown (ссылку привязки), необходимо определить идентификатор заголовка, на который должна указывать ссылка. Для этого просмотрите исходный код обработанной статьи, найдите идентификатор заголовка (например, `id="blockquote"`) и создайте ссылку, поставив перед идентификатором символ # (например, `#blockquote`).
Идентификатор формируется автоматически на основе текста заголовка. Например, для уникального раздела под названием `## Step 2` идентификатор будет выглядеть так: `id="step-2"`.

- Пример: `[Chapter 1](#chapter-1)`

Чтобы создать ссылку на файл Markdown в том же репозитории, используйте [относительную ссылку](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2), указав расширение .md в конце имени файла.

- Пример: `[Readme file](../readme.md)`
- Пример: `[Welcome to .NET](../docs/welcome.md)`

Чтобы создать ссылку на заголовок в файле Markdown в том же репозитории, используйте относительную ссылку + ссылку с символом решетки.

- Пример: `[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>Внешние ссылки

Чтобы создать ссылку на внешний файл, используйте полный URL-адрес.

- Пример: `[GitHub](http://www.github.com)`

Если URL-адрес включается в файл Markdown, он преобразуется в активную ссылку.

- Пример: `http://www.github.com`

### <a name="links-to-apis"></a>Ссылки на интерфейсы API

Система сборки имеет ряд расширений, которые позволяют ссылаться на основные интерфейсы API .NET, не используя внешние ссылки.
Для ссылки на интерфейс API можно использовать его уникальный идентификатор (UID), который формируется автоматически на основе исходного кода.

Можно использовать один из следующих вариантов синтаксиса.
1. Ссылка Markdown: `[link_text](xref:UID)`
2. Автоматическая ссылка: `<xref:UID>`
3. Сокращенная форма: `@UID`

- Пример: `@System.String`
- Пример: `[String class](xref:System.String)`

Дополнительные сведения об использовании этой нотации см. в разделе [Использование перекрестных ссылок](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference).

> В настоящее время простого способа определения уникальных идентификаторов нет. Лучший способ определения уникального идентификатора для интерфейса API — выполнить его поиск в этом репозитории: [docascode/coreapi](https://github.com/docascode/coreapi). Мы работаем над обеспечением более удобного способа.

Если уникальный идентификатор содержит специальные символы \` или \#, они должны быть представлены в значении идентификатора в кодировке HTML как %60 и %23 соответственно, как в приведенных ниже примерах.
- Пример @System.Threading.Tasks.Task\`1 становится `@System.Threading.Tasks.Task%601`
- Пример: @System.Exception.\#ctor становится `@System.Exception.%23ctor`

## <a name="lists"></a>Списки

### <a name="ordered-lists"></a>Упорядоченные списки

1. Это
1. Пример
1. Обычного
1. Упорядоченного
1. Списка


#### <a name="ordered-list-with-an-embedded-list"></a>Упорядоченный список с внедренным списком.

1. А
1. это
1. пример
1. внедренного
    1. Мисс Скарлетт
    1. Профессор Плам
1. упорядоченного
1. списка


### <a name="unordered-lists"></a>Неупорядоченные списки

- Вот
- это
- пример
- маркированного
- списка


##### <a name="unordered-list-with-an-embedded-list"></a>Неупорядоченный список с внедренным списком

- Этот
- маркированный
- списка
    - Миссис Пикок
    - Мистер Грин
- содержит
- другие
    1. Полковник Мастард
    1. Миссис Уайт
- списки


## <a name="horizontal-rule"></a>Горизонтальная линия

---

## <a name="tables"></a>Таблицы

| Таблицы        | Это           | Здорово  |
| ------------- |:-------------:| -----:|
| столбец 3      | выровнен по правому краю | $1600 |
| столбец 2      | выровнен по центру      |   $12 |
| столбец 1 по умолчанию | выровнен по левому краю     |    $1 |

Для удобства можно использовать [средство создания таблиц Markdown](http://www.tablesgenerator.com/markdown_tables).


### <a name="inline-code-blocks-with-language-identifier"></a>Встроенные блоки кода с идентификатором языка

Чтобы применить к блоку кода цветовое выделение синтаксиса, характерное для данного языка, используйте три обратных апострофа (\`\`\`) и идентификатор языка. Ниже приведен полный список [идентификаторов языков GFM](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs).

##### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>Общий блок кода

Для общего выделения синтаксиса в блоке кода используйте три обратных апострофа (&#96;&#96;&#96;).

> Рекомендуемым подходом является использование блоков кода с идентификаторами языков, как описано в предыдущем разделе, для обеспечения правильного выделения синтаксиса на сайте документации. Используйте общие блоки кода только при необходимости.

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>Встроенный код

Используйте обратные апострофы (&#96;) для `inline code`. Используйте встроенный код для команд командной строки, имен таблиц и столбцов в базах данных, а также ключевых слов языка.

## <a name="blockquotes"></a>Блок цитирования

> Засуха продолжалась десять миллионов лет, и царству ужасных ящеров уже давно пришел конец. Здесь, близ экватора, на материке, который позднее назовут Африкой, с новой яростью вспыхнула борьба за существование, и еще не ясно было, кто выйдет из нее победителем. На этой бесплодной, иссушенной зноем земле благоденствовать или хотя бы просто выжить могли только маленькие, или ловкие, или свирепые.

## <a name="images"></a>Изображения

### <a name="static-image-or-animated-gif"></a>Статическое изображение или анимационный GIF

```
![this is the alt text](images/dotnet.png)
```

![Адаптивный дизайн](images/responsivedesign.gif)

### <a name="linked-image"></a>Связанное изображение

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>Видеоролики

### <a name="channel-9"></a>Канал 9

Используйте этот специальный синтаксис blockquote:

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

URL-адрес видеоролика с веб-сайта Channel 9 должен начинаться с `https` и заканчиваться на `/player`, иначе вместо видеоролика будет внедрена вся страница полностью.

### <a name="youtube"></a>YouTube

Используйте этот специальный синтаксис blockquote:

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

Видеоролик с веб-сайта YouTube должен содержать `/embed/` (т. е. иметь указанный выше формат), иначе будут выполняться попытки отображения всей страницы и видео может не воспроизводиться.

## <a name="docsmicrosoft-extensions"></a>Расширения docs.microsoft

docs.microsoft предоставляет несколько дополнительных расширений для GitHub Flavored Markdown.

### <a name="alerts"></a>Предупреждения

Необходимо использовать показанные ниже стили оповещений, чтобы они правильно отображались на сайте документации. Однако модуль отрисовки GitHub не различает их.

#### <a name="note"></a>Бумага для заметок

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>Предупреждение

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>Совет

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>Важно

```
> [!IMPORTANT]
> This is IMPORTANT
```

Отображение будет таким:

![Стили оповещений](images/alerts.png)

### <a name="buttons"></a>Кнопки

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

Примеры кнопок в действии можно увидеть в [документации к Intune](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices).

### <a name="selectors"></a>Селекторы

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

Примеры селекторов в действии можно увидеть в [документации к Intune](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps).

### <a name="step-by-steps"></a>Пошаговые инструкции

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

Примеры пошаговых инструкций можно увидеть в [документации по Advanced Threat Analytics](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2).
