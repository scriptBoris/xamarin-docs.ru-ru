---
title: Распознавание речи, используя API распознавания речи Microsoft
description: Microsoft Speech API — это API на основе облака, который алгоритмы обработки устной. В этой статье объясняется, как с помощью REST API распознавания речи Microsoft преобразуйте звуковые данные в текст в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 282ebe330a370e0dda3af54287107b380c85cd80
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102819"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Распознавание речи, используя API распознавания речи Microsoft

_Microsoft Speech API — это API на основе облака, который алгоритмы обработки устной. В этой статье объясняется, как с помощью REST API распознавания речи Microsoft преобразуйте звуковые данные в текст в приложении Xamarin.Forms._

## <a name="overview"></a>Обзор

Microsoft Speech API состоит из двух компонентов:

- API распознавания речи для преобразования речь в текст. Распознавания речи можно выполнять с помощью REST API, клиентской библиотеки или библиотека службы.
- Преобразование текста в речь API для преобразования текста в речь. Преобразование текста в речь выполняется через REST API.

Эта статья посвящена выполняет распознавание речи с помощью REST API. Хотя библиотеки клиента и службы поддерживают, возвращая частичные результаты, REST API может возвращать только результат распознавания единый, не все частичные результаты.

Необходимо получить ключ API для использования в API распознавания речи Microsoft. Это может быть получен из Azure [портала](https://portal.azure.com/). Дополнительные сведения см. в разделе [создать учетную запись Cognitive Services на портале Azure](/azure/cognitive-services/cognitive-services-apis-create-account).

Дополнительные сведения о Microsoft Speech API, см. в разделе [документации по API распознавания речи Microsoft](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к REST API распознавания речи Microsoft требуется маркер доступа JSON Web Token (JWT), который можно получить от службы маркеров cognitive services в `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Маркер можно получить, выполнив запрос POST к службе маркеров, указав `Ocp-Apim-Subscription-Key` заголовок, содержащий ключ API, как его значение.

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

Маркер доступа должен быть указан в каждой Microsoft Speech REST API вызывать как `Authorization` префиксом со строкой заголовка `Bearer`, как показано в следующем примере кода:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Не удалось передать допустимый маркер доступа к REST API распознавания речи Microsoft приведет к ошибке с ответом 403.

## <a name="performing-speech-recognition"></a>Выполняет распознавание речи

Распознавание речи достигается путем отправки запроса POST к `recognition` API в `https://speech.platform.bing.com/speech/recognition/`. Один запрос не может содержать более 10 секунд аудио и длительность общее число запросов не может превышать 14 секунд.

Звукового содержимого должен быть помещен в тело запроса POST запроса в формате wav.

В приложении-примере `RecognizeSpeechAsync` метод вызывает процесс распознавания речи:

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

В каждом проекте платформы, как данные wav PCM, записи звука и `RecognizeSpeechAsync` использует метод `PCLStorage` пакет NuGet, чтобы открыть звуковой файл в виде потока. Созданный URI запроса распознавания речи и маркер доступа, извлекается из службы маркеров. Отправляется запрос распознавания речи `recognition` API, который возвращает ответ JSON, содержащий результат. В результате возвращается в вызывающий метод для отображения десериализуется ответ в формате JSON.

### <a name="configuring-speech-recognition"></a>Настройка распознавания речи

Процесс распознавания речи можно настроить путем указания параметров запроса HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/en-us/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

Основные конфигурации, выполненной по `GenerateRequestUri` метод заключается в установке языкового стандарта звукового содержимого. Список поддерживаемых языков, см. в разделе [поддерживаемые языки ](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/).

### <a name="sending-the-request"></a>Отправка запроса

`SendRequestAsync` Метод выполняет запрос POST по REST API распознавания речи Microsoft и возвращает ответ:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

Этот метод формирует запрос POST по:

- Упаковки звукового потока в `StreamContent` экземпляр, который предоставляет содержимое HTTP на основе потока.
- Установка `Content-Type` заголовок запроса на `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Добавление маркера доступа для `Authorization` заголовка с префиксом строкой `Bearer`.

Затем отправляется запрос POST `recognition` API. Ответ считывается и возвращается вызывающему методу.

`recognition` API отправит код состояния HTTP 200 (ОК) в ответе, при условии, что запрос является допустимым, который указывает, что запрос успешно выполнен, и что запрашиваемые данные находятся в ответе. Список возможные сообщения об ошибках, см. в разделе [Устранение неполадок](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>Обработка ответа

В ответе API возвращаются в формате JSON с распознанный текст, включены в `name` тега. Следующие данные JSON показывает типичный успешный ответ сообщение:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

В примере приложения, ответ JSON десериализуются в `SpeechResult` экземпляра, в результате возвращается в вызывающий метод для отображения, как показано на следующем снимке экрана:

![](speech-recognition-images/speech-recognition.png "Распознавание речи")

## <a name="summary"></a>Сводка

В этой статье описано, как с помощью REST API распознавания речи Microsoft преобразуйте звуковые данные в текст в приложении Xamarin.Forms. Помимо выполнения распознавания речи, API распознавания речи Microsoft также можно преобразовать текст в речь.

## <a name="related-links"></a>Связанные ссылки

- [Документация по API распознавания речи Microsoft](/azure/cognitive-services/speech/home/).
- [Использование веб-службу RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Cognitive Services TODO (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
