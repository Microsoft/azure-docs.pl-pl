---
title: Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure
description: Dowiedz się, jak zarządzać wszystkimi aspektami cyklu życia maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu narzędzi Microsoft Azure natywnych.
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 2cb9964b68769b1e784cebf62b4d336b355c68fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572206"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure

Narzędzia Microsoft Azure Native umożliwiają monitorowanie maszyn wirtualnych i zarządzanie nimi w środowisku platformy Azure. Ponadto umożliwiają również monitorowanie maszyn wirtualnych i zarządzanie nimi na platformie Azure VMware i lokalnych maszynach wirtualnych. W tym omówieniu Przyjrzyjmy się propozycjami zintegrowanej architektury monitorowania platformy Azure i sposobach używania jej natywnych narzędzi do zarządzania maszynami wirtualnymi rozwiązań VMware platformy Azure w całym cyklu ich życia.

## <a name="benefits"></a>Korzyści

- Usługi Azure Native Services mogą służyć do zarządzania maszynami wirtualnymi w środowisku hybrydowym (platforma Azure, rozwiązanie VMware platformy Azure i lokalne).
- Zintegrowane monitorowanie i widoczność platformy Azure oraz rozwiązań VMware platformy Azure i lokalnych maszyn wirtualnych.
- Za pomocą usługi Azure Update Management w programie Azure Automation można zarządzać aktualizacjami systemu operacyjnego dla maszyn z systemami Windows i Linux. 
- Azure Security Center zapewnia zaawansowaną ochronę przed zagrożeniami, w tym:
    - Monitorowanie integralności plików
    - Alerty zabezpieczeń bezplikowych
    - Ocena poprawek systemu operacyjnego
    - Ocena niezgodności konfiguracji zabezpieczeń
    - Ocena programu Endpoint Protection 
- Łatwo Wdrażaj agenta Log Analytics przy użyciu rozszerzenia maszyny wirtualnej z obsługą usługi Azure Arc w przypadku nowych i istniejących maszyn wirtualnych. 
- Obszar roboczy Log Analytics w Azure Monitor umożliwia zbieranie dzienników i zbieranie danych licznika wydajności przy użyciu agenta Log Analytics lub rozszerzeń. Zbieranie danych i dzienników w jednym punkcie i prezentowanie tych danych w różnych usługach natywnych platformy Azure. 
- Dodano zalety Azure Monitor obejmują: 
    - Bezproblemowe monitorowanie 
    - Lepsza widoczność infrastruktury 
    - Natychmiastowe powiadomienia 
    - Automatyczne rozwiązywanie 
    - Efektywność kosztów 

## <a name="integrated-azure-monitoring-architecture"></a>Zintegrowana architektura monitorowania platformy Azure

Na poniższym diagramie przedstawiono zintegrowaną architekturę monitorowania dla maszyn wirtualnych rozwiązań VMware platformy Azure.

