---
title: Распознавание эмоций с помощью API распознавания лиц
description: API распознавания лиц принимает данные о выражении лица на изображении в качестве входных данных и возвращает данные, которые содержат уровней достоверности между набором эмоции на каждом лице на изображении. В этой статье описывается использование API распознавания лиц для распознавания эмоций для оценки приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: d703de90378991d262a4b056b9ebc98d183e3fb8
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056494"
---
# <a name="emotion-recognition-using-the-face-api"></a>Распознавание эмоций с помощью API распознавания лиц

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_API распознавания лиц принимает данные о выражении лица на изображении в качестве входных данных и возвращает данные, которые содержат уровней достоверности между набором эмоции на каждом лице на изображении. В этой статье описывается использование API распознавания лиц для распознавания эмоций для оценки приложения Xamarin.Forms._

## <a name="overview"></a>Обзор

API распознавания лиц можно выполнить обнаружение эмоций на лице для обнаружения, гнев, презрение, отвращение, страх, счастье, Нейтральное, грусть и удивление, в выражении лица. Эти эмоции, глобально и что взаимодействуют через те же базовые выражения лиц. А также Возврат результата распознавания эмоций для выражения лиц, API распознавания лиц можно также Возвращает ограничивающий прямоугольник для обнаруженных лиц. Обратите внимание, что необходимо получить ключ API для использования API распознавания лиц. Его можно получить в [попробуйте Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Распознавание эмоций лица могут выполняться через клиентскую библиотеку, а также через REST API. Эта статья посвящена распознавания эмоций с помощью REST API. Дополнительные сведения о REST API, см. в разделе [REST API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

API распознавания лиц могут также использоваться для распознавания выражений лиц людей на видео и может возвращать сводка об их эмоциях. Дополнительные сведения см. в разделе [как анализ видео в реальном времени](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Дополнительные сведения о API распознавания лиц, см. в разделе [API распознавания лиц](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к API распознавания лиц требуется ключ API, который должен быть указан для параметра `Ocp-Apim-Subscription-Key` заголовка. В следующем примере кода показано, как добавить ключ API, чтобы `Ocp-Apim-Subscription-Key` заголовка запроса:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Не удалось передать допустимый ключ API для API распознавания лиц приведет к ошибке с ответом 401.

## <a name="performing-emotion-recognition"></a>Распознавания эмоций

Распознавание эмоций выполняется путем отправки запроса POST, содержащего изображение `detect` API в `https://[location].api.cognitive.microsoft.com/face/v1.0`, где `[location]]` — регион, используемый для получения ключа API. Используются следующие параметры необязательные параметры запроса.

- `returnFaceId` — следует ли возвращать faceIds обнаруженных лиц. Значение по умолчанию — `true`.
- `returnFaceLandmarks` — следует ли возвращать позу обнаруженных лиц. Значение по умолчанию — `false`.
- `returnFaceAttributes` — следует ли анализировать и возвращать один или несколько указанных лицевым атрибутам. Поддерживаемые атрибуты включают `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, и `noise`. Обратите внимание, что атрибут analysis лиц дополнительных затрат времени и вычислений.

Содержимое изображения должны находиться в тексте запроса POST, как URL-адрес или двоичные данные.

> [!NOTE]
> Поддерживаемые форматы используются JPEG, PNG, GIF и BMP и допустимый размер файла — от 1 КБ до 4 МБ.

В примере приложения, процесс распознавания эмоций вызывается путем вызова метода `DetectAsync` метод:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Вызов этого метода указывает поток, содержащий данные изображения, который должен быть возвращен faceIds, что не должны возвращаться позу и эмоций изображения должны быть проанализированы. Он также указывает, что результаты возвращаются в виде массива `Face` объектов. В свою очередь `DetectAsync` вызывает метод `detect` REST API, который выполняет распознавание эмоций лица:

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

Этот метод создает URI запроса, а затем отправляет запрос на `detect` API через `SendRequestAsync` метод.

> [!NOTE]
> В вызовах API распознавания лиц как можно использовать для получения ключи подписки, необходимо использовать тот же регион. Например, если вы приобрели ключи подписки из `westus` регион, конечная точка обнаружения лиц будут `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

### <a name="sending-the-request"></a>Отправка запроса

`SendRequestAsync` Метод выполняет запрос POST для API распознавания лиц и возвращает результат в виде `Face` массива:

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

Если изображение предоставляется через поток, метод создает запрос POST путем заключения поток изображения в `StreamContent` экземпляр, который предоставляет содержимое HTTP на основе потока. Кроме того, если изображение предоставляется через URL-адрес, метод сборки запрос POST, заключая URL-адрес в `StringContent` экземпляр, который предоставляет содержимое HTTP на основе строки.

Затем отправляется запрос POST `detect` API. Ответ считывается, десериализации и возвращается вызывающему методу.

`detect` API отправит код состояния HTTP 200 (ОК) в ответе, при условии, что запрос является допустимым, который указывает, что запрос успешно выполнен, и что запрашиваемые данные находятся в ответе. Список возможные сообщения об ошибках, см. в разделе [REST API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="processing-the-response"></a>Обработка ответа

В ответе API возвращаются в формате JSON. Следующие данные JSON показывает сообщение обычно успешный ответ, предоставляющий данные, запрашиваемые примера приложения:

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

Успешный ответ сообщение состоит из массив записей лиц, отсортированные по размеру прямоугольник лиц в нисходящем порядке, в то время как пустой ответ указывает лица не обнаружены. Каждый распознан лиц включает ряд атрибутов необязательно лиц, которые задаются `returnFaceAttributes` аргумент `DetectAsync` метод.

В примере приложения, ответ JSON десериализуются в массив `Face` объектов. При интерпретации результатов из API распознавания лиц, обнаруженных эмоций должны интерпретироваться как эмоций с наивысшей оценкой, как показатели нормализуются суммируемые к одному. Таким образом в примере приложения отображались распознанных эмоций с наивысшей оценкой для наибольшего обнаруженного лица на изображении. Это достигается следующим кодом:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

На следующем рисунке показан результат распознавания эмоций в приложении-примере:

![](emotion-recognition-images/emotion-recognition.png "Распознавание эмоций")

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как использовать API распознавания лиц для распознавания эмоций для оценки приложения Xamarin.Forms. API распознавания лиц принимает данные о выражении лица на изображении в качестве входных данных и возвращает данные, которые содержат уверенность в наборе точные сведения об эмоциях на каждом лице на изображении.

## <a name="related-links"></a>Связанные ссылки

- [API распознавания лиц](/azure/cognitive-services/face/overview/).
- [Cognitive Services TODO (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [REST API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
