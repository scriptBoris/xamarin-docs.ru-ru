---
title: Можно добавить файлы или удалите файлы из файла IPA после его создания в Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8b61ba38491b2085233dd1b30a82bc57d2baaed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777935"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Можно добавить файлы или удалите файлы из файла IPA после его создания в Visual Studio?

Да, возможно, но обычно потребует повторной подписи `.app` пакета после внесения изменений.

Обратите внимание, что изменение `.ipa` файл не используется при нормальном использовании. В этой статье предоставляется исключительно в информационных целях.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Пример: удаление файла из `.ipa` архива

В этом примере предположим, что имя проекта Xamarin.iOS `iPhoneApp1` и `generated session id` — `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Построение `.ipa` файл в обычном режиме из Visual Studio.

2.  Перейти к узлу Mac сборки.

3.  Найти сборку в `~/Library/Caches/Xamarin/mtbs/builds` папки. Можно вставить этот путь в **Finder > Go > перейдите в папку** для просмотра папки в средстве поиска. Найдите папку, которая совпадает с именем проекта. В этой папке найдите папку, соответствующую `generated session id` сборки. Скорее всего, это будет вложенная папка с самым поздним временем изменения.

4.  Откройте новый `Terminal.app` окна.

5.  Тип `cd ` в Terminal.app окна, а затем перетаскивания `generated session id` папки в `Terminal.app` окна:

    ![](modify-ipa-images/session-id-folder.png "Поиск папки идентификатор сеанса, созданный в средстве поиска")

6.  Введите ключ возврата, чтобы изменить каталог, в `generated session id` папки.

7.  Распакуйте `.ipa` файла во временную `old/` папки с помощью следующей команды. Настройка `Ad-Hoc` и `iPhoneApp1` имена, необходимые для конкретного проекта.

    > ditto - xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa старого /

8.  Сохранить `Terminal.app` открытое окно.

9.  Удалить необходимые файлы из `.ipa`. Можно либо переместить их в корзину, с помощью поиска, либо удалить их в командной строке с помощью `Terminal.app`. Чтобы просмотреть содержимое `Payload/iPhone` в Finder, удерживая нажатой файл и нажмите кнопку **Показать содержимое пакета**.

10.  Используя тот же общий подход, как в шаге 3, найдите файл журнала в разделе `~/Library/Logs/Xamarin/MonoTouchVS/` с именем проекта и `generated session id` в имени: ![ ] (modify-ipa-images/build-log.png "найдите файл журнала сборки проекта в Finder")

11.  Откройте журнал сборки из шага 10, например двойным щелчком.

12.  Найдите строку, которая включает в себя `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Тип `/usr/bin/codesign ` в окно Terminal.app из шага 8.

14.  Скопируйте все аргументы, начиная с `-v` из строки в шаг 12 и вставьте их в окне Terminal.app.

15.  Изменение последнего аргумента `.app` пакета, расположенного внутри `old/Payload/` папки, а затем выполните команду.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Преобразовать в `old/` каталог в терминала:

```bash
cd old
```

17.  Архивировать содержимое каталога в новый `.ipa` текстовый файл с помощью `zip` команды. Вы можете изменить `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` аргумент для вывода `.ipa` файл везде, где требуется:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Общие сообщения об ошибках

Если вы видите `Invalid Signature. A sealed resource is missing or invalid.`, это обычно означает, что что-то было изменено в `.app` пакета и что `.app` пакета не повторно подписана правильно после него. Также Обратите внимание, что если вы хотите создать `.ipa` с профилем распространения вы _должен_ построения исходного `.ipa` с профилем распространения. В противном случае `Entitlements.xcent` могут оказаться неверными.

Для предоставления конкретный пример как ошибка может возникать, если выполняется следующая `codesign --verify` команду в окне терминала после шага 9 появится ошибка, а также точную причину ошибки:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

И в процессе проверки магазина сообщает сообщение об ошибке:

> Ошибка ITMS-90035: «Недопустимая подпись. Запечатанный ресурса отсутствует или является недопустимым. Двоичный файл по пути [iPhoneApp1.app/iPhoneApp1] содержит недопустимую подпись. Убедитесь, что подписаны сертификатом распространения, не нерегламентированных сертификат или сертификат разработки приложения. Проверьте параметры подписывания кода в Xcode на уровне целевых (которые переопределяют любые значения на уровне проекта). Кроме того убедитесь, что пакет, который вы отправляете был создан с помощью целевой версии в Xcode, не симулятор целевого объекта. Если вы уверены, что выбраны правильные значения параметров подписывания кода, выберите «Очистить все» в Xcode, удалить каталог «сборка» в системе поиска и перестроить целевой версии. Дополнительные сведения см. в [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)»
