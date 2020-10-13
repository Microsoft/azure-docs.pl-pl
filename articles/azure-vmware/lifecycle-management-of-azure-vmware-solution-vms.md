---
title: Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure
description: Dowiedz się, jak zarządzać wszystkimi aspektami cyklu życia maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu narzędzi Microsoft Azure natywnych.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 452fa6bf4610c9d0dcb46587242e8d6df9cebd0d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950367"
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
- Łatwo Wdrażaj Microsoft Monitoring Agent (MMA) przy użyciu usługi Azure ARC dla nowych maszyn wirtualnych. 
- Obszar roboczy Log Analytics w Azure Monitor umożliwia zbieranie dzienników i zbieranie danych licznika wydajności przy użyciu MMA lub rozszerzeń. Zbieranie danych i dzienników w jednym punkcie i prezentowanie tych danych w różnych usługach natywnych platformy Azure. 
- Dodano zalety Azure Monitor obejmują: 
    - Bezproblemowe monitorowanie 
    - Lepsza widoczność infrastruktury 
    - Natychmiastowe powiadomienia 
    - Automatyczne rozwiązywanie 
    - Efektywność kosztów 

## <a name="integrated-azure-monitoring-architecture"></a>Zintegrowana architektura monitorowania platformy Azure

Na poniższym diagramie przedstawiono zintegrowaną architekturę monitorowania dla maszyn wirtualnych rozwiązań VMware platformy Azure.

