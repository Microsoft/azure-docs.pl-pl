---
title: Publikowanie potoków ML
titleSuffix: Azure Machine Learning
description: Uruchamiaj przepływy pracy uczenia maszynowego przy użyciu potoków uczenia maszynowego i zestawu Azure Machine Learning SDK dla języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520180"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publikowanie i śledzenie potoków uczenia maszynowego



W tym artykule przedstawiono sposób udostępniania potoku uczenia maszynowego współpracownikom lub klientom.

Potoki usługi Machine Learning to przepływy pracy wielokrotnego użytku dla zadań uczenia maszynowego. Jedną z korzyści płynących z potoku jest zwiększona Współpraca. Możesz również potoki wersji, umożliwiając klientom korzystanie z bieżącego modelu podczas pracy nad nową wersją. 

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie [obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md) do przechowywania wszystkich zasobów potoku

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning](concept-compute-instance.md) z już zainstalowanym zestawem SDK

* Utwórz i uruchom potok uczenia maszynowego, na przykład w następującym [samouczku: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md). Aby poznać inne opcje, zobacz [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Po utworzeniu potoku i uruchomieniu można opublikować potok, aby działał z innymi danymi wejściowymi. Dla punktu końcowego REST już opublikowanego potoku, aby akceptować parametry, należy skonfigurować potok tak, aby używał `PipelineParameter` obiektów dla argumentów, które będą się różnić.

1. Aby utworzyć parametr potoku, użyj obiektu [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) z wartością domyślną.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Dodaj ten `PipelineParameter` obiekt jako parametr do dowolnego z kroków w potoku w następujący sposób:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Opublikuj ten potok, który zostanie zaakceptowany przez wywoływany parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Uruchom opublikowany potok

Wszystkie opublikowane potoki mają punkt końcowy REST. Za pomocą punktu końcowego potoku można wyzwolić uruchomienie potoku z dowolnego systemu zewnętrznego, w tym klientów spoza języka Python. Ten punkt końcowy włącza "zarządzaną powtarzalność" w ramach oceniania wsadowego i scenariuszy ponownego szkolenia.

> [!IMPORTANT]
> Jeśli używasz kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby zarządzania dostępem do potoku, [Ustaw uprawnienia dla scenariusza potoku (szkolenie lub ocenianie)](how-to-assign-roles.md#common-scenarios).

Aby wywołać poprzedni potok, wymagany jest token nagłówka Azure Active Directory Authentication. Ten token jest opisany w dokumentacji [klasy AzureCliAuthentication](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) i w obszarze [uwierzytelnianie w Azure Machine Learning](https://aka.ms/pl-restep-auth) notesie.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

`json`Argument żądania post musi zawierać dla `ParameterAssignments` klucza słownik zawierający parametry potoku i ich wartości. Ponadto `json` argument może zawierać następujące klucze:

| Klucz | Opis |
| --- | --- | 
| `ExperimentName` | Nazwa eksperymentu skojarzonego z tym punktem końcowym |
| `Description` | Dowolny tekst opisujący punkt końcowy | 
| `Tags` | Pary klucz-wartość dowolnego kształtu, których można użyć do etykietowania żądań i dodawania do nich adnotacji  |
| `DataSetDefinitionValueAssignments` | Słownik używany do zmieniania zestawów danych bez ponownego szkolenia (zobacz omówienie poniżej) | 
| `DataPathAssignments` | Słownik używany do zmieniania ścieżek datapath bez ponownego szkolenia (zobacz omówienie poniżej) | 

### <a name="run-a-published-pipeline-using-c"></a>Uruchamianie opublikowanego potoku przy użyciu języka C # 

Poniższy kod pokazuje, jak wywołać potok asynchronicznie z języka C#. Częściowy fragment kodu pokazuje jedynie strukturę wywołania i nie jest częścią przykładu firmy Microsoft. Nie są wyświetlane kompletne klasy ani obsługa błędów. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Uruchamianie opublikowanego potoku przy użyciu języka Java

Poniższy kod przedstawia wywołanie potoku, które wymaga uwierzytelniania (zobacz [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](how-to-setup-authentication.md)). Jeśli potok został wdrożony publicznie, nie są potrzebne żadne wywołania `authKey` . Częściowy fragment kodu nie pokazuje klas języka Java i obsługi wyjątków. Kod używa `Optional.flatMap` do łączenia razem funkcji, które mogą zwracać puste `Optional` . Użycie `flatMap` skraca i objaśnia kod, ale należy pamiętać, że są to `getRequestBody()` wyjątki.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Zmiana zestawów danych i ścieżek datapath bez przeszkolenia

Możesz chcieć przeprowadzić uczenie i wnioskowanie dotyczące różnych zestawów danych i ścieżek dostępu. Na przykład możesz chcieć nauczyć się na mniejszym zestawie danych, ale wnioskować o kompletny zestaw danych. Zestawy danych są przełączane za pomocą `DataSetDefinitionValueAssignments` klucza w `json` argumencie żądania. Ścieżki datapaths są przełączane za pomocą `DataPathAssignments` . Technika obu tych metod jest podobna:

1. W skrypcie definicji potoku Utwórz `PipelineParameter` dla zestawu danych. Utwórz `DatasetConsumptionConfig` lub `DataPath` z `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. W skrypcie ML uzyskaj dostęp do dynamicznego określonego zestawu danych przy użyciu `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Zwróć uwagę, że skrypt ML uzyskuje dostęp do wartości określonej dla `DatasetConsumptionConfig` ( `tabular_dataset` ), a nie wartości `PipelineParameter` ( `tabular_ds_param` ).

1. W skrypcie definicji potoku ustaw wartość `DatasetConsumptionConfig` parametru jako parametr `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Aby dynamicznie przełączać zestawy danych w wywołaniu REST inferencing, użyj `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

W notesach prezentowanych przez [zestaw danych i PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) oraz [zaprezentowanie ścieżki i PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) zostały wykonane kompletne przykłady tej techniki.

## <a name="create-a-versioned-pipeline-endpoint"></a>Tworzenie punktu końcowego potoku z wersją

Można utworzyć punkt końcowy potoku z wieloma opublikowanymi potokami. Ta technika zapewnia stały punkt końcowy REST podczas iteracji i aktualizacji potoków ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Przesyłanie zadania do punktu końcowego potoku

Można przesłać zadanie do domyślnej wersji punktu końcowego potoku:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Możesz również przesłać zadanie do określonej wersji:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Tę samą funkcję można wykonać przy użyciu interfejsu API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Używanie opublikowanych potoków w Studio

Możesz również uruchomić opublikowany potok z programu Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **punkty końcowe**.

1. W górnej części wybierz pozycję **punkty końcowe potoku**.
 ![Lista opublikowanych potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Wybierz konkretny potok, który ma zostać uruchomiony, zużyty lub przejrzany wyniki poprzednich przebiegów punktu końcowego potoku.

## <a name="disable-a-published-pipeline"></a>Wyłącz opublikowany potok

Aby ukryć potok z listy opublikowanych potoków, należy go wyłączyć w programie Studio lub z zestawu SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Można włączyć ją ponownie za pomocą `p.enable()` . Aby uzyskać więcej informacji, zobacz [PublishedPipeline klasy](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline) Reference.

## <a name="next-steps"></a>Następne kroki

- [Te notesy Jupyter w usłudze GitHub](https://aka.ms/aml-pipeline-readme) umożliwiają dalsze Eksplorowanie potoków usługi Machine Learning.
- Zapoznaj się z dokumentacją zestawu SDK dla pakietu [Azure-Pipelines-Core](/python/api/azureml-pipeline-core/) oraz pakietem [kroków potoków usługi Azure](/python/api/azureml-pipeline-steps/) .
- Zobacz Porady [dotyczące debugowania](how-to-debug-pipelines.md) i rozwiązywania problemów z potokami.