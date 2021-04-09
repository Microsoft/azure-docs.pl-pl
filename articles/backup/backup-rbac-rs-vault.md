---
title: Zarządzanie kopiami zapasowymi przy użyciu kontroli dostępu opartej na rolach na platformie Azure
description: Za pomocą kontroli dostępu opartej na rolach platformy Azure można zarządzać dostępem do operacji zarządzania kopiami zapasowymi w magazynie Recovery Services.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613446"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Używanie kontroli dostępu opartej na rolach na platformie Azure do zarządzania Azure Backup punktów odzyskiwania

Kontrola dostępu oparta na rolach (Azure RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem na platformie Azure. Przy użyciu kontroli RBAC na platformie Azure można przeprowadzać segregowanie zadań w ramach zespołu i nadawać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań.

> [!IMPORTANT]
> Role udostępniane przez Azure Backup są ograniczone do akcji, które mogą być wykonywane w Azure Portal lub za pośrednictwem interfejsu API REST lub poleceń cmdlet programu PowerShell dla magazynu Recovery Services lub interfejsu wiersza polecenia. Akcje wykonywane w interfejsie użytkownika klienta agenta Azure Backup lub interfejsie użytkownika programu System Center Data Protection Manager lub interfejsie użytkownika Azure Backup Server nie kontrolują tych ról.

Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi. Dowiedz się więcej o [rolach wbudowanych platformy Azure](../role-based-access-control/built-in-roles.md)

* [Współautor kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-contributor) — ta rola ma wszystkie uprawnienia do tworzenia kopii zapasowych i zarządzania nimi z wyjątkiem usuwania magazynu Recovery Services i udzielania dostępu innym osobom. Wyobraź sobie tę rolę jako administrator zarządzania kopiami zapasowymi, który może wykonywać każdą operację zarządzania kopiami zapasowymi.
* [Operator kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-operator) — ta rola ma uprawnienia do wszystkich elementów współautora, z wyjątkiem usuwania kopii zapasowych i zarządzania zasadami tworzenia kopii zapasowych. Ta rola jest równoznaczna z współautorem, z wyjątkiem sytuacji, w której nie może wykonać operacji niszczących, takich jak zatrzymanie wykonywania kopii zapasowej z usuwaniem danych lub usuwanie rejestracji zasobów lokalnych.
* [Czytnik kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi. Wyobraź sobie, że ta rola jest osobą odpowiedzialną za monitorowanie.

Jeśli chcesz zdefiniować własne role, aby jeszcze bardziej kontrolować, zobacz jak [tworzyć role niestandardowe](../role-based-access-control/custom-roles.md) na platformie Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapowanie wbudowanych ról kopii zapasowej na akcje zarządzania kopiami zapasowymi

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Minimalne wymagania roli dla kopii zapasowej maszyny wirtualnej platformy Azure

W poniższej tabeli przedstawiono akcje zarządzania kopiami zapasowymi i odpowiadającą jej minimalną rolę platformy Azure wymaganą do wykonania tej operacji.

| Operacja zarządzania | Wymagana jest minimalna rola platformy Azure | Wymagany zakres | Różne |
| --- | --- | --- | --- |
| Tworzenie magazynu usługi Recovery Services | Współautor kopii zapasowej | Grupa zasobów zawierająca magazyn |   |
| Włącz tworzenie kopii zapasowych maszyn wirtualnych platformy Azure | Operator kopii zapasowych | Grupa zasobów zawierająca magazyn |   |
| | Współautor maszyny wirtualnej | Zasób maszyny wirtualnej |  Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| Tworzenie kopii zapasowej maszyny wirtualnej na żądanie | Operator kopii zapasowych | Magazyn usługi Recovery Services |   |
| Przywracanie maszyny wirtualnej | Operator kopii zapasowych | Magazyn usługi Recovery Services |   |
| | Współautor | Grupa zasobów, w której zostanie wdrożona maszyna wirtualna |   Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. resources/subscriptions/resourceGroups/Write Microsoft. DomainRegistration/domen/Write, Microsoft. COMPUTE/virtualMachines/Write Microsoft. Network/virtualNetworks/odczytywać Microsoft. Network/virtualNetworks/Subnets/Join/Action |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |   Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| Przywróć dyski niezarządzane kopia zapasowa maszyny wirtualnej | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono | Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| | Współautor konta magazynu | Zasób konta magazynu, w którym mają zostać przywrócone dyski |   Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. Storage/storageAccounts/Write |
| Przywróć dyski zarządzane z kopii zapasowej maszyny wirtualnej | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |    Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| | Współautor konta magazynu | Konto magazynu tymczasowego wybrane w ramach przywracania do przechowywania danych z magazynu przed przekonwertowaniem ich na dyski zarządzane |   Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. Storage/storageAccounts/Write |
| | Współautor | Grupa zasobów, do której zostaną przywrócone dyski zarządzane | Alternatywnie zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. resources/subscriptions/resourceGroups/Write|
| Przywróć pojedyncze pliki z kopii zapasowej maszyny wirtualnej | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono | Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| Przywracanie między regionami | Operator kopii zapasowych | Subskrypcja magazynu usługi Recovery Services | Jest to uzupełnienie uprawnień przywracania wymienionych powyżej. W odniesieniu do CRR zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: "Microsoft. RecoveryServices/Locations/backupAadProperties/Read" "Microsoft. RecoveryServices/Locations/backupCrrJobs/Action" "Microsoft. RecoveryServices/Locations/backupCrrJob/Action" "Microsoft. RecoveryServices/Locations/backupCrossRegionRestore/Action" "Microsoft. RecoveryServices/Locations/backupCrrOperationResults/Read" "Microsoft. RecoveryServices/Locations/backupCrrOperationsStatus/Read" |
| Tworzenie zasad kopii zapasowych dla kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Modyfikowanie zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Usuwanie zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Zatrzymaj kopię zapasową (z zachowaniem Zachowaj dane lub Usuń dane) na kopii zapasowej maszyny wirtualnej | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Rejestrowanie lokalnego systemu Windows Server/Client/SCDPM lub Azure Backup Server | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| Usuń zarejestrowane lokalne serwery z systemem Windows/Client/SCDPM lub Azure Backup Server | Współautor kopii zapasowej | Magazyn usługi Recovery Services |

> [!IMPORTANT]
> Jeśli określisz współautor maszyny wirtualnej w zakresie zasobów maszyny wirtualnej i wybierzesz pozycję **kopia zapasowa** jako część ustawień maszyny wirtualnej, zostanie otwarty ekran **Włączanie kopii zapasowej** , nawet jeśli utworzono kopię zapasową maszyny wirtualnej. Wynika to z faktu, że wywołanie weryfikacji stanu kopii zapasowej działa tylko na poziomie subskrypcji. Aby tego uniknąć, należy przejść do magazynu i otworzyć widok elementu kopii zapasowej maszyny wirtualnej lub określić rolę współautor maszyny wirtualnej na poziomie subskrypcji.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Minimalne wymagania roli dla kopii zapasowych obciążeń platformy Azure (kopie zapasowe baz danych SQL i HANA)

W poniższej tabeli przedstawiono akcje zarządzania kopiami zapasowymi i odpowiadającą jej minimalną rolę platformy Azure wymaganą do wykonania tej operacji.

| Operacja zarządzania | Wymagana jest minimalna rola platformy Azure | Wymagany zakres | Różne |
| --- | --- | --- | --- |
| Tworzenie magazynu usługi Recovery Services | Współautor kopii zapasowej | Grupa zasobów zawierająca magazyn |   |
| Włącz tworzenie kopii zapasowych baz danych SQL i/HANA | Operator kopii zapasowych | Grupa zasobów zawierająca magazyn |   |
| | Współautor maszyny wirtualnej | Zasób maszyny wirtualnej, w którym zainstalowano bazę danych |  Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| Tworzenie kopii zapasowej bazy danych na żądanie | Operator kopii zapasowych | Magazyn usługi Recovery Services |   |
| Przywracanie bazy danych lub przywracanie jako plików | Operator kopii zapasowych | Magazyn usługi Recovery Services |   |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopię zapasową utworzono |   Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| | Współautor maszyny wirtualnej | Docelowa maszyna wirtualna, w której zostanie przywrócona baza danych lub zostaną utworzone pliki |   Zamiast wbudowanej roli można rozważyć rolę niestandardową, która ma następujące uprawnienia: Microsoft. COMPUTE/virtualMachines/Write |
| Tworzenie zasad kopii zapasowych dla kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Modyfikowanie zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Usuwanie zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Zatrzymaj kopię zapasową (z zachowaniem Zachowaj dane lub Usuń dane) na kopii zapasowej maszyny wirtualnej | Współautor kopii zapasowej | Magazyn usługi Recovery Services |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimalne wymagania roli dla kopii zapasowej udziału plików platformy Azure

W poniższej tabeli przedstawiono akcje zarządzania kopiami zapasowymi i odpowiadające im role wymagane do wykonania operacji udziału plików platformy Azure.

| Operacja zarządzania | Rola jest wymagana | Zasoby |
| --- | --- | --- |
| Włącz tworzenie kopii zapasowych udziałów plików platformy Azure | Współautor kopii zapasowej |Magazyn usługi Recovery Services |
| |Konto magazynu | Zasób konta magazynu współautora |
| Tworzenie kopii zapasowej maszyny wirtualnej na żądanie | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| Przywróć udział plików | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor konta magazynu | Zasoby konta magazynu, w których znajdują się źródła przywracania i docelowe udziały plików |
| Przywróć pojedyncze pliki | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| |Współautor konta magazynu|Zasoby konta magazynu, w których znajdują się źródła przywracania i docelowe udziały plików |
| Zatrzymywanie ochrony |Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Wyrejestrowywanie konta magazynu z magazynu |Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| |Współautor konta magazynu | Zasób konta magazynu|

## <a name="next-steps"></a>Następne kroki

* [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): wprowadzenie do usługi Azure rbac w Azure Portal.
* Dowiedz się, jak zarządzać dostępem przy użyciu:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie problemów z kontrolą dostępu opartą na rolach na platformie Azure](../role-based-access-control/troubleshooting.md): Uzyskaj sugestie dotyczące rozwiązywania typowych problemów.
