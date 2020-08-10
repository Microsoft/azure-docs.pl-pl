---
title: Omówienie magazynów usługi Recovery Services
description: Omówienie i porównanie między magazynami Recovery Services i magazynami Azure Backup.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e1d061c6baf31fad2e937a604098f0baff6086d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041905"
---
# <a name="recovery-services-vaults-overview"></a>Przegląd magazynów usługi Recovery Services

W tym artykule opisano funkcje magazynu Recovery Services. Magazyn Recovery Services to jednostka magazynowa na platformie Azure, która zawiera dane. Dane są zwykle kopiami danych lub informacjami o konfiguracji maszyn wirtualnych, obciążeń, serwerów lub stacji roboczych. Magazynów Recovery Services można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) i bazy danych Azure SQL. Magazyny Recovery Services obsługują program System Center DPM, system Windows Server, Azure Backup Server i nie tylko. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem. Magazyny Recovery Services są oparte na modelu Azure Resource Manager platformy Azure, który zapewnia następujące funkcje:

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Porównywanie magazynów Recovery Services i magazynów kopii zapasowych

- **Udoskonalone funkcje ułatwiające zabezpieczanie danych kopii zapasowych**: w Recovery Services magazynów Azure Backup zapewnia funkcje zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Funkcje zabezpieczeń zapewniają możliwość zabezpieczenia kopii zapasowych i bezpiecznego odzyskiwania danych, nawet jeśli naruszone są serwery produkcyjne i zapasowe. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowego środowiska informatycznego**: z magazynami Recovery Services można monitorować nie tylko [maszyny wirtualne IaaS platformy Azure](backup-azure-manage-vms.md) , ale również [Zasoby lokalne](backup-azure-manage-windows-server.md#manage-backup-items) z portalu centralnego. [Dowiedz się więcej](backup-azure-monitoring-built-in-monitor.md)

- **Access Control oparte na rolach (RBAC)**: RBAC zapewnia precyzyjną kontrolę zarządzania dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role](../role-based-access-control/built-in-roles.md), a Azure Backup ma trzy [wbudowane role do zarządzania punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny Recovery Services są zgodne z RBAC, które ograniczają dostęp do kopii zapasowych i przywracania do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Usuwanie nietrwałe**: usuwanie nietrwałe, nawet jeśli złośliwy aktor usuwa kopię zapasową (lub dane kopii zapasowej są przypadkowo usuwane), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z kosztem dla klienta. [Dowiedz się więcej](backup-azure-security-feature-cloud.md).

- **Przywracanie między regionami**: Przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure. Jeśli platforma Azure deklaruje awarię w regionie podstawowym, dane zreplikowane w regionie pomocniczym są dostępne do przywrócenia w regionie pomocniczym w celu ograniczenia rzeczywistego przestoju awaryjnego w regionie podstawowym środowiska. [Dowiedz się więcej](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Ustawienia magazynu w magazynie Recovery Services

Magazyn Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn Recovery Services zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

- Azure Backup automatycznie obsługuje magazyn dla magazynu. Zobacz, jak [można zmienić ustawienia magazynu](./backup-create-rs-vault.md#set-storage-redundancy).

- Aby dowiedzieć się więcej o nadmiarowości magazynu, zapoznaj się z tymi artykułami dotyczącymi nadmiarowości [geograficznej](../storage/common/storage-redundancy.md) i [lokalnej](../storage/common/storage-redundancy.md) .

### <a name="additional-resources"></a>Zasoby dodatkowe

- [Obsługiwane i nieobsługiwane scenariusze dotyczące magazynu](backup-support-matrix.md#vault-support)
- [Często zadawane pytania dotyczące magazynu](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) to spersonalizowany konsultant ds. chmury, który pomaga zoptymalizować korzystanie z platformy Azure. Analizuje ona użycie platformy Azure i oferuje terminowe zalecenia ułatwiające optymalizację i zabezpieczenie wdrożeń. Zawiera zalecenia w czterech kategoriach: wysoka dostępność, bezpieczeństwo, wydajność i koszt.

Azure Advisor zawiera co godzinę [zalecenia](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) dotyczące maszyn wirtualnych, których kopie zapasowe nie są tworzone, dlatego nie można pominąć tworzenia kopii zapasowych ważnych maszyn wirtualnych. Możesz również kontrolować zalecenia, je.  Możesz wybrać zalecenie i włączyć tworzenie kopii zapasowych na maszynach wirtualnych w wierszu przez określenie magazynu (w którym będą przechowywane kopie zapasowe) i zasad tworzenia kopii zapasowych (harmonogram wykonywania kopii zapasowych i przechowywania kopii).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Następne kroki

Skorzystaj z następujących artykułów, aby:</br>
[Tworzenie kopii zapasowej maszyny wirtualnej IaaS](backup-azure-arm-vms-prepare.md)</br>
[Tworzenie kopii zapasowej Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-windows-with-mars-agent.md)
