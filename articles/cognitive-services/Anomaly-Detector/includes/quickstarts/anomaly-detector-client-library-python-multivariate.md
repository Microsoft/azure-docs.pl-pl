---
title: Narzędzie do wykrywania anomalii Szybki start dla wielowariiowej biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: b0eba6d0be1d6a65b911f05dabf3cdbecc9c666d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880283"
---
Wprowadzenie do wielozmianowej Narzędzie do wykrywania anomalii klienta dla języka Python. Wykonaj następujące kroki, aby rozpocząć instalację pakietu przy użyciu algorytmów dostarczonych przez usługę. Nowe, wielowariacyjne interfejsy API wykrywania anomalii umożliwiają deweloperom łatwe integrowanie zaawansowanych rozwiązań AI do wykrywania anomalii z grup metryk bez konieczności znajomości uczenia maszynowego ani danych oznaczonych etykietami. Zależności i korelacje między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj wielo Narzędzie do wykrywania anomalii biblioteki klienta dla języka Python, aby:

* Wykrywanie anomalii na poziomie systemu z grupy szeregów czasu.
* Gdy dowolny szereg czasowy nie będzie ci za dużo mówić i musisz przyjrzeć się wszystkim sygnałom, aby wykryć problem.
* Predykatywna konserwacja drogich zasobów fizycznych z dziesiątkami do setek różnych typów czujników mierząca różne aspekty kondycji systemu.

[Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pakiet (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)  |  [Przykładowy kod](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)  |  [Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)
* Biblioteka [analizy danych biblioteki Pandas](https://pandas.pydata.org/)
* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Narzędzie do wykrywania anomalii utwórz zasób Narzędzie do wykrywania anomalii w witrynie Azure Portal, aby <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" uzyskać klucz i punkt "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie, a następnie kliknij **przycisk Przejdź do** zasobu.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z interfejsem API Narzędzie do wykrywania anomalii API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.


## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

 Utwórz nowy plik języka Python i zaimportuj następujące biblioteki.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Utwórz zmienne dla klucza jako zmienną środowiskową, ścieżkę do pliku danych szeregów czasu i lokalizację platformy Azure subskrypcji. Na przykład `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Po zainstalowaniu języka Python możesz zainstalować bibliotekę klienta za pomocą:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności przy użyciu Narzędzie do wykrywania anomalii klienta dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Trenowanie modelu](#train-the-model)
* [Wykrywanie anomalii](#detect-anomalies)
* [Eksportowanie modelu](#export-model)
* [Usuwanie modelu](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Aby utworzyć wystąpienia nowego klienta Narzędzie do wykrywania anomalii należy przekazać klucz subskrypcji Narzędzie do wykrywania anomalii skojarzony punkt końcowy. Ustanowimy również źródło danych.  

Aby użyć Narzędzie do wykrywania anomalii wielozmianowych interfejsów API, musimy wytterować własny model przed użyciem wykrywania. Dane używane do trenowania to partia szeregów czasu. Każdy szereg czasowy powinien być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasu powinny być spakowane w jednym pliku zip i przekazane do [usługi Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Domyślnie nazwa pliku będzie służyć do reprezentowania zmiennej dla szeregów czasu. Alternatywnie można meta.jspliku zip, jeśli chcesz, aby nazwa zmiennej różniła się od nazwy pliku zip. Po [wygenerowaniu adresu URL sygnatury](../../../../storage/common/storage-sas-overview.md)dostępu współdzielonego obiektu blob możemy użyć adresu URL do pliku zip w celu szkolenia.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Trenowanie modelu

Najpierw przeszkolimy model, sprawdzimy jego stan podczas trenowania, aby ustalić, kiedy trenowanie zostanie ukończone, a następnie pobierzemy najnowszy identyfikator modelu, który będzie nam potrzebny podczas przechodzenia do fazy wykrywania.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Wykrywanie anomalii

Użyj i , aby określić, czy istnieją `detect_anomaly` `get_dectection_result` jakieś anomalie w źródle danych. Musisz przekazać identyfikator modelu, który właśnie został wytrenowany.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Eksportowanie modelu

Jeśli chcesz wyeksportować model, użyj go i przekaż identyfikator `export_model` modelu, który chcesz wyeksportować:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Usuwanie modelu

Aby usunąć użycie modelu `delete_multivariate_model` i przekazać identyfikator modelu, który chcesz usunąć:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Przed uruchomieniem aplikacji musimy dodać kod w celu wywołania nowo utworzonych funkcji.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Przed uruchomieniem warto sprawdzić projekt pod względem pełnego przykładowego kodu, [z](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) których pochodzi ten przewodnik Szybki start.

Mamy również szczegółowe [informacje Jupyter Notebook,](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) które pomogą Ci rozpocząć pracę.

Uruchom aplikację za pomocą `python` polecenia i nazwy pliku.


[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
