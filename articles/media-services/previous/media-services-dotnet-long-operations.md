---
title: Sondowanie Long-Running operacji | Microsoft Docs
description: Azure Media Services oferuje interfejsy API, które wysyłają żądania do Media Services do uruchamiania operacji (na przykład tworzenia, uruchamiania, zatrzymywania lub usuwania kanału), te operacje są długotrwałe. W tym temacie pokazano, jak sondować długotrwałe operacje.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013826"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Dostarczanie przesyłania strumieniowego na żywo za pomocą Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie

Microsoft Azure Media Services oferuje interfejsy API, które wysyłają żądania Media Services do uruchamiania operacji (na przykład: Tworzenie, uruchamianie, zatrzymywanie lub usuwanie kanału). Te operacje są długotrwałe.

Zestaw Media Services .NET SDK udostępnia interfejsy API, które wysyłają żądanie i oczekują na ukończenie operacji (wewnętrznie interfejsy API są sondowane w przypadku postępów operacji w określonych odstępach czasu). Na przykład po wywołaniu kanału. Start () — Metoda zwraca po uruchomieniu kanału. Można również użyć wersji asynchronicznej: await kanału. StartAsync () (Aby uzyskać informacje na temat wzorca asynchronicznego opartego na zadaniach, zobacz [TAP](./media-services-mes-schema.md)). Interfejsy API, które wysyłają żądanie operacji, a następnie sondowania stanu do momentu zakończenia operacji, są nazywane "metodami sondowania". Te metody (szczególnie wersja Async) są zalecane w przypadku rozbudowanych aplikacji klienckich i/lub usług stanowych.

Istnieją scenariusze, w których aplikacja nie może czekać na długotrwałe żądanie HTTP i chce przeprowadzić ręczne sondowanie postępu operacji. Typowym przykładem jest przeglądarka korzystająca z bezstanowej usługi sieci Web: gdy przeglądarka żąda utworzenia kanału, usługa sieci Web inicjuje długotrwałą operację i zwraca identyfikator operacji do przeglądarki. Przeglądarka może następnie poprosił usługi sieci Web o uzyskanie stanu operacji na podstawie identyfikatora. Zestaw SDK Media Services platformy .NET udostępnia interfejsy API, które są przydatne w tym scenariuszu. Te interfejsy API są nazywane "metodami niesondowania".
"Metody, które nie są sondowane" mają następujący wzorzec nazewnictwa:*sendname operacji (* na przykład SendCreateOperation). Metody *operacji sendname* zwracają obiekt **IOperation** ; zwrócony obiekt zawiera informacje, które mogą być używane do śledzenia operacji. Metody *sendname* OperationAsync zwracają **zadanie \<IOperation>**.

Obecnie następujące klasy obsługują metody inne niż sondowanie:  **Channel**, **StreamingEndpoint** i **program**.

Aby przeprowadzić sondowanie w poszukiwaniu stanu operacji, użyj metody **getoperation** w klasie **OperationBaseCollection** . Aby sprawdzić stan operacji dla operacji **kanału** i **StreamingEndpoint** , użyj następujących interwałów: 30 sekund; w przypadku operacji **programu** Użyj 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Przykład

W poniższym przykładzie zdefiniowano klasę o nazwie **ChannelOperations**. Ta definicja klasy może być punktem początkowym dla definicji klasy usługi sieci Web. Dla uproszczenia w poniższych przykładach są używane nieasynchroniczne wersje metod.

W przykładzie pokazano również, jak klient może korzystać z tej klasy.

### <a name="channeloperations-class-definition"></a>Definicja klasy ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
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

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Kod klienta

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]