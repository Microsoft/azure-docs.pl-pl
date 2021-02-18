---
title: Porównanie jednostek SKU platformy Azure w warstwie Standardowa/Premium
description: Ten artykuł zawiera omówienie standardowej i podstawowej jednostki SKU platformy Azure oraz różnice między nimi.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100249"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Omówienie jednostki SKU usługi Azure Front-Standard/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Z przodu platformy Azure oferowane są 3 różne jednostki SKU, [platformy Azure z przodu](../front-door-overview.md), standard drzwi platformy Azure (wersja zapoznawcza) i Azure Front-Premium (wersja zapoznawcza). Jednostki SKU platformy Azure w warstwie Standardowa/Premium łączą możliwości zewnętrznych drzwi platformy Azure, Azure CDN Standard od firmy Microsoft, usługa Azure WAF na jedną bezpieczną platformę sieci CDN w chmurze z funkcją inteligentnej ochrony przed zagrożeniami.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [**dodatkowe warunki użytkowania wersji**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

* **Standardowa jednostka SKU platformy Azure z przodu** :

    * Optymalizacja Content Delivery
    * Oferuje przyspieszenie zawartości statycznej i dynamicznej
    * Globalne Równoważenie obciążenia
    * Odciążanie protokołu SSL
    * Zarządzanie domenami i certyfikatami
    * Analiza zwiększonych ruchu 
    * Podstawowe możliwości zabezpieczeń

* **Usługa Azure Front drzwiczki Premium SKU** kompiluje się z możliwościami standardowej jednostki SKU i dodaje:

    * Szerokie możliwości zabezpieczeń w ramach WAF
    * Ochrona BOT
    * Obsługa linków prywatnych
    * Integracja z programem Microsoft Threat Intelligence and Security Analytics. 

![Diagram przedstawiający porównanie jednostek SKU z przodu.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Porównanie funkcji

| Cecha |      Standardowa (Standard)      |  Premium |
|----------|:-------------:|------:|
| Niestandardowe domeny | Tak | Tak |
| Odciążanie protokołu SSL | Tak | Tak |
| Buforowanie |  Tak  | Tak |
| Kompresja | Tak | Tak   |
| Globalne Równoważenie obciążenia | Tak  | Tak |
| Routing warstwy 7 | Tak | Tak |
| Regenerowanie adresów URL | Tak | Tak |
| Aparat reguł | Tak | Tak |
| Źródło prywatne (łącze prywatne) | Nie | Tak |
| Zapora aplikacji internetowej | Nie | Tak |
| Ochrona bot | Nie | Tak |
| Ulepszone metryki i Diagnostyka | Tak | Tak |
| Raporty dotyczące ruchu | Tak | Tak |
| Raport o zabezpieczeniach | Nie | Tak | 

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć drzwi tylne](create-front-door-portal.md)
