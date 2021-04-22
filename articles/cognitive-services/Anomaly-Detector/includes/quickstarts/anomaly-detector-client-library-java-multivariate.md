---
title: Narzędzie do wykrywania anomalii wielozmianowej biblioteki klienta Java Szybki start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: e85f54beb9a3d4203e527ed9c8ce5582b6a2f5b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880667"
---
Rozpoczynanie pracy z Narzędzie do wykrywania anomalii wielozmianową biblioteką klienta dla języka Java. Wykonaj następujące kroki, aby rozpocząć instalację pakietu przy użyciu algorytmów dostarczonych przez usługę. Nowe, wielowariacyjne interfejsy API wykrywania anomalii umożliwiają deweloperom łatwe integrowanie zaawansowanej ai do wykrywania anomalii z grup metryk bez konieczności znajomości uczenia maszynowego ani danych oznaczonych etykietami. Zależności i wzajemne korelacje między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj wielo Narzędzie do wykrywania anomalii klienta dla języka Java, aby:

* Wykrywanie anomalii na poziomie systemu z grupy szeregów czasu.
* Gdy poszczególne szeregi czasowe nie będą zbyt wiele powiedzą, a ty musisz przyjrzeć się wszystkim sygnałom, aby wykryć problem.
* Predykcyjna konserwacja drogich zasobów fizycznych z dziesiątkami do setek różnych typów czujników mierzące różne aspekty kondycji systemu.

[Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pakiet (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)  |  [Przykładowy kod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie [kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Narzędzie do wykrywania anomalii utwórz zasób Narzędzie do wykrywania anomalii w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Poczekaj na wdrożenie i kliknij przycisk **Przejdź do** zasobu.
    * Klucz i punkt końcowy będą potrzebne z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API Narzędzie do wykrywania anomalii API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu gradle

W tym przewodniku Szybki start jest używany menedżer zależności gradle. Więcej informacji o bibliotece klienta można znaleźć w centralnym [repozytorium Maven.](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `gradle init` z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla programu Gradle, w tym *pliku build.gradle.kts,* który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Znajdź *plik build.gradle.kts* i otwórz go za pomocą preferowanego środowiska IDE lub edytora tekstów. Następnie skopiuj dane w tej konfiguracji kompilacji. Pamiętaj, aby uwzględnić zależności projektu.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java

Utwórz folder dla przykładowej aplikacji. Z katalogu roboczego uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i utwórz plik o nazwie *MetricsQuickstarts.java.* Otwórz go w preferowanym edytorze lub w środowiskach IDE i dodaj następujące `import` instrukcje:

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

Utwórz zmienne punktu końcowego i klucza platformy Azure zasobu. Utwórz kolejną zmienną dla przykładowego pliku danych.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Aby użyć Narzędzie do wykrywania anomalii wielozmianowych interfejsów API, musimy wytszkolić własny model przed użyciem wykrywania. Dane używane do trenowania to partia szeregów czasu. Każdy szereg czasowy powinien być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasu powinny być spakowane w jednym pliku zip i przekazane do [usługi Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md) Domyślnie nazwa pliku będzie służyć do reprezentowania zmiennej szeregów czasu. Alternatywnie można meta.jspliku zip, jeśli chcesz, aby nazwa zmiennej różniła się od nazwy pliku zip. Po [wygenerowaniu adresu URL sygnatury](../../../../storage/common/storage-sas-overview.md)dostępu współdzielonego obiektu blob możemy użyć adresu URL do pliku zip w celu szkolenia.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności przy użyciu Narzędzie do wykrywania anomalii klienta programu Node.js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Szkolenie modelu](#train-a-model)
* [Wykrywanie anomalii](#detect-anomalies)
* [Eksportowanie modelu](#export-model)
* [Usuwanie modelu](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Należy utworzyć wystąpienia `anomalyDetectorClient` obiektu z punktem końcowym i poświadczeniami.

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

### <a name="construct-a-model-result-and-train-model"></a>Konstruowanie wyniku modelu i trenowanie modelu

Najpierw musimy skonstruować żądanie modelu. Upewnij się, że godzina rozpoczęcia i zakończenia jest dopasowana do źródła danych.

 Aby użyć Narzędzie do wykrywania anomalii wielozmianowych interfejsów API, musimy wytterować własny model przed użyciem wykrywania. Dane używane do trenowania to partia szeregów czasu. Każdy szereg czasowy powinien być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasu powinny być spakowane w jednym pliku zip i przekazane do [usługi Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Domyślnie nazwa pliku będzie służyć do reprezentowania zmiennej dla szeregów czasu. Alternatywnie można meta.jspliku zip, jeśli chcesz, aby nazwa zmiennej różniła się od nazwy pliku zip. Po [wygenerowaniu adresu URL sygnatury](../../../../storage/common/storage-sas-overview.md)dostępu współdzielonego obiektu blob możemy użyć adresu URL do pliku zip w celu szkolenia.

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

## <a name="detect-anomalies"></a>Wykrywanie anomalii

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

## <a name="export-model"></a>Eksportowanie modelu

Aby wyeksportować wytrenowany model, `exportModelWithResponse` użyj .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Usuwanie modelu

Aby usunąć istniejący model, który jest dostępny dla bieżącego zasobu, użyj `deleteMultivariateModelWithResponse` funkcji .

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować za pomocą:

```console
gradle build
```
### <a name="run-the-application"></a>Uruchamianie aplikacji

Przed uruchomieniem może być przydatne sprawdzenie kodu pod względem [pełnego przykładowego kodu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java).

Uruchom aplikację w `run` celu:

```console
gradle run
```

## <a name="next-steps"></a>Następne kroki

* [Narzędzie do wykrywania anomalii kowariantnych najlepszych rozwiązań](../../concepts/best-practices-multivariate.md)
