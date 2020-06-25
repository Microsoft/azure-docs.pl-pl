---
title: Wprowadzenie
description: Poznaj funkcje i korzyści wynikające z rozwiązania Azure VMware (Automatyczna synchronizacja) w celu wdrażania obciążeń opartych na oprogramowaniu VMware na platformie Azure i zarządzania nimi.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 9e3ab0abfdaa613a08675356bc5b01949e0381ae
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84749672"
---
# <a name="what-is-azure-vmware-solution-avs-preview"></a>Co to jest wersja zapoznawcza rozwiązania Azure VMware (Automatyczna synchronizacja)?

Rozwiązanie Azure VMware (AVS) zapewnia chmury prywatne na platformie Azure. Chmury prywatne zawierają klastry vSphere, zbudowane z dedykowanej infrastruktury platformy Azure bez systemu operacyjnego. Klastry chmury prywatnej można skalować z 3 do 16 hostów, z możliwością posiadania wielu klastrów w jednej chmurze prywatnej. Obsługiwane są wszystkie chmury prywatne z vCenter Server, sieci vSAN, vSphere i NSX-T. Można migrować obciążenia ze środowisk lokalnych, tworzyć i wdrażać nowe maszyny wirtualne oraz korzystać z usług platformy Azure z chmur prywatnych.

Automatyczna synchronizacja to zweryfikowane rozwiązanie VMware, które umożliwia weryfikowanie i testowanie ulepszeń i uaktualnień. Infrastruktura i oprogramowanie chmury prywatnej są zarządzane i utrzymywane przez firmę Microsoft, co pozwala skupić się na tworzeniu i uruchamianiu obciążeń w chmurach prywatnych.

Na poniższym diagramie przedstawiono sąsiedztwo między chmurami prywatnymi i sieci wirtualnych na platformie Azure, usługach platformy Azure i w środowiskach lokalnych. Dostęp sieciowy z chmur prywatnych do usług platformy Azure lub sieci wirtualnych zapewnia integrację punktów końcowych usługi platformy Azure opartej na umowie SLA. Dostęp do chmury prywatnej ze środowiska lokalnego używa Global Reach ExpressRoute do prywatnego i bezpiecznego połączenia.

![Obraz przedstawiający możliwość automatycznej synchronizacji sąsiedztwa chmury prywatnej na platformie Azure i lokalnie](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

Automatyczna synchronizacja chmur prywatnych i klastrów jest tworzona z hosta infrastruktury platformy Azure bez systemu operacyjnego. Hosty wysokiej klasy mają 576 GB pamięci RAM i dwa procesory Intel 18 Core, 2,3 GHz. Hosty mają dwie sieci vSAN diskgroups z łączną warstwą pojemności 15,36 TB (SSD), a w warstwie pamięci podręcznej 3,2 TB (interfejsu NVMe) sieci vSAN.

Nowe chmury prywatne są wdrażane za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="networking"></a>Networking

Gdy Chmura prywatna jest wdrożona, tworzone są sieci prywatne do zarządzania, aprowizacji i vMotion. Te sieci prywatne są używane do uzyskiwania dostępu do Menedżera vCenter i NSX-T oraz dla maszyny wirtualnej vMotion lub wdrożenia. Wszystkie sieci prywatne są dostępne z sieci wirtualnej na platformie Azure lub w środowiskach lokalnych. ExpressRoute Global Reach służy do łączenia chmur prywatnych z środowiskami lokalnymi, a to połączenie wymaga sieci wirtualnej z obwodem usługi ExpressRoute w ramach subskrypcji.

Dostęp do Internetu i usług platformy Azure jest inicjowany podczas wdrażania chmury prywatnej. Dostępny jest dostęp, dzięki czemu maszyny wirtualne w sieciach obciążeń produkcyjnych mogą korzystać z platformy Azure lub usług internetowych. Dostęp do Internetu jest domyślnie wyłączony w przypadku nowych chmur prywatnych i można go włączyć lub wyłączyć w dowolnym momencie.

Aby uzyskać więcej informacji na temat sieci i międzyłączności, zobacz artykuł dotyczący [pojęć dotyczących sieci](concepts-networking.md) .

## <a name="access-and-security"></a>Dostęp i zabezpieczenia

W celu zapewnienia zwiększonych zabezpieczeń, automatyczna synchronizacja chmur prywatnych używa kontroli dostępu opartej na rolach vSphere. funkcje vSphere SSO LDAP można zintegrować z Azure Active Directory. Aby uzyskać więcej informacji o tożsamości i uprawnieniach, zobacz artykuł dotyczący [pojęć dotyczących dostępu i tożsamości](concepts-identity.md) .

Sieci vSAN dane — szyfrowanie w spoczynku jest domyślnie włączone i służy do zapewniania bezpieczeństwa sieci vSAN magazynu danych. Jest on opisany bardziej szczegółowo w artykule [pojęcia dotyczące magazynu](concepts-storage.md) .

## <a name="host-and-software-lifecycle-maintenance"></a>Obsługa cyklu życia hosta i oprogramowania

Regularne uaktualnienia chmury prywatnej i oprogramowania VMware zapewniają najnowsze zabezpieczenia, stabilność i zestawy funkcji w chmurach prywatnych. Więcej informacji o konserwacji i uaktualnieniach platformy znajduje się w artykule [dotyczącym pojęć dotyczących uaktualniania](concepts-upgrades.md) .

## <a name="monitoring-your-private-cloud"></a>Monitorowanie chmury prywatnej

Korzystając z [dzienników w Azure monitor](../azure-monitor/overview.md) , można zbierać dzienniki na maszynach wirtualnych działających w chmurze prywatnej automatycznej synchronizacji. [Agenta MMA można pobrać i zainstalować](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) na maszynach wirtualnych z systemem Linux i Windows działających w chmurach prywatnych z możliwością automatycznej synchronizacji, korzystając z tych samych zapytań, które są uruchamiane na lokalnych maszynach wirtualnych. Można uruchamiać te same zapytania, które normalnie są uruchamiane na maszynach wirtualnych. Aby dowiedzieć się więcej na temat tworzenia zapytań, zobacz [jak pisać zapytania](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries).

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z najważniejszymi [pojęciami dotyczącymi chmury prywatnej i klastra](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
