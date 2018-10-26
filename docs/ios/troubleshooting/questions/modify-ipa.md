---
title: Можно добавить файлы или удалить файлы из файла IPA-ФАЙЛ после его сборки в Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: bf135755f64e4d17db2c187d58572c525dfee559
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117412"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Можно добавить файлы или удалить файлы из файла IPA-ФАЙЛ после его сборки в Visual Studio?

Да, возможно, но обычно потребует повторной подписи `.app` пакета после внесения изменений.

Обратите внимание, что изменение `.ipa` файл не используется при нормальном использовании. В этой статье предоставляется исключительно в информационных целях.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Пример: удаление файла из `.ipa` архива

В этом примере предположим, что имя проекта Xamarin.iOS `iPhoneApp1` и `generated session id` — `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Построение `.ipa` файл в обычном режиме из Visual Studio.

2.  Переключение к узлу сборки Mac.

3.  Найти сборку в `~/Library/Caches/Xamarin/mtbs/builds` папку. Можно вставить этот путь в **Finder > Перейти > Перейти к папке** для просмотра папки в Finder. Найдите папку, которая совпадает с именем проекта. В этой папке найдите папку, соответствующую `generated session id` построения. Скорее всего, это будет во вложенную папку, время последнего изменения.

4.  Откройте новую `Terminal.app` окна.

5.  Тип `cd ` в окне Terminal.app и затем перетаскивание `generated session id` в папку `Terminal.app` окна:

    ![](modify-ipa-images/session-id-folder.png "Поиск папки идентификатор сеанса, созданный в Finder")

6.  Тип возвращаемого значения ключа для изменения каталога, в `generated session id` папку.

7.  Распакуйте `.ipa` файл во временную `old/` папки, используя следующую команду. Настройка `Ad-Hoc` и `iPhoneApp1` имена, необходимые для вашего проекта.

    > Это также касается - xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa старый /

8.  Сохранить `Terminal.app` открытое окно.

9.  Удалить необходимые файлы из `.ipa`. Можно либо переместить их в корзину, с помощью поиска или удалить их в командной строке с использованием `Terminal.app`. Чтобы просмотреть содержимое `Payload/iPhone` файл в Finder, щелкните файл и выберите **Показать содержимое пакета**.

10.  Используя тот же общий подход, как в шаге 3, найдите файл журнала в разделе `~/Library/Logs/Xamarin/MonoTouchVS/` , имеет имя проекта и `generated session id` в имени: ![](modify-ipa-images/build-log.png "найдите файл журнала сборки проекта в Finder")

11.  Откройте журнал сборки в шаге 10, например, дважды щелкнув его.

12.  Найдите строку, которая включает в себя `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Тип `/usr/bin/codesign ` в окно Terminal.app из шага 8.

14.  Скопируйте все аргументы, начиная с `-v` из строки в шаг 12 и вставлять их в окно Terminal.app.

15.  Изменить последний аргумент должен быть `.app` пакета, расположенными в пределах `old/Payload/` папку, а затем выполните команду.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Изменение в `old/` каталог в окне терминала:

```bash
cd old
```

17.  Архивировать содержимое каталога в новый `.ipa` файлов с помощью `zip` команды. Вы можете изменить `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` аргумент для вывода `.ipa` файл везде, где угодно:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Распространенные сообщения об ошибках

Если вы видите `Invalid Signature. A sealed resource is missing or invalid.`, это обычно означает, что что-то было изменено в `.app` пакета и что `.app` пакета не повторно подписана правильно позже. Также Обратите внимание, что если вы хотите создать `.ipa` с профиля распространения, вы _необходимо_ сборки исходного `.ipa` с профиля распространения. В противном случае `Entitlements.xcent` будет неправильным.

Чтобы предоставить конкретный пример как ошибка может возникать, если вы выполните следующую команду, `codesign --verify` команды в окне терминала после шага 9, появится ошибка, а также точную причину ошибки:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

И в процессе проверки App Store будут сообщать, сообщение об ошибке:

> Ошибка ITMS-90035: «Недопустимая подпись. Запечатанный ресурса отсутствует или является недопустимым. Двоичный файл по пути [iPhoneApp1.app/iPhoneApp1] содержит недопустимую подпись. Убедитесь, что вы выполнили вход с помощью сертификата распространения, а не нерегламентированных сертификат или сертификат разработки приложения. Проверьте параметры подписывания кода в Xcode на целевом уровне (который переопределяют любые значения на уровне проекта). Кроме того убедитесь, что пакет, который вы передаете был создан с помощью целевого выпуска в Xcode, а не симулятор целевого объекта. Если вы уверены, что указаны правильные параметры подписывания кода, выберите «Очистить все» в Xcode, удалить каталог «сборка» в средстве поиска и перестроить целевой выпуск. Дополнительные сведения см. в [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
