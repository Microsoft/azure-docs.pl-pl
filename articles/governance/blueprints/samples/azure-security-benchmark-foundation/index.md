---
title: Przykładowy plan usługi Azure Security test Foundation — Omówienie
description: Omówienie i architektura przykładowego planu usługi Azure Security test Foundation.
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: 4bbc5b94ea9b977a2b71edbf15cf5a7aa0566974
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487119"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Omówienie przykładu planu usługi Azure Security test Foundation

Przykładowy plan usługi Azure Security test Foundation zawiera zestaw wzorców infrastruktury bazowej, które ułatwiają tworzenie bezpiecznego i zgodnego środowiska platformy Azure. Plan pomaga wdrożyć architekturę opartą na chmurze, która oferuje rozwiązania do scenariuszy, które mają akredytacji lub wymagania dotyczące zgodności. Tym podstawą przykładowego planu jest rozszerzenie [przykładowego planu wydajności Azure Security](../azure-security-benchmark/index.md). Wdraża i konfiguruje granice sieci, monitorowanie i inne zasoby z zastosowaniem zasad i innych guardrails zdefiniowanych w [teście zabezpieczeń platformy Azure](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Architektura

Utworzone przez ten przykład środowisko podstawowe jest oparte na jednostkach architektury [modelu gwiazdy i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
Plan służy do wdrażania sieci wirtualnej centrum, która zawiera wspólne i udostępnione zasoby, usługi i artefakty, takie jak Azure bastionu, Brama i Zapora na potrzeby połączeń, zarządzania i połączeń w pudełkach do hostowania dodatkowych/opcjonalnych sieci zarządzania, konserwacji, administracji i infrastruktury łączności. Co najmniej jedna sieć wirtualna szprych jest wdrażana do obciążeń aplikacji hosta, takich jak usługi sieci Web i bazy danych. Sieci wirtualne szprych są połączone z centralną siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure w celu zapewnienia bezproblemowej i bezpiecznej łączności. Dodatkowe szprychy mogą być dodawane przez ponowne przypisanie przykładowego planu lub ręczne utworzenie sieci wirtualnej platformy Azure i komunikację równorzędną z centralną siecią wirtualną. Wszystkie połączenia zewnętrzne z sieciami wirtualnymi szprych i podsieciami są skonfigurowane do kierowania za pośrednictwem sieci wirtualnej centrum oraz za pośrednictwem połączeń zapory, bramy i zarządzania.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Diagram architektury przykładowego planu usługi Azure Security test Foundation" border="false":::

Ten plan wdraża kilka usług platformy Azure, aby zapewnić bezpieczną, monitorowaną i przygotowaną przez przedsiębiorstwo podstawę. To środowisko zawiera następujące składniki:

- [Dzienniki Azure monitor](../../../../azure-monitor/platform/data-platform-logs.md) i konto usługi Azure Storage w celu zapewnienia, że dzienniki zasobów, dzienniki aktywności, metryki i sieci ruchu są przechowywane w centralnej lokalizacji w celu łatwego wykonywania zapytań, analiz, archiwizacji i alertów.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (wersja standardowa), aby zapewnić ochronę przed zagrożeniami dla zasobów platformy Azure.
- [Platforma Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) w centrum obsługuje podsieci w celu połączenia z siecią lokalną, ruchu przychodzącego i wychodzącego do/do łączności z Internetem oraz opcjonalnych podsieci na potrzeby wdrażania dodatkowych usług administracyjnych lub zarządzania. Virtual Network w szprychie zawiera podsieci do hostowania obciążeń aplikacji. Dodatkowe podsieci można utworzyć po wdrożeniu w razie potrzeby w celu zapewnienia obsługi odpowiednich scenariuszy.
- [Zapora platformy Azure](../../../../firewall/overview.md) do kierowania całego ruchu wychodzącego z Internetu oraz do włączania przychodzącego ruchu internetowego za pośrednictwem pola skoku. (Domyślne reguły zapory blokują cały ruch przychodzący i wychodzący z Internetu, a zasady muszą być skonfigurowane po wdrożeniu, zgodnie z wymaganiami).
- [Sieciowe grupy zabezpieczeń](../../../../virtual-network/network-security-group-how-it-works.md) (sieciowych grup zabezpieczeń) są przypisane do wszystkich podsieci (z wyjątkiem podsieci usługi Azure bastionu, bramy i zapory platformy Azure) skonfigurowanych tak, aby blokowały cały ruch przychodzący i wychodzący Internetu.
- [Grupy zabezpieczeń aplikacji](../../../../virtual-network/application-security-groups.md) umożliwiające grupowanie maszyn wirtualnych platformy Azure w celu zastosowania wspólnych zasad zabezpieczeń sieci.
- [Kierowanie tabel](../../../../virtual-network/manage-route-table.md) do routingu cały ruch wychodzący z podsieci przez zaporę. (Reguły zapory platformy Azure i sieciowej grupy zabezpieczeń muszą zostać skonfigurowane po wdrożeniu, aby otworzyć połączenie).
- [Usługa azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) umożliwia monitorowanie, diagnozowanie i wyświetlanie metryk zasobów w sieci wirtualnej platformy Azure.
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md) , aby chronić zasoby platformy Azure przed atakami DDoS.
- [Usługa Azure bastionu](../../../../bastion/bastion-overview.md) zapewnia bezproblemową i bezpieczną łączność z maszyną wirtualną, która nie wymaga publicznego adresu IP, agenta ani specjalnego oprogramowania klienckiego.
- [VPN Gateway platformy Azure](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) , aby włączyć zaszyfrowany ruch sieciowy między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznego Internetu.

> [!NOTE] 
> Podstawą usługi Azure Security test Foundation jest podstawowa architektura dla obciążeń. Powyższy diagram architektury zawiera kilka zasobów teoretycznych, aby zademonstrować potencjalne użycie podsieci. Nadal konieczne jest wdrożenie obciążeń w tej architekturze podstawowej.

## <a name="next-steps"></a>Następne kroki

Zawarto przegląd i architekturę przykładowego planu usługi Azure Security test Foundation.

> [!div class="nextstepaction"]
> [Plan usługi Azure Security test Foundation — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
