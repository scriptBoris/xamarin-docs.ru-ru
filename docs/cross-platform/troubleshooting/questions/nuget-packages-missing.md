---
title: Ошибка отсутствующих пакетов после обновления пакетов NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 7cb802dd60d4e4879a260ff56d4f94ea5acb2965
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674851"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Ошибка отсутствующих пакетов после обновления пакетов NuGet

Эта проблема обнаружена главным образом в Xamarin.Forms примеры приложений решений, но вероятность эта проблема может произойти в любой проект, который использует пакеты NuGet. 

Если и после обновления пакетов Nuget в проекте или решении, появится сообщение об ошибке, которое ссылается на старый номера версий пакета, такие как:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

В этом примере *Xamarin.Forms.1.3.1.6296* — старый номер версии, который был удален с помощью обновления пакета Nuget.

Это может произойти, если элементы XML в CSPROJ-файл, которые ссылаются на старые номера версии пакета добавленные вручную, или изменять, Nuget не удалять или обновлять их, если бы они были добавлены или редактирования вручную, поэтому проекта теперь ищет пакеты, которые были удалены. 

Чтобы устранить эту проблему, вручную изменить CSPROJ-файлам и удалить все элементы, которые ссылаются на старые номер версии. 

Пример элементов (если они имеют номер версии старого пакета):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```