---
title: 'Scenariusz: Zapora platformy Azure — Interhub Routing'
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące routingu routingu między wieloma centrami z zaporą platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568684"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Scenariusz: Zapora platformy Azure — Interhub (wersja zapoznawcza)

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu celem jest kierowanie między wieloma centrami zawierającymi zaporę platformy Azure. Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scenariusza

W tym scenariuszu przyjęto, że następująca konfiguracja:

* W każdym centrum znajduje się wiele centrów z zaporą platformy Azure.
* Używasz bezpiecznego koncentratora wirtualnego.
* Skonfigurowano odpowiednie zasady ruchu prywatnego (VNet), Internetu i rozgałęzień.
* Połączenia między sieciami wirtualnymi (V2I), VNet-to-Branch (V2B), Branch-to-VNet (B2V), wszystkie przechodzą przez zaporę platformy Azure w każdym centrum.

Dodatkowe kwestie do rozważenia:

* W przypadku scenariusza między centrami przy użyciu zapory platformy Azure założono, że szprycha sieci wirtualnych nie jest skojarzona z oddzielnymi tabelami tras. (np. **Sieć VNET 1** skojarzona z **tabelą tras a**i siecią **wirtualną 2** skojarzoną z **tabelą tras B**). Wszystkie sieci wirtualnych są skojarzone z tą samą tabelą tras, w której znajduje się trasa dla zapory platformy Azure.
* Zabezpieczanie za pomocą zapory platformy Azure jest obecnie ograniczone do **Rozgałęzienia <-> sieci wirtualnej** i ruchu **internetowego** . Przepływ gałęzi do gałęzi za pośrednictwem zapory platformy Azure nie jest obecnie obsługiwany.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="Będąc":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Przepływ pracy scenariusza

Aby skonfigurować ten scenariusz, należy wziąć pod uwagę następujące czynności:

### <a name="step-1"></a><a name="step-1"></a>Krok 1

Przy założeniu, że połączenia są już zabezpieczone za pośrednictwem Menedżera zapory Azure, pierwszym krokiem jest upewnienie się, że wszystkie połączenia gałęzi i sieci wirtualnej propagują **Brak**. Jest to wymagane, aby zapewnić, że ruch jest ustawiany za pośrednictwem zapory platformy Azure.

1. Wybierz centrum i edytuj tabelę tras **none** .
1. Aktualizuj **propagację** , aby zaznaczyć wszystkie gałęzie i wszystkie sieci wirtualnych.

### <a name="step-2"></a><a name="step-2"></a>Krok 2

Skonfiguruj niestandardową tabelę tras na centrum.

1. W przypadku **centrum 1**Utwórz tabelę tras **RT_Hub1**.

    * Jeśli używasz Menedżera zapory platformy Azure, automatycznie tworzy trasę dla 0.0.0.0/0 z następnym przeskokiem **AZFW1** w domyślnej tabeli tras centrum. Zmienimy to ustawienie w kroku 3. W przypadku **RT_Hub1**należy jawnie utworzyć wpis 0.0.0.0/0 z następnym przeskokiem **AZFW1**. To ustawienie spowoduje aktywowanie V2V, B2V i V2I za pośrednictwem AZFW1.
    * Ponieważ chcesz, aby gałęzie **Hub 2** i sieci wirtualnych zostały osiągnięte przez **AZFW2** z **centrum 1** (zamiast za pośrednictwem AZFW1), należy dodać kolejną 2 zagregowane trasy dla gałęzi **centrów 2** (10.5.0.0/16->AZFW2) i sieci wirtualnych (10.2.0.0/16->AZFW2).

1. W przypadku **centrum 2**Utwórz tabelę tras **RT_Hub2**.

    * Jeśli używasz Menedżera zapory platformy Azure, automatycznie tworzy trasę dla 0.0.0.0/0 z następnym przeskokiem **AZFW2** w domyślnej tabeli tras centrum. Zmienimy to ustawienie w kroku 3. W przypadku **RT_Hub2**należy jawnie utworzyć wpis 0.0.0.0/0 z następnym przeskokiem **AZFW2**. To ustawienie spowoduje aktywowanie V2V, B2V i V2I za pośrednictwem **AZFW2**.
    * Ze względu na to, że ruch między centrami ma być chroniony przez **AZFW2**Hub 2, nie trzeba jawnie dodawać kroku podobnego do drugiego punktora w poprzednim kroku Hub 1.

### <a name="step-3"></a><a name="step-3"></a>Krok 3

Zmodyfikuj **domyślną tabelę tras** w każdym z centrów, aby dodać trasę statyczną dla przepływów **do sieci VNET** (B2V) i **gałęzi do Internetu** (B2I) za pośrednictwem zapory platformy Azure. Gałąź do gałęzi nie jest obecnie obsługiwana przez zaporę platformy Azure.

1. W przypadku **tabeli tras domyślnych centrum 1**:

    * **Rozgałęzienie do gałęzi Hub 2 za pośrednictwem AZFW2**: 10.5.0.0/16->AZFW2. Ta konfiguracja konfiguruje trasę dla zapory koncentrator 2.
    * **Gałąź do Hub 2 sieci wirtualnych za pośrednictwem AZFW2**: 10.2.0.0/16->AZFW2. Ta konfiguracja konfiguruje trasę dla zapory koncentrator 2.
    * **Gałąź do gałęzi (lokalna)/gałąź do sieci wirtualnej (lokalna)/gałąź do Internetu**: 0.0.0.0/0->AZFW1.

2. W przypadku **tabeli tras domyślnych centrum 2**:

    * Gałąź do gałęzi (lokalna i zdalna)/gałąź do sieci wirtualnej (lokalna i zdalna)/gałąź do Internetu: 0.0.0.0/0->AZFW2.

Spowoduje to zmianę konfiguracji routingu, tak jak pokazano na poniższej ilustracji.

**Rysunek 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="utworzonego":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
* Więcej informacji o sposobie konfigurowania routingu koncentratora wirtualnego znajduje się w temacie [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md).
