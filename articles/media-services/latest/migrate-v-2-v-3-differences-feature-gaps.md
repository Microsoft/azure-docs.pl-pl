---
title: Przerwy w działaniu między Azure Media Services V2 i V3 | Microsoft Docs
description: W tym artykule opisano przerwy w działaniu między Azure Media Services V2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 0f15c2bcd921c431dba1d1cce0454a6b2e752141
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690657"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Przerwy w działaniu między Azure Media Services V2 i V3

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-2.svg)

Ta część wskazówek dotyczących migracji zawiera szczegółowe informacje o różnicach między interfejsami API v2 i v3.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Przerwy w działaniu funkcji między interfejsami API w wersji 2 i V3

Interfejs API v3 ma następujące luki w funkcjach interfejsu API w wersji 2. Kilka zaawansowanych funkcji Media Encoder Standard w interfejsie API v2 nie są obecnie dostępne w wersji 3:

- Wstawianie dyskretnej ścieżki audio, gdy dane wejściowe nie zawierają dźwięku, ponieważ nie jest to już wymagane w przypadku Azure Media Player.

- Wstawianie ścieżki wideo, gdy dane wejściowe nie zawierają wideo.

- Zdarzenia na żywo z funkcją transkodowania obecnie nie obsługują wstawiania z użyciem znacznika średniego strumienia i znaczników AD za pośrednictwem wywołania interfejsu API.

- Usługa Azure Media Premium Encoder nie będzie już obsługiwana w wersji 2. Jeśli używasz go do 8-bitowego kodowania HEVC, użyj nowej obsługi HEVC w standardowym Koderie. 
    - Dodaliśmy obsługę mapowania kanału dźwiękowego do kodera standardowego.  Zapoznaj [się z artykułem audio w dokumentacji programu Media Servicesing Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Jeśli używasz zaawansowanych funkcji lub formatów danych wyjściowych dla licencjonowanego produktu innej firmy, takich jak MXF lub ProRes, użyj rozwiązania partnerskiego platformy Azure ze strumienia, który będzie transakcyjny przez czas wycofania w wersji 2. Alternatywnie możesz skorzystać z metody "Rozpuść Communications" lub " [Bitmovin](http://bitmovin.com)".

- Właściwość "zestaw dostępności" w punkcie końcowym przesyłania strumieniowego w wersji 2 nie jest już obsługiwana. Zapoznaj się z przykładowym projektem i wskazówkami dotyczącymi dostarczania [wysokiej dostępności VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding) w interfejsie API v3.

- W Media Services V3 nie można określić FairPlay IV. Chociaż nie ma to wpływu na klientów korzystających z Media Services w przypadku dostarczania pakietów i licencji, może to stanowić problem w przypadku korzystania z systemu DRM innej firmy w celu dostarczenia licencji FairPlay (Tryb hybrydowy).

- Szyfrowanie magazynu po stronie klienta na potrzeby ochrony zasobów w spoczynku zostało usunięte z interfejsu API v3 i zastąpione przez szyfrowanie usługi Storage dla danych przechowywanych w spoczynku. Interfejsy API v3 nadal pracują z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie pozwalają na tworzenie nowych.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
