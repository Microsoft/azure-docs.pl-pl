---
title: Zabezpiecz swoje źródło za pomocą prywatnego linku na platformie Azure Front-Standard/Premium (wersja zapoznawcza)
description: Ta strona zawiera informacje o sposobach zabezpieczania łączności ze źródłem przy użyciu linku prywatnego.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: 88c000c96e632f970db075fafb13ea5eb4bbddfc
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799939"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Zabezpiecz swoje źródło za pomocą prywatnego linku na platformie Azure Front-Standard/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? Wyświetl [dokumenty z przodu platformy Azure](../front-door-overview.md).

## <a name="overview"></a>Omówienie

[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia dostęp do usług Azure PaaS oraz usług hostowanych platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jednostka SKU platformy Azure z przodu Premium może połączyć się ze źródłem za pośrednictwem usługi link prywatny. Aplikacje mogą być hostowane w prywatnej sieci wirtualnej lub za usługą PaaS, taką jak aplikacja sieci Web i konto magazynu, co eliminuje konieczność publicznego dostępu do źródła.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Architektura prywatnych punktów końcowych z drzwiami":::

Po włączeniu prywatnego linku do źródła w konfiguracji Premium drzwi platformy Azure, drzwi tylne tworzą prywatny punkt końcowy w Twoim imieniu od regionalnej sieci prywatnej drzwi. Ten punkt końcowy jest zarządzany przez platformy Azure z przodu. Otrzymasz żądanie prywatnego punktu końcowego drzwi platformy Azure dla komunikatu zatwierdzenia w miejscu pochodzenia. Po zatwierdzeniu żądania prywatny adres IP jest przypisywany z sieci wirtualnej z drzwiami przednimi, ruch między usługami frontonu platformy Azure a źródłem przechodzą przez nawiązane połączenie prywatne z siecią szkieletową sieci platformy Azure. Ruch przychodzący do źródła jest teraz zabezpieczony podczas powracania z zewnętrznych drzwi platformy Azure.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Włącz prywatny punkt końcowy":::

> [!NOTE]
> Po włączeniu prywatnego źródła linku i zaakceptowaniu prywatnego punktu końcowego Conenction potrwa kilka minut, aby można było nawiązać połączenie. W tym czasie żądania kierowane do źródła otrzymają komunikat o błędzie z Drzwiem przednim. Po nawiązaniu połączenia zostanie wyświetlony komunikat o błędzie.

## <a name="limitations"></a>Ograniczenia

Prywatne punkty końcowe platformy Azure są dostępne w następujących regionach w trakcie publicznej wersji zapoznawczej: Wschodnie stany USA, zachodnie stany USA i Południowo-środkowe stany USA.

W celu uzyskania najlepszego opóźnienia należy zawsze wybrać region platformy Azure znajdujący się najbliżej źródła, wybierając opcję włączania prywatnego linku tylnego drzwi.

Prywatne punkty końcowe platformy Azure są zarządzane przez platformę i w ramach subskrypcji usług frontonu platformy Azure. Drzwi frontonu platformy Azure umożliwiają połączenie prywatne z tą samą subskrypcją klienta, która jest używana do tworzenia profilu frontonu.

## <a name="next-steps"></a>Następne kroki

* Aby nawiązać połączenie z platformą Azure w warstwie Premium z aplikacją sieci Web za pośrednictwem usługi link prywatny, zobacz [nawiązywanie połączenia z aplikacją internetową przy użyciu prywatnego punktu końcowego](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Aby nawiązać połączenie z platformą Azure przed drzwiami do konta magazynu za pośrednictwem usługi link prywatny, zobacz [nawiązywanie połączenia z kontem magazynu przy użyciu prywatnego punktu końcowego](../../private-link/tutorial-private-endpoint-storage-portal.md).
