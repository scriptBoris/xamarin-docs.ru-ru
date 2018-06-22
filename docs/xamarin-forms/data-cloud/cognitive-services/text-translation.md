---
title: Перевод текста с помощью преобразователя API
description: API-Интерфейс Microsoft Translator можно использовать для преобразования речи и текста с помощью API-интерфейса REST. В этой статье объясняется, как использовать API-Интерфейс Microsoft Translator текст для перевода текста с одного языка на другой в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 5c1001335fb030f9a91ec72456042316864ccf5c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776668"
---
# <a name="text-translation-using-the-translator-api"></a>Перевод текста с помощью преобразователя API

_API-Интерфейс Microsoft Translator можно использовать для преобразования речи и текста с помощью API-интерфейса REST. В этой статье объясняется, как использовать API-Интерфейс Microsoft Translator текст для перевода текста с одного языка на другой в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

Транслятор API состоит из двух компонентов:

- Перевод текста REST API для перевод текста с одного языка на другой язык текста. API-Интерфейс автоматически определяет язык текста, который был отправлен до его преобразования.
- Перевод речи REST API для ведения протокола речи с одного языка в текст другого языка. API-Интерфейс также сочетает в себе возможности преобразования текста в речь оперировать переведенный текст обратно.

Эта статья посвящена перевод текста с одного языка на другую с помощью API текст переводчик.

Использовать API-Интерфейс преобразователя текста необходимо получить ключ API. Это можно сделать в [регистрация для API-интерфейса Microsoft Translator текст](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Дополнительные сведения об API-Интерфейсе Microsoft Translator текста см. в разделе [переводчик текст документации API](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к API текст переводчик требуется маркер доступа JSON Web Token (JWT), который можно получить от службы маркеров когнитивных служб в `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Маркер можно получить, выполнив запрос POST в службу маркера указание `Ocp-Apim-Subscription-Key` заголовок, содержащий ключ API, как его значение.

В следующем примере кода показано, как запросить доступ маркера от службы маркеров:

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

Маркер доступа, возвращаемый, являющийся текст в кодировке Base64, имеет время отключения 10 минут. Таким образом образец приложения обновляет токен доступа минутам 9.

Маркер доступа должен быть указан в API текст каждого транслятора вызывать как `Authorization` префиксом со строкой заголовка `Bearer`, как показано в следующем примере кода:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Дополнительные сведения о службе маркеров когнитивных служб см. в разделе [API маркеров проверки подлинности](http://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Выполняет перевод текста

Перевод текста можно получить, сделав запрос GET к `translate` API по `https://api.microsofttranslator.com/v2/http.svc/translate`. В примере приложения `TranslateTextAsync` метод вызывает процесс преобразования текста:

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

`TranslateTextAsync` Метод приводит к возникновению ошибки URI запроса и извлекает токен доступа от службы маркеров. Затем отправляется запрос перевод текста `translate` API, который возвращает XML-ответ, содержащий результат. Выполняется синтаксический анализ XML-ответа и перевод результат возвращается в вызывающий метод для отображения.

Дополнительные сведения об интерфейсах API REST перевод текста см. в разделе [Microsoft Translator текст API](http://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Настройка преобразования текста

Процесс преобразования текстового может настраиваться путем указания параметров запроса HTTP:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Этот метод устанавливает текст, которые будут преобразованы и язык перевода текста. Список языков, поддерживаемых Microsoft Translator см. в разделе [поддерживаемые языки в API-Интерфейс Microsoft Translator текст](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Если приложению нужно знать, какой язык, текст находится в, `Detect` API можно вызвать для определения языка текстовую строку.

### <a name="sending-the-request"></a>Идет отправка запроса

`SendRequestAsync` Метод выполняет запрос GET к REST API преобразования текста и возвращает ответ:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Этот метод создает запрос GET, добавляя токен доступа для `Authorization` префиксом со строкой заголовка `Bearer`. Затем отправляется запрос GET `translate` API с URL-АДРЕСЕ запроса задания текста перевода и язык перевода текста. Ответ считывается и возвращается вызывающему методу.

`translate` API отправит код состояния HTTP 200 (ОК) в ответе, что запрос допустим, указывающая, что запрос выполнен успешно, и что запрашиваемые данные находятся в ответе. Список возможных ошибках см. в разделе ответные сообщения в [получить перевод](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Обработка ответа

API ответ возвращается в формате XML. Следующий XML-данных показано сообщение обычно успешный ответ:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

В примере приложения XML-ответа разбивается на `XDocument` экземпляр со значением корневой XML возвращается в вызывающий метод для отображения, как показано на следующем снимке экрана:

![](text-translation-images/text-translation.png "Перевод текста на немецкий")

## <a name="summary"></a>Сводка

В этой статье описано, как использовать API-Интерфейс Microsoft Translator текст для перевод текста с одного языка на другой язык в приложении Xamarin.Forms текст. Помимо перевод текста, API-Интерфейс Microsoft Translator также можно переписать речи с одного языка в текст другого языка.

## <a name="related-links"></a>Связанные ссылки

- [Транслятор текст API документации](/azure/cognitive-services/translator/).
- [Использование веб-службы RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [TODO Когнитивных службы (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Текст Microsoft Translator API](http://docs.microsofttranslator.com/text-translate.html).
