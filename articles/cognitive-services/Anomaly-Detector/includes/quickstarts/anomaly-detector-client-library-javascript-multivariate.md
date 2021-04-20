---
title: 'Narzędzie do wykrywania anomalii kowariantną bibliotekę klienta JavaScript : Szybki start'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 03fbd5e641c72a03a4a3cb19219678bc3d3fff51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732298"
---
Rozpoczynanie pracy z Narzędzie do wykrywania anomalii kowariantną biblioteką klienta dla języka JavaScript. Wykonaj następujące kroki, aby zainstalować pakiet i rozpocząć korzystanie z algorytmów dostarczonych przez usługę. Nowe, wielowariacyjne interfejsy API wykrywania anomalii umożliwiają deweloperom łatwe integrowanie zaawansowanych rozwiązań AI do wykrywania anomalii z grup metryk bez konieczności znajomości uczenia maszynowego ani danych oznaczonych etykietami. Zależności i korelacje między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj wielo Narzędzie do wykrywania anomalii biblioteki klienta dla języka JavaScript, aby:

* Wykrywanie anomalii na poziomie systemu z grupy szeregów czasu.
* Gdy dowolny szereg czasowy nie będzie ci za dużo mówić i musisz przyjrzeć się wszystkim sygnałom, aby wykryć problem.
* Predykatywna konserwacja drogich zasobów fizycznych z dziesiątkami do setek różnych typów czujników mierząca różne aspekty kondycji systemu.

[Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Pakiet (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Narzędzie do wykrywania anomalii utwórz zasób Narzędzie do wykrywania anomalii w witrynie Azure Portal, aby <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" uzyskać klucz i punkt "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie, a następnie kliknij **przycisk Przejdź do** zasobu.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Narzędzie do wykrywania anomalii API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `npm init` , aby utworzyć aplikację node z `package.json` plikiem. 

```console
npm init
```

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki: "
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Utwórz zmienne punktu końcowego i klucza platformy Azure zasobu. Utwórz kolejną zmienną dla przykładowego pliku danych.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Aby użyć Narzędzie do wykrywania anomalii wielozmianowych interfejsów API, musimy wytterować własny model przed użyciem wykrywania. Dane używane do trenowania to partia szeregów czasu. Każdy szereg czasowy powinien być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasu powinny być spakowane w jednym pliku zip i przekazane do [usługi Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md) Domyślnie nazwa pliku będzie służyć do reprezentowania zmiennej dla szeregów czasu. Alternatywnie można meta.jspliku zip, jeśli chcesz, aby nazwa zmiennej różniła się od nazwy pliku zip. Po [wygenerowaniu adresu URL sygnatury](../../../../storage/common/storage-sas-overview.md)dostępu współdzielonego obiektu blob możemy użyć adresu URL do pliku zip w celu szkolenia.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Zainstaluj pakiety `ms-rest-azure` `azure-ai-anomalydetector` NPM i . Biblioteka csv-parse jest również używana w tym przewodniku Szybki start:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Plik aplikacji `package.json` zostanie zaktualizowany o zależności.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności przy użyciu Narzędzie do wykrywania anomalii klienta programu Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Szkolenie modelu](#train-a-model)
* [Wykrywanie anomalii](#detect-anomalies)
* [Eksportowanie modelu](#export-model)
* [Usuwanie modelu](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Należy utworzyć wystąpienia `AnomalyDetectorClient` obiektu z punktem końcowym i poświadczeniami.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Szkolenie modelu

### <a name="construct-a-model-result"></a>Konstruowanie wyniku modelu

Najpierw musimy skonstruować żądanie modelu. Upewnij się, że godzina rozpoczęcia i zakończenia jest dopasowana do źródła danych.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Trenowanie nowego modelu

Należy przekazać żądanie modelu do metody Narzędzie do wykrywania anomalii `trainMultivariateModel` klienta.

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Aby sprawdzić, czy trenowanie modelu jest ukończone, możesz śledzić jego stan:

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

## <a name="detect-anomalies"></a>Wykrywanie anomalii

Użyj funkcji i , aby określić, czy istnieją `detectAnomaly` `getDectectionResult` jakieś anomalie w źródle danych.

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

## <a name="export-model"></a>Eksportowanie modelu

Aby wyeksportować wytrenowany model, użyj `exportModel` funkcji .

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Usuwanie modelu

Aby usunąć istniejący model, który jest dostępny dla bieżącego zasobu, użyj `deleteMultivariateModel` funkcji .

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

* [Narzędzie do wykrywania anomalii kowariantnych najlepszych rozwiązań](../../concepts/best-practices-multivariate.md)
