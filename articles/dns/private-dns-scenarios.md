---
title: Scenariusze dotyczące stref prywatnych — Azure DNS
description: W tym artykule omówiono typowe scenariusze korzystania z Azure DNS Private Zones.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76939339"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS scenariusze stref prywatnych

Azure DNS Private Zones zapewnić rozpoznawanie nazw w sieci wirtualnej, a także między sieciami wirtualnymi. W tym artykule przedstawiono kilka typowych scenariuszy, które mogą być zrealizowane przy użyciu tej funkcji.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenariusz: Rozpoznawanie nazw do zakresu pojedynczej sieci wirtualnej
W tym scenariuszu masz sieć wirtualną na platformie Azure, która zawiera wiele zasobów platformy Azure, w tym maszyn wirtualnych. Chcesz rozwiązać zasoby z sieci wirtualnej za pośrednictwem określonej nazwy domeny (strefy DNS) i potrzebujesz rozpoznawania nazw jako prywatnego i niedostępności z Internetu. Ponadto w przypadku maszyn wirtualnych w sieci wirtualnej potrzebna jest platforma Azure do automatycznego rejestrowania ich w strefie DNS. 

Ten scenariusz przedstawiono poniżej. Virtual Network o nazwie "A" zawiera dwie maszyny wirtualne (VNETA-VM1 i VNETA-VM2). Każdy z nich ma skojarzone prywatne adresy IP. Po utworzeniu strefy prywatnej o nazwie contoso.com i połączeniu tej sieci wirtualnej jako sieci wirtualnej rejestracji Azure DNS automatycznie utworzy dwa rekordy A w strefie. Teraz zapytania DNS z VNETA-VM1 do rozwiązania VNETA-VM2.contoso.com będą otrzymywać odpowiedzi DNS zawierające prywatny adres IP VNETA-VM2. Ponadto odwrotne zapytanie DNS (PTR) dla prywatnego adresu IP VNETA-VM1 (10.0.0.1) wydane z VNETA-VM2 otrzyma odpowiedź DNS, która zawiera nazwę VNETA-VM1, zgodnie z oczekiwaniami. 

![Rozpoznawanie pojedynczej sieci wirtualnej](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenariusz: Rozpoznawanie nazw w sieciach wirtualnych

Ten scenariusz jest najpopularniejszym przypadkiem, w którym należy skojarzyć strefę prywatną z wieloma sieciami wirtualnymi. Ten scenariusz może być zgodny z architekturą, taką jak model gwiazdy i gwiazdy, w której istnieje Centralna Sieć wirtualna koncentratora, do której są połączone wiele innych sieci wirtualnych. Centralną sieć wirtualną centrum można połączyć jako sieć wirtualną rejestracji ze strefą prywatną, a sieci wirtualne szprychy mogą być połączone jako rozwiązania sieci wirtualne. 

Na poniższym diagramie przedstawiono prostą wersję tego scenariusza, w której istnieją tylko dwie sieci wirtualne-A i B. A jest wyznaczeni jako sieć wirtualna rejestracji, a B jest wyznaczono jako sieć wirtualna rozpoznawania. Celem jest dla obu sieci wirtualnych udostępnienie wspólnej strefy contoso.com. Gdy strefa zostanie utworzona, a sieci wirtualne rozpoznawania i rejestracji są połączone ze strefą, platforma Azure automatycznie rejestruje rekordy DNS dla maszyn wirtualnych (VNETA-VM1 i VNETA-VM2) z sieci wirtualnej A. Rekordy DNS można także dodać ręcznie do strefy dla maszyn wirtualnych w sieci wirtualnej rozwiązania B. W przypadku tej konfiguracji zaobserwujesz następujące zachowanie zapytań DNS do przodu i wstecznego:
* Zapytanie DNS z VNETC-VM1 w sieci wirtualnej rozpoznawania B, dla VNETA-VM1.contoso.com, otrzyma odpowiedź DNS zawierającą prywatny adres IP VNETA-VM1.
* Odwrotna kwerenda DNS (PTR) od VNETC-VM2 w sieci wirtualnej rozpoznawania B, dla 10.1.0.1, otrzyma odpowiedź DNS zawierającą VNETB-VM1.contoso.com FQDN.  
* Zapytanie odwrotne DNS (PTR) od VNETC-VM3 w sieci wirtualnej rozpoznawania B, dla 10.0.0.1, będzie otrzymywać NXDOMAIN. Przyczyną jest to, że zapytania odwrotne DNS są ograniczone do tej samej sieci wirtualnej. 


![Wiele rozwiązań sieci wirtualnej](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenariusz: funkcja Split-Horizon

W tym scenariuszu masz przypadek użycia, w którym chcesz zrealizować inne zachowanie rozpoznawania nazw DNS w zależności od tego, gdzie klient znajduje się w ramach platformy Azure lub z Internetu, w przypadku tej samej strefy DNS. Na przykład możesz mieć prywatną i publiczną wersję aplikacji, która ma inne funkcje lub zachowanie, ale chcesz użyć tej samej nazwy domeny dla obu wersji. W tym scenariuszu można zrealizować Azure DNS, tworząc publiczną strefę DNS oraz strefę prywatną o tej samej nazwie.

Na poniższym diagramie przedstawiono ten scenariusz. Masz sieć wirtualną A, która ma dwie maszyny wirtualne (VNETA-VM1 i VNETA-VM2) z przydzielonymi prywatnymi adresami IP i publicznymi adresami IP. Tworzysz publiczną strefę DNS o nazwie contoso.com i zarejestruj publiczne adresy IP dla tych maszyn wirtualnych jako rekordy DNS w strefie. Należy również utworzyć strefę Prywatna strefa DNS o nazwie contoso.com, określając jako sieć wirtualną rejestracji. Platforma Azure automatycznie rejestruje maszyny wirtualne jako rekordy w strefie prywatnej, wskazując na ich prywatne adresy IP.

Teraz, gdy klient internetowy wystawia zapytanie DNS w celu wyszukania VNETA-VM1.contoso.com, platforma Azure zwróci publiczny rekord IP ze strefy publicznej. Jeśli to samo zapytanie DNS zostało wystawione przez inną maszynę wirtualną (na przykład: VNETA-VM2) w tej samej sieci wirtualnej A, platforma Azure zwróci prywatny rekord IP ze strefy prywatnej. 

![Podziel Brian rozwiązanie](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md).

Dowiedz się, jak [utworzyć prywatną strefę DNS](./private-dns-getstarted-powershell.md) w Azure DNS.

Dowiedz się więcej o strefach i rekordach DNS, odwiedzając: [Omówienie stref i rekordów DNS](dns-zones-records.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

