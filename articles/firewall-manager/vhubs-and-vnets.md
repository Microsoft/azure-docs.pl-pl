---
title: Jakie są opcje architektury Menedżera zapory platformy Azure?
description: Porównanie i kontrast przy użyciu sieci wirtualnej Hub lub bezpiecznych architektur koncentratorów wirtualnych za pomocą Menedżera zapory platformy Azure.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444578"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Jakie są opcje architektury Menedżera zapory platformy Azure?

Menedżer zapory platformy Azure może zapewnić zarządzanie zabezpieczeniami dla dwóch typów architektury sieci:

- **bezpieczny koncentrator wirtualny**

   [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) to zasób zarządzany przez firmę Microsoft, który umożliwia łatwe tworzenie architektur Hub i szprych. Gdy zasady zabezpieczeń i routingu są skojarzone z tym centrum, jest ono nazywane *[bezpiecznym koncentratorem wirtualnym](secured-virtual-hub.md)*. 
- **Sieć wirtualna centrum**

   Jest to standardowa Sieć wirtualna platformy Azure, która jest tworzona i zarządzana. Gdy zasady zabezpieczeń są skojarzone z takim centrum, jest ono określane jako *centralny sieci wirtualnej*. W tej chwili obsługiwane są tylko zasady zapory platformy Azure. Można połączyć sieci wirtualne równorzędne, które zawierają serwery obciążeń i usługi. Można także zarządzać zaporami w autonomicznych sieciach wirtualnych, które nie są połączone z żadną szprychą.

## <a name="comparison"></a>Porównanie

W poniższej tabeli porównano te dwie opcje architektury i można ułatwić decydowanie o tym, który z nich jest odpowiedni dla wymagań dotyczących zabezpieczeń w organizacji:


|  |**Sieć wirtualna centrum**|**Bezpieczny koncentrator wirtualny**  |
|---------|---------|---------|
|**Zasób źródłowy**     |Sieć wirtualna|Koncentrator wirtualnych sieci WAN|
|**& szprychy centrum**     |Używa komunikacji równorzędnej sieci wirtualnej|Zautomatyzowane przy użyciu połączenia sieci wirtualnej centrum|
|**Łączność Premium**     |VPN Gateway do 10 GB/s i 30 połączeń S2S; ExpressRoute|Bardziej skalowalne VPN Gateway do 20 GB/s i połączeń S2S 1000 Express Route|
|**Zautomatyzowana łączność gałęzi przy użyciu SDWAN**      |Nieobsługiwane|Obsługiwane|
|**Centra na region**     |Wiele sieci wirtualnych na region|Pojedynczy koncentrator wirtualny na region. Wiele centrów z wieloma wirtualnymi sieciami WAN|
|**Zapora systemu Azure — wiele publicznych adresów IP**      |Dostarczone przez klienta|Generowane automatycznie. Do udostępnienia w wersji ogólnie dostępnej.|
|**Strefy dostępności zapory platformy Azure**     |Obsługiwane|Niedostępne w wersji zapoznawczej. Do udostępnienia w wersji ogólnie dostępnej|
|**Zaawansowane zabezpieczenia internetowe z zabezpieczeniami innych firm jako partnerzy usługi**     |Klient i zarządzana łączność z siecią VPN z usługą partnerski wybór|Automatyczne za pomocą przepływu zaufanych partnerów zabezpieczeń i środowiska zarządzania partnerami|
|**Scentralizowane zarządzanie trasami umożliwiające kierowanie ruchu do centrum**     |Trasa zdefiniowana przez użytkownika zarządzana przez klienta|Obsługiwane przy użyciu protokołu BGP|
|**Zapora aplikacji internetowej w usłudze Application Gateway** |Obsługiwane w Virtual Network|Obecnie obsługiwane w sieci szprychy|
|**Sieciowe urządzenie wirtualne**|Obsługiwane w Virtual Network|Obecnie obsługiwane w sieci szprychy|

## <a name="next-steps"></a>Następne kroki

- Przegląd [wdrożenia usługi Azure firewall Manager w wersji zapoznawczej](deployment-overview.md)
- Poznaj [bezpieczne centra wirtualne](secured-virtual-hub.md).