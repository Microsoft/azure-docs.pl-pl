---
title: Narzędzie do wykrywania anomalii szybki start dla wielozmianowej biblioteki klienta .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: a657b8f5bf967131a0168dbea5bb1db86b3b559e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799815"
---
Rozpoczynanie pracy z Narzędzie do wykrywania anomalii wielozmianową biblioteką klienta dla programu .NET. Wykonaj następujące kroki, aby zainstalować pakiet i rozpocząć korzystanie z algorytmów dostarczonych przez usługę. Nowe, wielowariacyjne interfejsy API wykrywania anomalii umożliwiają deweloperom łatwe integrowanie zaawansowanej ai do wykrywania anomalii z grup metryk bez konieczności znajomości uczenia maszynowego ani danych oznaczonych etykietami. Zależności i wzajemne korelacje między różnymi sygnałami są automatycznie liczone jako kluczowe czynniki. Pomaga to aktywnie chronić złożone systemy przed awariami.

Użyj wielo Narzędzie do wykrywania anomalii biblioteki klienta dla programu .NET, aby:

* Wykrywanie anomalii na poziomie systemu z grupy szeregów czasu.
* Gdy poszczególne szeregi czasowe nie będą zbyt wiele powiedzą, a ty musisz przyjrzeć się wszystkim sygnałom, aby wykryć problem.
* Predykcyjna konserwacja drogich zasobów fizycznych z dziesiątkami do setek różnych typów czujników mierzące różne aspekty kondycji systemu.

[Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Narzędzie do wykrywania anomalii utwórz zasób Narzędzie do wykrywania anomalii w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Poczekaj na wdrożenie i wybierz **przycisk Przejdź do** zasobu.
    * Klucz i punkt końcowy będą potrzebne z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API Narzędzie do wykrywania anomalii API. Wklej klucz i punkt końcowy do poniższego kodu w dalszej części tego przewodnika Szybki start.
    Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji .NET Core

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `anomaly-detector-quickstart-multivariate` . To polecenie tworzy prosty projekt "Hello world" z pojedynczym plikiem źródłowym języka C#: *Program.cs.*

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować za pomocą:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę Narzędzie do wykrywania anomalii klienta dla programu .NET za pomocą następującego polecenia:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

W katalogu projektu otwórz *plik program.cs* i dodaj następujący kod przy użyciu narzędzia `directives` :

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

W metodzie aplikacji utwórz zmienne dla punktu końcowego platformy Azure zasobu, klucza interfejsu `main()` API i niestandardowego źródła danych.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Aby użyć Narzędzie do wykrywania anomalii wielozmianowych interfejsów API, musimy wytterować własny model przed użyciem wykrywania. Dane używane do trenowania to partia szeregów czasu. Każdy szereg czasowy powinien być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasu powinny być spakowane w jednym pliku zip i przekazane do [usługi Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Domyślnie nazwa pliku będzie służyć do reprezentowania zmiennej dla szeregów czasu. Alternatywnie można meta.jspliku zip, jeśli chcesz, aby nazwa zmiennej różniła się od nazwy pliku zip. Po wygenerowaniu adresu [URL sygnatury](../../../../storage/common/storage-sas-overview.md)dostępu współdzielonego (sygnatury dostępu współdzielonego) obiektu blob możemy użyć adresu URL do pliku zip w celu szkolenia.

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą Narzędzie do wykrywania anomalii wielozmianowej biblioteki klienta dla programu .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Trenowanie modelu](#train-the-model)
* [Wykrywanie anomalii](#detect-anomalies)
* [Eksportowanie modelu](#export-model)
* [Usuwanie modelu](#delete-model)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Należy utworzyć wystąpienia Narzędzie do wykrywania anomalii klienta z punktem końcowym i kluczem.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Trenowanie modelu

Utwórz nowe prywatne zadanie asynchroniczne, jak po poniższej stronie, aby obsłużyć trenowanie modelu. Użyjemy funkcji `TrainMultivariateModel` do trenowania modelu `GetMultivariateModelAysnc` i sprawdzenia, kiedy trenowanie zostanie ukończone.

```csharp
private async Task trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        ModelStatus? model_status = null;
        int tryout_count = 0;
        TimeSpan create_limit = new TimeSpan(0, 3, 0);
        while (tryout_count < max_tryout & model_status != ModelStatus.Ready)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            ModelInfo model_info = get_response.Value.ModelInfo;
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, model_info.Status));

            if (model_info != null)
            {
                model_status = model_info.Status;
            }
            tryout_count += 1;
        };
        get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);

        if (model_status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Wykrywanie anomalii

Aby wykryć anomalie przy użyciu nowo wytrenowany model, utwórz element o `private async Task` nazwie `detectAsync` . Utworzysz nowy i `DetectionRequest` przekażemy go jako parametr do . `DetectAnomalyAsync`

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        int tryout_count = 0;
        while (result.Value.Summary.Status != DetectionStatus.Ready & tryout_count < max_tryout)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
            tryout_count += 1;
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Eksportowanie modelu

Aby wyeksportować wcześniej wytrenowany model, utwórz o `private async Task` nazwie `exportAysnc` . Użyjemy identyfikatora modelu, który chcesz `ExportModelAsync` wyeksportować, i przekażemy go.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Usuwanie modelu

Aby usunąć utworzony wcześniej model, użyj i przekaż identyfikator modelu, `DeleteMultivariateModelAsync` który chcesz usunąć. Aby pobrać identyfikator modelu, możesz `getModelNumberAsync` użyć:

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Metoda main

Teraz, gdy masz wszystkie części składowe, musisz dodać dodatkowy kod do metody głównej w celu wywołania nowo utworzonych zadań.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `dotnet run` polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Następne kroki

* [Narzędzie do wykrywania anomalii kowariantnych najlepszych rozwiązań](../../concepts/best-practices-multivariate.md)
