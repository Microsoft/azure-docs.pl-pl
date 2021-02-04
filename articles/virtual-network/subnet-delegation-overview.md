---
title: Co to jest delegowanie podsieci w usłudze Azure Virtual Network?
description: Informacje na temat delegowania podsieci w usłudze Azure Virtual Network
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2020
ms.author: kumud
ms.openlocfilehash: 2801aa6b2e2b72df815b170200587c49ec0bae14
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538996"
---
# <a name="what-is-subnet-delegation"></a>Co to jest delegowanie podsieci?

Delegowanie podsieci pozwala wyznaczyć określoną podsieć dla wybranej usługi Azure PaaS, która musi zostać wprowadzona do sieci wirtualnej. Delegowanie podsieci zapewnia klientom pełną kontrolę nad zarządzaniem integracją usług platformy Azure z sieciami wirtualnymi.

W przypadku delegowania podsieci do usługi platformy Azure można umożliwić tej usłudze ustanowienie niektórych podstawowych reguł konfiguracji sieci dla tej podsieci, co pomoże usłudze platformy Azure w stabilny sposób. W związku z tym usługa platformy Azure może ustalić niektóre warunki wstępne lub po wdrożeniu, takie jak:
- Wdróż usługę w podsieci udostępnionej i dedykowanej.
- Dodaj do usługi zestaw zasad określania wymagań sieciowych po wdrożeniu, który jest wymagany, aby usługa działała prawidłowo.

##  <a name="advantages-of-subnet-delegation"></a>Zalety delegowania podsieci

Delegowanie podsieci do określonych usług zapewnia następujące korzyści:

- pomaga wyznaczyć podsieć dla jednej lub większej liczby usług platformy Azure i zarządzać wystąpieniami w podsieci zgodnie z wymaganiami. Na przykład właściciel sieci wirtualnej może zdefiniować następujące elementy dla delegowanej podsieci w celu lepszego zarządzania zasobami i dostępu w następujący sposób:
    - zasady ruchu filtrowania sieci z sieciowymi grupami zabezpieczeń.
    - zasady routingu ze zdefiniowanymi przez użytkownika trasami.
    - Integracja usług z konfiguracjami punktów końcowych usługi.
- ułatwia wstrzyknięcie usług w celu lepszego integrowania z siecią wirtualną przez definiowanie ich warunków wstępnych w postaci zasad dotyczących elementów sieciowych. Gwarantuje to, że wszystkie akcje, które mogą mieć wpływ na działanie wstrzykniętej usługi, można zablokować w miejscu.


## <a name="who-can-delegate"></a>Kto może delegować?
Delegowanie podsieci to ćwiczenie, które właściciele sieci wirtualnej muszą wykonać, aby wyznaczyć jedną z podsieci dla określonej usługi platformy Azure. Usługa platformy Azure z kolei wdraża wystąpienia w tej podsieci do użycia przez obciążenia klientów.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Wpływ delegowania podsieci w podsieci
Każda usługa systemu Azure definiuje własny model wdrażania, w którym można zdefiniować właściwości, które wykonują lub nie obsługują w delegowanej podsieci na potrzeby iniekcji, na przykład:
- podsieć udostępniona z innymi usługami platformy Azure lub MASZYNami wirtualnymi/zestawami skalowania maszyn wirtualnych w tej samej podsieci lub obsługuje tylko dedykowaną podsieć z tylko wystąpieniami tej usługi.
- obsługuje skojarzenie sieciowej grupy zabezpieczeń z delegowaną podsiecią.
- obsługuje sieciowej grupy zabezpieczeń skojarzone z delegowaną podsiecią, można także skojarzyć z każdą inną podsiecią.
- zezwala na skojarzenie tabeli tras z delegowaną podsiecią.
- zezwala na skojarzenie tabeli tras skojarzonej z delegowaną podsiecią z inną podsiecią.
- Określa minimalną liczbę adresów IP w delegowanej podsieci.
- określa przestrzeń adresów IP w delegowanej podsieci, aby pochodzić z prywatnej przestrzeni adresowej IP (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- Określa, że niestandardowa konfiguracja DNS ma wpis Azure DNS.
- wymaga usunięcia delegowania, aby można było usunąć podsieć lub sieć wirtualną.
- nie można używać z prywatnym punktem końcowym, jeśli podsieć jest delegowana.

Wprowadzone usługi mogą również dodać własne zasady w następujący sposób:
- **Zasady zabezpieczeń**: zbieranie reguł zabezpieczeń wymaganych przez daną usługę do pracy.
- **Zasady tras**: Kolekcja tras wymaganych przez daną usługę do pracy.

## <a name="what-subnet-delegation-does-not-do"></a>Co to jest delegowanie podsieci

Usługi platformy Azure wprowadzane do delegowanej podsieci nadal mają podstawowy zestaw właściwości, które są dostępne dla podsieci niedelegowanych, takich jak:
-  Usługi platformy Azure mogą wstrzyknąć wystąpienia do podsieci klienta, ale nie mogą mieć wpływu na istniejące obciążenia.
-  Zasady lub trasy, które są stosowane przez te usługi, są elastyczne i mogą zostać zastąpione przez klienta.

## <a name="next-steps"></a>Następne kroki

- [Delegowanie podsieci](manage-subnet-delegation.md)
