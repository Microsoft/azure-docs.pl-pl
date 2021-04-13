---
title: Wykrywanie anomalii wieloczynnikowa Java Client Library — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: eae4d00cd7b1a0ff90648086320135505a0d900a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316042"
---
Rozpocznij pracę z biblioteką klienta wykrywania anomalii wieloczynnikowa dla języka Java. Wykonaj następujące kroki, aby zainstalować pakiet startowy przy użyciu algorytmów udostępnianych przez usługę. Nowe interfejsy API wykrywania anomalii w usłudze wieloczynnikowa umożliwiają deweloperom łatwe integrowanie zaawansowanej pamięci AI do wykrywania anomalii z grup metryk, bez konieczności znajomości wiedzy uczenia maszynowego ani etykietowania danych. Zależności i wzajemnych korelacji między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj biblioteki wieloczynnikowa Client dla środowiska Java, aby:

* Wykrywaj anomalie na poziomie systemu z grupy szeregów czasowych.
* Gdy każda z poszczególnych szeregów czasowych nie powiedzie się, należy sprawdzić wszystkie sygnały w celu wykrycia problemu.
* Predicative konserwację kosztownych zasobów fizycznych z dziesiątki do setek różnych typów czujników mierzących różne aspekty kondycji systemu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" Utwórz zasób wykrywania anomalii "  target="_blank"> Utwórz zasób wykrywania anomalii </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Potrzebny będzie klucz i punkt końcowy z zasobu utworzonego w celu połączenia aplikacji z interfejsem API wykrywania anomalii. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Więcej informacji o bibliotece klienta można znaleźć w [Maven centralnym repozytorium](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS* , który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Znajdź element *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj w tej konfiguracji kompilacji. Pamiętaj, aby uwzględnić zależności projektu.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *MetricsAdvisorQuickstarts. Java*. Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Utwórz zmienne i klucz punktu końcowego platformy Azure dla zasobu. Utwórz kolejną zmienną dla przykładowego pliku danych.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Aby korzystać z interfejsów API wieloczynnikowa wykrywania anomalii, musimy nauczyć nasz model przed użyciem wykrywania. Dane używane do szkoleń to partia szeregów czasowych, każda seria czasu powinna być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie szeregi czasowe powinny być spakowane w jednym pliku zip i przekazywane do [usługi Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md). Domyślnie nazwa pliku zostanie użyta do reprezentowania zmiennej dla szeregów czasowych. Alternatywnie, dodatkowe meta.jsw pliku można umieścić w pliku zip, jeśli chcesz, aby nazwa zmiennej była inna niż nazwa pliku zip. Po wygenerowaniu [adresu URL SAS (sygnatury dostępu współdzielonego)](../../../../storage/common/storage-sas-overview.md)można użyć adresu URL do szkolenia.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności z użyciem biblioteki klienta wykrywania anomalii dla Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Szkolenie modelu](#train-a-model)
* [Wykrywaj anomalie](#detect-anomalies)
* [Eksportuj model](#export-model)
* [Usuń model](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Tworzenie wystąpienia `anomalyDetectorClient` obiektu za pomocą punktu końcowego i poświadczeń.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Szkolenie modelu

### <a name="construct-a-model-result-and-train-model"></a>Konstruowanie modelu wyników i modelu szkolenia

Najpierw musimy utworzyć żądanie modelu. Upewnij się, że godzina początkowa i końcowa są wyrównane ze źródłem danych.

 Aby korzystać z interfejsów API wieloczynnikowa wykrywania anomalii, musimy nauczyć nasz model przed użyciem wykrywania. Dane używane do szkoleń to partia szeregów czasowych, każda seria czasu powinna być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie szeregi czasowe powinny być spakowane w jednym pliku zip i przekazywane do [usługi Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Domyślnie nazwa pliku zostanie użyta do reprezentowania zmiennej dla szeregów czasowych. Alternatywnie, dodatkowe meta.jsw pliku można umieścić w pliku zip, jeśli chcesz, aby nazwa zmiennej była inna niż nazwa pliku zip. Po wygenerowaniu [adresu URL SAS (sygnatury dostępu współdzielonego)](../../../../storage/common/storage-sas-overview.md)można użyć adresu URL do szkolenia.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Wykrywaj anomalie

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Eksportuj model

Aby wyeksportować przeszkolony model, użyj `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Usuń model

Aby usunąć istniejący model, który jest dostępny dla bieżącego zasobu, użyj `deleteMultivariateModelWithResponse` funkcji.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować przy użyciu:

```console
gradle build
```
### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z `run` celem:

```console
gradle run
```

## <a name="next-steps"></a>Następne kroki

* [Najlepsze rozwiązania dotyczące wykrywania anomalii wieloczynnikowa](../../concepts/best-practices-multivariate.md)
