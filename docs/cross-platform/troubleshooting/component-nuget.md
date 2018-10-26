---
title: Обновление ссылок на компоненты в NuGet
description: В этом документе описывается, как заменить компонент ссылок пакеты NuGet, чтобы возможность последующего развития ваших приложений, поскольку Store компонента Xamarin был снят с производства.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 70ca9a73c83bed5233b77a6f7be80a13f04f2bcb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122157"
---
# <a name="updating-component-references-to-nuget"></a>Обновление ссылок на компоненты в NuGet

> [!IMPORTANT]
> Store компонента была прекращена начиная с 15 мая 2018 г. (это закрытие изначально [объявила о](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) в ноября 2017 г.).
>
> Компоненты Xamarin больше не поддерживаются в Visual Studio и должны быть заменены пакетами NuGet. Следуйте инструкциям ниже, чтобы вручную удалить ссылки на компонент из проектов.

Воспользуйтесь следующими инструкциями по добавлению пакетов NuGet на [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) или [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Список популярных Xamarin [подключаемые модули и библиотеки](https://github.com/xamarin/XamarinComponents/blob/master/README.md) поможет найти альтернативные варианты для компонентов, которые недоступны в виде пакетов NuGet.

## <a name="manually-removing-component-references"></a>Удаление ссылок на компоненты вручную

15.6 выпуска Visual Studio и 7,4 выпуска Visual Studio для Mac больше не поддерживает компоненты в проекте. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

При загрузке проекта в Visual Studio, отобразится следующее диалоговое окно, объясняющее, что необходимо удалить все компоненты из проекта вручную.

![Предупреждения, диалоговое окно о том, что компонент находится в проекте и должен быть удален](component-nuget-images/component-alert-vs.png)

Чтобы удалить компонент из проекта:

1. Откройте **.csproj** файл. Чтобы сделать это, щелкните правой кнопкой мыши имя проекта и выберите **выгрузить проект**. 

2. Снова щелкните правой кнопкой мыши выгруженного проекта и выберите **изменить CSPROJ {your-project-name}**.

3. Найти все ссылки в файле `XamarinComponentReference`. Он должен выглядеть аналогично приведенному ниже:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Удалите ссылки на `XamarinComponentReference` и сохраните файл. В приведенном выше примере, можно безопасно удалить весь `ItemGroup`.

5. После сохранения файла, щелкните правой кнопкой мыши имя проекта и выберите **перезагрузить проект**.

6. Повторите предыдущие шаги для каждого проекта в решении.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

При загрузке проекта в Visual Studio для Mac, отобразится следующее диалоговое окно, объясняющее, что необходимо удалить все компоненты из проекта вручную.

![Предупреждения, диалоговое окно о том, что компонент находится в проекте и должен быть удален](component-nuget-images/component-alert.png)

Чтобы удалить компонент из проекта:

1. Откройте CSPROJ-файл. Чтобы сделать это, щелкните правой кнопкой мыши имя проекта и выберите **Сервис > изменить файл**.

2. Найти все ссылки в файле `XamarinComponentReference`. Он должен выглядеть аналогично приведенному ниже:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Удалите ссылки на `XamarinComponentReference` и сохраните файл. В приведенном выше примере можно безопасно удалить весь `ItemGroup`

4. Повторите предыдущие шаги для каждого проекта в решении.

-----

> [!WARNING]
> Приведенные ниже инструкции работают только с более ранних версиях Visual Studio.
> **Компоненты** узел больше не доступен в текущих выпусках Visual Studio 2017 или Visual Studio для Mac.

Как обновить существующие решения Xamarin, чтобы изменить компонент ссылки на пакеты NuGet в следующих разделах.

- [Компоненты, которые содержат пакеты NuGet](#contain)
- [Компоненты с заменами NuGet](#replace)

Большинство компонентов относятся к одной из этих категорий.
Если вы используете компонент, не имеют эквивалентных NuGet-пакет, ознакомьтесь со статьей [компонентов без перехода NuGet](#require-update) разделе ниже.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Компоненты, которые содержат пакеты NuGet

Многие компоненты, уже содержат пакеты NuGet и способ переноса — это просто удалить ссылки на компонент.

Можно определить, распространяется ли компонент уже пакета NuGet, дважды щелкнув компонент в решении:

![Расширенным узлом компонентов](component-nuget-images/solution-sml.png)

**Пакетов** вкладке будут перечислены все пакеты NuGet, входящих в компонент:

![Вкладка "пакеты" содержит NuGet](component-nuget-images/packages-tab-sml.png)

Обратите внимание, что **сборки** вкладке будет пустым:

![Вкладка "сборки" пуст](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Обновление решения

Чтобы обновить решение, удалите **компонент** запись из решения:

![Удаление компонента](component-nuget-images/delete-component-sml.png)

Пакет NuGet будет оставаться в **пакетов** узла и ваше приложение будет выполняться компиляция и запустите обычным образом. В будущем будет выполнять обновления для этого пакета с помощью **Nuget** функцию обновления:

![Обновите пакет NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Компоненты с заменами NuGet

Если страница сведений о компоненте **сборки** вкладка содержит записи, как показано ниже, необходимо найти эквивалент пакета NuGet вручную.

![Содержит сборки](component-nuget-images/assemblies-tab-sml.png)

Обратите внимание, что **пакетов** вкладку, вероятно, будет пустым:

![](component-nuget-images/packages-tab-empty-sml.png)

_Он может содержать зависимости NuGet, но их можно проигнорировать._

Чтобы подтвердить замену существует пакет NuGet, выполните поиск [NuGet.org](https://www.nuget.org/packages), используя имя компонента, или автором.

Например, можно найти популярного **sqlite-net-pcl** пакет, выполнив поиск:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) — Имя продукта.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) — профиль автора.

### <a name="updating-the-solution"></a>Обновление решения

Когда вы убедитесь, что компонент доступен в NuGet, выполните следующие действия.

#### <a name="delete-the-component"></a>Удаление компонента

Щелкнуть правой кнопкой мыши компонент в решении и выберите **удалить**:

![Удаление компонента](component-nuget-images/remove-component-sml.png)

Компонент и все ссылки будут удалены. Это приведет к разрыву построения, пока вы не добавите эквивалент пакета NuGet для его замены.

#### <a name="add-the-nuget-package"></a>Добавьте пакет NuGet

1. Щелкните правой кнопкой мыши **пакетов** узел и выберите **добавить пакеты...** .
2. Поиск для замены NuGet по имени или автора:

  ![](component-nuget-images/nuget-search-sml.png)

3. Нажмите клавишу **добавьте пакет**.

Пакет NuGet будет добавлен в проект, а также любые зависимости.
Это должно исправить сборки. Если построение по-прежнему не удается, проверьте каждой ошибки, чтобы просмотреть различия в API компонента и пакета NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Компоненты без пути миграции NuGet

Не стоит беспокоиться в том случае, если вы не нашли немедленно замены для компонентов, используемых в вашем приложении. Существующие компоненты будут работать в Visual Studio версии 15.5 и **компоненты** узел будет отображаться в вашем решении, как обычно.

Тем не менее, в будущих выпусках Visual Studio будет _не_ восстановления или обновления компонентов.
Это означает, что при открытии решения на новом компьютере, компонент не будет загрузить и установить; и автор не сможет предоставить все обновления. Необходимо запланировать:

* Извлечение сборки из компонента и ссылаться на них непосредственно в проект.
* Обратитесь к разработчику компонента и попросите о планах для переноса в NuGet.
* Исследовать альтернативные пакеты NuGet, или поиск исходного кода, если компонент является открытым исходным кодом.

Многие поставщики компонентов по-прежнему работаем над перехода на NuGet, и другим пользователям (включая коммерческий продукты) может анализ параметров альтернативные доставки.


## <a name="related-links"></a>Связанные ссылки
- [Список популярных Xamarin подключаемые модули и библиотеки](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Установка и использование пакета NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Включение пакета NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
