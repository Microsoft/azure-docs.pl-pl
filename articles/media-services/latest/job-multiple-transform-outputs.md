---
title: Tworzenie zadania z wieloma wyjściami transformacji
description: W tym temacie pokazano, jak utworzyć zadanie Azure Media Services z wieloma wyjściami transformacji.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0396415593f2d0f41abc55433bb8f19c4913ac26
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954144"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Tworzenie zadania z wieloma wyjściami transformacji

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym temacie pokazano, jak utworzyć transformację z dwoma wyjściami transformacji. Pierwsze wywołanie dla danych wejściowych jest kodowane na potrzeby przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów przy użyciu wbudowanego ustawienia wstępnego [AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) . Druga z nich wywołuje sygnał audio w wejściowym wideo do przetworzenia przy użyciu [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets). Po utworzeniu przekształcenia można przesłać zadanie, które będzie odpowiednio przetwarzać wideo. Ponieważ w tym przykładzie określono dwa wyjścia transformacji, należy określić dwa dane wyjściowe zadania. Można zdecydować się na kierowanie obu zadań do tego samego zasobu (jak pokazano poniżej) lub zapisanie wyników w osobnych zasobach.
 

> [!TIP]
> Przed rozpoczęciem opracowywania, zobacz [Tworzenie aplikacji przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (w tym informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itp.).

## <a name="create-a-transform"></a>Tworzenie przekształcenia

Poniższy kod pokazuje, jak utworzyć transformację, która produkuje dwa dane wyjściowe.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```
## <a name="submit-a-job"></a>Przesyłanie zadania

Utwórz zadanie z danymi wejściowymi protokołu HTTPS i dwoma wynikami zadania.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Przykłady Azure Media Services v3 przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
