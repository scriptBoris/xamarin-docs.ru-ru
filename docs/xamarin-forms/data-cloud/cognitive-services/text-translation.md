---
title: Перевод текста с помощью Translator API
description: API Microsoft Translator можно использовать для перевода речи и текста с помощью REST API. В этой статье объясняется, как использовать API Microsoft Translator текста для перевода текста с одного языка на другой в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 18e5b430d9a56b22a0b4cc72d6aff1c4e3049362
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050573"
---
# <a name="text-translation-using-the-translator-api"></a>Перевод текста с помощью Translator API

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_API Microsoft Translator можно использовать для перевода речи и текста с помощью REST API. В этой статье объясняется, как использовать API Microsoft Translator текста для перевода текста с одного языка на другой в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

Translator API состоит из двух компонентов:

- Перевод текста REST API, перевод текста с одного языка на текст другого языка. API автоматически определяет язык текста, который был отправлен перед его преобразование.
- Перевод речи REST API для транскрипция речи с одного языка в текст другого языка. API также сочетает в себе возможности преобразования текста в речь для Озвучивание переведенного текста обратно.

Эта статья посвящена перевод текста с одного языка в другой с помощью API перевода текстов.

Чтобы использовать API перевода текстов необходимо получить ключ API. Его можно получить в [как зарегистрироваться в Microsoft Translator Text API](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Дополнительные сведения о Microsoft Translator Text API, см. в разделе [документации по API перевода текста](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к API перевода текстов требуется маркер доступа JSON Web Token (JWT), который можно получить от службы маркеров cognitive services в `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Маркер можно получить, выполнив запрос POST к службе маркеров, указав `Ocp-Apim-Subscription-Key` заголовок, содержащий ключ API, как его значение.

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

Токен возвращаемый доступа, представляющий собой текст в кодировке Base64, имеет время окончания срока действия 10 минут. Таким образом пример приложения обновляет токен доступа каждые 9 минут.

Маркер доступа должен быть указан в каждом API перевода текстов вызов в качестве `Authorization` префиксом со строкой заголовка `Bearer`, как показано в следующем примере кода:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Дополнительные сведения о службе маркеров cognitive services, см. в разделе [API маркеров проверки подлинности](http://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Выполняет перевод текста

Перевод текста можно достичь, выполнив запрос GET к `translate` API в `https://api.microsofttranslator.com/v2/http.svc/translate`. В приложении-примере `TranslateTextAsync` метод вызывает процесс перевода текста:

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

`TranslateTextAsync` Метод создает URI запроса и получает маркер доступа от службы маркеров. Затем отправляется запрос перевода текста `translate` API, который возвращает XML-ответ, содержащий результат. Ответ в формате XML анализируется, и результат перевода возвращается в вызывающий метод для отображения.

Дополнительные сведения об API-интерфейсы REST для преобразования текста, см. в разделе [Microsoft Translator Text API](http://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Настройка преобразования текста

Процесс преобразования текста можно настроить путем указания параметров запроса HTTP:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Этот метод задает текст для перевода, а также язык перевода текста. Список языков, поддерживаемых Microsoft Translator, см. в разделе [поддерживаемые языки в Microsoft Translator Text API](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Если приложению нужно знать, какой язык, текст находится в, `Detect` API можно вызвать, чтобы определить язык текста строки.

### <a name="sending-the-request"></a>Отправка запроса

`SendRequestAsync` Метод выполняет запрос GET к REST API перевода текста и возвращает ответ:

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

Этот метод создает запрос GET, добавляя токен доступа для `Authorization` заголовка с префиксом строкой `Bearer`. Затем отправляется запрос GET `translate` API, с помощью URL-АДРЕСЕ запроса указание текст для перевода, а также язык перевода текста. Ответ считывается и возвращается вызывающему методу.

`translate` API отправит код состояния HTTP 200 (ОК) в ответе, при условии, что запрос является допустимым, который указывает, что запрос успешно выполнен, и что запрашиваемые данные находятся в ответе. Список возможные сообщения об ошибках, см. в разделе сообщений-ответов в [получить перевод](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Обработка ответа

В ответе API возвращаются в формате XML. Следующий XML-данных показывает типичный успешный ответ сообщение:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

В примере приложения, ответ в формате XML преобразуется в `XDocument` экземпляра со значением корневой XML возвращается в вызывающий метод для отображения, как показано на следующем снимке экрана:

![](text-translation-images/text-translation.png "Перевод текста на немецкий")

## <a name="summary"></a>Сводка

В этой статье описываются способы использования API Microsoft Translator Text перевод текста с одного языка на текст другого языка, в приложении Xamarin.Forms. Помимо перевода текста, API Microsoft Translator можно также транскрипция речи с одного языка в текст другого языка.

## <a name="related-links"></a>Связанные ссылки

- [Translator Text API документации](/azure/cognitive-services/translator/).
- [Использование веб-службу RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Cognitive Services TODO (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft Translator Text API](http://docs.microsofttranslator.com/text-translate.html).
