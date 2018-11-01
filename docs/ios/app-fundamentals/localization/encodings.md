---
title: Кодировки интернационализации в Xamarin.iOS
description: В этом документе описывается кодировки интернационализации в Xamarin.iOS, посвященные доступные кодировки и их добавлении в приложение.
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
ms.openlocfilehash: db24c8677b0a3099193132575e92bc43a4c31dea
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675566"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Кодировки интернационализации в Xamarin.iOS

Не все кодировки по умолчанию включены в библиотеке классов Xamarin.iOS.

Чтобы уменьшить размер приложения, Xamarin.iOS не включают конкретной кодировке, и вам нужно указать mtouch включить сборки, которая содержит средства поддержки для кодирования, что нужно.

Это выполняется путем выбора дополнительных кодировки из области построения/Дополнительно iOS в Visual Studio для Mac или Visual Studio:

 [![](encodings-images/00.png "Выбор дополнительных кодировки")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Выбор дополнительных кодировки")](encodings-images/00a.png#lightbox)

Можно выбрать один из следующих:

-  ККЯ: Chineese, японского и корейского языков
-  mideast: арабский, иврит, турецком и Latin5.
-  другие: кириллица, балтийская, вьетнамский, украинский и тайский
-  редко: кодировки EBCDIC и других редких кодовые страницы
-  Западная: романские языки, Пасхи и Западная Европа
-  все


 <a name="cjk" />


## <a name="cjk"></a>ККЯ

-  CP51932
-  CP932
-  CP936
-  CP949
-  CP950
-  CP54936


 <a name="mideast" />


## <a name="mideast"></a>mideast

-  CP1254
-  CP1255
-  CP1256
-  CP28596
-  CP28598
-  CP28599
-  CP38598


 <a name="other" />


## <a name="other"></a>другие

-  CP1251
-  CP1257
-  CP1258
-  CP20866
-  CP21866
-  CP28594
-  CP28595
-  CP57002
-  CP874


 <a name="rare" />


## <a name="rare"></a>редко

-  CP1026
-  CP1047
-  CP1140
-  CP1141
-  CP1142
-  CP1143
-  CP1144
-  CP1145
-  CP1146
-  CP1147
-  CP1148
-  CP1149
-  CP20273
-  CP20277
-  CP20278
-  CP20280
-  CP20284
-  CP20285
-  CP20290
-  CP20297
-  CP20420
-  CP20424
-  CP20871
-  CP21025
-  CP37
-  CP500
-  CP708
-  CP852
-  CP855
-  CP857
-  CP858
-  CP862
-  CP864
-  CP866
-  CP869
-  CP870
-  CP875


 <a name="west" />


## <a name="west"></a>Запад

-  CP10000
-  CP10079
-  CP1250
-  CP1252
-  CP1253
-  CP28592
-  CP28593
-  CP28597
-  CP28605
-  CP437
-  CP850
-  CP860
-  CP861
-  CP863
-  CP865

