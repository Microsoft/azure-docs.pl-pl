---
title: 'Samouczek: Wyodrębnienie szczegółów obrazu za pomocą interfejsu API REST i języka C# wyszukiwanie obrazów Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego samouczka, aby utworzyć aplikację w języku C#, która wyodrębnia szczegóły obrazu przy użyciu interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 76670c6a061ce3f257d9df8cfe61b1cc020194c6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100222"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Samouczek: wyodrębnianie szczegółów obrazu przy użyciu interfejs API wyszukiwania obrazów Bing i języka C#

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania obrazów Bing udostępnia wiele [punktów końcowych](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). Punkt końcowy `/details` akceptuje żądania POST z obrazem i może zwracać różnorodne szczegółowe informacje o tym obrazie. Ta aplikacja języka C# wysyła obraz przy użyciu tego interfejsu API i wyświetla szczegółowe informacje zwrócone przez usługę Bing, które są obiektami JSON, takimi jak poniższe:

![[Wyniki JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

W tym samouczku wyjaśniono:

> [!div class="checklist"]
> * Używanie punktu końcowego `/details` wyszukiwania obrazów w żądaniu `POST`
> * Określanie nagłówków żądania
> * Określanie wyników przy użyciu parametrów adresu URL
> * Przekazywanie danych obrazu i wysyłanie żądania `POST`
> * Drukowanie w konsoli wyników w formacie JSON

Kod źródłowy dla tego przykładu jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Konstruowanie żądania wyszukiwania szczegółów obrazu

Poniżej przedstawiono punkt końcowy `/details`, który akceptuje żądania POST z danymi obrazu w treści żądania. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

Podczas tworzenia adresu URL żądania wyszukiwania parametr `modules` śledzi powyższy punkt końcowy i określa typy szczegółowych informacji, które będą zawarte w wynikach:

* `modules=All`
* `modules=RecognizedEntities` (osoby lub miejsca widoczne na obrazie)

Określ parametr `modules=All` w żądaniu POST, aby uzyskać tekst JSON, który zawiera następujące elementy:

* `bestRepresentativeQuery` — zapytanie Bing, które zwraca obrazy podobne do przekazanego obrazu
* `detectedObjects` — obiekty znajdujące się na obrazie
* `image` — metadane obrazu
* `imageInsightsToken` — token dla późniejszych żądań GET, które pobierają z obrazu elementy `RecognizedEntities` (osoby lub miejsca widoczne na obrazie).
* `imageTags` — tagi dla obrazu
* `pagesIncluding` — strony internetowe, które zawierają obraz
* `relatedSearches` — wyszukiwania oparte na szczegółach obrazu.
* `visuallySimilarImages` — podobne obrazy w Internecie.

Określ parametr `modules=RecognizedEntities` w żądaniu POST, aby pobrać tylko element `imageInsightsToken`, który może zostać użyty w następującym później żądaniu GET w celu zidentyfikowania osób lub miejsc na obrazie.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Tworzenie obiektu WebClient i ustawianie nagłówków dla żądania interfejsu API

Utwórz obiekt `WebClient`, a następnie ustaw nagłówki. Wszystkie żądania do interfejsu API wyszukiwania Bing wymagają klucza `Ocp-Apim-Subscription-Key`. Żądanie `POST` przekazania obrazu musi także określać `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Przekazywanie obrazu i wyświetlanie wyników

Metoda `UpLoadFile()` klasy `WebClient` formatuje dane dla żądania `POST` z uwzględnieniem formatowania `RequestStream` i wywoływania `HttpWebRequest`.

Wywołaj funkcję `WebClient.UpLoadFile()` z punktem końcowym `/details` i plikiem obrazu do przekazania. Użyj odpowiedzi w formacie JSON, aby zainicjować wystąpienie struktury `SearchResult` i zachować odpowiedź.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Odpowiedź JSON można następnie wydrukować w konsoli.

## <a name="use-an-image-insights-token-in-a-request"></a>Używanie w żądaniu tokenu szczegółowych informacji obrazu

Aby użyć tokenu `ImageInsightsToken` zwróconego z wynikami żądania `POST`, możesz dodać go do żądania `GET`. Przykład:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Jeśli na obrazie znajdują się osoby lub miejsca, które można zidentyfikować, to żądanie zwróci informacje na ich temat.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyświetlanie obrazów i opcji wyszukiwania w jednostronicowej aplikacji internetowej](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
