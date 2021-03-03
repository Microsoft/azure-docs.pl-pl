---
title: Omówienie serwerów z obsługą usługi Azure Arc
description: Dowiedz się, jak używać serwerów z obsługą usługi Azure Arc do zarządzania serwerami hostowanymi poza platformą Azure, takimi jak zasób platformy Azure.
keywords: Azure Automation, DSC, PowerShell, Konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, Python, graficzne, hybrydowe
ms.date: 02/18/2021
ms.topic: overview
ms.openlocfilehash: 5692dfaceb15086b04ee951b8ecdf88f73c7d122
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686227"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Co to są serwery z obsługą usługi Azure Arc?

Serwery z obsługą usługi Azure ARC umożliwiają zarządzanie serwerami fizycznymi z systemami Windows i Linux oraz maszynami wirtualnymi hostowanymi *poza* platformą Azure, w sieci firmowej lub innym dostawcy chmury. To środowisko zarządzania zostało zaprojektowane z założeniami, aby zapewnić spójność zarządzania natywnymi maszynami wirtualnymi platformy Azure. Gdy maszyna hybrydowa jest połączona z platformą Azure, zostanie ona podłączona i jest traktowana jako zasób na platformie Azure. Każda podłączona maszyna ma identyfikator zasobu, jest uwzględniony w grupie zasobów i korzysta ze standardowych konstrukcji platformy Azure, takich jak Azure Policy i stosowanie tagów. Dostawcy usług zarządzający infrastrukturą lokalną klienta mogą zarządzać swoimi maszynami hybrydowymi, podobnie jak w przypadku natywnych zasobów platformy Azure, w wielu środowiskach klientów przy użyciu [usługi Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) z usługą Azure Arc.

Aby zapewnić to środowisko dla maszyn hybrydowych hostowanych poza platformą Azure, należy zainstalować agenta maszyny połączonej z platformą Azure na każdym komputerze, na którym ma zostać nawiązane połączenie z platformą Azure. Ten agent nie dostarcza żadnych innych funkcji i nie zastępuje [agenta log Analytics](../../azure-monitor/agents/log-analytics-agent.md)platformy Azure. Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy użytkownik chce aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim za pomocą elementów Runbook usługi Automation lub rozwiązań, takich jak Update Management, lub używać innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Po połączeniu maszyny z serwerami z obsługą usługi Azure Arc umożliwia ona wykonywanie następujących zadań związanych z zarządzaniem konfiguracją i monitorowaniem:

