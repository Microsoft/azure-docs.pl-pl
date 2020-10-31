---
title: 'Szybki Start: sprawdzanie pisowni za pomocą interfejsu API REST i języka Python — sprawdzanie pisowni Bing'
titleSuffix: Azure Cognitive Services
description: Zacznij korzystać z interfejsu API REST sprawdzanie pisowni Bing i języka Python, aby sprawdzić pisownię i gramatykę.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 22311c48c64ae1bd62a055aae3aa39362dd13421
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083808"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Szybki Start: sprawdzanie pisowni za pomocą interfejsu API REST sprawdzanie pisowni Bing i języka Python

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja w języku Python wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. 

Mimo że aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania. Kod źródłowy dla tej aplikacji jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Python [3. x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i Dodaj następujące instrukcje importu:

   ```python
   import requests
   import json
   ```

2. Utwórz zmienne dla tekstu, w którym ma być sprawdzana pisownia, klucza subskrypcji oraz punktu końcowego sprawdzania pisowni Bing. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Tworzenie parametrów dla żądania

1. Utwórz nowy słownik, w którym element `text` jest kluczem, a tekst — wartością.

    ```python
    data = {'text': example_text}
    ```

2. Dodaj parametry żądania: 

   1. Przypisz kod rynkowy do `mkt` parametru `=` operatorem. Kod rynkowy to kod kraju/regionu, z którego pochodzi żądanie. 

   1. Dodaj `mode` parametr z `&` operatorem, a następnie przypisz tryb sprawdzania pisowni. Trybem może być albo `proof` (przechwycenie większości błędów pisowni/gramatyki) lub `spell` (przechwycenie większości błędów pisowni, ale nie tyle błędów gramatycznych). 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Dodaj `Content-Type` Nagłówek i klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Wysyłanie żądania i odczytywanie odpowiedzi

1. Wyślij żądanie POST przy użyciu biblioteki Requests.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Pobierz odpowiedź JSON i wydrukuj ją.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Uruchamianie aplikacji

Jeśli używasz wiersza polecenia, użyj następującego polecenia, aby uruchomić aplikację:

```bash
python <FILE_NAME>.py
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
