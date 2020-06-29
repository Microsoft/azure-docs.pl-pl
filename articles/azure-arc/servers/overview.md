---
title: Azure ARC dla serwerów (wersja zapoznawcza) — Omówienie
description: Dowiedz się, jak używać usługi Azure ARC dla serwerów do zarządzania maszynami hostowanymi poza platformą Azure, tak jakby były to zasoby platformy Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, Konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, Python, graficzne, hybrydowe
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: e775945526a5453085946ed4eea2a2e19761ba78
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482194"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Co to jest usługa Azure ARC dla serwerów (wersja zapoznawcza)?

Usługa Azure ARC dla serwerów (wersja zapoznawcza) umożliwia zarządzanie maszynami z systemami Windows i Linux hostowanymi poza platformą Azure w sieci firmowej lub innym dostawcy chmury, podobnie jak w przypadku zarządzania natywnymi maszynami wirtualnymi platformy Azure. Gdy maszyna hybrydowa jest połączona z platformą Azure, zostanie ona podłączona i jest traktowana jako zasób na platformie Azure. Każdy połączony komputer ma identyfikator zasobu, który jest zarządzany jako część grupy zasobów w ramach subskrypcji, i korzyści ze standardowych konstrukcji platformy Azure, takich jak Azure Policy i stosowanie tagów.

Aby zapewnić to środowisko dla maszyn hybrydowych hostowanych poza platformą Azure, należy zainstalować agenta maszyny połączonej z platformą Azure na każdym komputerze, na którym ma zostać nawiązane połączenie z platformą Azure. Ten agent nie dostarcza żadnych innych funkcji i nie zastępuje [agenta log Analytics](../../azure-monitor/platform/log-analytics-agent.md)platformy Azure. Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy użytkownik chce aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim za pomocą elementów Runbook usługi Automation lub rozwiązań, takich jak Update Management, lub używać innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Ta wersja zapoznawcza jest przeznaczona do celów ewaluacyjnych i zalecamy, aby nie zarządzać krytycznymi maszynami produkcyjnymi.
>

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje następujące scenariusze z połączonymi maszynami:

- Przypisanie [Azure Policy konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md) przy użyciu tego samego środowiska co przypisanie zasad dla maszyn wirtualnych platformy Azure.
- Dane dziennika zebrane przez agenta Log Analytics, przechowywane w obszarze roboczym Log Analytics zarejestrowano maszynę. Dane dziennika z maszyny hybrydowej zawierają teraz właściwości specyficzne dla maszyny, takie jak identyfikator zasobu, który może służyć do obsługi dostępu do dziennika [kontekstu zasobów](../../azure-monitor/platform/design-logs-deployment.md#access-mode) .

## <a name="supported-regions"></a>Obsługiwane regiony

Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje tylko niektóre regiony:

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

W większości przypadków lokalizacja wybrana podczas tworzenia skryptu instalacji powinna być regionem platformy Azure geograficznie najbliżej lokalizacji maszyny. Dane przechowywane w obszarze geograficznym platformy Azure zawierają określony region, co może również mieć wpływ na wybór regionu, jeśli istnieją wymagania dotyczące danych znajdujących się na miejscu. Jeśli do regionu platformy Azure, z którym jest połączona dana maszyna, ma wpływ awaria, nie ma to wpływ na przyłączoną maszynę, ale nie można ukończyć operacji zarządzania przy użyciu platformy Azure. W celu uzyskania odporności w przypadku awarii regionalnej, jeśli masz wiele lokalizacji, które zapewniają geograficznie nadmiarową usługę, najlepszym rozwiązaniem jest połączenie maszyn w każdej lokalizacji z innym regionem świadczenia usługi Azure.

### <a name="agent-status"></a>Stan agenta

Agent połączonej maszyny wysyła do usługi zwykły komunikat pulsu co 5 minut. Jeśli usługa przestanie odbierać te komunikaty pulsu z komputera, komputer jest traktowany jako w trybie offline, a stan zostanie automatycznie zmieniony na **rozłączony** w portalu w ciągu od 15 do 30 minut. Po odebraniu kolejnego komunikatu pulsu z agenta połączonej maszyny jego stan zostanie automatycznie zmieniony na **połączone**.

## <a name="next-steps"></a>Następne kroki

Przed przeprowadzeniem oceny lub włączenia usługi ARC dla serwerów (wersja zapoznawcza) na wielu maszynach hybrydowych zapoznaj się z artykułem [Omówienie agenta połączonej maszyny](agent-overview.md) , aby zrozumieć, co jest wymagane, szczegółowe informacje techniczne na temat agenta i metody wdrażania.
