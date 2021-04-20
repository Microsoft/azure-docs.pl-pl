---
title: Narzędzie do wykrywania anomalii Szybki start dla wielowątkowa biblioteka klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 684c61dfb34d55681904943160ca389c19a4c8db
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732531"
---
Rozpoczynanie pracy z Narzędzie do wykrywania anomalii kowariantną biblioteką klienta dla języka Python. Wykonaj następujące kroki, aby rozpocząć instalację pakietu przy użyciu algorytmów dostarczonych przez usługę. Nowe, wielowariacyjne interfejsy API wykrywania anomalii umożliwiają deweloperom łatwe integrowanie zaawansowanej ai do wykrywania anomalii z grup metryk bez konieczności znajomości uczenia maszynowego ani danych oznaczonych etykietami. Zależności i wzajemne korelacje między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj wielo Narzędzie do wykrywania anomalii biblioteki klienta dla języka Python, aby:

* Wykrywanie anomalii na poziomie systemu z grupy szeregów czasu.
* Gdy poszczególne szeregi czasowe nie będą zbyt wiele powiedzą, a ty musisz przyjrzeć się wszystkim sygnałom, aby wykryć problem.
* Predykcyjna konserwacja drogich zasobów fizycznych z dziesiątkami do setek różnych typów czujników mierzące różne aspekty kondycji systemu.

[Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pakiet (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)
* Biblioteka [analizy danych biblioteki Pandas](https://pandas.pydata.org/)
* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Narzędzie do wykrywania anomalii utwórz zasób Narzędzie do wykrywania anomalii w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Poczekaj na wdrożenie i kliknij przycisk **Przejdź do** zasobu.
    * Klucz i punkt końcowy będą potrzebne z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API Narzędzie do wykrywania anomalii API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.


## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

 Utwórz nowy plik w języku Python i zaimportuj następujące biblioteki.

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

Po zainstalowaniu języka Python bibliotekę klienta można zainstalować za pomocą:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą Narzędzie do wykrywania anomalii klienta dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Trenowanie modelu](#train-the-model)
* [Wykrywanie anomalii](#detect-anomalies)
* [Eksportowanie modelu](#export-model)
* [Usuwanie modelu](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Aby utworzyć nowe Narzędzie do wykrywania anomalii, należy przekazać klucz subskrypcji Narzędzie do wykrywania anomalii skojarzony punkt końcowy. Ustanowimy również źródło danych.  

Aby użyć Narzędzie do wykrywania anomalii wielozmianowych interfejsów API, musimy wytszkolić własny model przed użyciem wykrywania. Dane używane do trenowania to partia szeregów czasu. Każdy szereg czasowy powinien być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasu powinny być spakowane w jednym pliku zip i przekazane do [usługi Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Domyślnie nazwa pliku będzie służyć do reprezentowania zmiennej szeregów czasu. Alternatywnie można meta.jspliku zip, jeśli chcesz, aby nazwa zmiennej różniła się od nazwy pliku zip. Po [wygenerowaniu adresu URL sygnatury](../../../../storage/common/storage-sas-overview.md)dostępu współdzielonego obiektu blob możemy użyć adresu URL do pliku zip na użytek szkolenia.

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

Najpierw przeszkolimy model, sprawdzimy jego stan podczas trenowania, aby ustalić, kiedy trenowanie zostanie ukończone, a następnie pobierzemy najnowszy identyfikator modelu, którego będziemy potrzebować po zakończeniu fazy wykrywania.

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

Użyj i , aby określić, czy w źródle danych `detect_anomaly` `get_dectection_result` występują jakieś anomalie. Musisz przekazać identyfikator modelu, który właśnie został wytrenowany.

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

Jeśli chcesz wyeksportować model, użyj go i przekaż identyfikator modelu, `export_model` który chcesz wyeksportować:

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

Aby usunąć model, `delete_multivariate_model` użyj i przekaż identyfikator modelu, który chcesz usunąć:

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

Uruchom aplikację za pomocą `python` polecenia i nazwy pliku.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
