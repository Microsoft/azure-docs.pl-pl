---
title: Azure Automanage dla systemu Windows Server
description: Dowiedz się więcej Azure Automanage najlepszych rozwiązań dotyczących maszyn wirtualnych dla usług, które są automatycznie dołączane i konfigurowane dla maszyn z systemem Windows Server.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 98d91356b55be015b2c1b73787dad0bb7d8fd424
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863015"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure Automanage najlepszych rozwiązań dla maszyn wirtualnych — Windows Server

Te usługi platformy Azure są automatycznie dołączane podczas korzystania z funkcji Automatycznego zarządzania maszynami wirtualnymi na maszynie wirtualnej z systemem Windows Server. Są one niezbędne w naszym dokumencie z najlepszymi rozwiązaniami, który można znaleźć w [naszym Cloud Adoption Framework.](/azure/cloud-adoption-framework/manage/azure-server-management)

Dla wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować dryf i korygować w przypadku wykrycia dryfu. Aby dowiedzieć się więcej na temat tego procesu, zobacz [Azure Automanage maszyn wirtualnych.](automanage-virtual-machines.md)

## <a name="supported-windows-server-versions"></a>Obsługiwane wersje systemu Windows Server

Funkcja automatycznego zarządzania obsługuje następujące wersje systemu Windows Server:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Usługi biorące udział

|Usługa    |Opis    |Obsługiwane środowiska<sup>1</sup>    |Obsługiwane preferencje<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Monitorowanie szczegółowych informacji o maszynie wirtualnej](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor dla maszyn wirtualnych monitoruje wydajność i kondycję maszyn wirtualnych, w tym uruchomione procesy i zależności od innych zasobów. Dowiedz się [więcej](../azure-monitor/vm/vminsights-overview.md).    |Produkcja    |Nie    |
|[Tworzenie kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-overview)    |Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Dowiedz się [więcej](../backup/backup-azure-vms-introduction.md). Opłaty są naliczane na podstawie liczby i rozmiaru chronionych maszyn wirtualnych. Dowiedz się [więcej](https://azure.microsoft.com/pricing/details/backup/).    |Produkcja    |Tak    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury, który wzmacnia poziom zabezpieczeń centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w obciążeniach hybrydowych w chmurze. Dowiedz się [więcej](../security-center/security-center-introduction.md).  Funkcja automatycznego konfigurowania subskrypcji, w której znajduje się maszyna wirtualna, jest oferowana w warstwie Bezpłatna Azure Security Center. Jeśli Subskrypcja została już do niego do Azure Security Center, automatyczne konfigurowanie jej nie zostanie ponownie skonfigurowane.    |Produkcja, tworzenia i testowania    |Nie    |
|[Rozwiązanie Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Microsoft Antimalware dla platformy Azure to bezpłatna ochrona w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Generuje alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w systemach platformy Azure. Dowiedz się [więcej](../security/fundamentals/antimalware.md). |Produkcja, tworzenia i testowania    |Tak    |
|[Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/automation/update-management/overview)    |Za pomocą usługi Update Management Azure Automation zarządzać aktualizacjami systemu operacyjnego dla maszyn wirtualnych. Możesz szybko ocenić stan dostępnych aktualizacji na wszystkich maszynach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów. Dowiedz się [więcej](../automation/update-management/overview.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Change Tracking & spisu](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Śledzenie zmian i spis funkcje śledzenia zmian i spisu, aby umożliwić śledzenie zmian infrastruktury maszyn wirtualnych i serwerów. Usługa obsługuje śledzenie zmian w usługach, oprogramowaniu demonów, rejestrze i plikach w środowisku, co pomaga diagnozować niepożądane zmiany i zgłaszać alerty. Obsługa spisu umożliwia wykonywanie zapytań o zasoby gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji.  Dowiedz się [więcej](../automation/change-tracking/overview.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Konfiguracja gościa platformy Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | Zasady konfiguracji gościa służą do monitorowania konfiguracji i raportów dotyczących zgodności maszyny. Usługa Automatycznego zarządzania zainstaluje linie bazowe [zabezpieczeń systemu Windows przy](/windows/security/threat-protection/windows-security-baselines) użyciu rozszerzenia Konfiguracja gościa. W przypadku maszyn z systemem Windows usługa konfiguracji gościa automatycznie ponownie rozpocznie stosowanie ustawień odniesienia, jeśli nie są one zgodne. Dowiedz się [więcej](../governance/policy/concepts/guest-configuration.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Diagnostyka rozruchu](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia diagnostykę błędów rozruchu maszyny wirtualnej. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu maszyny wirtualnej podczas rozruchu przez zbieranie informacji dziennika szeregowego i zrzutów ekranu. Ta opcja zostanie włączona tylko dla maszyn używających dysków zarządzanych. |Produkcja, tworzenia i testowania    |Nie    |
|[Konto usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation obsługuje zarządzanie w całym cyklu życia infrastruktury i aplikacji. Dowiedz się [więcej](../automation/automation-intro.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor dane dzienników są przechowywane w obszarze roboczym usługi Log Analytics, który jest zasobem platformy Azure i kontenerem, w którym dane są zbierane, agregowane i służą jako granice administracyjne. Dowiedz się [więcej](../azure-monitor/logs/design-logs-deployment.md).    |Produkcja, tworzenia i testowania    |Nie    |


<sup>1</sup> Wybór środowiska jest dostępny podczas włączania funkcji Automanage. Dowiedz się [więcej](automanage-virtual-machines.md#environment-configuration). Można również dostosować ustawienia domyślne środowiska i ustawić własne preferencje w ramach ograniczeń dotyczących najlepszych rozwiązań.


## <a name="next-steps"></a>Następne kroki

Spróbuj w celu włączenia funkcji Automanage dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz automatycznemanage dla maszyn wirtualnych w Azure Portal](quick-create-virtual-machines-portal.md)