---
title: Architektury Media Services
titleSuffix: Azure Media Services
description: W tym artykule opisano architektury dla Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: e00079190371b3ae0e318aaadd8cf724d9d339d0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512725"
---
# <a name="media-services-architectures"></a>Architektury Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Transmisja strumieniowa na żywo — multimedia cyfrowe

Rozwiązanie przesyłania strumieniowego na żywo umożliwia Przechwytywanie wideo w czasie rzeczywistym i emitowanie go do klientów w czasie rzeczywistym, takich jak rozmowy przesyłania strumieniowego, konferencje i wydarzenia sportowe w trybie online. W tym rozwiązaniu wideo jest przechwytywane przez kamerę wideo i wysyłane do wejściowego punktu końcowego kanału. Kanał odbiera strumień danych wejściowych na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem punktu końcowego przesyłania strumieniowego do przeglądarki sieci Web lub aplikacji mobilnej. Kanał udostępnia również punkt końcowy monitorowania podglądu, który umożliwia wyświetlenie podglądu i zweryfikowanie strumienia przed dalszem przetwarzaniem i dostarczaniem. Kanał może również rejestrować i przechowywać pozyskaną zawartość w celu późniejszego przesyłania strumieniowego (wideo na żądanie).

To rozwiązanie jest oparte na usługach zarządzanych platformy Azure: Media Services i Content Delivery Network. Te usługi działają w środowisku o wysokiej dostępności, z poprawkami i obsługiwanymi, co pozwala skupić się na rozwiązaniu, a nie w środowisku, w którym są uruchamiane.

Zobacz [multimedia cyfrowe przesyłania strumieniowego na żywo](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream) w centrum architektury platformy Azure.

## <a name="instant-broadcasting-on-serverless-architecture"></a>Błyskawiczne emitowanie na architekturze bezserwerowej

Uprość jeden do wielu komunikacji w czasie rzeczywistym i aktualizacje za pomocą kodu bezserwerowego. Ta architektura używa Azure Functions, usługi sygnalizującego, konta magazynu i bazy danych SQL.

Zobacz [błyskawiczne emitowanie na architekturze Bezserwerowej](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/instant-broadcasting-on-serverless-architecture) w centrum architektury platformy Azure.

## <a name="video-on-demand-digital-media"></a>Wideo na żądanie — multimedia cyfrowe

Podstawowe rozwiązanie wideo na żądanie zapewniające możliwość przesyłania strumieniowego nagranej zawartości wideo, takiej jak filmy, klipy z wiadomościami, segmenty sportowe, wideo szkoleniowe i samouczki pomocy technicznej, do dowolnego urządzenia punktu końcowego z obsługą filmów wideo, aplikacji mobilnej lub przeglądarki klasycznej. Pliki wideo są przekazywane do usługi Azure Blob Storage, kodowane w formacie standardowym o wysokiej szybkości transmisji bitów, a następnie dystrybuowane za pośrednictwem wszystkich głównych adaptacyjnych protokołów przesyłania strumieniowego z szybkością transmisji bitów (HLS, MPEG-KRESKa, Wygładź) do klienta Azure Media Player.

To rozwiązanie jest oparte na usługach zarządzanych platformy Azure: Blob Storage, Content Delivery Network i Azure Media Player. Te usługi działają w środowisku o wysokiej dostępności, z poprawkami i obsługiwanymi, co pozwala skupić się na rozwiązaniu, a nie w środowisku, w którym są uruchamiane.

Zobacz [wideo na żądanie multimedia cyfrowe](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video) w centrum architektury platformy Azure.

## <a name="gridwich-media-processing-system"></a>System przetwarzania multimediów Gridwich

System Gridwich jest oparty na najlepszych rozwiązaniach dotyczących przetwarzania i dostarczania zasobów multimedialnych na platformie Azure. Chociaż system Gridwich jest specyficzny dla nośnika, przetwarzanie komunikatów i struktura zdarzeń mogą być stosowane do dowolnego przepływu pracy przetwarzania zdarzeń bezstanowych.

Zobacz [Gridwiching Media processing system](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture) w centrum architektury platformy Azure.

## <a name="next-steps"></a>Następne kroki

> [Omówienie usługi Azure Media Services](media-services-overview.md)