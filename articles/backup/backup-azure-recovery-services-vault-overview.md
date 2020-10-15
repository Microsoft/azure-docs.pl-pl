---
title: Omówienie magazynów usługi Recovery Services
description: Omówienie magazynów Recovery Services.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: ab6b27bdc7ac9b01385ed43830d0af5900210d43
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093671"
---
# <a name="recovery-services-vaults-overview"></a>Przegląd magazynów usługi Recovery Services

W tym artykule opisano funkcje magazynu Recovery Services. Magazyn Recovery Services to jednostka magazynowa na platformie Azure, która zawiera dane. Dane są zwykle kopiami danych lub informacjami o konfiguracji maszyn wirtualnych, obciążeń, serwerów lub stacji roboczych. Magazynów Recovery Services można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) i bazy danych Azure SQL. Magazyny Recovery Services obsługują program System Center DPM, system Windows Server, Azure Backup Server i nie tylko. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem. Magazyny Recovery Services są oparte na modelu Azure Resource Manager platformy Azure, który zapewnia następujące funkcje:

- **Udoskonalone funkcje ułatwiające zabezpieczanie danych kopii zapasowych**: w Recovery Services magazynów Azure Backup zapewnia funkcje zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Funkcje zabezpieczeń zapewniają możliwość zabezpieczenia kopii zapasowych i bezpiecznego odzyskiwania danych, nawet jeśli naruszone są serwery produkcyjne i zapasowe. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowego środowiska informatycznego**: z magazynami Recovery Services można monitorować nie tylko [maszyny wirtualne IaaS platformy Azure](backup-azure-manage-vms.md) , ale również [Zasoby lokalne](backup-azure-manage-windows-server.md#manage-backup-items) z portalu centralnego. [Dowiedz się więcej](backup-azure-monitoring-built-in-monitor.md)

- **Kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)**: usługa Azure RBAC zapewnia precyzyjną kontrolę zarządzania dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role](../role-based-access-control/built-in-roles.md), a Azure Backup ma trzy [wbudowane role do zarządzania punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny Recovery Services są zgodne z funkcją RBAC platformy Azure, która ogranicza dostęp i przywracanie do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Usuwanie nietrwałe**: usuwanie nietrwałe, nawet jeśli złośliwy aktor usuwa kopię zapasową (lub dane kopii zapasowej są przypadkowo usuwane), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z pozostałymi kosztami. [Dowiedz się więcej](backup-azure-security-feature-cloud.md).

- **Przywracanie między regionami**: Przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure. Jeśli platforma Azure deklaruje awarię w regionie podstawowym, dane zreplikowane w regionie pomocniczym są dostępne do przywrócenia w regionie pomocniczym w celu ograniczenia rzeczywistego przestoju awaryjnego w regionie podstawowym środowiska. [Dowiedz się więcej](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Ustawienia magazynu w magazynie Recovery Services

Magazyn Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn Recovery Services zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

- Azure Backup automatycznie obsługuje magazyn dla magazynu. Zobacz, jak [można zmienić ustawienia magazynu](./backup-create-rs-vault.md#set-storage-redundancy).

- Aby dowiedzieć się więcej o nadmiarowości magazynu, zapoznaj się z tymi artykułami dotyczącymi nadmiarowości [geograficznej](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), [lokalnej](../storage/common/storage-redundancy.md#locally-redundant-storage) i [strefowej](../storage/common/storage-redundancy.md#zone-redundant-storage) .

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Ustawienia szyfrowania w magazynie Recovery Services

W tej sekcji omówiono opcje szyfrowania danych kopii zapasowej przechowywanych w magazynie Recovery Services.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć szyfrowanie, nie trzeba podejmować żadnych działań jawnych. Ma to zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Możesz zdecydować się na szyfrowanie danych przy użyciu kluczy szyfrowania należących do Ciebie i zarządzanych przez użytkownika. Azure Backup umożliwia korzystanie z kluczy RSA przechowywanych w Azure Key Vault na potrzeby szyfrowania kopii zapasowych. Klucz szyfrowania używany do szyfrowania kopii zapasowych może być inny niż użyty dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na protokole AES 256, który jest z kolei chroniony przy użyciu kluczy. Zapewnia to pełną kontrolę nad danymi i kluczami. Aby umożliwić szyfrowanie, magazyn Recovery Services musi mieć udzielony dostęp do klucza szyfrowania w Azure Key Vault. W razie konieczności można wyłączyć klucz lub odwołać dostęp. Należy jednak włączyć szyfrowanie przy użyciu kluczy przed podjęciem próby włączenia ochrony wszystkich elementów do magazynu.

Przeczytaj więcej na temat sposobu szyfrowania danych kopii zapasowej [przy użyciu kluczy zarządzanych przez klienta](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) to spersonalizowany konsultant ds. chmury, który pomaga zoptymalizować korzystanie z platformy Azure. Analizuje ona użycie platformy Azure i oferuje terminowe zalecenia ułatwiające optymalizację i zabezpieczenie wdrożeń. Zawiera zalecenia w czterech kategoriach: wysoka dostępność, bezpieczeństwo, wydajność i koszt.

Azure Advisor zawiera co godzinę [zalecenia](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) dotyczące maszyn wirtualnych, których kopie zapasowe nie są tworzone, dlatego nie można pominąć tworzenia kopii zapasowych ważnych maszyn wirtualnych. Możesz również kontrolować zalecenia, je.  Możesz wybrać zalecenie i włączyć tworzenie kopii zapasowych na maszynach wirtualnych w wierszu przez określenie magazynu (w którym będą przechowywane kopie zapasowe) i zasad tworzenia kopii zapasowych (harmonogram wykonywania kopii zapasowych i przechowywania kopii).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Obsługiwane i nieobsługiwane scenariusze dotyczące magazynu](backup-support-matrix.md#vault-support)
- [Często zadawane pytania dotyczące magazynu](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Następne kroki

Skorzystaj z następujących artykułów, aby:

- [Tworzenie kopii zapasowej maszyny wirtualnej IaaS](backup-azure-arm-vms-prepare.md)
- [Tworzenie kopii zapasowej Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej systemu Windows Server](backup-windows-with-mars-agent.md)
