---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego z Azure Media Services v3
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego z Azure Media Services v3.
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
ms.openlocfilehash: 8d3835d78ec55f120f6b9439aea9c29434e6bd47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289481"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Po utworzeniu konta Media Services zostanie do niego dodany **domyślny** [punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) w stanie **zatrzymanym** . Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i [szyfrowania dynamicznego](content-protection-overview.md), punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie **uruchomienia** .

W tym artykule pokazano, jak wykonać polecenie [uruchamiania](/rest/api/media/streamingendpoints/start) w punkcie końcowym przesyłania strumieniowego przy użyciu różnych technologii. 
 
> [!NOTE]
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.
    
## <a name="prerequisites"></a>Wymagania wstępne

Ponownego 

* [Koncepcje Media Services](concepts-overview.md)
* [Koncepcja punktu końcowego przesyłania strumieniowego](streaming-endpoint-concept.md)
* [Dynamiczne tworzenie pakietów](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Używanie interfejsu REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Aby uzyskać więcej informacji, zobacz: 

* Dokumentacja dotycząca [uruchamiania StreamingEndpoint](/rest/api/media/streamingendpoints/start) .
* Uruchamianie punktu końcowego przesyłania strumieniowego jest operacją asynchroniczną. 

    Aby uzyskać informacje o sposobach monitorowania długotrwałych operacji, zobacz [długotrwałe operacje](media-services-apis-overview.md).
* Ta [Kolekcja ogłoszeń](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) zawiera przykłady wielu operacji REST, w tym informacje na temat uruchamiania punktu końcowego przesyłania strumieniowego.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do swojego konta Azure Media Services.
1. W lewym okienku wybierz pozycję  **punkty końcowe przesyłania strumieniowego**.
1. Wybierz punkt końcowy przesyłania strumieniowego, który chcesz uruchomić, a następnie wybierz pozycję **Uruchom**.

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Aby uzyskać więcej informacji, zobacz [AZ AMS Streaming-Endpoint Start](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

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

Zobacz kompletny [przykład kodu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Następne kroki

* [Media Services v3 — Specyfikacja OpenAPI (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operacje punktów końcowych przesyłania strumieniowego](/rest/api/media/streamingendpoints)
