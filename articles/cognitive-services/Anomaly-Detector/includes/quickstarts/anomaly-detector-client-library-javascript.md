---
title: Szybki start biblioteki klienta JavaScript detektora anomalii
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: dca07f37377880008160fa3521d66ed4f6afc084
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759986"
---
Wprowadzenie do biblioteki klienta detektora anomalii dla języka JavaScript. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa Wykrywanie anomalii umożliwia znajdowanie nieprawidłowości w danych szeregów czasowych, automatycznie korzystając z najlepiej dopasowanych modeli, niezależnie od branży, scenariusza lub ilości danych.

Użyj biblioteki klienta Detektor anomalii dla języka JavaScript, aby:

* Wykrywanie anomalii w całym zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [referencyjna Biblioteka kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [źródłowy Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | Znajdź kod w[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja pliku [Node.js](https://nodejs.org/)
* Klucz i punkt końcowy detektora anomalii

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-azure-resource"></a>Tworzenie zasobu platformy Azure detektora anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację `package.json` węzła z plikiem. 

```console
npm init
```

Tworzenie pliku `index.js` o nazwie i importowanie następujących bibliotek:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Tworzenie zmiennych punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej. Utwórz inną zmienną dla przykładowego pliku danych, który zostanie pobrany w późniejszym kroku, oraz pustą listę punktów danych. Następnie utwórz obiekt zawierający `ApiKeyCredentials` klucz.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Zainstaluj `ms-rest-azure` pakiety i `azure-cognitiveservices-anomalydetector` NPM. Biblioteka csv-parse jest również używana w tym przewodniku Szybki start:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

`package.json` Plik aplikacji zostanie zaktualizowany o zależności.

## <a name="object-model"></a>Model obiektu

Klient detektora anomalii jest obiektem [AnomalyDetectorClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) który uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia dwie metody wykrywania anomalii: na całym zestawie danych przy użyciu [wholeDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)i na najnowszym punkcie danych przy użyciu [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Dane szeregów czasowych są wysyłane jako seria [punktów](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) w [request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) obiektu. Obiekt `Request` zawiera właściwości opisujące dane (na przykład[ziarnistość)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) i parametry wykrywania anomalii. 

Odpowiedź detektora anomalii jest obiektem [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) lub [WholeDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) w zależności od zastosowanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta detektora anomalii dla pliku Node.js:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Ładowanie zestawu danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywanie anomalii w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Tworzenie wystąpienia obiektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) z punktem końcowym i poświadczeniami.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Ładowanie danych szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki start z [GitHub:](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv)
1. W przeglądarce kliknij prawym przyciskiem myszy **pozycję Raw**.
2. Kliknij **pozycję Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik csv.

Te dane serii czasowych są formatowane jako plik csv i będą wysyłane do interfejsu API detektora anomalii.

Odczyt pliku danych za pomocą `readFileSync()` metody biblioteki csv-parse i `parse()`przeanalizowanie pliku za pomocą pliku . Dla każdego wiersza wypchnij obiekt [Punkt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) zawierający sygnaturę czasową i wartość liczbową.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywanie anomalii w całym zestawie danych 

Wywołaj interfejs API, aby wykryć anomalie za pośrednictwem całej serii czasowej jako partia za pomocą [metody wholeDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Przechowuj zwrócony obiekt [WholeDetectResponse.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) Iteruje za pośrednictwem `isAnomaly` listy odpowiedzi i wydrukuj indeks dowolnych `true` wartości. Wartości te odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołanie interfejsu API detektora anomalii, aby ustalić, czy najnowszy punkt danych jest anomalią przy użyciu metody [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta i przechowywać zwrócony obiekt [LastDetectResponse.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) `isAnomaly` Wartość odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `node` pomocą polecenia w pliku szybkiego startu.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
