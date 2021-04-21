---
title: Azure Arc z włączonymi serwerami — omówienie
description: Dowiedz się, jak używać serwerów Azure Arc do zarządzania serwerami hostowanych poza platformą Azure, takich jak zasób platformy Azure.
keywords: azure automation, DSC, powershell, konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, python, graficzne, hybrydowe
ms.date: 04/21/2021
ms.topic: overview
ms.openlocfilehash: 324f6cc29bd9e4eca1a20413032c213c2618a11e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832000"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Co to są serwery z obsługą usługi Azure Arc?

Azure Arc umożliwiają zarządzanie serwerami fizycznymi i maszynami wirtualnymi  z systemami Windows i Linux hostowanych poza platformą Azure, w sieci firmowej lub u innego dostawcy usług w chmurze. To środowisko zarządzania zostało zaprojektowane tak, aby zapewnić spójność z zarządzaniem natywnymi maszynami wirtualnymi platformy Azure. Gdy maszyna hybrydowa jest połączona z platformą Azure, staje się maszyną połączeniową i jest traktowana jako zasób na platformie Azure. Każda połączona maszyna ma identyfikator zasobu, jest uwzględniona w grupie zasobów i korzysta ze standardowych konstrukcji platformy Azure, takich jak Azure Policy i stosowanie tagów. Dostawcy usług, którzy zarządzają lokalną infrastrukturą klienta, mogą zarządzać swoimi maszynami hybrydowymi, [](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) podobnie jak obecnie, za pomocą natywnych zasobów platformy Azure w wielu środowiskach klientów, używając Azure Lighthouse z Azure Arc.

