---
title: Co to jest usługa Azure Firewall Manager?
description: Informacje o funkcjach Menedżera zapory platformy Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 37cbc3737b826060e96524528b065bc8d711bd8b
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384773"
---
# <a name="what-is-azure-firewall-manager"></a>Co to jest usługa Azure Firewall Manager?

Menedżer zapory platformy Azure to usługa zarządzania zabezpieczeniami, która zapewnia centralne zasady zabezpieczeń i Zarządzanie trasami dla obwodów zabezpieczeń opartych na chmurze. 

Menedżer zapory może zapewnić zarządzanie zabezpieczeniami dla dwóch typów architektury sieci:

- **Bezpieczny koncentrator wirtualny**

   [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) to zasób zarządzany przez firmę Microsoft, który umożliwia łatwe tworzenie architektur Hub i szprych. Gdy zasady zabezpieczeń i routingu są skojarzone z takim centrum, jest ono nazywane *[bezpiecznym koncentratorem wirtualnym](secured-virtual-hub.md)*. 
- **Sieć wirtualna centrum**

   Jest to standardowa Sieć wirtualna platformy Azure, która jest tworzona i zarządzana. Gdy zasady zabezpieczeń są skojarzone z takim centrum, jest ono określane jako *centralny sieci wirtualnej*. W tej chwili obsługiwane są tylko zasady zapory platformy Azure. Można połączyć sieci wirtualne równorzędne, które zawierają serwery obciążeń i usługi. Można także zarządzać zaporami w autonomicznych sieciach wirtualnych, które nie są połączone z żadną szprychą.

Aby zapoznać się ze szczegółowym porównaniem bezpiecznych architektur wirtualnych *centrów wirtualnych* i *koncentratorów* , zobacz [co to są opcje architektury usługi Azure firewall Manager?](vhubs-and-vnets.md).

![Zapora — Menedżer](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Funkcje Menedżera zapory platformy Azure

Menedżer zapory platformy Azure oferuje następujące funkcje:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Wdrażanie i konfiguracja centralnej zapory platformy Azure

Można centralnie wdrażać i konfigurować wiele wystąpień zapory platformy Azure, które obejmują różne regiony i subskrypcje platformy Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Zasady hierarchiczne (globalne i lokalne)

Za pomocą Menedżera zapory platformy Azure można centralnie zarządzać zasadami zapory platformy Azure w wielu zabezpieczonych koncentratorach wirtualnych. Centralne zespoły IT mogą tworzyć globalne zasady zapory w celu wymuszenia zasad zapory dla całej organizacji w zespołach. Lokalne zasady zapory umożliwiają DevOps samoobsługowego modelu w celu zwiększenia elastyczności.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integracja z zabezpieczeniami jako usługą innych firm w celu zapewnienia bezpieczeństwa zaawansowanego

Oprócz zapory platformy Azure można zintegrować dostawców usługi Security AS (SECaaS) innych firm w celu zapewnienia dodatkowej ochrony sieci dla sieci wirtualnej i połączeń internetowych oddziałów.

Ta funkcja jest dostępna tylko w przypadku bezpiecznych wdrożeń koncentratora wirtualnego.

- Filtrowanie ruchu między sieciami wirtualnymi (V2I)

   - Filtrowanie ruchu wychodzącego z sieci wirtualnej za pomocą preferowanego dostawcy zabezpieczeń innej firmy.
   - Korzystaj z zaawansowanej, opartej na użytkownikach internetowej ochrony obciążeń w chmurze działających na platformie Azure.

- Filtrowanie ruchu z gałęzi do Internetu (B2I)

   Korzystaj z łączności z platformą Azure i globalnej dystrybucji, aby łatwo dodawać filtrowanie oddziałów dla gałęzi do scenariuszy internetowych.

Aby uzyskać więcej informacji o dostawcach partnerów zabezpieczeń, zobacz [co to są dostawcy partnera usługi Azure firewall Security?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Scentralizowane zarządzanie trasami

Łatwo kieruj ruch do bezpiecznego centrum w celu filtrowania i rejestrowania bez konieczności ręcznego konfigurowania tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych. 

Ta funkcja jest dostępna tylko w przypadku bezpiecznych wdrożeń koncentratora wirtualnego.

Dostawców innych firm można używać do filtrowania ruchu z Internetu (B2I), obok siebie przy użyciu zapory platformy Azure dla gałęzi do sieci wirtualnej (B2V), sieci wirtualnej z siecią wirtualną (V2V) i Sieć wirtualna do Internetu (V2I). Dostawców innych firm można także używać do filtrowania ruchu V2I, o ile nie jest wymagana Zapora platformy Azure dla B2V lub V2V. 

## <a name="region-availability"></a>Dostępność w danym regionie

Zasady zapory platformy Azure mogą być używane w różnych regionach. Można na przykład utworzyć zasady w regionie zachodnie stany USA i używać ich w regionach Wschodnie stany USA. 

## <a name="known-issues"></a>Znane problemy

Menedżer zapory platformy Azure ma następujące znane problemy:

|Problem  |Opis  |Ograniczanie ryzyka  |
|---------|---------|---------|
|Dzielenie ruchu nie jest obecnie obsługiwane.|Dzielenie pakietów Office 365 i Public PaaS nie jest obecnie obsługiwane. W związku z tym wybranie dostawcy innej firmy dla usługi V2I lub B2I spowoduje również wysłanie wszystkich usług Azure Public PaaS i Office 365 przez usługę partnera.|Badanie podziału ruchu w centrum.
|Jeden bezpieczny koncentrator wirtualny na region.|Na region nie można mieć więcej niż jednego zabezpieczonego koncentratora wirtualnego.|Utwórz wiele wirtualnych sieci WAN w regionie.|
|Zasady podstawowe muszą znajdować się w tym samym regionie co zasady lokalne.|Utwórz wszystkie zasady lokalne w tym samym regionie co zasady podstawowe. Można nadal stosować zasady, które zostały utworzone w jednym regionie w zabezpieczonym centrum z innego regionu.|Badanie|
|Komunikacja między centrami, która nie przechodzi przez zabezpieczone centrum wirtualne|Zabezpieczona wirtualna koncentrator do bezpiecznej komunikacji z koncentratorem wirtualnym nie jest jeszcze obsługiwana, ale komunikacja między centrum a usługą Hub będzie nadal możliwa.|Badanie|
|Wszystkie zabezpieczone centra wirtualne współużytkujące tę samą wirtualną sieć WAN muszą znajdować się w tej samej grupie zasobów.|To zachowanie jest wyrównane z koncentratorami wirtualnych sieci WAN już dziś.|Utwórz wiele wirtualnych sieci WAN, aby umożliwić tworzenie zabezpieczonych koncentratorów wirtualnych w różnych grupach zasobów.|

## <a name="next-steps"></a>Następne kroki

- Przegląd [wdrożenia Menedżera zapory platformy Azure](deployment-overview.md)
- Poznaj [bezpieczne centra wirtualne](secured-virtual-hub.md).
