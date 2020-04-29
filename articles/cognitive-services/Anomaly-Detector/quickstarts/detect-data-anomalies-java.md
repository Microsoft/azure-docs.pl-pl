---
title: 'Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i środowiska Java'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wykrywania anomalii w celu wykrywania niezależności w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239051"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i środowiska Java

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych szeregów czasowych. Ta aplikacja Java wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykryj anomalie jako partię                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych oraz pozycje wykryte anomalie. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszy.
- Klucz wykrywania anomalii i punkt końcowy
- Zaimportuj te biblioteki z repozytorium Maven
    - [Kod JSON w pakiecie java](https://mvnrepository.com/artifact/org.json/json)
    - Pakiet [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. Utwórz nowy projekt Java i zaimportuj poniższe biblioteki.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później w celu utworzenia adresów URL żądań interfejsu API.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie najnowszego punktu danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję o nazwie `sendRequest()` , która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz `CloseableHttpClient` obiekt, który może wysyłać żądania do interfejsu API. Wyślij żądanie do obiektu `HttpPost` żądania przez połączenie punktu końcowego i adresu URL wykrywania anomalii.

3. Użyj `setHeader()` funkcji żądania, aby ustawić `Content-Type` nagłówek na `application/json`, i Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka.

4. Użyj `setEntity()` funkcji żądania do wysyłanych danych.

5. Użyj `execute()` funkcji klienta, aby wysłać żądanie i zapisać ją w `CloseableHttpResponse` obiekcie.

6. Utwórz `HttpEntity` obiekt do przechowywania zawartości odpowiedzi. Pobierz zawartość za pomocą `getEntity()`programu. Jeśli odpowiedź nie jest pusta, zwróć ją.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Wykryj anomalie jako partię

1. Utwórz metodę o nazwie `detectAnomaliesBatch()` , aby wykrywać anomalie w danych jako partię. Wywołaj `sendRequest()` metodę utworzoną powyżej przy użyciu swojego punktu końcowego, adresu URL, klucza subskrypcji i danych JSON. Pobierz wynik i wydrukuj go w konsoli programu.

2. Jeśli odpowiedź zawiera `code` pole, Wydrukuj kod błędu i komunikat o błędzie.

3. W przeciwnym razie Znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera wartość logiczną odnoszącą się do tego, czy dany punkt danych jest anomalią. Pobierz tablicę JSON i wykonaj iterację, drukując indeks dowolnych `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Utwórz metodę o nazwie `detectAnomaliesLatest()` w celu wykrycia stanu anomalii ostatniego punktu danych w zestawie danych. Wywołaj `sendRequest()` metodę utworzoną powyżej przy użyciu swojego punktu końcowego, adresu URL, klucza subskrypcji i danych JSON. Pobierz wynik i wydrukuj go w konsoli programu.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i Wyślij żądanie

1. W głównej metodzie aplikacji przeczytaj plik JSON zawierający dane, które zostaną dodane do żądań.

2. Wywołaj dwie utworzone powyżej funkcje wykrywania anomalii.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe linki, aby wyświetlić odpowiedź JSON w serwisie GitHub:
* [Przykładowa reakcja wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład ostatniej odpowiedzi wykrywania punktu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