Aby zapewnić to środowisko na maszynach hybrydowych hostowanych poza platformą Azure, agent usługi Azure Connected Machine musi być zainstalowany na każdej maszynie, która ma zostać nakierowana na połączenie z platformą Azure. Ten agent nie dostarcza żadnych innych funkcji i nie zastępuje agenta usługi Azure [Log Analytics.](../../azure-monitor/agents/log-analytics-agent.md) Agent usługi Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz aktywnie monitorować system operacyjny i obciążenia uruchomione na maszynie, zarządzać nim przy użyciu runbook usługi Automation lub rozwiązań, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich [jak Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Połączenie maszyny z Azure Arc serwerami umożliwia wykonywanie następujących zadań monitorowania i zarządzania konfiguracją:

- Przypisz [Azure Policy gościa przy](../../governance/policy/concepts/guest-configuration.md) użyciu tego samego środowiska co przypisanie zasad dla maszyn wirtualnych platformy Azure. Obecnie większość zasad konfiguracji gościa nie stosuje konfiguracji, tylko przejmuje ustawienia wewnątrz maszyny. Aby zrozumieć koszty korzystania z zasad Azure Policy konfiguracji gościa z serwerami z obsługą usługi Arc, zobacz Azure Policy [cennika](https://azure.microsoft.com/pricing/details/azure-policy/).

- Raport o zmianach konfiguracji dotyczących zainstalowanego oprogramowania, usługi usługi firmy Microsoft, rejestru i plików systemu Windows oraz demonów systemu Linux na monitorowanych serwerach przy użyciu programów Azure Automation [Śledzenie zmian i spis](../../automation/change-tracking/overview.md) i [Azure Security Center File Integrity Monitoring](../../security-center/security-center-file-integrity-monitoring.md)dla serwerów z włączoną obsługą programu Azure Defender [dla](../../security-center/defender-for-servers-introduction.md)serwerów programu .

- Monitoruj wydajność systemu operacyjnego gościa połączonej maszyny i odnajduj składniki aplikacji w celu monitorowania ich procesów i zależności z innymi zasobami, które aplikacja komunikuje za pomocą szczegółowych informacji [o maszynach wirtualnych.](../../azure-monitor/vm/vminsights-overview.md)

- Uprość wdrażanie przy użyciu innych usług platformy Azure, takich jak Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) i Azure Monitor Log Analytics, przy użyciu obsługiwanych rozszerzeń maszyn wirtualnych platformy [Azure](manage-vm-extensions.md) dla maszyny z systemem Windows lub Linux spoza platformy Azure. Obejmuje to konfigurację po wdrożeniu lub instalację oprogramowania przy użyciu rozszerzenia niestandardowego skryptu.

- Użyj [Update Management](../../automation/update-management/overview.md) w Azure Automation do zarządzania aktualizacjami systemu operacyjnego dla serwerów z systemami Windows i Linux

    > [!NOTE]
    > Obecnie włączanie obsługi Update Management bezpośrednio z serwera z obsługą usługi Arc nie jest obsługiwane. Zobacz [Włączanie Update Management z konta usługi Automation,](../../automation/update-management/enable-from-automation-account.md) aby poznać wymagania i sposób włączania dla serwera.

- Uwzględnij serwery spoza platformy Azure w celu wykrywania zagrożeń i proaktywnego monitorowania potencjalnych zagrożeń bezpieczeństwa przy [użyciu Azure Security Center](../../security-center/security-center-introduction.md).

Dane dzienników zebrane i przechowywane w obszarze roboczym usługi Log Analytics z maszyny hybrydowej zawierają teraz właściwości specyficzne dla maszyny, takie jak identyfikator zasobu. Może to służyć do obsługi [dostępu do dziennika kontekstu](../../azure-monitor/logs/design-logs-deployment.md#access-mode) zasobu.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Obsługiwane regiony

Aby uzyskać ostateczną listę obsługiwanych regionów z Azure Arc serwerów, zobacz stronę [Produkty platformy Azure według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

W większości przypadków lokalizacja wybierana podczas tworzenia skryptu instalacji powinna być regionem świadczenia usługi Azure geograficznie najbliższym lokalizacji maszyny. Dane magazynowane są przechowywane w lokalizacji geograficznej platformy Azure zawierającej określony region, co może mieć również wpływ na wybór regionu, jeśli masz wymagania dotyczące rezydencji danych. Jeśli na region platformy Azure, z którego łączy się maszyna, ma wpływ błąd, nie ma to wpływu na podłączaną maszynę, ale operacje zarządzania przy użyciu platformy Azure mogą nie być zakończone. W przypadku regionalnej 3000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 00

Następujące informacje o metadanych dotyczące podłączonej maszyny są zbierane i przechowywane w regionie, w którym skonfigurowano zasób Azure Arc maszyny wirtualnej:

- Nazwa i wersja systemu operacyjnego
- Nazwa komputera
- W pełni kwalifikowana nazwa domeny komputera (FQDN)
- Wersja agenta połączonej maszyny

Jeśli na przykład maszyna jest zarejestrowana w Azure Arc regionie Wschodnie usa, te dane są przechowywane w regionie USA.

### <a name="supported-environments"></a>Obsługiwane środowiska

Serwery z usługą Arc obsługują zarządzanie serwerami fizycznymi i maszynami wirtualnymi hostowanych *poza platformą* Azure. Aby uzyskać szczegółowe informacje o tym, które środowiska chmury hybrydowej hostują maszyny wirtualne, zobacz [Wymagania wstępne agenta połączonej maszyny.](agent-overview.md#supported-environments)

> [!NOTE]
> Serwery z usługą Arc nie są zaprojektowane ani obsługiwane w celu umożliwienia zarządzania maszynami wirtualnymi uruchomionymi na platformie Azure.

### <a name="agent-status"></a>Stan agenta

Agent Connected Machine wysyła do usługi regularny komunikat pulsu co 5 minut. Jeśli usługa przestanie odbierać te komunikaty pulsu z maszyny, ta maszyna zostanie uznana za w tryb offline, a stan zostanie automatycznie zmieniony na **Odłączony** w portalu w ciągu 15–30 minut. Po odebraniu kolejnego komunikatu pulsu z agenta Connected Machine jego stan zostanie automatycznie zmieniony na **Connected (Połączono).**

## <a name="next-steps"></a>Następne kroki

Przed oceną lub włączeniem serwerów z [](agent-overview.md) obsługą usługi Arc na wielu maszynach hybrydowych zapoznaj się z omówieniem agenta połączonej maszyny, aby poznać wymagania, szczegóły techniczne dotyczące agenta i metody wdrażania.