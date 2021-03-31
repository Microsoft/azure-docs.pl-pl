---
title: Usługa komunikacji równorzędnej platformy Azure — często zadawane pytania
description: Informacje o Microsoft Azure często zadawane pytania dotyczące usługi komunikacji równorzędnej
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 95ce90dbbf47ffe527fe6f25704d9cd28b834ea9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026699"
---
# <a name="peering-service-faq"></a>Często zadawane pytania dotyczące usługi Komunikacja równorzędna

W tym artykule opisano najczęściej zadawane pytania dotyczące połączeń usługi Komunikacja równorzędna Azure.


**Pytania. Którzy są klientami docelowymi?**

A. Klienci docelowi są przedsiębiorstwami łączącymi się z chmurą firmy Microsoft przy użyciu Internetu jako transportu.

**Pytania. Czy klienci mogą zarejestrować się w usłudze Komunikacja równorzędna z wieloma dostawcami?** 

A. Tak, klienci mogą zarejestrować się w usłudze Komunikacja równorzędna z wieloma dostawcami w tym samym regionie lub w różnych regionach, ale nie dla tego samego prefiksu.

**Pytania. Czy klienci mogą wybrać unikatowego usługodawcę internetowego dla swoich witryn w regionie geograficznym?**

A. Tak. klienci mogą tego robić. Wybierz usługodawcę internetowego dla partnerów w regionie, który odpowiada potrzebom biznesowym i operacyjnym.

**Pytania. Co to jest punkt obecności przeglądarki Microsoft Edge?**

A. Jest to fizyczna lokalizacja, w której firma Microsoft nawiązuje połączenie z innymi sieciami. W lokalizacji PoP w przeglądarce Microsoft Edge są obsługiwane usługi, takie jak Azure Front drzwiczki i Azure CDN. Aby uzyskać więcej informacji, zobacz [Azure CDN](../cdn/cdn-features.md).

## <a name="peering-service-unique-characteristics"></a>Usługa komunikacji równorzędnej: unikatowe cechy

**Pytania. Jak usługa komunikacji równorzędnej różni się od normalnego dostępu do Internetu?**

A. Partnerzy zarejestrowani w usłudze komunikacji równorzędnej firmy Microsoft współpracują z firmą Microsoft w celu oferowania zoptymalizowanej routingu i niezawodnej łączności z usługami firmy Microsoft.  

**Pytania. Jak różni się usługa komunikacji równorzędnej od ExpressRoute?**

A. Usługa Azure ExpressRoute to prywatne, dedykowane połączenie z jednej lub wielu lokalizacji klientów. Chociaż usługa komunikacji równorzędnej oferuje zoptymalizowaną łączność publiczną i nie obsługuje łączności prywatnej, oferuje również zoptymalizowaną łączność dla lokalnego podgrupach internetowego.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze komunikacji równorzędnej, zobacz [Omówienie usługi Komunikacja równorzędna](about.md).
- Aby znaleźć dostawcę usług, zobacz [Komunikacja równorzędna z partnerami i lokalizacjami usług](location-partners.md).
- Aby dołączyć połączenie usługi komunikacji równorzędnej, zobacz Dołączanie [usługi komunikacji równorzędnej](onboarding-model.md).
- Aby zarejestrować połączenie usługi komunikacji równorzędnej, zobacz temat [Rejestrowanie połączenia z usługą komunikacji równorzędnej — Azure Portal](azure-portal.md).
- Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).