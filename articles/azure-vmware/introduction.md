---
title: Wprowadzenie
description: Poznaj funkcje i korzyści płynące z rozwiązań VMware platformy Azure w celu wdrażania obciążeń opartych na oprogramowaniu VMware na platformie Azure i zarządzania nimi.
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: a75c04cf6b51068ef9867e3f9438b2820da703fe
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048820"
---
# <a name="what-is-azure-vmware-solution"></a>Co to jest usługa Azure VMware Solution?

Rozwiązanie VMware dla platformy Azure udostępnia chmurom prywatnym zawierającym klastry vSphere, które są zbudowane z dedykowanej infrastruktury platformy Azure bez systemu operacyjnego. Minimalne początkowe wdrożenie to trzy hosty, ale dodatkowe hosty mogą być dodawane pojedynczo do maksymalnie 16 hostów na klaster.  Wszystkie obsługiwane chmury prywatne mają vCenter Server, sieci vSAN, vSphere i NSX-T. Możesz migrować obciążenia ze środowisk lokalnych, wdrożyć nowe maszyny wirtualne i korzystać z usług platformy Azure z chmur prywatnych.

Rozwiązanie VMware platformy Azure to zweryfikowane rozwiązanie VMware, które umożliwia weryfikowanie i testowanie ulepszeń i uaktualnień. Firma Microsoft zarządza infrastrukturą i oprogramowaniem chmury prywatnej. Umożliwia skoncentrowanie się na tworzeniu i uruchamianiu obciążeń w chmurach prywatnych. 

Diagram przedstawia sąsiedztwo między chmurami prywatnymi i sieci wirtualnych na platformie Azure, usługach platformy Azure i w środowiskach lokalnych. Dostęp sieciowy z chmur prywatnych do usług platformy Azure lub sieci wirtualnych zapewnia integrację punktów końcowych usługi platformy Azure opartej na umowie SLA. ExpressRoute Global Reach nawiązuje połączenie środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware. 

![Obraz przedstawiający chmurę prywatną rozwiązania Azure VMware na platformie Azure i lokalnie](./media/adjacency-overview-drawing-final.png)

## <a name="customer-communication"></a>Komunikacja z klientem
Problemy z usługą, planowana konserwacja, klasyfikatory kondycji, powiadomienia doradców zabezpieczeń są publikowane za pomocą **Service Health** w Azure Portal. Podczas konfigurowania alertów dziennika aktywności dla tych powiadomień można podejmować odpowiednie działania. Aby uzyskać więcej informacji, zobacz [tworzenie alertów dotyczących kondycji usługi przy użyciu Azure Portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Zrzut ekranu przedstawiający powiadomienia Service Health":::

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

Chmury prywatne i klastry dla rozwiązań VMware platformy Azure są zbudowane z hosta infrastruktury platformy Azure bez systemu operacyjnego. Hosty wysokiej klasy mają 576 GB pamięci RAM i dwa procesory Intel 18 Core, 2,3 GHz. Hosty mają dwie sieci vSAN diskgroups z 15,36 TB (SSD) warstwy pierwotnej sieci vSAN pojemności i warstwy pamięci podręcznej 3,2 TB (interfejsu NVMe) sieci vSAN.

Nowe chmury prywatne są wdrażane za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="networking"></a>Sieć

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące sieci](concepts-networking.md).

## <a name="access-and-security"></a>Dostęp i zabezpieczenia

Chmury prywatne rozwiązań VMware platformy Azure używają kontroli dostępu opartej na rolach vSphere na potrzeby zwiększonych zabezpieczeń. Możliwości usługi vSphere SSO LDAP można zintegrować z Azure Active Directory. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).  

Sieci vSAN szyfrowanie danych w czasie spoczynku jest domyślnie włączone i służy do zapewniania bezpieczeństwa sieci vSAN datastore. Aby uzyskać więcej informacji, zobacz [pojęcia związane z magazynem](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Obsługa cyklu życia hosta i oprogramowania

Regularne uaktualnienia chmury prywatnej i oprogramowania VMware na platformie Azure zapewniają najnowsze zabezpieczenia, stabilność i zestawy funkcji działające w chmurach prywatnych. Aby uzyskać więcej informacji, zobacz [aktualizacje i uaktualnienia w chmurze prywatnej](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Monitorowanie chmury prywatnej

Po wdrożeniu rozwiązania Azure VMware w ramach subskrypcji [Azure monitor dzienniki](../azure-monitor/overview.md) są generowane automatycznie. 

W chmurze prywatnej można:
- Zbierz dzienniki na każdej z maszyn wirtualnych.
- [Pobierz i Zainstaluj agenta MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options) na maszynach wirtualnych z systemem Linux i Windows.
- Włącz [rozszerzenie diagnostyki Azure](../azure-monitor/agents/diagnostics-extension-overview.md).
- [Twórz i uruchamiaj nowe zapytania](../azure-monitor/logs/data-platform-logs.md#log-queries).
- Uruchamiaj te same zapytania, które zwykle są uruchamiane na maszynach wirtualnych.

Wzorce monitorowania w ramach rozwiązania VMware platformy Azure są podobne do maszyn wirtualnych platformy Azure w ramach platformy IaaS. Aby uzyskać więcej informacji i porady, zobacz [monitorowanie maszyn wirtualnych platformy Azure za pomocą Azure monitor](../azure-monitor/vm/monitor-vm-azure.md).

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z najważniejszymi [pojęciami dotyczącymi chmury prywatnej i klastra](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
