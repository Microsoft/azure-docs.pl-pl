---
title: Wykrywanie anomalii Python wieloczynnikowa Client Library — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 9b848f6c86f2ff2e95fa5cc191b088b7175f2311
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316034"
---
Rozpocznij pracę z biblioteką klienta wykrywania anomalii wieloczynnikowa dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet startowy przy użyciu algorytmów udostępnianych przez usługę. Nowe interfejsy API wykrywania anomalii w usłudze wieloczynnikowa umożliwiają deweloperom łatwe integrowanie zaawansowanej pamięci AI do wykrywania anomalii z grup metryk, bez konieczności znajomości wiedzy uczenia maszynowego ani etykietowania danych. Zależności i wzajemnych korelacji między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj biblioteki wieloczynnikowa Client dla języka Python, aby:

* Wykrywaj anomalie na poziomie systemu z grupy szeregów czasowych.
* Gdy każda z poszczególnych szeregów czasowych nie powiedzie się, należy sprawdzić wszystkie sygnały w celu wykrycia problemu.
* Predicative konserwację kosztownych zasobów fizycznych z dziesiątki do setek różnych typów czujników mierzących różne aspekty kondycji systemu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)
* [Biblioteka analizy danych Pandas](https://pandas.pydata.org/)
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" Utwórz zasób wykrywania anomalii "  target="_blank"> Utwórz zasób wykrywania anomalii </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Potrzebny będzie klucz i punkt końcowy z zasobu utworzonego w celu połączenia aplikacji z interfejsem API wykrywania anomalii. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.


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

Utwórz zmienne klucza jako zmienną środowiskową, ścieżkę do pliku danych szeregów czasowych i lokalizację subskrypcji platformy Azure. Na przykład `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować bibliotekę kliencką z:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta wykrywania anomalii dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Trenowanie modelu](#train-the-model)
* [Wykrywaj anomalie](#detect-anomalies)
* [Eksportuj model](#export-model)
* [Usuń model](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Aby utworzyć wystąpienie nowego klienta wykrywania anomalii, należy przekazać klucz subskrypcji wykrywania anomalii i skojarzony punkt końcowy. Utworzymy również źródło danych.  

Aby korzystać z interfejsów API wieloczynnikowa wykrywania anomalii, musimy nauczyć nasz model przed użyciem wykrywania. Dane używane do szkoleń to partia szeregów czasowych, każda seria czasu powinna być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie szeregi czasowe powinny być spakowane w jednym pliku zip i przekazywane do [usługi Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Domyślnie nazwa pliku zostanie użyta do reprezentowania zmiennej dla szeregów czasowych. Alternatywnie, dodatkowe meta.jsw pliku można umieścić w pliku zip, jeśli chcesz, aby nazwa zmiennej była inna niż nazwa pliku zip. Po wygenerowaniu [adresu URL SAS (sygnatury dostępu współdzielonego)](../../../../storage/common/storage-sas-overview.md)można użyć adresu URL do szkolenia.

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

Najpierw sprawdzimy model, sprawdzisz stan modelu podczas szkolenia, aby określić, kiedy szkolenie zostało zakończone, a następnie Pobierz najnowszy Identyfikator modelu, którego będziemy potrzebować po przejściu do fazy wykrywania.

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

## <a name="detect-anomalies"></a>Wykrywaj anomalie

Użyj `detect_anomaly` i, `get_dectection_result` Aby określić czy istnieją jakieś anomalie w źródle danych. Należy przekazać Identyfikator modelu dla właśnie przeszkolonego modelu.

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

## <a name="export-model"></a>Eksportuj model

Jeśli chcesz wyeksportować użycie modelu `export_model` i przekazać Identyfikator modelu modelu, który chcesz wyeksportować:

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

## <a name="delete-model"></a>Usuń model

Aby usunąć model, użyj `delete_multivariate_model` i przekaż Identyfikator modelu modelu, który chcesz usunąć:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Przed uruchomieniem aplikacji musimy dodać kod, aby wywołać nasze nowo utworzone funkcje.

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
