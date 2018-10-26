---
title: Xamarin Profiler Устранение неполадок
description: Этот документ содержит сведения об устранении неполадок, связанных с Xamarin Profiler. Он описывает проблемы, связанные с ведения журнала и диагностики, интегрированной среды разработки и другим темам.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: 34406ce8d4276ba36ab2ddd49e73681ff2fef773
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103755"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler Устранение неполадок

## <a name="logging-and-diagnostics"></a>Ведение журнала и диагностики

Команда Xamarin может помочь отслеживать проблемы, если нам предоставить сведения, включая:

- Презентация проблему, сбоя или сбоя и рабочий процесс, привели к нему.
- Выходные данные журнала (см. ниже).
- **.Mlpd** создаваемый в течение сеанса профилирования (см. ниже).

### <a name="getting-log-outputs"></a>Получение выходных данных журнала

На компьютере Mac для сохранения журналов `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

В Windows сохраняются в `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` включите последний журнал при каждой отправке проблемы.

Мы добавили дополнительные ведения журнала при переходе, поэтому эти выходные данные должны увеличиваться и станут более полезной со временем.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Создание файлов .mlpd

**.Mlpd** файл создается сжатый профилировщика среды выполнения mono. Графический пользовательский Интерфейс Xamarin Profiler считывает данные из **.mlpd** и отображает его для пользователя. **.mlpd** файлы полезны, средства отладки для Xamarin, так как они помогают наши инженеры диагностировать проблемы возникли Profiler с данными.

**.Mlpd** для текущего сеанса автоматически сохраняются в компьютере Mac `/tmp` directory и могут идентифицироваться по метке времени. При включении ведения журнала, первые выходные данные будут содержать путь к **.mlpd** файла. **.Mlpd** обычно файл будет сохранен в каталоге, начиная ~/var и папки...

**.Mlpd** для текущего сеанса также могут сохраняться, выбрав **файл > Сохранить как...** Profiler меню:

**Visual Studio для Mac**:

![](troubleshooting-images/image17.png "Сохранение файла .mlpd в Visual Studio для Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Сохранение файла .mlpd в Visual Studio")

Важно отметить, что **.mlpd** содержат большой объем сведений, а размер файла будет большим.

## <a name="troubleshooting"></a>Устранение неполадок

В следующем списке перечислены распространенные подводные камни, поиске их решений и советы и рекомендации по использованию Profiler.

> [!NOTE]
> **Примечание**: вы должны быть Visual Studio **Enterprise** подписчика, чтобы разблокировать эту функцию в любом Visual Studio Enterprise на Windows или Visual Studio для Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Я не вижу параметр профилировщика iOS, или он неактивен [Visual Studio и Visual Studio для Mac]

Проверьте параметры ниже, чтобы устранить эту проблему:

- Убедитесь, что при использовании конфигурации отладки
- Убедитесь, что вы используете сборщик мусора SGen.
- Обеспечения платформы [поддерживается](~/tools/profiler/index.md#Profiler_Support).
- Убедитесь в наличии соответствующей лицензии.
- Убедитесь, что вы вошли в и должным образом проверку подлинности.
- [Visual Studio] Необходимо использовать [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) и иметь действительную лицензию Enterprise.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Сообщение об ошибке при попытке запуска профилировщика

При возникновении этого окно ошибки при использовании профилировщика в Visual Studio:

![](troubleshooting-images/error.png "Окно ошибки при использовании профилировщика в Visual Studio")

Это обычно из-за невозможности запуска в симуляторе или эмуляторе. Попробуйте и запустите приложение, как правило, устранить проблемы, которые он предоставляет, а затем повторите попытку использовать Profiler.

#### <a name="to-watch-a-specific-thread"></a>Для просмотра из определенного потока

Если у вас есть поток, который вы хотите смотреть в частности, было бы идеально для именования в поток с самого начала его создания, таким образом, чтобы получить get `ThreadName` вместо `0x0`. Например задать имя потока в качестве пользовательского интерфейса можно использовать следующий код:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Связанные ссылки

- [Пошаговое руководство. Использование Xamarin Profiler](~/tools/profiler/index.md)
- [Рекомендации по памяти и производительности](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Заметки о выпуске](https://developer.xamarin.com/releases/profiler/preview/)
