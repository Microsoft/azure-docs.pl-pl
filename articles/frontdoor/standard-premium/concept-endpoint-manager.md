---
title: 'Moje drzwi platformy Azure: Menedżer punktów końcowych'
description: Ten artykuł zawiera Omówienie Menedżera punktu końcowego drzwi platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100100"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Co to jest Menedżer punktów końcowych platformy Azure w warstwie Standardowa/Premium (wersja zapoznawcza)?

> [!NOTE]
> * Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? Wyświetl [dokumenty z przodu platformy Azure](../front-door-overview.md).

Program Endpoint Manager zawiera omówienie punktów końcowych skonfigurowanych dla drzwi platformy Azure. Punkt końcowy jest logicznym grupą domen i skojarzonych z nimi konfiguracji. Program Endpoint Manager ułatwia zarządzanie kolekcją punktów końcowych dla operacji CRUD (Create, Read, Update i Delete). Można zarządzać następującymi elementami punktów końcowych za pomocą programu Endpoint Manager:

* Domains
* Grupy pierwotne
* Trasy
* Zabezpieczenia

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Zrzut ekranu Menedżera punktów końcowych bez konfiguracji." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Menedżer punktów końcowych wyświetla liczbę wystąpień każdego elementu tworzonych w punkcie końcowym. Zostanie również wyświetlony stan skojarzenia dla każdego elementu. Można na przykład utworzyć wiele domen i grup początkowych, a następnie przypisać skojarzenie między nimi przy użyciu różnych tras.

> [!IMPORTANT]
> * Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [**dodatkowe warunki użytkowania wersji**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="linked-view"></a>Widok połączony

Widok połączony w programie Endpoint Manager umożliwia łatwe zidentyfikowanie skojarzenia między elementami czołowymi drzwi platformy Azure, takimi jak:

* Które domeny są skojarzone z bieżącym punktem końcowym?
* Która grupa pochodzenia jest skojarzona z tą domeną?
* Które zasady WAF są skojarzone z którą domeną?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Zrzut ekranu programu Endpoint Manager z konfiguracjami." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
