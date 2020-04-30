---
title: Wykrywacz anomalii JavaScript — Biblioteka klienta — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: dca07f37377880008160fa3521d66ed4f6afc084
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759986"
---
Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla języka JavaScript. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla języka JavaScript, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | referencyjna[biblioteki kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [źródłowego (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | —[Znajdź kod w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org/)
* Klucz wykrywania anomalii i punkt końcowy

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-azure-resource"></a>Tworzenie zasobu platformy Azure wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

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

Zainstaluj pakiety `ms-rest-azure` i `azure-cognitiveservices-anomalydetector` npm. Biblioteka analizy woluminów CSV jest również używana w tym przewodniku szybki start:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

`package.json` Plik aplikacji zostanie zaktualizowany z zależnościami.

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient oferuje dwie metody wykrywania anomalii: w całym zestawie danych przy użyciu [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)i najnowszego punktu danych przy użyciu [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Dane szeregów czasowych są wysyłane jako seria [punktów](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) w obiekcie [żądania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) . `Request` Obiekt zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) ) oraz parametry wykrywania anomalii. 

Odpowiedź wykrywania anomalii jest obiektem [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) lub [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) , w zależności od używanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta wykrywania anomalii dla środowiska Node. js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Załaduj zestaw danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywaj anomalie w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie obiektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) z punktem końcowym i poświadczeniami.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Załaduj dane szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z witryny [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. W przeglądarce kliknij prawym przyciskiem myszy pozycję **RAW**.
2. Kliknij pozycję **Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik CSV.

Te dane szeregów czasowych są formatowane jako plik CSV i wysyłane do interfejsu API wykrywania anomalii.

Odczytaj plik danych za pomocą `readFileSync()` metody biblioteki analizy woluminów CSV i Przeanalizuj plik za pomocą `parse()`programu. Dla każdego wiersza wypchnij obiekt [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) zawierający sygnaturę czasową i wartość liczbową.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Wywołaj interfejs API w celu wykrycia anomalii przez całą serię czasową jako partię przy użyciu metody [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Zapisz zwrócony obiekt [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) . Wykonaj iterację na `isAnomaly` liście odpowiedzi i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołaj interfejs API wykrywania anomalii, aby określić, czy najnowszy punkt danych jest anomalią przy użyciu metody [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta i przechowywanie zwróconego obiektu [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) . `isAnomaly` Wartość odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
