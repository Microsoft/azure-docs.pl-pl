---
title: Usługa Azure automanage dla Virtual Machines najlepszych rozwiązań
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi platformy Azure dla maszyn wirtualnych dla usług, które są automatycznie dołączane i konfigurowane.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 2b8009d6d571a9daf7163d2126b6d52edfc4bfdd
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521088"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Usługa Azure automanage dla najlepszych rozwiązań dotyczących maszyn wirtualnych


Te usługi platformy Azure są automatycznie dołączane w przypadku używania autozarządzania dla maszyn wirtualnych. Są one istotne dla naszych najlepszych rozwiązań, które można znaleźć w naszej [strukturze wdrożenia chmury](/azure/cloud-adoption-framework/manage/azure-server-management).

W przypadku wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować pod kątem dryfowania i korygować w przypadku wykrycia dryfu. Aby dowiedzieć się więcej o tym procesie, zobacz [Azure automanage for Virtual Machines](automanage-virtual-machines.md).


## <a name="participating-services"></a>Usługi uczestniczące

|Usługa    |Opis    |Obsługiwane profile<sup>1</sup>    |Preferencje obsługiwane<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorowanie usługi VM Insights    |Azure Monitor dla maszyn wirtualnych monitoruje wydajność i kondycję maszyn wirtualnych, w tym ich uruchomione procesy i zależności od innych zasobów. Dowiedz się [więcej](../azure-monitor/insights/vminsights-overview.md).    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja    |Nie    |
|Backup    |Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Dowiedz się [więcej](../backup/backup-azure-vms-introduction.md). Opłaty są naliczane na podstawie liczby i rozmiaru chronionych maszyn wirtualnych. Dowiedz się [więcej](https://azure.microsoft.com/pricing/details/backup/).    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja    |Tak    |
|Azure Security Center    |Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury, który wzmacnia stan zabezpieczeń centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze. Dowiedz się [więcej](../security-center/security-center-introduction.md).  Autozarządzanie skonfiguruje subskrypcję, w której znajduje się Twoja maszyna wirtualna, do oferty w warstwie Bezpłatna Azure Security Center. Jeśli Twoja subskrypcja została już dołączona do Azure Security Center, wówczas Autozarządzanie nie zostanie ponownie skonfigurowana.    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja, najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure — tworzenie i testowanie    |Nie    |
|Usługa firmy Microsoft chroniąca przed złośliwym kodem    |Firma Microsoft chroniąca przed złośliwym kodem dla systemu Azure to bezpłatna ochrona w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Generuje alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w swoich systemach platformy Azure. Dowiedz się [więcej](../security/fundamentals/antimalware.md). |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja, najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure — tworzenie i testowanie    |Tak    |
|Zarządzanie aktualizacjami    |Update Management w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego dla maszyn wirtualnych. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów. Dowiedz się [więcej](../automation/update-management/update-mgmt-overview.md).    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja, najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure — tworzenie i testowanie    |Nie    |
|Change Tracking & spis    |Change Tracking i spis łączy funkcje śledzenia zmian i spisu, aby umożliwić śledzenie zmian infrastruktury maszyny wirtualnej i serwera. Usługa obsługuje śledzenie zmian między usługami, przedemonuje oprogramowanie, rejestr i pliki w swoim środowisku, aby ułatwić diagnozowanie niepożądanych zmian i zgłaszanie alertów. Obsługa spisu umożliwia wykonywanie zapytań dotyczących zasobów gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji.  Dowiedz się [więcej](../automation/change-tracking/overview.md).    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja, najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure — tworzenie i testowanie    |Nie    |
|Konto usługi Azure Automation    |Azure Automation obsługuje zarządzanie przez cały cykl życia infrastruktury i aplikacji. Dowiedz się [więcej](../automation/automation-intro.md).    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja, najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure — tworzenie i testowanie    |Nie    |
|Obszar roboczy usługi Log Analytics    |Azure Monitor przechowuje dane dzienników w obszarze roboczym Log Analytics, który jest zasobem platformy Azure i kontenerem, w którym zbierane są dane, agregowane i obsługiwane jako granica administracyjna. Dowiedz się [więcej](../azure-monitor/platform/design-logs-deployment.md).    |Najlepsze praktyki dotyczące maszyn wirtualnych platformy Azure — produkcja, najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure — tworzenie i testowanie    |Nie    |


<sup>1</sup> profile konfiguracji są dostępne podczas włączania autozarządzania. Dowiedz się [więcej](automanage-virtual-machines.md#configuration-profiles). Możesz również dostosować ustawienia domyślne profilu konfiguracji i ustawić własne preferencje w ramach ograniczeń najlepszych rozwiązań.


## <a name="next-steps"></a>Następne kroki

Spróbuj włączyć Autozarządzanie dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)