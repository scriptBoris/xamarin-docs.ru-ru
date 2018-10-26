---
title: Не обновляется файл Android Resource.designer.cs
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: ba3c2b07e7f35bf9fd84d10b74d034a02ca6a73d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106420"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Не обновляется файл Android Resource.designer.cs

> [!NOTE]
> Эта проблема устранена в Xamarin Studio 5.1.4 и более поздних версий. Тем не менее, если эта проблема возникает в Visual Studio для Mac, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.

Ошибка в Xamarin.Studio 5.1 ранее повреждены CSPROJ-файлов, частично или полностью удалите XML-код в CSPROJ-файл. Это приведет к важной части Android построить систему (например, обновление Android Resource.designer.cs) переход на другой. По состоянию на 5.1.4 стабильный выпуск 15 июля, эта ошибка будет исправлена; но во многих случаях файл проекта требуется восстановить вручную, как описано ниже.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Два возможных подхода к исправлению файла проекта

### <a name="either"></a>Варианты

1) Создайте новый проект приложения Xamarin.Android, задать все свойства проекта в соответствии с старый проект и добавьте все ресурсы, исходные файлы, т. д. обратно в проект.

### <a name="or"></a>OR

2) Создайте резервную копию исходного проекта CSPROJ-файл, откройте его в текстовом редакторе и добавьте обратно в отсутствующие элементы из аккуратно созданные csproj-файла.

### <a name="if-this-does-not-solve-the-problem"></a>Если это не устранит проблему

Поэкспериментировав с этими элементами, вы могли заметить, что после обратно добавления элементов и перестроение проекта, обновит файл Resource.designer.cs, но затем может по-прежнему необходимо закрыть и снова откройте решение для автозавершения кода для распознавания новые типы, содержащиеся в Resource.designer.cs. 
