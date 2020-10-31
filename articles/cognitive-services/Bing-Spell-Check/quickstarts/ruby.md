---
title: 'Szybki Start: sprawdzanie pisowni za pomocą interfejsu API REST i języka Ruby-sprawdzanie pisowni Bing'
titleSuffix: Azure Cognitive Services
description: Zacznij korzystać z interfejsu API REST sprawdzanie pisowni Bing i języka Ruby, aby sprawdzić pisownię i gramatykę.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: b1b57968b0f9283a8ad2b07f82b7f30e60e8660e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083766"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Szybki Start: sprawdzanie pisowni przy użyciu interfejsu API REST sprawdzanie pisowni Bing i języka Ruby

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Użyj tego przewodnika Szybki start, aby wykonać swoje pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing przy użyciu języka Ruby. Ta prosta aplikacja wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. 

Mimo że aplikacja jest zapisywana w języku Ruby, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania. Kod źródłowy dla tej aplikacji jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Wymagania wstępne

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) lub nowszy.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik Ruby w ulubionym edytorze lub w środowisku IDE, a następnie Dodaj następujące wymagania: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Utwórz zmienne dla klucza subskrypcji, identyfikatora URI punktu końcowego i ścieżki. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu. Utwórz parametry żądania:

   1. Przypisz kod rynkowy do `mkt` parametru `=` operatorem. Kod rynkowy to kod kraju/regionu, z którego pochodzi żądanie. 

   1. Dodaj `mode` parametr z `&` operatorem, a następnie przypisz tryb sprawdzania pisowni. Trybem może być albo `proof` (przechwycenie większości błędów pisowni/gramatyki) lub `spell` (przechwycenie większości błędów pisowni, ale nie tyle błędów gramatycznych). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Wysyłanie żądania sprawdzania pisowni

1. Utwórz identyfikator URI na podstawie identyfikatora URI hosta, ścieżki oraz ciągu parametrów. Ustaw, aby zapytanie zawierało tekst, który ma być sprawdzany.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Utwórz żądanie przy użyciu identyfikatora URI złożonego wcześniej. Dodaj klucz do nagłówka `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Wyślij żądanie.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Pobierz odpowiedź JSON i wyświetl ją w konsoli. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Skompiluj i Uruchom projekt. Jeśli używasz wiersza polecenia, użyj następującego polecenia, aby uruchomić aplikację:

   ```bash
   ruby <FILE_NAME>.rb
   ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja wersji 7 interfejsu API sprawdzanie pisowni Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
