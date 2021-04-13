---
title: Wieloczynnikowa wykrywania anomalii — Biblioteka klienta JavaScript — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316049"
---
Rozpocznij pracę z biblioteką klienta wieloczynnikowa wykrywania anomalii dla języka JavaScript. Wykonaj następujące kroki, aby zainstalować pakiet i rozpocząć korzystanie z algorytmów udostępnianych przez usługę. Nowe interfejsy API wykrywania anomalii w usłudze wieloczynnikowa umożliwiają deweloperom łatwe integrowanie zaawansowanej pamięci AI do wykrywania anomalii z grup metryk, bez konieczności znajomości wiedzy uczenia maszynowego ani etykietowania danych. Zależności i wzajemnych korelacji między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użycie biblioteki wieloczynnikowa Client wykrywania anomalii dla języka JavaScript do:

* Wykrywaj anomalie na poziomie systemu z grupy szeregów czasowych.
* Gdy każda z poszczególnych szeregów czasowych nie powiedzie się, należy sprawdzić wszystkie sygnały w celu wykrycia problemu.
* Predicative konserwację kosztownych zasobów fizycznych z dziesiątki do setek różnych typów czujników mierzących różne aspekty kondycji systemu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" Utwórz zasób wykrywania anomalii "  target="_blank"> Utwórz zasób wykrywania anomalii </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Potrzebny będzie klucz i punkt końcowy z zasobu utworzonego w celu połączenia aplikacji z interfejsem API wykrywania anomalii. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

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
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Utwórz zmienne i klucz punktu końcowego platformy Azure dla zasobu. Utwórz kolejną zmienną dla przykładowego pliku danych.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Aby korzystać z interfejsów API wieloczynnikowa wykrywania anomalii, musimy nauczyć nasz model przed użyciem wykrywania. Dane używane do szkoleń to partia szeregów czasowych, każda seria czasu powinna być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie szeregi czasowe powinny być spakowane w jednym pliku zip i przekazywane do [usługi Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md). Domyślnie nazwa pliku zostanie użyta do reprezentowania zmiennej dla szeregów czasowych. Alternatywnie, dodatkowe meta.jsw pliku można umieścić w pliku zip, jeśli chcesz, aby nazwa zmiennej była inna niż nazwa pliku zip. Po wygenerowaniu [adresu URL SAS (sygnatury dostępu współdzielonego)](../../../../storage/common/storage-sas-overview.md)można użyć adresu URL do szkolenia.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj `ms-rest-azure` pakiety i `azure-ai-anomalydetector` npm. Biblioteka analizy woluminów CSV jest również używana w tym przewodniku szybki start:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności z użyciem biblioteki klienta wykrywania anomalii dla Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Szkolenie modelu](#train-a-model)
* [Wykrywaj anomalie](#detect-anomalies)
* [Eksportuj model](#export-model)
* [Usuń model](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Tworzenie wystąpienia `AnomalyDetectorClient` obiektu za pomocą punktu końcowego i poświadczeń.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Szkolenie modelu

### <a name="construct-a-model-result"></a>Konstruowanie wyniku modelu

Najpierw musimy utworzyć żądanie modelu. Upewnij się, że godzina początkowa i końcowa są wyrównane ze źródłem danych.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Uczenie nowego modelu

Musisz przekazać żądanie modelu do metody klienta wykrywania anomalii `trainMultivariateModel` .

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Aby sprawdzić, czy jest to możliwe, możesz śledzić stan modelu:

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Wykrywaj anomalie

Użyj `detectAnomaly` funkcji i, `getDectectionResult` Aby określić czy istnieją jakieś anomalie w źródle danych.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>Eksportuj model

Aby wyeksportować przeszkolony model, użyj `exportModel` funkcji.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Usuń model

Aby usunąć istniejący model, który jest dostępny dla bieżącego zasobu, użyj `deleteMultivariateModel` funkcji.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

## <a name="next-steps"></a>Następne kroki

* [Najlepsze rozwiązania dotyczące wykrywania anomalii wieloczynnikowa](../../concepts/best-practices-multivariate.md)
