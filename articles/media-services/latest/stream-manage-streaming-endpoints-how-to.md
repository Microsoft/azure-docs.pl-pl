---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego za pomocą Azure Media Services v3.
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
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791737"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego za Media Services w wersji 3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Po utworzeniu Media Services zostanie dodany  domyślny punkt [końcowy](stream-streaming-endpoint-concept.md) przesyłania strumieniowego do Twojego konta w **stanie Zatrzymany.** Aby rozpocząć przesyłanie strumieniowe [](encode-dynamic-packaging-concept.md) zawartości oraz korzystać z dynamicznego pakowania i szyfrowania dynamicznego, [](drm-content-protection-concept.md)punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo zawartość, musi mieć **stan** Uruchomiony.

W tym artykule pokazano, jak wykonać polecenie [start](/rest/api/media/streamingendpoints/start) w punkcie końcowym przesyłania strumieniowego przy użyciu różnych technologii. 
 
> [!NOTE]
> Rozliczenia są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.
    
## <a name="prerequisites"></a>Wymagania wstępne

Który napisze recenzję: 

* [Media Services pojęcia](concepts-overview.md)
* [Koncepcja punktu końcowego przesyłania strumieniowego](stream-streaming-endpoint-concept.md)
* [Dynamiczne tworzenie pakietów](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>Używanie interfejsu REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Aby uzyskać więcej informacji, zobacz: 

* Rozpoczynanie [dokumentacji referencyjnej punktu przesyłania strumieniowego.](/rest/api/media/streamingendpoints/start)
* Uruchamianie punktu końcowego przesyłania strumieniowego jest operacją asynchroniczną. 

    Aby uzyskać informacje na temat monitorowania długotrwałych operacji, zobacz [Long-running operations (Długotrwałe operacje).](media-services-apis-overview.md)
* Ta [kolekcja Postman zawiera](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) przykłady wielu operacji REST, w tym informacje na temat uruchamiania punktu końcowego przesyłania strumieniowego.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do swojego Azure Media Services konta.
1. W okienku po lewej stronie wybierz pozycję **Punkty końcowe przesyłania strumieniowego.**
1. Wybierz punkt końcowy przesyłania strumieniowego, który chcesz uruchomić, a następnie wybierz pozycję **Uruchom.**

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Aby uzyskać więcej informacji, zobacz [az ams streaming-endpoint start](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart).

## <a name="use-sdks"></a>Korzystanie z zestawów SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Zobacz kompletny [przykładowy kod Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Zobacz kompletny [przykładowy kod .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Następne kroki

* [Media Services specyfikacji OpenAPI w wersji 3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operacje punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints)
