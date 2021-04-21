---
title: Wprowadzenie
description: Poznaj funkcje i korzyści platformy Azure VMware Solution wdrażania obciążeń opartych na programie VMware i zarządzania nimi na platformie Azure. Azure VMware Solution SLA gwarantuje, że narzędzia do zarządzania programu Azure VMware (vCenter Server i NSX Manager) będą dostępne przez co najmniej 99,9% czasu.
ms.topic: overview
ms.date: 04/20/2021
ms.openlocfilehash: 9471aa2e427d28e0b8211dc8b25b2e61a4bfa8c4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752646"
---
# <a name="what-is-azure-vmware-solution"></a>Co to jest usługa Azure VMware Solution?

Azure VMware Solution udostępnia chmury prywatne, które zawierają klastry vSphere, zbudowane na dedykowanej infrastrukturze platformy Azure bez systemu od zerowego. Minimalne początkowe wdrożenie to trzy hosty, ale można dodawać po jednym na raz, maksymalnie 16 hostów na klaster.  Wszystkie aprowizowane chmury prywatne mają vCenter Server, vSAN, vSphere i NSX-T. Możesz migrować obciążenia ze środowisk lokalnych, wdrażać nowe maszyny wirtualne i korzystać z usług platformy Azure z chmur prywatnych.  Narzędzia do zarządzania programu Azure VMware (vCenter Server i NSX Manager) będą dostępne przez co najmniej 99,9% czasu. Aby uzyskać więcej informacji, zobacz [Azure VMware Solution SLA](https://aka.ms/avs/sla).

Azure VMware Solution to zweryfikowane rozwiązanie VMware z trwaną weryfikacją i testowaniem ulepszeń i uaktualnień. Firma Microsoft zarządza infrastrukturą i oprogramowaniem chmury prywatnej i utrzymuje je. Umożliwia skoncentrowanie się na tworzeniu i uruchamianiu obciążeń w chmurach prywatnych. 

Diagram przedstawia sąsiedztwo między chmurami prywatnymi i sieciami wirtualnmi na platformie Azure, usługami platformy Azure i środowiskami lokalnymi. Dostęp sieciowy z chmur prywatnych do usług platformy Azure lub sieci wirtualnych zapewnia integrację punktów końcowych usługi platformy Azure w ramach umowy SLA. Usługa ExpressRoute Global Reach połączenie środowiska lokalnego z Azure VMware Solution chmurą prywatną. 

![Obraz Azure VMware Solution sąsiedztwa chmury prywatnej z platformą Azure i środowiskiem lokalnym](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

Azure VMware Solution chmury prywatne i klastry są zbudowane z hosta infrastruktury platformy Azure bez systemu od zerowego, hiperkonwergentnego. Hosty wysokiej klasy mają 576 GB pamięci RAM i dwa procesory Intel 18-rdzeniowe 2,3 GHz. Hosty HE mają dwie grupy dysków vSAN z 15,36 TB (SSD) pierwotnej warstwy pojemności vSAN i warstwą pamięci podręcznej vSAN o pojemności 3,2 TB (NVMe).

Nowe chmury prywatne są wdrażane za pośrednictwem interfejsu Azure Portal wiersza polecenia platformy Azure.

## <a name="networking"></a>Sieć

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Aby uzyskać więcej informacji, zobacz [Pojęcia dotyczące sieci.](concepts-networking.md)

## <a name="access-and-security"></a>Dostęp i zabezpieczenia

Azure VMware Solution chmury prywatne używają kontroli dostępu opartej na rolach vSphere w celu zwiększenia bezpieczeństwa. Możliwości protokołu LDAP vSphere SSO można zintegrować z Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Access and Identity concepts (Pojęcia dotyczące dostępu i tożsamości).](concepts-identity.md)  

Szyfrowanie danych magazynowych vSAN jest domyślnie włączone i służy do zapewnienia bezpieczeństwa magazynu danych sieci vSAN. Aby uzyskać więcej informacji, zobacz [Pojęcia dotyczące magazynu](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Konserwacja cyklu życia hosta i oprogramowania

Regularne uaktualnienia usługi Azure VMware Solution chmury prywatnej i oprogramowania VMware zapewniają najnowsze zabezpieczenia, stabilność i zestawy funkcji działające w chmurach prywatnych. Aby uzyskać więcej informacji, zobacz [Aktualizacje i uaktualnienia chmury prywatnej.](concepts-upgrades.md)

## <a name="monitoring-your-private-cloud"></a>Monitorowanie chmury prywatnej

Po Azure VMware Solution w ramach subskrypcji dzienniki [Azure Monitor](../azure-monitor/overview.md) generowane automatycznie. 

W chmurze prywatnej można:
- Zbieraj dzienniki na każdej z maszyn wirtualnych.
- [Pobierz i zainstaluj agenta MMA na](../azure-monitor/agents/log-analytics-agent.md#installation-options) maszyny wirtualne z systemami Linux i Windows.
- Włącz rozszerzenie [Diagnostyka Azure](../azure-monitor/agents/diagnostics-extension-overview.md).
- [Utwórz i uruchom nowe zapytania.](../azure-monitor/logs/data-platform-logs.md#log-queries)
- Uruchom te same zapytania, które zwykle są uruchamiane na twoich maszyn wirtualnych.

Wzorce monitorowania wewnątrz Azure VMware Solution są podobne do maszyn wirtualnych platformy Azure na platformie IaaS. Aby uzyskać więcej informacji i how-tos, zobacz [Monitoring Azure VMs with Azure Monitor](../azure-monitor/vm/monitor-vm-azure.md)(Monitorowanie maszyn wirtualnych platformy Azure za pomocą Azure Monitor).

## <a name="customer-communication"></a>Komunikacja z klientami
[!INCLUDE [customer-communications](includes/customer-communications.md)]

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest poznanie kluczowych [pojęć dotyczących chmury prywatnej i klastra.](concepts-private-clouds-clusters.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md


