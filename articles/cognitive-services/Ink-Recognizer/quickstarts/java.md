---
title: 'Szybki Start: Rozpoznawanie cyfrowego atramentu za pomocą interfejsu API REST rozpoznawania atramentu i języka Java'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API rozpoznawania pisma odręcznego i języka Java, aby rozpocząć rozpoznawanie cyfrowych pociągnięć atramentu w tym przewodniku Szybki Start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: dea46a07a7357be6079c52634be8ea2ff79cc8f3
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369124"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Szybki Start: Rozpoznawanie cyfrowego atramentu za pomocą interfejsu API REST rozpoznawania atramentu i języka Java

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z interfejsu API rozpoznawania atramentu na potrzeby pociągnięć atramentów cyfrowych Ta aplikacja Java wysyła żądanie interfejsu API zawierające dane pociągnięcia odręcznego w formacie JSON i pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji cyfrowego odręcznego. Ten przewodnik Szybki Start wysyła dane pociągnięć odręcznych dla poniższego przykładu napisanego ręcznie z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw Java &trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszy.

- Zaimportuj te biblioteki z repozytorium Maven
    - [Kod JSON w pakiecie java](https://mvnrepository.com/artifact/org.json/json)
    - Pakiet [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Przykładowe dane pociągnięcia farbą dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania atramentu

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Utwórz zmienne dla klucza subskrypcji, punktu końcowego i pliku JSON. Punkt końcowy zostanie później dołączony do identyfikatora URI rozpoznawania pisma odręcznego.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję o nazwie `sendRequest()` , która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz `CloseableHttpClient` obiekt, który może wysyłać żądania do interfejsu API. Wyślij żądanie do `HttpPut` obiektu żądania, łącząc punkt końcowy i adres URL aparatu rozpoznawania atramentu.

3. Użyj `setHeader()` funkcji żądania, aby ustawić `Content-Type` nagłówek na `application/json` , i Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka.

4. Użyj `setEntity()` funkcji żądania do wysyłanych danych.   

5. Użyj `execute()` funkcji klienta, aby wysłać żądanie i zapisać ją w `CloseableHttpResponse` obiekcie. 

6. Utwórz `HttpEntity` obiekt do przechowywania zawartości odpowiedzi. Pobierz zawartość za pomocą programu `getEntity()` . Jeśli odpowiedź nie jest pusta, zwróć ją.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Wyślij żądanie rozpoznawania pisma odręcznego

Utwórz metodę o nazwie, `recognizeInk()` Aby rozpoznać dane pociągnięcia odręcznego. Wywołaj `sendRequest()` metodę utworzoną powyżej przy użyciu swojego punktu końcowego, adresu URL, klucza subskrypcji i danych JSON. Pobierz wynik i wydrukuj go w konsoli programu.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Ładowanie danych cyfrowego atramentu i wysyłanie żądania

1. W głównej metodzie aplikacji przeczytaj plik JSON zawierający dane, które zostaną dodane do żądań.

2. Wywołaj funkcję rozpoznawania pisma odręcznego utworzoną powyżej.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację i Wyświetl odpowiedź

Uruchom aplikację. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź na kod JSON można również znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w aplikacji do cyfrowego odkróla, zapoznaj się z następującymi przykładowymi aplikacjami w witrynie GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)