![Zintegrowana architektura monitorowania platformy Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrowanie i wdrażanie usług natywnych platformy Azure

**Usługa Azure Arc** rozszerza zarządzanie platformą Azure na dowolną infrastrukturę, w tym rozwiązanie VMware platformy Azure, lokalne lub inne platformy w chmurze. Usługę Azure ARC można wdrożyć, instalując klaster usługi Azure Kubernetes Service (AKS) w środowisku rozwiązań VMware platformy Azure. Aby uzyskać więcej informacji, zobacz [łączenie klastra Kubernetes z obsługą usługi Azure Arc](../azure-arc/kubernetes/connect-cluster.md).

Maszyny wirtualne rozwiązań VMware platformy Azure można monitorować za pośrednictwem MMA (nazywanego również agentem Log Analytics lub agentem usługi OMS Linux). MMA można instalować automatycznie, gdy maszyny wirtualne są obsługiwane za pośrednictwem przepływów pracy cyklu życia maszyny wirtualnej ARC. MMA można także zainstalować podczas wdrażania maszyn wirtualnych na podstawie szablonu w programie vCenter; ponownie z maszynami wirtualnymi, które są obsługiwane za pośrednictwem przepływów pracy ARC. Wszystkie maszyny wirtualne rozwiązań VMware z obsługą administracyjną mogą następnie zainstalować MMA i wysłać dzienniki do obszaru roboczego usługi Azure Log Analytics. Aby uzyskać więcej informacji, zobacz [Omówienie agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics obszar roboczy** umożliwia zbieranie dzienników i zbieranie danych licznika wydajności przy użyciu MMA lub rozszerzeń. Aby utworzyć obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Aby dodać maszyny wirtualne z systemem Windows do obszaru roboczego usługi log Analytics, zobacz [Install log Analytics Agent na komputerach z systemem Windows](../azure-monitor/platform/agent-windows.md).
- Aby dodać maszyny wirtualne z systemem Linux do obszaru roboczego usługi log Analytics, zobacz [Install log Analytics Agent na komputerach z systemem Linux](../azure-monitor/platform/agent-linux.md).

**Usługa Azure Update Management** w Azure Automation zarządza aktualizacjami systemu operacyjnego dla maszyn z systemami Windows i Linux w środowisku hybrydowym. Monitoruje zgodność poprawek i przekazuje alerty odchylenia poprawek do Azure Monitor na potrzeby korygowania. Usługa Azure Update Management musi połączyć się z obszarem roboczym Log Analytics, aby użyć przechowywanych danych do oceny stanu aktualizacji na maszynach wirtualnych.
- Aby dodać Log Analytics do usługi Azure Update Management, musisz najpierw [utworzyć konto Azure Automation](../automation/automation-create-standalone-account.md).
- Aby połączyć obszar roboczy Log Analytics z kontem usługi Automation, zobacz [log Analytics obszaru roboczego i konta usługi Automation](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Aby włączyć Update Management platformy Azure dla maszyn wirtualnych, zobacz [włączanie Update Management na podstawie konta usługi Automation](../automation/update-management/update-mgmt-enable-automation-account.md).
- Po dodaniu maszyn wirtualnych do usługi Azure Update Management można [wdrażać aktualizacje na maszynach wirtualnych i przeglądać wyniki](../automation/update-management/update-mgmt-deploy-updates.md). 

**Azure Security Center** zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze i lokalnie. Ocenia ona lukę w zabezpieczeniach maszyn wirtualnych rozwiązań platformy Azure VMware i zgłasza alerty zgodnie z potrzebami. Te alerty zabezpieczeń można przesłać dalej do Azure Monitor w celu rozwiązania problemu.
- Azure Security Center nie wymaga wdrożenia. Aby uzyskać więcej informacji, zobacz listę [obsługiwanych funkcji dla maszyn wirtualnych](../security-center/security-center-services.md).
- Aby dodać maszyny wirtualne rozwiązań VMware platformy Azure i maszyny wirtualne spoza platformy Azure do Azure Security Center, zobacz Dołączanie [komputerów z systemem Windows do Azure Security Center](../security-center/quickstart-onboard-machines.md) i dołączania [komputerów z systemem Linux do Azure Security Center](../security-center/quickstart-onboard-machines.md).
- Po dodaniu maszyn wirtualnych Azure Security Center analizuje stan zabezpieczeń zasobów, aby identyfikować potencjalne luki w zabezpieczeniach. Zawiera również zalecenia na karcie Przegląd. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące zabezpieczeń w Azure Security Center](../security-center/security-center-recommendations.md).
- Zasady zabezpieczeń można definiować w Azure Security Center. Aby uzyskać informacje na temat konfigurowania zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](../security-center/tutorial-security-policy.md).

**Azure monitor** to kompleksowe rozwiązanie do zbierania i analizowania danych telemetrycznych w środowiskach chmurowych i lokalnych. Nie wymaga wdrożenia.
- Azure Monitor umożliwia zbieranie danych z różnych źródeł do monitorowania i analizowania. Aby uzyskać więcej informacji, zobacz [źródła danych monitorowania dla Azure monitor](../azure-monitor/platform/data-sources.md).
- Możesz również zbierać różne typy danych do analizy, wizualizacji i tworzenia alertów. Aby uzyskać więcej informacji, zobacz [Azure monitor platformę danych](../azure-monitor/platform/data-platform.md).
- Aby skonfigurować Azure Monitor przy użyciu obszaru roboczego Log Analytics, zobacz [Konfigurowanie obszaru roboczego log Analytics dla Azure monitor dla maszyn wirtualnych](../azure-monitor/insights/vminsights-configure-workspace.md).
- Można tworzyć reguły alertów w celu identyfikowania problemów w środowisku, takich jak wysokie wykorzystanie zasobów, brak poprawek, mała ilość miejsca na dysku i pulsy maszyn wirtualnych. Możesz również ustawić automatyczną odpowiedź na wykryte zdarzenia, wysyłając alert do narzędzi do zarządzania usługami IT (narzędzia ITSM). Powiadomienie o wykryciu alertu można również wysłać za pośrednictwem poczty e-mail. Aby utworzyć takie reguły, zobacz:
    - [Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Twórz i wyświetlaj alerty dzienników oraz zarządzaj nimi przy użyciu Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Reguły akcji](../azure-monitor/platform/alerts-action-rules.md) do ustawiania zautomatyzowanych akcji i powiadomień.
    - [Połącz platformę Azure z narzędziami narzędzia ITSM przy użyciu Łącznik zarządzania usługami IT](../azure-monitor/platform/itsmc-overview.md).

**Platforma Azure jako usługa (PaaS)** to środowisko programistyczne i wdrożeniowe w chmurze, dzięki czemu można dostarczać aplikacje oparte na chmurze. Na przykład możesz zintegrować Azure SQL Database z maszynami wirtualnymi rozwiązań VMware platformy Azure. Alerty SQL mogą następnie być skorelowane z alertami maszyn wirtualnych rozwiązania Azure VMware. Załóżmy na przykład, że SQL Database rozgałęzienia aplikacji jest w ramach usługi Azure PAAS, a warstwa aplikacji sieci Web tej samej aplikacji jest hostowana na maszynach wirtualnych rozwiązania Azure VMware. Alerty bazy danych można następnie skorelować z alertami aplikacji sieci Web. Rozwiązywanie problemów jest uproszczone dzięki pojedynczej zintegrowanej widoczności platformy Azure, rozwiązań VMware platformy Azure i lokalnych maszyn wirtualnych.