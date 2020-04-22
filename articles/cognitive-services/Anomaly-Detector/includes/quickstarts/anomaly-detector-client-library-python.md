---
title: Szybki start biblioteki klienta Detektor anomalii Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759993"
---
Wprowadzenie do biblioteki klienta detektora anomalii dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa Wykrywanie anomalii umożliwia znajdowanie nieprawidłowości w danych szeregów czasowych, automatycznie korzystając z najlepiej dopasowanych modeli, niezależnie od branży, scenariusza lub ilości danych.

Użyj biblioteki klienta Detektor anomalii dla Języka Python, aby:

* Wykrywanie anomalii w całym zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [referencyjna biblioteki Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pakiet (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | Znajdź[przykładowy kod w usłudze GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Klucz i punkt końcowy detektora anomalii
* [Python 3.x](https://www.python.org/)
* [Biblioteka analizy danych Pand](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu detektora anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji języka Python

 Utwórz nowy plik języka Python i zaimportuj następujące biblioteki.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Utwórz zmienne dla klucza jako zmienną środowiskową, ścieżkę do pliku danych szeregów czasowych i lokalizację platformy azure subskrypcji. Na przykład `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Po zainstalowaniu języka Python można zainstalować bibliotekę klienta za pomocą:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Model obiektu

Klient detektora anomalii jest obiektem [AnomalyDetectorClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) który uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia dwie metody wykrywania anomalii: na całym zestawie danych przy użyciu [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)oraz na najnowszym punkcie danych przy użyciu [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Dane szeregów czasowych są wysyłane jako seria [punktów](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) w [request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) obiektu. Obiekt `Request` zawiera właściwości opisujące dane (na przykład[ziarnistość)](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) i parametry wykrywania anomalii. 

Odpowiedź detektora anomalii jest obiektem [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) lub [WholeDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) w zależności od zastosowanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta detektora anomalii dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Ładowanie zestawu danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywanie anomalii w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Dodaj zmienną lokalizacji platformy Azure do punktu końcowego i uwierzytelnij klienta za pomocą klucza.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Ładowanie danych szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki start z [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. W przeglądarce kliknij prawym przyciskiem myszy **pozycję Raw**.
2. Kliknij **pozycję Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik csv.

Te dane serii czasowych są formatowane jako plik csv i będą wysyłane do interfejsu API detektora anomalii.

Załaduj plik danych za `read_csv()` pomocą metody biblioteki Pand i skonsuj pustą zmienną listy do przechowywania serii danych. Iteracja za pośrednictwem pliku i dołączyć dane jako [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) obiektu. Ten obiekt będzie zawierał sygnaturę czasową i wartość liczbową z wierszy pliku danych csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Utwórz [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) obiektu z szeregów czasowych i [szczegółowości](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (lub okresowości) jego punktów danych. Na przykład `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywanie anomalii w całym zestawie danych 

Wywołanie interfejsu API do wykrywania anomalii za pośrednictwem całych danych szeregów czasowych przy użyciu [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) klienta metody. Przechowuj zwrócony obiekt [WholeDetectResponse.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) Iteruje za pośrednictwem `is_anomaly` listy odpowiedzi i wydrukuj indeks dowolnych `true` wartości. Wartości te odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołanie interfejsu API detektora anomalii, aby ustalić, czy najnowszy punkt danych jest anomalią przy użyciu [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) klienta metody i przechowywać [zwrócony Obiekt LastDetectResponse.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) `is_anomaly` Wartość odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `python` pomocą polecenia i nazwy pliku.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
