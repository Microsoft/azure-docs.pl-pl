---
title: Moduł równoważenia obciążenia między regionami (wersja zapoznawcza)
titleSuffix: Azure Load Balancer
description: Przegląd warstwy międzyregionowego modułu równoważenia obciążenia dla Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 89bf920a5a5dd833425f1b41bd206beaae9d30fd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946260"
---
# <a name="cross-region-load-balancer-preview"></a>Moduł równoważenia obciążenia między regionami (wersja zapoznawcza)

Azure Load Balancer dystrybuuje ruch przychodzący, który dociera do frontonu modułu równoważenia obciążenia, do wystąpień puli zaplecza.

Usługa Azure usługa Load Balancer w warstwie Standardowa obsługuje międzyregionowe Równoważenie obciążenia, co umożliwia geograficznie nadmiarowe scenariusze HA, takie jak:

* Ruch przychodzący pochodzący z wielu regionów.
* [Błyskawiczne globalne przejście w tryb failover](#regional-redundancy) do następnego optymalnego wdrożenia regionalnego.
* Rozkład obciążenia między regionami w najbliższym regionie platformy Azure z [niezwykle opóźnieniem](#ultra-low-latency).
* Możliwość [skalowania w górę i w dół](#ability-to-scale-updown-behind-a-single-endpoint) za pojedynczym punktem końcowym.
* [Statyczny adres IP](#static-ip)
* [Zachowywanie adresu IP klienta](#client-ip-preservation)
* [Kompiluj na istniejącym rozwiązaniu modułu równoważenia obciążenia](#build-cross-region-solution-on-existing-azure-load-balancer) bez krzywej uczenia

> [!IMPORTANT]
> Moduł równoważenia obciążenia między regionami jest obecnie w wersji zapoznawczej i można go wdrożyć w portalu. Zaloguj się, aby **https://preview.portal.azure.com** wyświetlić i wdrożyć funkcję... </br> </br>
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Równoważenie obciążenia między regionami zapewnia te same korzyści wynikające z wysokiej wydajności i małych opóźnień jako regionalnej standardowej usługi równoważenia obciążenia. 

Konfiguracja adresu IP frontonu dla międzyregionowego modułu równoważenia obciążenia jest statyczna i anonsowana w [większości regionów świadczenia usługi Azure](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagram modułu równoważenia obciążenia między regionami." border="true":::

> [!NOTE]
> Port zaplecza reguły równoważenia obciążenia w module równoważenia obciążenia między regionami powinien być zgodny z portem frontonu reguły równoważenia obciążenia/reguły NAT dla ruchu przychodzącego w regionalnym standardowym module równoważenia obciążenia. 

### <a name="regional-redundancy"></a>Nadmiarowość regionalna

Skonfiguruj nadmiarowość regionalną przez dodanie globalnego publicznego adresu IP frontonu do istniejących modułów równoważenia obciążenia. 

Jeśli jeden region ulegnie awarii, ruch jest kierowany do następnego najbliższego w dobrej kondycji regionalnego modułu równoważenia obciążenia.  

Sonda kondycji modułu równoważenia obciążenia między regionami zbiera informacje o dostępności co 20 sekund. Jeśli jeden z regionalnych modułów równoważenia obciążenia powróci swoją dostępność na 0, moduł równoważenia obciążenia między regionami wykryje awarię. Moduł równoważenia obciążenia w regionie jest wyłączany. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagram widoku ruchu globalnego w regionie." border="true":::

### <a name="ultra-low-latency"></a>Niezwykle małe opóźnienia

Algorytm równoważenia obciążenia w sąsiedztwie geograficznej jest oparty na lokalizacji geograficznej użytkowników i wdrożeniach regionalnych. 

Ruch uruchomiony z klienta zostanie osiągnięty w najbliższym uczestniczącym regionie i przepływa przez globalny szkielet sieci firmy Microsoft, aby dotrzeć do najbliższego wdrożenia regionalnego. 

Na przykład masz międzyregionowy moduł równoważenia obciążenia ze standardowymi modułami równoważenia obciążenia w regionach platformy Azure:

* Zachodnie stany USA
* Europa Północna

Jeśli przepływ jest uruchamiany z Seattle, ruch przechodzi do regionu zachodnie stany USA. Ten region jest najbliższym uczestnictwem w regionie Seattle. Ruch jest kierowany do najbliższego modułu równoważenia obciążenia, który jest zachodnie stany USA.

Moduł równoważenia obciążenia między regionami platformy Azure używa algorytmu równoważności z funkcją geograficzną dla decyzji routingu. 

Skonfigurowany tryb dystrybucji obciążenia dla regionalnych modułów równoważenia obciążenia jest używany do wykonywania ostatecznej decyzji routingu, gdy w sąsiedztwie geograficznym są używane wiele regionalnych modułów równoważenia obciążenia.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Możliwość skalowania w górę/w dół za pojedynczym punktem końcowym

Po udostępnieniu globalnego punktu końcowego międzyregionowego modułu równoważenia obciążenia klientom można dodać lub usunąć wdrożenia regionalne za globalnym punktem końcowym bez wpływu na klienta. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statyczny adres IP
Moduł równoważenia obciążenia między regionami zawiera statyczny publiczny adres IP, który zapewnia, że adres IP pozostaje taki sam. Aby dowiedzieć się więcej o statycznym adresie IP, Przeczytaj więcej [tutaj](../virtual-network/public-ip-addresses.md#allocation-method)

### <a name="client-ip-preservation"></a>Zachowywanie adresu IP klienta
Moduł równoważenia obciążenia między regionami to moduł równoważenia obciążenia sieci z przekazaniem do warstwy 4. To przekazanie zachowuje oryginalny adres IP pakietu.  Oryginalny adres IP jest dostępny dla kodu uruchomionego na maszynie wirtualnej. To zachowanie umożliwia zastosowanie logiki specyficznej dla adresu IP.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Kompiluj rozwiązanie między regionami na istniejącym Azure Load Balancer
Pula zaplecza modułu równoważenia obciążenia między regionami zawiera co najmniej jedną regionalną usługę równoważenia obciążenia. 

Dodaj istniejące wdrożenia modułu równoważenia obciążenia do międzyregionowego modułu równoważenia obciążenia w przypadku wdrożenia o wysokiej dostępności.

W **regionie głównym** jest wdrażany moduł równoważenia obciążenia między regionami. Ten region nie ma wpływu na sposób, w jaki ruch będzie kierowany. Jeśli region domowy ulegnie awarii, nie ma to wpływu na przepływ ruchu.

### <a name="home-regions"></a>Regiony domowe
* Wschodnie stany USA 2
* Zachodnie stany USA
* West Europe
* Southeast Asia
* Central US
* Europa Północna
* Azja Wschodnia

> [!NOTE]
> Moduł równoważenia obciążenia między regionami można wdrożyć tylko w jednym z 8 regionów powyżej.

**Region uczestniczący** to miejsce, w którym dostępny jest globalny publiczny adres IP usługi równoważenia obciążenia. 

Ruch uruchomiony przez użytkownika zostanie przetransmitowany do najbliższego uczestniczącego regionu za pomocą sieci podstawowej firmy Microsoft. 

Moduł równoważenia obciążenia między regionami kieruje ruch do odpowiedniego regionalnego modułu równoważenia obciążenia.

### <a name="participating-regions"></a>Uczestniczące regiony
* East US 
* West Europe 
* Środkowe stany USA 
* Wschodnie stany USA 2 
* Zachodnie stany USA 
* Europa Północna 
* South Central US 
* Zachodnie stany USA 2 
* Południowe Zjednoczone Królestwo 
* Southeast Asia 
* Północno-środkowe stany USA 
* Japonia Wschodnia 
* Azja Wschodnia 
* Zachodnio-środkowe stany USA 
* Australia Południowo-Wschodnia 
* Australia Wschodnia 
* Indie Środkowe 

## <a name="limitations"></a>Ograniczenia

* Konfiguracje adresów IP frontonu w różnych regionach są tylko publiczne. Wewnętrzna fronton nie jest obecnie obsługiwana.

* Nie można dodać prywatnego lub wewnętrznego modułu równoważenia obciążenia do puli zaplecza międzyregionalnego modułu równoważenia obciążenia 

* Konfiguracje adresów IP frontonu IPv6 między regionami nie są obsługiwane. 

* Nie można obecnie skonfigurować sondy kondycji. Domyślna sonda kondycji automatycznie zbiera informacje o dostępności dla regionalnego modułu równoważenia obciążenia co 20 sekund. 

* Obecnie nie można zintegrować usługi Azure Kubernetes Service (AKS) z Load Balancerem między regionami. Podczas konfigurowania Load Balancer międzyregionowego przed publicznym Load Balancer wdrożonym za pomocą AKS należy oczekiwać utraty łączności.

## <a name="pricing-and-sla"></a>Cennik i Umowa SLA
Moduł równoważenia obciążenia dla wielu regionów, udostępnia umowę [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) usługi równoważenia obciążenia w warstwie Standardowa.

 
## <a name="next-steps"></a>Następne kroki

- Zobacz [Samouczek: Tworzenie międzyregionowego modułu równoważenia obciążenia przy użyciu Azure Portal,](tutorial-cross-region-portal.md) aby utworzyć moduł równoważenia obciążenia między regionami.
- Aby utworzyć standardowy moduł równoważenia obciążenia, zobacz [Tworzenie publicznego](quickstart-load-balancer-standard-public-portal.md) modułu równoważenia obciążenia.
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
