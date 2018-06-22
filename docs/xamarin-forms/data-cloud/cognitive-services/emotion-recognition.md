---
title: Распознавание эмоций — с помощью API начертание шрифта
description: API начертания принимает выражения лица изображение в качестве входных данных и возвращает данные, включая степени достоверности во множестве эмоций для каждой грани в образе. В этой статье объясняется, как использовать API-Интерфейс лицевой стороны для распознавания эмоций — оценка приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dc04cb077b894b255eb496b2cb2983626573897
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049770"
---
# <a name="emotion-recognition-using-the-face-api"></a>Распознавание эмоций — с помощью API начертание шрифта

_API начертания принимает выражения лица изображение в качестве входных данных и возвращает данные, включая степени достоверности во множестве эмоций для каждой грани в образе. В этой статье объясняется, как использовать API-Интерфейс лицевой стороны для распознавания эмоций — оценка приложения Xamarin.Forms._

## <a name="overview"></a>Обзор

Начертания API можно выполнять обнаружение эмоций — обнаружение anger, contempt, disgust, опасаясь, счастье, нейтральный, sadness и категории "неожиданное", в выражения лица. Эти эмоций, глобально и что взаимодействуют через те же основные выражения лица. А также возвратить результат эмоций — для выражения лица, API лицевой стороны можно также Возвращает ограничивающий прямоугольник для обнаруженных гарнитуры. Обратите внимание, что ключ API, которые должны быть получены для использования API начертания. Это можно сделать в [повторите служб Когнитивных](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Распознавание эмоций — могут выполняться через клиентскую библиотеку, а также через REST API. Эта статья посвящена выполнения распознавания эмоций — через REST API. Дополнительные сведения об REST API см. в разделе [API-интерфейса REST лицевой](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

API лицевой стороны можно также использовать для распознавания выражения лица людей в видео и может возвращать Сводка их эмоций. Дополнительные сведения см. в разделе [как анализировать видео в режиме реального времени](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Дополнительные сведения об API-Интерфейсе начертания. в разделе [API лицевой](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Проверка подлинности

Каждый запрос к API лицевой стороны требуется ключ API, которое необходимо задать в качестве значения `Ocp-Apim-Subscription-Key` заголовок. В следующем примере кода показано, как добавить ключ API `Ocp-Apim-Subscription-Key` заголовок запроса:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Передать допустимый ключ API API лицевой стороны приведет к ошибке ответ 401.

## <a name="performing-emotion-recognition"></a>Выполнение эмоций распознавания

Распознавание эмоций производится путем создания запроса POST, содержащего изображение, `detect` API по `https://[location].api.cognitive.microsoft.com/face/v1.0`, где `[location]]` — регион, используемые для получения ключа API. Используются следующие параметры необязательные параметры запроса.

- `returnFaceId` — следует ли возвращать faceIds обнаруженных сторон. Значение по умолчанию — `true`.
- `returnFaceLandmarks` — следует ли возвращать ориентиры начертания обнаруженных сторон. Значение по умолчанию — `false`.
- `returnFaceAttributes` — следует ли анализировать и возвращают один или несколько указанных сталкиваются атрибуты. Поддерживаемые начертания атрибуты включают `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, и `noise`. Обратите внимание, что анализ атрибута лицевой стороны дополнительных затрат времени и вычислений.

Содержимое этого образа должны находиться в тексте запроса POST URL-адрес или двоичные данные.

> [!NOTE]
> Поддерживаемые форматы, JPEG, GIF, PNG и BMP и допустимом размере файла — от 1 КБ до 4 МБ.

В примере приложения процесс распознавания эмоций вызывается путем вызова метода `DetectAsync` метод:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Вызов этого метода указывает поток, содержащий данные изображения, которое должно быть возвращено faceIds, не должен возвращаться ориентиры лицевой и эмоций изображения должен быть проанализирован. Также указывает, что результаты будут возвращаться как массив `Face` объектов. В свою очередь `DetectAsync` вызывает метод `detect` API REST, который выполняет распознавание эмоций:

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

Этот метод приводит к возникновению ошибки URI запроса, а затем отправляет запрос на `detect` API-интерфейса через `SendRequestAsync` метод.

> [!NOTE]
> Необходимо использовать один регион при вызовах API лицевой стороны как можно использовать для получения ключей подписки. Например, если вы приобрели подписку ключи из `westus` , конечную точку обнаружения начертания, будет ли области `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

### <a name="sending-the-request"></a>Идет отправка запроса

`SendRequestAsync` Метод выполняет запрос POST API лицевой и возвращает результат в виде `Face` массива:

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

Если изображение предоставляется через поток, метод сборки запроса POST, заключив в поток изображений `StreamContent` экземпляр, который предоставляет содержимое HTTP на основе потока. Кроме того, если изображение предоставляется через URL-адрес, метод сборки запроса POST, заключив URL-адрес в `StringContent` экземпляр, который предоставляет содержимое HTTP на основе строки.

Затем отправляется запрос POST `detect` API. Ответ считывается, десериализация и возвращается вызывающему методу.

`detect` API отправит код состояния HTTP 200 (ОК) в ответе, что запрос допустим, указывающая, что запрос выполнен успешно, и что запрашиваемые данные находятся в ответе. Список возможных ошибках см. в разделе [API-интерфейса REST лицевой](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="processing-the-response"></a>Обработка ответа

API ответ возвращается в формате JSON. Следующие данные JSON показывает сообщение обычно успешный ответ, предоставляющий данные, запрашиваемые образец приложения:

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

Успешный ответ сообщение состоит из массив записей начертания, сортируя лицевой стороны прямоугольника размер по убыванию, а пустой ответ указывает фрагменты, не обнаружены. Каждый распознан начертания включает ряд необязательно начертания атрибутов, которые задаются `returnFaceAttributes` аргумент `DetectAsync` метод.

В примере приложения, ответ JSON десериализуется в массив `Face` объектов. При интерпретации результатов из API начертания, обнаруженных эмоций должен интерпретироваться как эмоций с самая высокая оценка как оценки нормализуются на сумму в один. Таким образом в примере приложения отображались распознаваемым эмоций с высший показатель для наибольшего грани обнаруженных в образе. Это достигается с помощью следующего кода:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

На следующем рисунке показан результат процесса распознавания эмоций — в демонстрационном приложении:

![](emotion-recognition-images/emotion-recognition.png "Распознавание эмоций")

## <a name="summary"></a>Сводка

В этой статье описано, как использовать API лицевой стороны для распознавания эмоций — оценка приложения Xamarin.Forms. API начертания принимает выражения лица изображение в качестве входных данных и возвращает данные, включая уверенность в наборе эмоций для каждой грани в образе.

## <a name="related-links"></a>Связанные ссылки

- [Сталкиваются API](/azure/cognitive-services/face/overview/).
- [TODO Когнитивных службы (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Начертания REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
