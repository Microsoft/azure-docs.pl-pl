---
title: Biblioteka klienta Python wykrywania anomalii — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759993"
---
Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla języka Python, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Biblioteka dokumentacji](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | referencyjnej[biblioteki Code Source](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [(PyPi) (kod źródłowy)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Wyszukiwanie przykładowego kodu w witrynie GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Klucz wykrywania anomalii i punkt końcowy
* [Python 3.x](https://www.python.org/)
* [Biblioteka analizy danych Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

 Utwórz nowy plik w języku Python i zaimportuj następujące biblioteki.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Utwórz zmienne klucza jako zmienną środowiskową, ścieżkę do pliku danych szeregów czasowych i lokalizację subskrypcji platformy Azure. Na przykład `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować bibliotekę kliencką z:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient oferuje dwie metody wykrywania anomalii: w całym zestawie danych przy użyciu [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)i najnowszego punktu danych przy użyciu [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Dane szeregów czasowych są wysyłane jako serie [punktów](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) w obiekcie [żądania](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) . `Request` Obiekt zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) oraz parametry wykrywania anomalii. 

Odpowiedź wykrywania anomalii jest obiektem [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) lub [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) , w zależności od używanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta wykrywania anomalii dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Załaduj zestaw danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywaj anomalie w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodaj zmienną lokalizacji platformy Azure do punktu końcowego i Uwierzytelnij klienta przy użyciu klucza.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Załaduj dane szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z witryny [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. W przeglądarce kliknij prawym przyciskiem myszy pozycję **RAW**.
2. Kliknij pozycję **Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik CSV.

Te dane szeregów czasowych są formatowane jako plik CSV i wysyłane do interfejsu API wykrywania anomalii.

Załaduj plik danych za pomocą `read_csv()` metody biblioteki Pandas i ustaw pustą zmienną listy, aby przechowywać serie danych. Wykonaj iterację pliku i Dołącz dane jako obiekt [punktu](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) . Ten obiekt będzie zawierać sygnaturę czasową i wartość liczbową z wierszy pliku danych CSV. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Utwórz obiekt [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) z serią czasową oraz [stopień szczegółowości](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (lub okresowość) punktów danych. Na przykład `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Wywołaj interfejs API w celu wykrycia anomalii za pośrednictwem wszystkich danych szeregów czasowych przy użyciu metody [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) klienta. Zapisz zwrócony obiekt [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) . Wykonaj iterację na `is_anomaly` liście odpowiedzi i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołaj interfejs API wykrywania anomalii, aby określić, czy najnowszy punkt danych jest anomalią przy użyciu metody [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) klienta i przechowuj zwrócony obiekt [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) . `is_anomaly` Wartość odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `python` polecenia i nazwy pliku.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
