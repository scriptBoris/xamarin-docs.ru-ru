---
title: Проверка орфографии с помощью API проверки орфографии Bing
description: API проверки орфографии выполняет контекстную орфографии для текста, предоставление встроенного по слова с ошибками. В этой статье объясняется, как с помощью API проверки орфографии REST исправлять орфографические ошибки в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 08ac86674e4f10d6bd17d765de2bcdf7c2d3f901
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061763"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Проверка орфографии с помощью API проверки орфографии Bing

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_API проверки орфографии выполняет контекстную орфографии для текста, предоставление встроенного по слова с ошибками. В этой статье объясняется, как с помощью API проверки орфографии REST исправлять орфографические ошибки в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

API проверки орфографии REST имеет два режима работы, и должен быть указан режим, при выполнении запроса к API:

- `Spell` исправляет краткий текст (до 9 слова) без необходимости изменения регистра.
- `Proof` исправляет длинный текст, предоставляет регистр исправлений и базовую пунктуацию и подавляет агрессивной исправления.

Необходимо получить ключ API для использования API проверки орфографии Bing. Его можно получить в [попробуйте Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)

Список языков, поддерживаемых API проверки орфографии Bing, см. в разделе [поддерживаемые языки](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Дополнительные сведения о API проверки орфографии Bing, см. в разделе [документации проверки орфографии Bing](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к API проверки орфографии Bing требует наличия ключа API, которое необходимо задать для параметра `Ocp-Apim-Subscription-Key` заголовка. В следующем примере кода показано, как добавить ключ API, чтобы `Ocp-Apim-Subscription-Key` заголовка запроса:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Не удалось передать допустимый ключ API для API проверки орфографии Bing приведет к ошибке ответ 401.

## <a name="performing-spell-checking"></a>Выполнение проверки орфографии

Проверка орфографии можно достичь, выполнив запрос GET или POST к `SpellCheck` API в `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. При выполнении запроса GET, текст, быть орфографии отправляется как параметр запроса. При выполнении запроса POST, текст, быть орфографии отправляется в тексте запроса. Запросы GET ограничены орфографии 1 500 символов из-за ограничения длины строки параметр запроса. Таким образом запросы POST обычно должны быть доступны, если не выполняется проверка орфографии коротких строк.

В приложении-примере `SpellCheckTextAsync` процесс проверки орфографии вызывает метод:

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync` Метод создает URI запроса, а затем отправляет запрос на `SpellCheck` API, который возвращает ответ JSON, содержащий результат. В результате возвращается в вызывающий метод для отображения десериализуется ответ в формате JSON.

### <a name="configuring-spell-checking"></a>Настройка проверки орфографии

Процесс проверки орфографии можно настроить путем указания параметров запроса HTTP:

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

Дополнительные сведения о API проверки орфографии REST, см. в разделе [Справочник по API проверки орфографии версии 7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Отправка запроса

`SendRequestAsync` Метод выполняет запрос GET по API проверки орфографии REST и возвращает ответ:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Этот метод отправляет запрос GET, чтобы `SpellCheck` API, с помощью URL-АДРЕСЕ запроса указание текст для перевода, а также режим проверки орфографии. Ответ считывается и возвращается вызывающему методу.

`SpellCheck` API отправит код состояния HTTP 200 (ОК) в ответе, при условии, что запрос является допустимым, который указывает, что запрос успешно выполнен, и что запрашиваемые данные находятся в ответе. Список объектов ответа, см. в разделе [объектов ответа](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Обработка ответа

В ответе API возвращаются в формате JSON. Следующие данные JSON показано сообщение ответа для неправильно написанного текста `Go shappin tommorow`:

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

`flaggedTokens` Массив содержит массив слов в тексте, которые были помечены как не работает правильно, или являются грамматически неправильный. Массив будет иметь пустой, если нет орфографические или грамматические ошибки не найдены. Теги в массиве являются:

- `offset` — к слову, производительность которого оказалась нулевое смещение с начала текстовой строки.
- `token` — слово в текстовой строке, указано неправильно или грамматически неверен.
- `type` — Тип ошибки, вызвавшей слово будет помечен. Существует два возможных значения: `RepeatedToken` и `UnknownToken`.
- `suggestions` — Массив слов, для исправления ошибки правописания. Массив состоит из `suggestion` и `score`, которое определяет уровень уверенности в правильности предлагаемые исправления.

В примере приложения, ответ JSON десериализуются в `SpellCheckResult` экземпляра, в результате возвращается в вызывающий метод для отображения. В следующем примере кода показано как `SpellCheckResult` экземпляра обрабатывается для отображения:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Этот код выполняет итерацию `FlaggedTokens` коллекции и заменяет все с ошибкой или грамматически некорректный слова в тексте исходного кода с помощью первого предложения. На следующих снимках экрана показано, до и после проверки орфографии:

![](spell-check-images/before-spell-check.png "Прежде чем проверки орфографии")

![](spell-check-images/after-spell-check.png "После проверки орфографии")

## <a name="summary"></a>Сводка

В этой статье описано, как с помощью API проверки орфографии REST исправлять орфографические ошибки в приложении Xamarin.Forms. API проверки орфографии выполняет контекстную орфографии для текста, предоставление встроенного по слова с ошибками.

## <a name="related-links"></a>Связанные ссылки

- [Документация по проверка орфографии Bing](/azure/cognitive-services/bing-spell-check/)
- [Использование веб-службу RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Cognitive Services TODO (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Справочник по API проверки орфографии Bing версии 7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
