---
title: Проверка орфографии, с помощью API проверки правописания Bing
description: Проверка правописания Bing выполняет контекстно-зависимое орфографии для текста, предоставление встроенного по слов с ошибками. В этой статье описывается использование интерфейса API REST проверки правописания Bing исправление орфографических ошибок в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 259be743a706c9316e2e275ff305a0fe5ad97906
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049913"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Проверка орфографии, с помощью API проверки правописания Bing

_Проверка правописания Bing выполняет контекстно-зависимое орфографии для текста, предоставление встроенного по слов с ошибками. В этой статье описывается использование интерфейса API REST проверки правописания Bing исправление орфографических ошибок в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

Интерфейса API REST проверки правописания Bing имеет два режима работы, а должен быть указан режим, при выполнении запроса к API.

- `Spell` исправляет краткий текст (до 9 слова) без необходимости изменения регистра.
- `Proof` исправляет длинный текст, предоставляет регистр исправлений и основные знаки пунктуации и подавляет агрессивной исправления.

Необходимо получить ключ API для использования API проверки правописания Bing. Это можно сделать в [повторите Когнитивных служб](https://azure.microsoft.com/try/cognitive-services/)

Список языков, поддерживаемых API проверки правописания Bing см. в разделе [поддерживаемые языки](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Дополнительные сведения об API проверки правописания Bing см. в разделе [документации проверки правописания Bing](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к API проверки правописания Bing требуется ключ API, которое необходимо задать в качестве значения `Ocp-Apim-Subscription-Key` заголовок. В следующем примере кода показано, как добавить ключ API `Ocp-Apim-Subscription-Key` заголовок запроса:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Передайте допустимый ключ API для API проверки правописания Bing приведет к ошибке ответ 401.

## <a name="performing-spell-checking"></a>Выполнение проверки орфографии

Проверка орфографии можно достичь, выполнив запрос GET или POST к `SpellCheck` API по `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. При выполнении запроса GET, проверка орфографии текста отправляется как параметр запроса. При выполнении запроса POST, проверка орфографии текста отправляется в тексте запроса. Запросов GET не более 1 500 символов из-за ограничения длины строки параметр запроса проверки орфографии. Таким образом запросы POST обычно должны быть доступны, если не выполняется проверка орфографии коротких строк.

В примере приложения `SpellCheckTextAsync` метод вызывает процесс проверки правописания:

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync` Метод приводит к возникновению ошибки URI запроса, а затем отправляет запрос на `SpellCheck` API, который возвращает ответ JSON, содержащий результат. Результат возвращается в вызывающий метод для отображения десериализуется ответ JSON.

### <a name="configuring-spell-checking"></a>Настройка проверки орфографии

Процесс проверки орфографии может настраиваться путем указания параметров запроса HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Этот метод задает текст для проверки орфографии, а также режим проверки орфографии.

Дополнительные сведения об интерфейсе API REST Bing проверка орфографии см. в разделе [Справочник по API проверки правописания v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Идет отправка запроса

`SendRequestAsync` Метод выполняет запрос GET для интерфейса API REST проверки правописания Bing и возвращает ответ:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Этот метод отправляет запрос GET для `SpellCheck` API с URL-АДРЕСЕ запроса задания текста преобразования и режим проверки орфографии. Ответ считывается и возвращается вызывающему методу.

`SpellCheck` API отправит код состояния HTTP 200 (ОК) в ответе, что запрос допустим, указывающая, что запрос выполнен успешно, и что запрашиваемые данные находятся в ответе. Список объектов ответа см. в разделе [объектов ответа](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Обработка ответа

API ответ возвращается в формате JSON. В следующих данных JSON показывается сообщение ответа для неправильно написанного текста `Go shappin tommorow`:

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

`flaggedTokens` Массив содержит массив слов в тексте, которые были отмечены как выполняется неправильно написано или являются грамматически некорректный. Массив будет пустым, если нет орфографических или грамматические ошибки не найдены. Теги в массиве, являются:

- `offset` — Отсчитываемый от нуля смещение с начала текстовой строки, чтобы слова, которые были отмечен.
- `token` — слова в текстовую строку, указано неправильно или грамматически неверен.
- `type` — Тип ошибки, вызвавшей слова, будут отмечены. Существует два возможных значения — `RepeatedToken` и `UnknownToken`.
- `suggestions` — Массив слов, для исправления ошибки правописания. Массив состоит из `suggestion` и `score`, которое определяет уровень достоверности, правильность предлагаемые исправления.

В примере приложения, ответ JSON десериализуется в `SpellCheckResult` экземпляра, результат возвращается в вызывающий метод для отображения. В следующем примере кода показан способ `SpellCheckResult` экземпляра обрабатывается для отображения:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Этот код выполняет итерацию `FlaggedTokens` коллекции и заменяет любой с ошибками или грамматически некорректный слова в исходном тексте, с помощью первого предложения. Следующих снимках экрана показано до и после проверки правописания.

![](spell-check-images/before-spell-check.png "Прежде чем проверка орфографии")

![](spell-check-images/after-spell-check.png "После проверки орфографии")

## <a name="summary"></a>Сводка

В этой статье описываются способы использования интерфейса API REST проверки правописания Bing исправление орфографических ошибок в приложении Xamarin.Forms. Проверка правописания Bing выполняет контекстно-зависимое орфографии для текста, предоставление встроенного по слов с ошибками.

## <a name="related-links"></a>Связанные ссылки

- [Документация проверки правописания Bing](/azure/cognitive-services/bing-spell-check/)
- [Использование веб-службы RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [TODO Когнитивных службы (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Справочник по API проверки правописания Bing v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
