---
title: Usługa Azure automanage dla systemu Windows Server
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących platformy Azure dla maszyn wirtualnych dla usług, które są automatycznie dołączane i konfigurowane dla maszyn z systemem Windows Server.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: c1093491c5d4f0c475254e31c2be079d7dd4f5e1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278851"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Usługa Azure automanage dla najlepszych rozwiązań dotyczących maszyn wirtualnych — system Windows Server

Te usługi platformy Azure są automatycznie dołączane w przypadku używania autozarządzania dla maszyn wirtualnych na maszynie wirtualnej z systemem Windows Server. Są one istotne dla naszych najlepszych rozwiązań, które można znaleźć w naszej [strukturze wdrożenia chmury](/azure/cloud-adoption-framework/manage/azure-server-management).

W przypadku wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować do dryfowania i korygować w przypadku wykrycia dryfu. Aby dowiedzieć się więcej o tym procesie, zobacz [Azure automanage for Virtual Machines](automanage-virtual-machines.md).

## <a name="supported-windows-server-versions"></a>Obsługiwane wersje systemu Windows Server

Autozarządzanie obsługuje następujące wersje systemu Windows Server:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Usługi uczestniczące

|Usługa    |Opis    |Obsługiwane środowiska<sup>1</sup>    |Preferencje obsługiwane<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Monitorowanie usługi VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor dla maszyn wirtualnych monitoruje wydajność i kondycję maszyn wirtualnych, w tym ich uruchomione procesy i zależności od innych zasobów. Dowiedz się [więcej](../azure-monitor/vm/vminsights-overview.md).    |Produkcja    |Nie    |
|[Tworzenie kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-overview)    |Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Dowiedz się [więcej](../backup/backup-azure-vms-introduction.md). Opłaty są naliczane na podstawie liczby i rozmiaru chronionych maszyn wirtualnych. Dowiedz się [więcej](https://azure.microsoft.com/pricing/details/backup/).    |Produkcja    |Tak    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury, który wzmacnia stan zabezpieczeń centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze. Dowiedz się [więcej](../security-center/security-center-introduction.md).  Autozarządzanie skonfiguruje subskrypcję, w której znajduje się Twoja maszyna wirtualna, do oferty w warstwie Bezpłatna Azure Security Center. Jeśli Twoja subskrypcja została już dołączona do Azure Security Center, wówczas Autozarządzanie nie skonfiguruje jej ponownie.    |Produkcja, tworzenie i testowanie    |Nie    |
|[Rozwiązanie Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Firma Microsoft chroniąca przed złośliwym kodem dla systemu Azure to bezpłatna ochrona w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Generuje alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w swoich systemach platformy Azure. Dowiedz się [więcej](../security/fundamentals/antimalware.md). |Produkcja, tworzenie i testowanie    |Tak    |
|[Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/automation/update-management/overview)    |Update Management w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego dla maszyn wirtualnych. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów. Dowiedz się [więcej](../automation/update-management/overview.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|[Change Tracking & spis](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Change Tracking i spis łączy funkcje śledzenia zmian i spisu, aby umożliwić śledzenie zmian infrastruktury maszyny wirtualnej i serwera. Usługa obsługuje śledzenie zmian między usługami, przedemonuje oprogramowanie, rejestr i pliki w swoim środowisku, aby ułatwić diagnozowanie niepożądanych zmian i zgłaszanie alertów. Obsługa spisu umożliwia wykonywanie zapytań dotyczących zasobów gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji.  Dowiedz się [więcej](../automation/change-tracking/overview.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|[Konfiguracja gościa platformy Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | Zasady konfiguracji gościa służą do monitorowania konfiguracji i raportu dotyczącego zgodności maszyny. Usługa autozarządzania zainstaluje [linie bazowe zabezpieczeń systemu Windows](/windows/security/threat-protection/windows-security-baselines) przy użyciu rozszerzenia konfiguracji gościa. W przypadku maszyn z systemem Windows usługa konfiguracji gościa automatycznie ponownie zastosuje ustawienia planu bazowego, jeśli nie są zgodne. Dowiedz się [więcej](../governance/policy/concepts/guest-configuration.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|[Diagnostyka rozruchu](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia Diagnozowanie błędów rozruchu maszyn wirtualnych. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu swojej maszyny wirtualnej w trakcie jej uruchamiania przez zbieranie informacji o dzienniku seryjnym i zrzuty ekranu. |Produkcja, tworzenie i testowanie    |Nie    |
|[Konto usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation obsługuje zarządzanie przez cały cykl życia infrastruktury i aplikacji. Dowiedz się [więcej](../automation/automation-intro.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|[Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor przechowuje dane dzienników w obszarze roboczym Log Analytics, który jest zasobem platformy Azure i kontenerem, w którym zbierane są dane, agregowane i obsługiwane jako granica administracyjna. Dowiedz się [więcej](../azure-monitor/logs/design-logs-deployment.md).    |Produkcja, tworzenie i testowanie    |Nie    |


<sup>1</sup> wybór środowiska jest dostępny po włączeniu autozarządzania. Dowiedz się [więcej](automanage-virtual-machines.md#environment-configuration). Możesz również dostosować ustawienia domyślne środowiska i ustawić własne preferencje w ramach ograniczeń najlepszych rozwiązań.


## <a name="next-steps"></a>Następne kroki

Spróbuj włączyć Autozarządzanie dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)