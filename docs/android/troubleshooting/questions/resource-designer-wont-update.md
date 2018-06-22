---
title: Файл Android Resource.designer.cs не будут обновлены
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: 6d20c92fbb61ab11fcfeda3e9d2f63fdfc5a6d54
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762624"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Файл Android Resource.designer.cs не будут обновлены

> [!NOTE]
> Эта проблема устранена в Xamarin Studio 5.1.4 и более поздних версиях. Тем не менее, если эта проблема возникает в Visual Studio для Mac, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.

Ошибка в Xamarin.Studio 5.1 ранее поврежденные файлы .csproj, частично или полностью удалите XML-код в CSPROJ-файл. Это приведет к важные части Android системы (например, обновление Android Resource.designer.cs) сборки к сбою. Начиная с 5.1.4 стабильный выпуске на 15 июля см. в этот момент был исправлен; но во многих случаях файл проекта исправить вручную, как описано ниже.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Два возможных подходов к исправлению файла проекта

### <a name="either"></a>Варианты

1) Создание нового проекта приложения Xamarin.Android, настроить все свойства проекта для сопоставления старого проекта и добавьте все ресурсы, файлы исходного кода, т. д. обратно в проект.

### <a name="or"></a>OR

2) Создайте резервную копию исходного проекта CSPROJ-файл, откройте его в текстовый редактор и снова добавьте в отсутствующие элементы из полностью созданный csproj-файла.

### <a name="if-this-does-not-solve-the-problem"></a>Если это не устранит проблему

После экспериментов с этими элементами можно заметить, что после добавления обратно элементы и перестроения проекта, обновит файл Resource.designer.cs, но может по-прежнему необходимо закрыть и снова откройте решение, чтобы получить дополнение кода для распознавания новые типы, содержащиеся в Resource.designer.cs. 