- Przypisanie [Azure Policy konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md) przy użyciu tego samego środowiska co przypisanie zasad dla maszyn wirtualnych platformy Azure. Obecnie większość zasad konfiguracji gościa nie stosuje konfiguracji, ale tylko ustawienia inspekcji w obrębie maszyny. Aby zrozumieć koszt używania Azure Policy zasad konfiguracji Gościa z serwerami z włączoną funkcją ARC, zobacz Azure Policy [Przewodnik po cenach](https://azure.microsoft.com/pricing/details/azure-policy/).

- Raport dotyczący zmian w konfiguracji dotyczących zainstalowanego oprogramowania, usług firmy Microsoft, rejestru systemu Windows i demonów Linux na monitorowanych serwerach przy użyciu Azure Automation [Change Tracking i spisu](../../automation/change-tracking/overview.md).

- Monitoruj wydajność systemu operacyjnego gościa połączonej maszyny i odnajduj składniki aplikacji, aby monitorować ich procesy i zależności z innymi zasobami, z którymi aplikacja komunikuje się za pomocą [Azure monitor dla maszyn wirtualnych](../../azure-monitor/vm/vminsights-overview.md).

- Uprość wdrażanie za pomocą innych usług platformy Azure, takich jak [Konfiguracja stanu](../../automation/automation-dsc-overview.md) Azure Automation i Azure monitor obszar roboczy log Analytics, korzystając z obsługiwanych [rozszerzeń maszyny wirtualnej platformy Azure](manage-vm-extensions.md) dla maszyny z systemem Windows lub Linux. Obejmuje to przeprowadzanie konfiguracji po wdrożeniu lub instalację oprogramowania przy użyciu niestandardowego rozszerzenia skryptu.

- Użyj [Update Management](../../automation/update-management/overview.md) w Azure Automation do zarządzania aktualizacjami systemu operacyjnego dla serwerów z systemami Windows i Linux

    > [!NOTE]
    > W tej chwili Włączanie Update Management bezpośrednio z serwera z włączonymi Łukiemmi nie jest obsługiwane. Zapoznaj się z tematem [włączanie Update Management na koncie usługi Automation](../../automation/update-management/enable-from-automation-account.md) , aby poznać wymagania i jak włączyć serwer.

- Uwzględnij serwery spoza platformy Azure, aby wykrywać zagrożenia i aktywnie monitorować potencjalne zagrożenia bezpieczeństwa przy użyciu [Azure Security Center](../../security-center/security-center-introduction.md).

Dane dziennika zbierane i przechowywane w obszarze roboczym Log Analytics z maszyny hybrydowej zawierają teraz właściwości specyficzne dla maszyny, takie jak identyfikator zasobu. Może służyć do obsługi dostępu do dziennika [kontekstu zasobów](../../azure-monitor/logs/design-logs-deployment.md#access-mode) .

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Obsługiwane regiony

Aby uzyskać ostateczną listę obsługiwanych regionów z serwerami z obsługą usługi Azure ARC, zobacz stronę [produkty na platformie Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) .

W większości przypadków lokalizacja wybrana podczas tworzenia skryptu instalacji powinna być regionem platformy Azure geograficznie najbliżej lokalizacji maszyny. Dane przechowywane w obszarze geograficznym platformy Azure zawierają określony region, co może również mieć wpływ na wybór regionu, jeśli istnieją wymagania dotyczące danych znajdujących się na miejscu. Jeśli w regionie świadczenia usługi Azure, z którym nawiąże się połączenie, ma to wpływ na awarię, nie ma to wpływ na przyłączoną maszynę, ale nie można ukończyć operacji zarządzania przy użyciu platformy Azure. Jeśli wystąpi awaria regionalna, a w przypadku wielu lokalizacji, które obsługują geograficznie nadmiarową usługę, najlepszym rozwiązaniem jest połączenie maszyn w każdej lokalizacji z innym regionem platformy Azure.

Następujące metadane dotyczące połączonej maszyny są zbierane i przechowywane w regionie, w którym jest skonfigurowany zasób maszyny usługi Azure ARC:

- Nazwa i wersja systemu operacyjnego
- Nazwa komputera
- W pełni kwalifikowana nazwa domeny komputera (FQDN)
- Wersja agenta połączonej maszyny

Na przykład jeśli maszyna jest zarejestrowana w usłudze Azure Arc w regionie Wschodnie stany USA, te dane są przechowywane w regionie US.

### <a name="supported-environments"></a>Obsługiwane środowiska

Serwery z obsługą Arc obsługują zarządzanie serwerami fizycznymi i maszynami wirtualnymi hostowanymi *poza* platformą Azure. Aby uzyskać szczegółowe informacje o tym, które środowiska chmury hybrydowej obsługują maszyny wirtualne, zobacz [wymagania wstępne dotyczące programu Connect Machine Agent](agent-overview.md#supported-environments).

> [!NOTE]
> Serwery z włączonymi łukiemmi nie są zaprojektowane ani obsługiwane, aby można było zarządzać maszynami wirtualnymi działającymi na platformie Azure.

### <a name="agent-status"></a>Stan agenta

Agent połączonej maszyny wysyła do usługi zwykły komunikat pulsu co 5 minut. Jeśli usługa przestanie odbierać te komunikaty pulsu z komputera, komputer jest traktowany jako w trybie offline, a stan zostanie automatycznie zmieniony na **rozłączony** w portalu w ciągu od 15 do 30 minut. Po odebraniu kolejnego komunikatu pulsu z agenta połączonej maszyny jego stan zostanie automatycznie zmieniony na **połączone**.

## <a name="next-steps"></a>Następne kroki

Przed przeszacowaniem lub włączeniem serwerów z obsługą łuku na wielu maszynach hybrydowych Przejrzyj [Omówienie agenta połączonej maszyny](agent-overview.md) , aby poznać wymagania, szczegóły techniczne dotyczące agenta i metod wdrażania.