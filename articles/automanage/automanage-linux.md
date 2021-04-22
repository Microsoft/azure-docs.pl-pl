---
title: Azure Automanage dla systemu Linux
description: Dowiedz się więcej Azure Automanage najlepszych rozwiązań dotyczących maszyn wirtualnych dla usług, które są automatycznie dołączane i konfigurowane dla maszyn z systemem Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0d35963d96ead68c35ca44467119b3c03d0b16c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863069"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure Automanage najlepszych rozwiązań dla maszyn wirtualnych — Linux

Te usługi platformy Azure są automatycznie dołączane podczas korzystania z funkcji Automatycznego zarządzania maszynami wirtualnymi na maszynie wirtualnej z systemem Linux. Są one niezbędne w naszym dokumencie z najlepszymi rozwiązaniami, który można znaleźć w [naszym Cloud Adoption Framework.](/azure/cloud-adoption-framework/manage/azure-server-management)

Dla wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować dryf i korygować w przypadku wykrycia dryfu. Aby dowiedzieć się więcej na temat tego procesu, zobacz [Azure Automanage maszyn wirtualnych.](automanage-virtual-machines.md)

## <a name="supported-linux-distributions-and-versions"></a>Obsługiwane dystrybucje i wersje systemu Linux

Automanage obsługuje następujące dystrybucje i wersje systemu Linux:

- CentOS 7.3+
- RHEL 7.4+
- Ubuntu 16.04 i 18.04
- SLES 12 (tylko SP3-SP5)

## <a name="participating-services"></a>Usługi biorące udział

>[!NOTE]
> Microsoft Antimalware nie jest obecnie obsługiwana na maszynach wirtualnych z systemem Linux.

|Usługa    |Opis    |Obsługiwane środowiska<sup>1</sup>    |Obsługiwane preferencje<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Monitorowanie szczegółowych informacji o maszynie wirtualnej](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor dla maszyn wirtualnych monitoruje wydajność i kondycję maszyn wirtualnych, w tym uruchomione procesy i zależności od innych zasobów. Dowiedz się [więcej](../azure-monitor/vm/vminsights-overview.md).    |Produkcja    |Nie    |
|[Tworzenie kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-overview)   |Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Dowiedz się [więcej](../backup/backup-azure-vms-introduction.md). Opłaty są naliczane na podstawie liczby i rozmiaru chronionych maszyn wirtualnych. Dowiedz się [więcej](https://azure.microsoft.com/pricing/details/backup/).    |Produkcja    |Tak    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury, który wzmacnia poziom bezpieczeństwa centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w obciążeniach hybrydowych w chmurze. Dowiedz się [więcej](../security-center/security-center-introduction.md).  Automanage skonfiguruje subskrypcję, w której znajduje się maszyna wirtualna, do oferty w warstwie Bezpłatna usługi Azure Security Center. Jeśli Subskrypcja została już do niego do Azure Security Center, funkcja Automatycznego Azure Security Center nie skonfiguruje jej ponownie.    |Produkcja, tworzenia i testowania    |Nie    |
|[Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/automation/update-management/overview)    |Za pomocą usługi Update Management Azure Automation zarządzać aktualizacjami systemu operacyjnego dla maszyn wirtualnych. Możesz szybko ocenić stan dostępnych aktualizacji na wszystkich maszynach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów. Dowiedz się [więcej](../automation/update-management/overview.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Change Tracking & spisu](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Śledzenie zmian i spis funkcje śledzenia zmian i spisu, aby umożliwić śledzenie zmian infrastruktury maszyn wirtualnych i serwerów. Usługa obsługuje śledzenie zmian między usługami, oprogramowaniem demonów, rejestrem i plikami w środowisku, aby ułatwić diagnozowanie niepożądanych zmian i zgłaszanie alertów. Obsługa spisu umożliwia wykonywanie zapytań o zasoby gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji.  Dowiedz się [więcej](../automation/change-tracking/overview.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Konfiguracja gościa platformy Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | Zasady konfiguracji gościa służą do monitorowania konfiguracji i raportów dotyczących zgodności maszyny. Usługa Automatycznego zarządzania zainstaluje punkt odniesienia systemu Linux platformy Azure przy użyciu rozszerzenia konfiguracja gościa. W przypadku maszyn z systemem Linux usługa konfiguracji gościa zainstaluje punkt odniesienia w trybie tylko do inspekcji. Będzie można zobaczyć, gdzie maszyna wirtualna jest niezgodne z punktem odniesienia, ale nie zostanie automatycznie skorygowana niezgodność. Dowiedz się [więcej](../governance/policy/concepts/guest-configuration.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Diagnostyka rozruchu](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia diagnostykę błędów rozruchu maszyny wirtualnej. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu maszyny wirtualnej podczas rozruchu przez zbieranie informacji dziennika szeregowego i zrzutów ekranu. Ta opcja zostanie włączona tylko w przypadku maszyn, które są korzystające z dysków zarządzanych. |Produkcja, tworzenia i testowania    |Nie    |
|[Konto usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation obsługuje zarządzanie w całym cyklu życia infrastruktury i aplikacji. Dowiedz się [więcej](../automation/automation-intro.md).    |Produkcja, tworzenia i testowania    |Nie    |
|[Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor dane dzienników są przechowywane w obszarze roboczym usługi Log Analytics, który jest zasobem platformy Azure i kontenerem, w którym dane są zbierane, agregowane i służą jako granice administracyjne. Dowiedz się [więcej](../azure-monitor/logs/design-logs-deployment.md).    |Produkcja, tworzenia i testowania    |Nie    |


<sup>1</sup> Wybór środowiska jest dostępny podczas włączania funkcji Automanage. Dowiedz się [więcej](automanage-virtual-machines.md#environment-configuration). Można również dostosować ustawienia domyślne środowiska i ustawić własne preferencje w ramach ograniczeń dotyczących najlepszych rozwiązań.


## <a name="next-steps"></a>Następne kroki

Spróbuj w celu włączenia funkcji Automanage dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz automatycznemanage dla maszyn wirtualnych w Azure Portal](quick-create-virtual-machines-portal.md)