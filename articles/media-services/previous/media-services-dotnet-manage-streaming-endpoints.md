---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą zestawu .NET SDK. | Microsoft Docs
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego za pomocą zestawu .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c84e7c753e6d1eb1d357320857e4c159cc13cfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013758"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą zestawu .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

>[!NOTE]
>Zapoznaj się z artykułem [Przegląd](media-services-streaming-endpoints-overview.md) . Sprawdź również [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

W tym artykule przedstawiono sposób wykonywania następujących zadań przy użyciu zestawu SDK programu Azure Media Services .NET:

- Badanie domyślnego punktu końcowego przesyłania strumieniowego.
- Utwórz/Dodaj nowy punkt końcowy przesyłania strumieniowego.

    Jeśli planujesz korzystać z różnych sieci CDN lub sieci CDN i bezpośredniego dostępu, możesz potrzebować wielu punktów końcowych przesyłania strumieniowego.

    > [!NOTE]
    > Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.
    
- Zaktualizuj punkt końcowy przesyłania strumieniowego.
    
    Upewnij się, że jest wywoływana funkcja Update ().

- Usuń punkt końcowy przesyłania strumieniowego.

    >[!NOTE]
    >Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego.

Informacje o sposobie skalowania punktu końcowego przesyłania strumieniowego znajdują się w [tym](media-services-portal-scale-streaming-endpoints.md) artykule.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="add-code-that-manages-streaming-endpoints"></a>Dodaj kod zarządzający punktami końcowymi przesyłania strumieniowego
    
Zastąp kod w programie. cs następującym kodem:

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
    class Program
    {
        // Read values from the App.config file.

        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