![Zintegrowana architektura monitorowania platformy Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli dopiero zaczynasz na platformie Azure lub nie znasz żadnej z wymienionych powyżej usług, zapoznaj się z następującymi artykułami:

- [Omówienie uwierzytelniania konta usługi Automation](../automation/automation-security-overview.md)
- [Projektowanie wdrożenia dzienników Azure monitor](../azure-monitor/logs/design-logs-deployment.md) i [Azure monitor](../azure-monitor/overview.md)
- [Planowanie](../security-center/security-center-planning-and-operations-guide.md) i [obsługiwane platformy](../security-center/security-center-os-coverage.md) dla Azure Security Center
- [Włącz przegląd Azure Monitor dla maszyn wirtualnych](../azure-monitor/vm/vminsights-enable-overview.md)
- [Co to są serwery z włączonym usługą Azure Arc?](../azure-arc/servers/overview.md) a [co to jest usługa Azure Arc Kubernetes?](../azure-arc/kubernetes/overview.md)
- [Omówienie rozwiązania Update Management](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrowanie i wdrażanie usług natywnych platformy Azure

### <a name="enable-azure-update-management"></a>Włącz Update Management platformy Azure

Usługa Azure Update Management w Azure Automation zarządza aktualizacjami systemu operacyjnego dla maszyn z systemami Windows i Linux w środowisku hybrydowym. Monitoruje zgodność poprawek i przekazuje alerty odchylenia poprawek do Azure Monitor na potrzeby korygowania. Usługa Azure Update Management musi połączyć się z obszarem roboczym Log Analytics, aby użyć przechowywanych danych do oceny stanu aktualizacji na maszynach wirtualnych.

1.  Aby można było dodać Log Analytics do usługi Azure Update Management, należy najpierw [utworzyć konto Azure Automation](../automation/automation-create-standalone-account.md). Jeśli wolisz utworzyć konto przy użyciu szablonu, zobacz [Tworzenie konta usługi Automation przy użyciu szablonu Azure Resource Manager](../automation/quickstart-create-automation-account-template.md).

2. **Log Analytics obszar roboczy** umożliwia zbieranie dzienników i zbieranie danych licznika wydajności przy użyciu agenta log Analytics lub rozszerzeń. Aby utworzyć obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/logs/quick-create-workspace.md). Jeśli wolisz, możesz również utworzyć obszar roboczy za pomocą szablonu [interfejsu wiersza polecenia](../azure-monitor/logs/quick-create-workspace-cli.md), [programu PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md)lub [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md).

3. Aby włączyć Update Management platformy Azure dla maszyn wirtualnych, zobacz [włączanie Update Management na podstawie konta usługi Automation](../automation/update-management/enable-from-automation-account.md). W procesie zostanie połączony obszar roboczy Log Analytics z kontem usługi Automation. 
 
4. Po dodaniu maszyn wirtualnych do usługi Azure Update Management można [wdrażać aktualizacje na maszynach wirtualnych i przeglądać wyniki](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Włącz Azure Security Center

Azure Security Center zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze i lokalnie. Ocenia ona lukę w zabezpieczeniach maszyn wirtualnych rozwiązań platformy Azure VMware i zgłasza alerty zgodnie z potrzebami. Te alerty zabezpieczeń można przesłać dalej do Azure Monitor w celu rozwiązania problemu.

Azure Security Center nie wymaga wdrożenia. Aby uzyskać więcej informacji, zobacz listę [obsługiwanych funkcji dla maszyn wirtualnych](../security-center/security-center-services.md).

1. Aby dodać maszyny wirtualne rozwiązań VMware platformy Azure i maszyny wirtualne spoza platformy Azure do Security Center, zobacz [Szybki Start: konfigurowanie Azure Security Center](../security-center/security-center-get-started.md). 

2. Po dodaniu maszyn wirtualnych lub maszyn wirtualnych rozwiązań VMware platformy Azure z poziomu środowiska niepochodzącego od platformy Azure należy włączyć usługę Azure Defender w Security Center. Security Center będzie oceniać maszyny wirtualne pod kątem potencjalnych problemów z zabezpieczeniami. Zawiera również zalecenia na karcie Przegląd. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące zabezpieczeń w Azure Security Center](../security-center/security-center-recommendations.md).

3. Zasady zabezpieczeń można definiować w Azure Security Center. Aby uzyskać informacje na temat konfigurowania zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Dołączanie maszyn wirtualnych do serwerów z obsługą usługi Azure Arc

Usługa Azure Arc rozszerza zarządzanie platformą Azure na dowolną infrastrukturę, w tym rozwiązanie VMware platformy Azure, lokalne lub inne platformy w chmurze.

- Aby uzyskać informacje na temat włączania serwerów z obsługą usługi Azure ARC dla wielu maszyn wirtualnych z systemem Windows lub Linux, zobacz [łączenie maszyn hybrydowych z platformą Azure w dużej](../azure-arc/servers/onboard-service-principal.md)

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Dołączanie hybrydowych klastrów Kubernetes z włączonym łukiem Kubernetes

Możesz dołączyć klaster Kubernetes hostowany w środowisku rozwiązania Azure VMware przy użyciu usługi Azure Arc Kubernetes. 

- Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi dołączania z włączoną funkcją Azure Arc](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>Wdrażanie agenta Log Analytics

Maszyny wirtualne rozwiązań VMware platformy Azure mogą być monitorowane za pośrednictwem agenta Log Analytics (nazywanego także Microsoft Monitoring Agent (MMA) lub pakietu OMS Linux. Utworzono już Log Analytics obszar roboczy podczas włączania Azure Automation Update Management.

- Wdróż agenta Log Analytics przy użyciu [obsługi rozszerzenia maszyny wirtualnej z obsługą usługi Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Włącz Azure Monitor

Azure Monitor to kompleksowe rozwiązanie do zbierania i analizowania danych telemetrycznych w środowiskach chmurowych i lokalnych. Nie wymaga wdrożenia. Za pomocą Azure Monitor można monitorować wydajność systemu operacyjnego gościa oraz odnajdywać i mapować zależności aplikacji dla rozwiązań VMware platformy Azure lub lokalnych maszyn wirtualnych.

- Azure Monitor umożliwia zbieranie danych z różnych źródeł do monitorowania i analizowania. Aby uzyskać więcej informacji, zobacz [źródła danych monitorowania dla Azure monitor](../azure-monitor/agents/data-sources.md).

- Zbieraj różne typy danych do analizy, wizualizacji i tworzenia alertów. Aby uzyskać więcej informacji, zobacz [Azure monitor platformę danych](../azure-monitor/data-platform.md).

- Aby skonfigurować Azure Monitor przy użyciu obszaru roboczego Log Analytics, zobacz [Konfigurowanie obszaru roboczego log Analytics dla Azure monitor dla maszyn wirtualnych](../azure-monitor/vm/vminsights-configure-workspace.md).

- Można tworzyć reguły alertów w celu identyfikowania problemów w środowisku, takich jak wysokie wykorzystanie zasobów, brak poprawek, mała ilość miejsca na dysku i pulsy maszyn wirtualnych. Możesz również ustawić automatyczną odpowiedź na wykryte zdarzenia, wysyłając alert do narzędzi do zarządzania usługami IT (narzędzia ITSM). Powiadomienie o wykryciu alertu można również wysłać za pośrednictwem poczty e-mail. Aby utworzyć takie reguły, zobacz:
    - [Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure monitor](../azure-monitor/alerts/alerts-metric.md).
    - [Twórz i wyświetlaj alerty dzienników oraz zarządzaj nimi przy użyciu Azure monitor](../azure-monitor/alerts/alerts-log.md).
    - [Reguły akcji](../azure-monitor/alerts/alerts-action-rules.md) do ustawiania zautomatyzowanych akcji i powiadomień.
    - [Połącz platformę Azure z narzędziami narzędzia ITSM przy użyciu Łącznik zarządzania usługami IT](../azure-monitor/alerts/itsmc-overview.md).
