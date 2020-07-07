---
title: 'Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i języka C #'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wykrywania anomalii w celu wykrywania niezależności w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 585731212fa31be2757d5b5d4c4e0a2ef1212ca8
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980224"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i języka C # 

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych szeregów czasowych. Ta aplikacja w języku C# wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wykryj anomalie jako partię                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych oraz pozycje wykryte anomalie. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                        |

 Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" Utwórz zasób wykrywania anomalii "  target="_blank"> Utwórz zasób wykrywania anomalii <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    - Potrzebny będzie klucz i punkt końcowy z zasobu utworzonego w celu połączenia aplikacji z interfejsem API wykrywania anomalii. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
- Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/)
- Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet. Aby zainstalować Newtonsoft.Jsna jako pakiet NuGet w programie Visual Studio:
    
    1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.
    2. Wybierz pozycję **Zarządzaj pakietami NuGet**.
    3. Wyszukaj *Newtonsoft.Js* i zainstaluj pakiet.

- Jeśli używasz systemu Linux/MacOS, ta aplikacja może być uruchamiana przy użyciu narzędzia [mono](https://www.mono-project.com/).

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli i Dodaj następujące pakiety. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później w celu utworzenia adresów URL żądań interfejsu API.

    | Metoda wykrywania                   | Identyfikator URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Wykrywanie partii                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Wykrywanie najnowszego punktu danych | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję asynchroniczną o nazwie `Request` , która przyjmuje utworzone powyżej zmienne.

2. Ustaw informacje o protokole i nagłówku zabezpieczeń klienta przy użyciu `HttpClient` obiektu. Pamiętaj, aby dodać klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka. Następnie Utwórz `StringContent` obiekt dla żądania.

3. Wyślij żądanie `PostAsync()` , a następnie Zwróć odpowiedź.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Wykryj anomalie jako partię

1. Utwórz nową funkcję o nazwie `detectAnomaliesBatch()` . Utwórz żądanie i wyślij je, wywołując `Request()` funkcję z punktem końcowym, kluczem subskrypcji, adresem URL na potrzeby wykrywania anomalii partii oraz danymi szeregów czasowych.

2. Deserializacji obiektu JSON i Zapisz go w konsoli.

3. Jeśli odpowiedź zawiera `code` pole, Wydrukuj kod błędu i komunikat o błędzie. 

4. W przeciwnym razie Znajdź pozycje anomalii w zestawie danych. `isAnomaly`Pole odpowiedzi zawiera tablicę wartości logicznych, z których każdy wskazuje, czy punkt danych jest anomalią. Przekonwertuj ten element na tablicę ciągów z funkcją obiektu Response `ToObject<bool[]>()` . Wykonaj iterację tablicy i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

1. Utwórz nową funkcję o nazwie `detectAnomaliesLatest()` . Utwórz żądanie i wyślij je, wywołując `Request()` funkcję z punktem końcowym, kluczem subskrypcji, adresem URL dla ostatniego wykrywania anomalii w punkcie i danymi szeregów czasowych.

2. Deserializacji obiektu JSON i Zapisz go w konsoli.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i Wyślij żądanie

1. W głównej metodzie aplikacji Załaduj dane z szeregów czasowych JSON przy użyciu programu `File.ReadAllText()` . 

2. Wywołaj utworzone powyżej funkcje wykrywania anomalii. Użyj, `System.Console.ReadKey()` Aby zachować otwarte okno konsoli po uruchomieniu aplikacji.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe linki, aby wyświetlić odpowiedź JSON w serwisie GitHub:
* [Przykładowa reakcja wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład ostatniej odpowiedzi wykrywania punktu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
