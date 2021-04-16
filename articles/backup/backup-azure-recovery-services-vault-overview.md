---
title: Omówienie magazynów usługi Recovery Services
description: Omówienie magazynów usługi Recovery Services.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2f2018f0f3d3135d632418c2e591e6ad938d62d2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517457"
---
# <a name="recovery-services-vaults-overview"></a>Przegląd magazynów usługi Recovery Services

W tym artykule opisano funkcje magazynu usługi Recovery Services. Magazyn usługi Recovery Services to jednostka magazynu na platformie Azure, w którym znajdują się dane. Dane są zazwyczaj kopiami danych lub informacjami o konfiguracji maszyn wirtualnych, obciążeń, serwerów lub stacji roboczych. Magazynów usługi Recovery Services można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) i Azure SQL baz danych. Magazyny usługi Recovery Services obsługują System Center DPM, Windows Server, Azure Backup Server i inne. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem. Magazyny usługi Recovery Services są oparte na Azure Resource Manager platformy Azure, który udostępnia funkcje takie jak:

- **Ulepszone możliwości zabezpieczania danych kopii zapasowych:** magazyny usługi Recovery Services Azure Backup zapewniają możliwości zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Funkcje zabezpieczeń zapewniają bezpieczeństwo kopii zapasowych i bezpieczne odzyskiwanie danych, nawet w przypadku naruszenia zabezpieczeń serwerów produkcyjnych i kopii zapasowych. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowego** środowiska IT: dzięki magazynom usługi Recovery Services można monitorować nie tylko [](backup-azure-manage-windows-server.md#manage-backup-items) maszyny wirtualne [IaaS](backup-azure-manage-vms.md) platformy Azure, ale także zasoby lokalne z centralnego portalu. [Dowiedz się więcej](backup-azure-monitoring-built-in-monitor.md)

- **Kontrola dostępu oparta na rolach (RBAC)** na platformie Azure: kontrola dostępu oparta na rolach platformy Azure zapewnia precyzyjne zarządzanie dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role](../role-based-access-control/built-in-roles.md), a Azure Backup ma trzy wbudowane role do zarządzania [punktami odzyskiwania.](backup-rbac-rs-vault.md) Magazyny usługi Recovery Services są zgodne z funkcją RBAC platformy Azure, która ogranicza dostęp do kopii zapasowych i przywracania do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Usuwanie nie** softne: w przypadku usuwania nie softowego, nawet jeśli złośliwy aktor usunie kopię zapasową (lub dane kopii zapasowej zostaną przypadkowo usunięte), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co umożliwia odzyskiwanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nieciągłe" nie wiąże się z żadnymi kosztami. [Dowiedz się więcej](backup-azure-security-feature-cloud.md).

- **Przywracanie między regionami:** przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest sparowany z platformą Azure. Po włączeniu tej funkcji na [poziomie magazynu](backup-create-rs-vault.md#set-cross-region-restore)możesz przywrócić zreplikowane dane w regionie pomocniczym w dowolnym momencie, gdy wybierzesz tę opcję. Dzięki temu można przywrócić dane regionu pomocniczego na potrzeby zgodności inspekcji i podczas scenariuszy awarii bez konieczności oczekiwania na zadeklarowanie awarii przez platformę Azure (w przeciwieństwie do ustawień magazynu GRS). [Dowiedz się więcej](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Ustawienia magazynu w magazynie usługi Recovery Services

Magazyn usługi Recovery Services to jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn usługi Recovery Services zawiera również zasady tworzenia kopii zapasowych skojarzone z chronionymi maszynami wirtualnymi.

- Azure Backup automatycznie obsługuje magazyn dla magazynu. Zobacz, [jak można zmienić ustawienia magazynu.](./backup-create-rs-vault.md#set-storage-redundancy)

- Aby dowiedzieć się więcej na temat nadmiarowości [](../storage/common/storage-redundancy.md#locally-redundant-storage) magazynu, zobacz następujące artykuły na temat [nadmiarowości geograficznej,](../storage/common/storage-redundancy.md#geo-zone-redundant-storage)lokalnej [i strefowej.](../storage/common/storage-redundancy.md#zone-redundant-storage)

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Ustawienia szyfrowania w magazynie usługi Recovery Services

W tej sekcji omówiono dostępne opcje szyfrowania danych kopii zapasowej przechowywanych w magazynie usługi Recovery Services.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć to szyfrowanie, nie musisz nic jawnie akcję od swojej strony. Dotyczy to wszystkich obciążeń, których kopię zapasową należy wrócić do magazynu usługi Recovery Services.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta

Możesz szyfrować dane przy użyciu kluczy szyfrowania należących do Ciebie i zarządzanych przez Ciebie. Azure Backup umożliwia szyfrowanie kopii zapasowych przy użyciu kluczy RSA przechowywanych Azure Key Vault chmurze. Klucz szyfrowania używany do szyfrowania kopii zapasowych może różnić się od klucza używanego dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych (DEK) opartego na standardach AES 256, który z kolei jest chroniony przy użyciu kluczy. Daje to pełną kontrolę nad danymi i kluczami. Aby zezwolić na szyfrowanie, magazyn usługi Recovery Services musi mieć przyznany dostęp do klucza szyfrowania w Azure Key Vault. W razie potrzeby można wyłączyć klucz lub odwołać dostęp. Jednak przed podjęciem próby ochrony jakichkolwiek elementów w magazynie należy włączyć szyfrowanie przy użyciu kluczy.

Przeczytaj więcej na temat szyfrowania danych kopii zapasowej [przy użyciu kluczy zarządzanych przez klienta.](encryption-at-rest-with-cmk.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) to spersonalizowany konsultant ds. chmury, który pomaga zoptymalizować korzystanie z platformy Azure. Analizuje ona użycie platformy Azure i udostępnia terminowe rekomendacje, które ułatwiają optymalizację i zabezpieczanie wdrożeń. Zawiera zalecenia w czterech kategoriach: wysoka dostępność, zabezpieczenia, wydajność i koszt.

Azure Advisor godzinowe zalecenia [](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) dotyczące maszyn wirtualnych, dla których nie jest owana ich kopii zapasowej, dzięki czemu nigdy nie przeoczysz kopii zapasowej ważnych maszyn wirtualnych. Rekomendacje można również kontrolować, wywłaszając je.  Możesz wybrać zalecenie i włączyć tworzenie kopii zapasowych na maszynach wirtualnych w wierszu, określając magazyn (w którym będą przechowywane kopie zapasowe) i zasady tworzenia kopii zapasowych (harmonogram tworzenia kopii zapasowych i przechowywania kopii zapasowych).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Obsługiwane i nieobsługiwane scenariusze dotyczące magazynu](backup-support-matrix.md#vault-support)
- [Magazyn często zadawane pytania](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Następne kroki

Skorzystaj z następujących artykułów, aby:

- [Back up an IaaS VM](backup-azure-arm-vms-prepare.md)
- [Kopii zapasowej Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Kopii zapasowej systemu Windows Server](backup-windows-with-mars-agent.md)
