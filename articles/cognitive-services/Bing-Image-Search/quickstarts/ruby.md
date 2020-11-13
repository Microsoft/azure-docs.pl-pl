---
title: 'Szybki Start: Wyszukiwanie obrazów przy użyciu interfejsu API REST wyszukiwanie obrazów Bing i języka Ruby'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania wyszukiwania obrazów do interfejsu API wyszukiwania obrazów Bing przy użyciu języka Ruby i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 83679af635de13040ed7d8f314c9a358d162deda
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591952"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-ruby"></a>Szybki Start: Wyszukiwanie obrazów przy użyciu interfejsu API REST wyszukiwanie obrazów Bing i języka Ruby

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania obrazów Bing i odbieranie odpowiedzi w formacie JSON. Ta prosta aplikacja w języku Ruby wysyła zapytanie wyszukiwania do interfejsu API i wyświetla nieprzetworzone wyniki.

Mimo że aplikacja jest zapisywana w języku Ruby, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

Kod źródłowy dla tego przykładu jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingImageSearchv7.rb).

## <a name="prerequisites"></a>Wymagania wstępne

* [Najnowsza wersja środowiska Ruby](https://www.ruby-lang.org/en/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Aby uzyskać więcej informacji, zobacz [Cognitive Services Cennik — wyszukiwanie Bing API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Zaimportuj następujące pakiety do pliku kodu:

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, ścieżki wyszukiwania interfejsu API obrazów, klucza subskrypcji i wyszukiwanego terminu. W przypadku programu `uri` można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

    ```ruby
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/images/search"
    term = "puppies"
    ```

## <a name="format-and-make-an-api-request"></a>Formatowanie i wykonywanie żądania interfejsu API

Użyj zmiennych z poprzedniego kroku, aby sformatować adres URL wyszukiwania dla żądania interfejsu API. Następnie Wyślij żądanie.

```ruby
uri = URI(uri + path + "?q=" + URI.escape(term))


request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end
```

## <a name="process-and-print-the-json"></a>Przetwarzanie i wyświetlanie odpowiedzi JSON

Po otrzymaniu odpowiedzi można przeanalizować kod JSON i pobrać z niego wartości. Na przykład adres URL miniatury pierwszego wyniku i łączną liczbę zwróconych obrazów.

```ruby
response.each_header do |key, value|
    # header names are lowercased
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

parsed_json = JSON.parse(response.body)
total_returned_images = parsed_json["totalEstimatedMatches"]
first_result = parsed_json["value"][0]["thumbnailUrl"]

puts "total number of returned matches: #{total_returned_images}"
puts "Url to the thumbnail of the first returned search result: #{first_result}"
```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta w celu pokazania pojedynczego wyniku.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też

* [Co to jest interfejs API wyszukiwania obrazów Bing?](../overview.md)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)   
* [Dokumentacja usługi Azure Cognitive Services](../../index.yml)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)