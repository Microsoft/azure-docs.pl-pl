---
title: Wykrywacz anomalii JavaScript — Biblioteka klienta — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: 36b8a6952a8dc0b34df7bf32a708c71547bf5b33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947130"
---
Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla języka JavaScript. Wykonaj następujące kroki, aby zainstalować pakiet startowy przy użyciu algorytmów udostępnianych przez usługę. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla języka JavaScript, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych
* Wykrywaj punkty zmian trendu w zestawie danych.

[Dokumentacja biblioteki](https://go.microsoft.com/fwlink/?linkid=2090788)  |  [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector)  |  [Znajdź kod w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" Utwórz zasób wykrywania anomalii "  target="_blank"> Utwórz zasób wykrywania anomalii <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Potrzebny będzie klucz i punkt końcowy z zasobu utworzonego w celu połączenia aplikacji z interfejsem API wykrywania anomalii. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem. 

```console
npm init
```

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Utwórz zmienne i klucz punktu końcowego platformy Azure dla zasobu. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej. Utwórz kolejną zmienną dla przykładowego pliku danych, który zostanie pobrany w późniejszym kroku, i pustą listę punktów danych. Następnie Utwórz `ApiKeyCredentials` obiekt, który będzie zawierać klucz.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj `ms-rest-azure` pakiety i `azure-cognitiveservices-anomalydetector` npm. Biblioteka analizy woluminów CSV jest również używana w tym przewodniku szybki start:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient może przeprowadzić wykrywanie anomalii w całym zestawie danych przy użyciu [entireDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--servicecallback-entiredetectresponse--)lub najnowszego punktu danych przy użyciu [LastDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-). Metoda [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) wykrywa punkty, które oznaczają zmiany w trendzie. 

Dane szeregów czasowych są wysyłane jako seria [punktów](/javascript/api/@azure/cognitiveservices-anomalydetector/point) w obiekcie [żądania](/javascript/api/@azure/cognitiveservices-anomalydetector/request) . `Request`Obiekt zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](/javascript/api/@azure/cognitiveservices-anomalydetector/request#granularity) ) oraz parametry wykrywania anomalii. 

Odpowiedź wykrywania anomalii jest obiektem [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse), [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse)lub [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) , w zależności od używanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności z użyciem biblioteki klienta wykrywania anomalii dla Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Załaduj zestaw danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywaj anomalie w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)
* [Wykrywaj punkty zmian w zestawie danych](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie obiektu [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) z punktem końcowym i poświadczeniami.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Załaduj dane szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z witryny [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. W przeglądarce kliknij prawym przyciskiem myszy pozycję **RAW**.
2. Kliknij pozycję **Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik CSV.

Te dane szeregów czasowych są formatowane jako plik CSV i wysyłane do interfejsu API wykrywania anomalii.

Odczytaj plik danych za pomocą metody biblioteki analizy woluminów CSV `readFileSync()` i Przeanalizuj plik za pomocą programu `parse()` . Dla każdego wiersza wypchnij obiekt [Point](/javascript/api/@azure/cognitiveservices-anomalydetector/point) zawierający sygnaturę czasową i wartość liczbową.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Wywołaj interfejs API w celu wykrycia anomalii przez całą serię czasową jako partię przy użyciu metody [entireDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--msrest-requestoptionsbase-) klienta. Zapisz zwrócony obiekt [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse) . Wykonaj iterację na `isAnomaly` liście odpowiedzi i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołaj interfejs API wykrywania anomalii, aby określić, czy najnowszy punkt danych jest anomalią przy użyciu metody [lastDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-) klienta i przechowywanie zwróconego obiektu [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse) . `isAnomaly`Wartość odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Wykryj punkty zmian w zestawie danych

Wywołaj interfejs API w celu wykrycia punktów zmiany w szeregu czasowym przy użyciu metody [detectChangePoint ()](https://go.microsoft.com/fwlink/?linkid=2090788) klienta. Zapisz zwrócony obiekt [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) . Wykonaj iterację na `isChangePoint` liście odpowiedzi i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksom punktów zmiany trendu, jeśli zostały znalezione.

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
