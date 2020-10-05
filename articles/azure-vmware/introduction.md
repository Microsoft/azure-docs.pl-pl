---
title: Wprowadzenie
description: Poznaj funkcje i korzyści płynące z rozwiązań VMware platformy Azure w celu wdrażania obciążeń opartych na oprogramowaniu VMware na platformie Azure i zarządzania nimi.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: b71427402e08167e5868128ed1e553c69bef425d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574465"
---
# <a name="what-is-azure-vmware-solution"></a>Co to jest usługa Azure VMware Solution?

Rozwiązanie VMware firmy Azure udostępnia chmurę prywatną na platformie Azure. Chmury prywatne zawierają klastry vSphere, zbudowane z dedykowanej infrastruktury platformy Azure bez systemu operacyjnego. Klastry chmury prywatnej można skalować z 3 do 16 hostów, z możliwością posiadania wielu klastrów w jednej chmurze prywatnej. Obsługiwane są wszystkie chmury prywatne z vCenter Server, sieci vSAN, vSphere i NSX-T. Można migrować obciążenia ze środowisk lokalnych, tworzyć i wdrażać nowe maszyny wirtualne oraz korzystać z usług platformy Azure z chmur prywatnych.

Rozwiązanie VMware platformy Azure to zweryfikowane rozwiązanie VMware, które umożliwia weryfikowanie i testowanie ulepszeń i uaktualnień. Infrastruktura i oprogramowanie chmury prywatnej są zarządzane i utrzymywane przez firmę Microsoft, co pozwala skupić się na tworzeniu i uruchamianiu obciążeń w chmurach prywatnych.

Na poniższym diagramie przedstawiono sąsiedztwo między chmurami prywatnymi i sieci wirtualnych na platformie Azure, usługach platformy Azure i w środowiskach lokalnych. Dostęp sieciowy z chmur prywatnych do usług platformy Azure lub sieci wirtualnych zapewnia integrację punktów końcowych usługi platformy Azure opartej na umowie SLA. Dostęp do chmury prywatnej ze środowiska lokalnego używa Global Reach ExpressRoute do prywatnego i bezpiecznego połączenia.

![Obraz przedstawiający chmurę prywatną rozwiązania Azure VMware na platformie Azure i lokalnie](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

Chmury prywatne i klastry dla rozwiązań VMware platformy Azure są zbudowane z hosta infrastruktury platformy Azure bez systemu operacyjnego. Hosty wysokiej klasy mają 576 GB pamięci RAM i dwa procesory Intel 18 Core, 2,3 GHz. Hosty mają dwie sieci vSAN diskgroups z łączną warstwą pojemności 15,36 TB (SSD), a w warstwie pamięci podręcznej 3,2 TB (interfejsu NVMe) sieci vSAN.

Nowe chmury prywatne są wdrażane za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="networking"></a>Networking

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Aby uzyskać więcej informacji na temat sieci i międzyłączności, zobacz artykuł dotyczący [pojęć dotyczących sieci](concepts-networking.md) .

## <a name="access-and-security"></a>Dostęp i zabezpieczenia

W celu zwiększenia bezpieczeństwa chmury prywatne rozwiązań VMware platformy Azure używają kontroli dostępu opartej na rolach vSphere. funkcje vSphere SSO LDAP można zintegrować z Azure Active Directory. Aby uzyskać więcej informacji o tożsamości i uprawnieniach, zobacz artykuł dotyczący [pojęć dotyczących dostępu i tożsamości](concepts-identity.md) .

Sieci vSAN dane — szyfrowanie w spoczynku jest domyślnie włączone i służy do zapewniania bezpieczeństwa sieci vSAN magazynu danych. Jest on opisany bardziej szczegółowo w artykule [pojęcia dotyczące magazynu](concepts-storage.md) .

## <a name="host-and-software-lifecycle-maintenance"></a>Obsługa cyklu życia hosta i oprogramowania

Regularne uaktualnianie chmury prywatnej i oprogramowania VMware na platformie Azure zapewnia najnowsze zabezpieczenia, stabilność i zestawy funkcji działające w chmurach prywatnych. Więcej informacji o konserwacji i uaktualnieniach platformy znajduje się w artykule [dotyczącym pojęć dotyczących uaktualniania](concepts-upgrades.md) .

## <a name="monitoring-your-private-cloud"></a>Monitorowanie chmury prywatnej

Po wdrożeniu rozwiązania Azure VMware w ramach subskrypcji [Azure monitor dzienniki](../azure-monitor/overview.md) są generowane automatycznie. Ponadto można zbierać dzienniki na poszczególnych maszynach wirtualnych w chmurze prywatnej. [Agenta MMA można pobrać i zainstalować](../azure-monitor/platform/log-analytics-agent.md#installation-options) na maszynach wirtualnych z systemem Linux i Windows uruchomionym w chmurach prywatnych rozwiązań VMware platformy Azure, a także włączyć [rozszerzenie Diagnostyka Azure](../azure-monitor/platform/diagnostics-extension-overview.md). Można nawet uruchamiać te same zapytania, które są normalnie uruchamiane na maszynach wirtualnych. Aby dowiedzieć się więcej na temat tworzenia zapytań, zobacz [jak pisać zapytania](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries). Wzorce monitorowania w ramach rozwiązania VMware platformy Azure są podobne do usługi Azure Virtual Machines w ramach platformy IaaS. Aby uzyskać dodatkowe informacje i porady, zobacz [monitorowanie maszyn wirtualnych platformy Azure przy użyciu Azure monitor](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z najważniejszymi [pojęciami dotyczącymi chmury prywatnej i klastra](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
