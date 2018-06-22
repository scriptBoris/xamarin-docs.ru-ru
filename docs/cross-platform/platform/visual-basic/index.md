---
title: Переносимый Visual Basic.NET
description: В этом руководстве объясняется, как Visual Basic можно использовать для записи проекты переносимой библиотеки классов (PCL), которые можно использовать в решениях, предназначенные для Xamarin.iOS и Xamarin.Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 71ee153135df97d3b4fa149d3d788d3b940fe944
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917971"
---
# <a name="portable-visual-basicnet"></a>Переносимый Visual Basic.NET

Проекты Xamarin iOS и Android не поддерживаются Visual Basic; Однако разработчики могут использовать переносимые библиотеки классов перенос существующего кода Visual Basic в iOS и Android, или для записи значительную часть логики своих приложений в Visual Basic. Приложение Xamarin.Forms можно создать полностью на языке Visual Basic (за исключением пользовательских модулей подготовки отчетов, зависят от других служб и фонового кода XAML).

## <a name="requirements"></a>Требования

Переносимый поддержке библиотеки классов была добавлена в Xamarin.Android 4.10.1 Xamarin.iOS 7.0.4 и Xamarin Studio 4.2, это означает, что все проекты Xamarin, созданных с помощью этих средств можно внедрить сборки PCL Visual Basic.

Создание и компиляция переносимой библиотеки классов Visual Basic необходимо использовать Visual Studio в Windows (Visual Studio 2012 или более поздней версии).

> [!NOTE]
> Библиотеки Visual Basic могут быть созданы только и скомпилировать с помощью Visual Studio. Язык Visual Basic не поддерживают Xamarin.iOS и Xamarin.Android.
>
> Если работает только в Visual Studio можно ссылаться из проектов Xamarin.iOS и Xamarin.Android проекта Visual Basic.
>
> Если ваш проекты iOS и Android должен также быть загружен в Visual Studio для Mac должен ссылаться в выходную сборку из Классов, в Visual Basic.


## <a name="creating-a-visual-basicnet-pcl"></a>Создание Visual Basic.NET PCL

В этом разделе рассматриваются способы создания Visual Basic переносимой библиотеки классов с помощью Visual Studio.
Затем библиотеки можно ссылаться в других проектах, включая приложения Xamarin.iOS, Xamarin.Android и Xamarin.Forms.

### <a name="creating-a-pcl"></a>Создание PCL

При добавлении PCL Visual Basic в Visual Studio необходимо выбрать профиль, который описывает, какие платформы библиотека должна быть совместимой с. Введение в PCL документа описываются профилей.

Чтобы создать переносимую библиотеку Классов и выберите его профиль, необходимо:

1.  В **новый проект** выберите **Visual Basic > Библиотека классов (переносимая)** параметр:

    [![](images/image1-sml.png "Создать новый переносимой библиотеки Visual Basic")](images/image1.png#lightbox)

1.  Visual Studio предложит немедленно со следующими **Добавление переносимой библиотеки классов** диалоговое окно, чтобы можно было настроить профиль. Деления платформы, необходимые для поддержки и нажмите клавишу **ОК**.

    [![](images/image2-sml.png "Выберите профиль PCL, выбрав платформы")](images/image2.png#lightbox)

1.  Проект переносимой библиотеки Классов Visual Basic отображаются так, как показано в **обозревателе решений** следующим образом:

    [![](images/image3-sml.png "Пустой проект Visual Studio PCL")](images/image3.png#lightbox)


PCL готов для кода Visual Basic для добавления. Проекты PCL можно ссылаться в других проектах (проекты приложений, проекты библиотек и даже в других проектах PCL).

### <a name="editing-the-pcl-profile"></a>Изменение профиля PCL

Профиль PCL (который определяет, какие платформы PCL совместима с) можно просмотреть и изменить, щелкнув правой кнопкой мыши проект и выбрав **свойства > библиотеки > изменение...** . На этом снимке экрана показан результирующий диалог:

 [![](images/image4-sml.png "Изменение профиля PCL в свойствах проекта")](images/image4.png#lightbox)

Если профиль был изменен после кода уже был добавлен в переносимую библиотеку классов, существует возможность, что библиотеки больше не будет компилироваться, если код ссылается на функции, которые не являются частью вновь выбранный профиль.


## <a name="summary"></a>Сводка

В этой статье продемонстрировал, как использовать код Visual Basic в приложениях Xamarin, с помощью Visual Studio и переносимой библиотеки классов. Несмотря на то что Xamarin непосредственно не поддерживает Visual Basic, компиляция Visual Basic в PCL позволяет кода, написанного на Visual Basic должны быть включены в iOS и Android.

Ниже описаны способы использования Visual Basic.NET PCLs в собственном режиме или Xamarin.Forms приложений:

- [Создание собственного Xamarin.iOS и Xamarin.Android приложений, использующих VB](native-apps.md)
- [Создание приложений Xamarin.Forms в VB](xamarin-forms.md)


## <a name="related-links"></a>Связанные ссылки

- [TaskyPortableVB (пример)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (пример)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Межплатформенная разработка в .NET Framework (Майкрософт)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
