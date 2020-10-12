---
title: Zarządzanie zasadami buforowania Azure CDN w programie Azure Media Services | Microsoft Docs
description: W tym artykule opisano sposób zarządzania zasadami buforowania Azure CDN w programie Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 6beaee98e78e79c48270801f5696e4e487b0a2c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84883717"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Zarządzanie zasadami buforowania Azure CDN w programie Azure Media Services
Azure Media Services zapewnia oparty na protokole HTTP adaptacyjne przesyłanie strumieniowe i pobieranie progresywne. Przesyłanie strumieniowe oparte na protokole HTTP jest wysoce skalowalne dzięki zaletom buforowania w warstwach serwera proxy i sieci CDN, a także buforowania po stronie klienta. Punkty końcowe przesyłania strumieniowego zapewniają ogólne możliwości przesyłania strumieniowego, a także konfigurację dla nagłówków pamięci podręcznej HTTP. Punkty końcowe przesyłania strumieniowego ustawiają pamięć podręczną HTTP: max-age i Expiress. Więcej informacji o nagłówkach pamięci podręcznej HTTP można uzyskać z [w3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Domyślne nagłówki buforowania
Domyślnie Przesyłanie strumieniowe — punkty końcowe mają 3 dni nagłówków pamięci podręcznej dla danych przesyłanych strumieniowo na żądanie (rzeczywiste fragmenty nośników/fragmenty) i manifest (lista odtwarzania). W przypadku przesyłania strumieniowego na żywo punkty końcowe przesyłania strumieniowego stosują 3-dniowe nagłówki pamięci podręcznej dla danych (rzeczywiste fragmenty nośników/fragmenty) i nagłówek pamięci podręcznej 2 s dla żądań manifestu Gdy program na żywo włącza na żądanie (Archiwum na żywo), są stosowane nagłówki pamięci podręcznej przesyłania strumieniowego na żądanie.

## <a name="azure-cdn-integration"></a>Integracja Azure CDN
Azure Media Services zapewnia [zintegrowaną sieć CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) dla punktów końcowych przesyłania strumieniowego. Nagłówki kontroli pamięci podręcznej są stosowane w taki sam sposób jak punkty końcowe przesyłania strumieniowego do punktów końcowych przesyłania strumieniowego w usłudze CDN Azure CDN używa wartości pamięci podręcznej skonfigurowanych punktów końcowych przesyłania strumieniowego w celu zdefiniowania czasu życia wewnętrznie buforowanych obiektów, a także używa tej wartości do ustawiania nagłówków pamięci podręcznej dostarczania. W przypadku korzystania z punktów końcowych przesyłania strumieniowego z obsługą sieci CDN nie zaleca się ustawiania małych wartości pamięci podręcznej. Ustawienie małych wartości zmniejsza wydajność i zmniejsza korzyści płynące z sieci CDN. Nie można ustawić nagłówków pamięci podręcznej mniejszej niż 600 sekund dla punktów końcowych przesyłania strumieniowego z obsługą sieci CDN.

> [!IMPORTANT]
>Azure Media Services ma pełną integrację z Azure CDN. Za pomocą jednego kliknięcia możesz zintegrować wszystkich dostępnych dostawców Azure CDN do punktu końcowego przesyłania strumieniowego, w tym produktów standardowych i Premium. Aby uzyskać więcej informacji, zobacz ten [anons](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Opłaty za dane przesyłane z punktu końcowego przesyłania strumieniowego do sieci CDN są wyłączane tylko wtedy, gdy sieć CDN jest włączona za pośrednictwem interfejsów API punktu końcowego przesyłania strumieniowego lub z sekcji Azure Portal Ręczna integracja lub bezpośrednie tworzenie punktu końcowego usługi CDN przy użyciu interfejsów API lub portalu usługi CDN nie powoduje wyłączenia opłat za dane.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurowanie nagłówków pamięci podręcznej za pomocą Azure Media Services
Aby skonfigurować wartości nagłówka pamięci podręcznej, można użyć interfejsów API Azure Portal lub Azure Media Services.

1. Aby skonfigurować nagłówki pamięci podręcznej przy użyciu Azure Portal, zobacz sekcję [jak zarządzać punktami końcowymi przesyłania strumieniowego](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) konfigurowanie punktu końcowego przesyłania strumieniowego.
2. Azure Media Services interfejs API REST, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [Właściwości StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Kolejność pierwszeństwa konfiguracji pamięci podręcznej
1. Azure Media Services skonfigurowana wartość pamięci podręcznej przesłania wartość domyślną.
2. Jeśli nie istnieje konfiguracja ręczna, stosowane są wartości domyślne.
3. Domyślnie 2 s nagłówki pamięci podręcznej mają zastosowanie do manifestu przesyłania strumieniowego na żywo, niezależnie od konfiguracji usługi Azure Media lub Azure Storage, a zastąpienie tej wartości jest niedostępne.

