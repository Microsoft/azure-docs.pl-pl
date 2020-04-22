---
title: Szybki start biblioteki klienta Detektor anomalii .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: f5f135dd44ad9e5ebd1df2144295f9a3cf3cb743
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760000"
---
Wprowadzenie do biblioteki klienta detektora anomalii dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa Wykrywanie anomalii umożliwia znajdowanie nieprawidłowości w danych szeregów czasowych, automatycznie korzystając z najlepiej dopasowanych modeli, niezależnie od branży, scenariusza lub ilości danych.

Użyj biblioteki klienta Detektor anomalii dla platformy .NET, aby:

* Wykrywanie anomalii w całym zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Dokumentacja](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [referencyjna biblioteki Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | Znajdź kod w[usłudze GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Klucz i punkt końcowy detektora anomalii

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu detektora anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji .NET Core

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `anomaly-detector-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" z jednym plikiem źródłowym języka C#: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta Detektor anomalii dla platformy .NET za pomocą następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

W katalogu projektu otwórz plik *program.cs* i dodaj następujące `directives`elementy za pomocą:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

W `main()` metodzie aplikacji utwórz zmienne dla lokalizacji platformy Azure zasobu i klucz jako zmienną środowiskową. Jeśli utworzono zmienną środowiskową po uruchomieniu aplikacji, edytor, IDE lub powłoka uruchomiona będzie musiała zostać zamknięta i ponownie załadowana, aby uzyskać dostęp do zmiennej.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Model obiektu

Klient detektora anomalii jest obiektem [AnomalyDetectorClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) który uwierzytelnia się na platformie Azure przy użyciu [funkcji ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), która zawiera klucz. Klient udostępnia dwie metody wykrywania anomalii: na całym zestawie danych przy użyciu [WholeDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)oraz na najnowszym punkcie danych przy użyciu [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Dane szeregów czasowych są wysyłane jako seria [punktów](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) w [request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) obiektu. Obiekt `Request` zawiera właściwości opisujące dane (na przykład[ziarnistość)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) i parametry wykrywania anomalii. 

Odpowiedź detektora anomalii jest obiektem [WholeDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) lub [LastDetectResponse,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) w zależności od zastosowanej metody. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta detektora anomalii dla platformy .NET:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Ładowanie zestawu danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywanie anomalii w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

W nowej metodzie wystąpienia klienta z punktem końcowym i kluczem. Utwórz [obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt [AnomalyDetectorClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Ładowanie danych szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki start z [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. W przeglądarce kliknij prawym przyciskiem myszy **pozycję Raw**.
2. Kliknij **pozycję Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik csv.

Te dane serii czasowych są formatowane jako plik csv i będą wysyłane do interfejsu API detektora anomalii.

Utwórz nową metodę odczytu w danych szeregów czasowych i dodaj ją do [obiektu Request.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Nawoż `File.ReadAllLines()` do ścieżki pliku i utwórz listę obiektów [punktowych](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) i rozłóż nowe znaki wiersza. Wyodrębnij wartości i oddziel sygnaturę dat od jego `Point` wartości liczbowej i dodaj je do nowego obiektu. 

Zrób `Request` obiekt z serii punktów `Granularity.Daily` i [ziarnistość](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (lub okresowość) punktów danych.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywanie anomalii w całym zestawie danych 

Utwórz metodę wywołania metody [WholeDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta `Request` z obiektem i oczekuje na odpowiedź jako obiekt [WholeDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) Jeśli szeregi czasowe zawierają jakieś anomalie, iterować za pomocą wartości [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) odpowiedzi i drukować dowolne, które są `true`. Wartości te odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Utwórz metodę wywołania metody [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta `Request` z obiektem i oczekuje na odpowiedź jako obiekt [LastDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) Sprawdź odpowiedź [IsAnomal atrybut,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) aby ustalić, czy ostatni punkt danych wysłany był anomalią, czy nie. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `dotnet run` pomocą polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
