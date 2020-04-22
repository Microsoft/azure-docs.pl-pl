---
title: Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure
description: W tym artykule odnajduj odpowiedzi na typowe pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757473"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Często zadawane pytania — zapasowe maszyn wirtualnych platformy Azure

W tym artykule odpowiedzieć na typowe pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure za pomocą usługi [Azure Backup.](backup-introduction-to-azure-backup.md)

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Które obrazy maszyn wirtualnych można włączyć do tworzenia kopii zapasowych podczas ich tworzenia?

Podczas tworzenia maszyny Wirtualnej można włączyć tworzenie kopii zapasowych dla maszyn wirtualnych z [obsługiwanymi systemami operacyjnymi](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Czy koszt kopii zapasowej jest uwzględniony w koszcie maszyny Wirtualnej?

Nie. Koszty tworzenia kopii zapasowej są niezależne od kosztów maszyny Wirtualnej. Dowiedz się więcej o [cenach usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Jakie uprawnienia są wymagane do włączenia tworzenia kopii zapasowych dla maszyny Wirtualnej?

Jeśli jesteś współautorem maszyny Wirtualnej, możesz włączyć tworzenie kopii zapasowych na maszynie Wirtualnej. Jeśli używasz roli niestandardowej, potrzebujesz następujących uprawnień, aby włączyć tworzenie kopii zapasowej na maszynie Wirtualnej:

- Microsoft.RecoveryServices/Przechowalnia/zapis
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoverySługs/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Jeśli magazyn usług odzyskiwania i maszyna wirtualna mają różne grupy zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów dla magazynu usług odzyskiwania.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Czy zadanie kopii zapasowej na żądanie używa tego samego harmonogramu przechowywania, co zaplanowane kopie zapasowe?

Nie. Określ zakres przechowywania zadania tworzenia kopii zapasowych na żądanie. Domyślnie jest zachowywany przez 30 dni po wyzwoleniu z portalu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ostatnio na niektórych maszynach wirtualnych została włączona usługa Azure Disk Encryption. Czy operacje tworzenia kopii zapasowych będą nadal działać?

Podaj uprawnienia do usługi Azure Backup, aby uzyskać dostęp do magazynu kluczy. Określ uprawnienia w programie PowerShell zgodnie z opisem w sekcji **Włącz tworzenie kopii zapasowej** w dokumentacji programu Azure Backup [PowerShell.](backup-azure-vms-automation.md)

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migrowałem dyski maszyn wirtualnych na dyski zarządzane. Czy operacje tworzenia kopii zapasowych będą nadal działać?

Tak, kopie zapasowe działają bezproblemowo. Nie ma potrzeby ponownego konfigurowania czegokolwiek.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Dlaczego nie widzę mojej maszyny wirtualnej w kreatorze konfigurowania kopii zapasowych?

Kreator wyświetla tylko maszyny wirtualne w tym samym regionie co magazyn i które nie są jeszcze archiwizowane.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Moja maszyna wirtualna jest zamknięta. Czy kopia zapasowa na żądanie lub zaplanowane wykonanie kopii zapasowej będzie działać?

Tak. Kopie zapasowe są uruchamiane po zamknięciu komputera. Punkt odzyskiwania jest oznaczony jako zgodny z awarią.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Czy można anulować zadanie tworzenia kopii zapasowej w toku?

Tak. Zadanie tworzenia kopii zapasowej można anulować w stanie **wykonywanie migawki.** Nie można anulować zadania, jeśli trwa przesyłanie danych z migawki.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Włączyłem blokadę grupy zasobów utworzonej przez usługę Azure `AzureBackupRG_<geo>_<number>`Backup Service (na przykład ). Czy operacje tworzenia kopii zapasowych będą nadal działać?

Jeśli zablokujesz grupę zasobów utworzoną przez usługę Azure Backup Service, kopie zapasowe zaczną się niepowodzeniem, ponieważ istnieje maksymalny limit 18 punktów przywracania.

Usuń blokadę i wyczyść kolekcję punktów przywracania z tej grupy zasobów, aby przyszłe kopie zapasowe zakończyły się pomyślnie. [Wykonaj następujące kroki,](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) aby usunąć kolekcję punktów przywracania.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Czy kopia zapasowa platformy Azure obsługuje standardowe dyski zarządzane przez dyski SSD?

Tak, usługa Azure Backup obsługuje [standardowe dyski zarządzane SSD](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Czy możemy zrobić na maszynie wirtualnej kopii zapasowej dysk z włączoną akceleratorem zapisu (WA)?

Migawek nie można robić na dysku z obsługą WA. Jednak usługa Azure Backup można wykluczyć dysk z włączoną funkcją WA z kopii zapasowej.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mam maszynę wirtualną z dyskami Akceleratora Zapisu (WA) i zainstalowanym SAP HANA. Jak wykonać zapas kopii zapasowej?

Usługa Azure Backup nie może wykonać kopii zapasowej dysku obsługującego wa, ale może wykluczyć go z kopii zapasowej. Jednak kopia zapasowa nie zapewni spójności bazy danych, ponieważ informacje na dysku z włączoną usługą WA nie są archiwizowane. Można wykonać kopię zapasową dysków z tej konfiguracji, jeśli chcesz kopii zapasowej dysku systemu operacyjnego i kopii zapasowej dysków, które nie są włączone WA.

Usługa Azure Backup zapewnia rozwiązanie do tworzenia kopii zapasowych przesyłania strumieniowego dla baz danych SAP HANA z 15-minutowym obiektem RPO. Jest to certyfikat Backint firmy SAP, aby zapewnić natywną obsługę kopii zapasowych wykorzystującą natywne interfejsy API SAP HANA. Dowiedz się więcej [o tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/backup/sap-hana-db-about)

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Jakie jest maksymalne opóźnienie, jakiego mogę się spodziewać w czasie rozpoczęcia tworzenia kopii zapasowej od zaplanowanego czasu tworzenia kopii zapasowej ustawionego w zasadach tworzenia kopii zapasowych maszyny Wirtualnej?

Zaplanowana kopia zapasowa zostanie wyzwolona w ciągu 2 godzin od zaplanowanego czasu tworzenia kopii zapasowej. Na przykład jeśli 100 maszyn wirtualnych mają ich czas rozpoczęcia tworzenia kopii zapasowej zaplanowano na 2:00 AM, a następnie do 4:00 AM najpóźniej wszystkie 100 maszyn wirtualnych będzie ich zadanie tworzenia kopii zapasowej w toku. Jeśli zaplanowane kopie zapasowe zostały wstrzymane z powodu awarii i wznowione lub ponowione, kopia zapasowa może zostać rozpocznie się poza tym zaplanowanym dwugodzinnym oknem.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Jaki jest minimalny dozwolony zakres przechowywania dla dziennego punktu kopii zapasowej?

Zasady tworzenia kopii zapasowych maszyny wirtualnej platformy Azure obsługują minimalny zakres przechowywania od siedmiu dni do 9999 dni. Wszelkie modyfikacje istniejących zasad tworzenia kopii zapasowych maszyn wirtualnych z mniej niż siedem dni będzie wymagać aktualizacji, aby spełnić minimalny zakres przechowywania siedmiu dni.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Co się stanie, jeśli zmienię wielkość liter nazwy mojej maszyny Wirtualnej lub grupy zasobów maszyny Wirtualnej?

Jeśli zmienisz wielkość liter (na górną lub niższą) grupy zasobów maszyny Wirtualnej lub Maszyny Wirtualnej, wielkość liter nazwy elementu kopii zapasowej nie ulegnie zmianie. Jednak jest to oczekiwane zachowanie usługi Azure Backup. Zmiana sprawy nie pojawi się w elemencie kopii zapasowej, ale jest aktualizowana w wewnętrznej bazy danych.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Czy można wywrzeć kopię zapasową lub przywrócić dyski selektywne podłączone do maszyny Wirtualnej?

Usługa Azure Backup obsługuje teraz selektywną kopię zapasową dysku i przywracanie przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.

Obecnie usługa Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (system operacyjny i dane) na maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej. Z funkcji dysku wykluczeń, można uzyskać opcję, aby wykonać jedną lub kilka z wielu dysków danych w maszynie Wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb związanych z tworzeniem kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzbioru dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i z magazynu.

Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Przywracanie

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Jak zdecydować, czy przywrócić tylko dyski, czy pełną maszynę wirtualną?

Przywracanie maszyny Wirtualnej jako opcja szybkiego tworzenia dla maszyny Wirtualnej platformy Azure. Ta opcja powoduje zmianę nazw dysków, kontenerów używanych przez dyski, publicznych adresów IP i nazw interfejsów sieciowych. Zmiana zachowuje unikatowe zasoby podczas tworzenia maszyny Wirtualnej. Maszyna wirtualna nie jest dodawany do zestawu dostępności.

Możesz użyć opcji przywróć dysk, jeśli chcesz:

- Dostosuj maszynę wirtualną, która zostanie utworzona. Na przykład zmień rozmiar.
- Dodaj ustawienia konfiguracji, których nie było w momencie tworzenia kopii zapasowej.
- Steruj konwencją nazewnictwa dla tworzonych zasobów.
- Dodaj maszynę wirtualną do zestawu dostępności.
- Dodaj inne ustawienie, które musi być skonfigurowane przy użyciu programu PowerShell lub szablonu.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Czy można przywrócić kopie zapasowe niezarządzanych dysków maszyn wirtualnych po uaktualnieniu do dysków zarządzanych?

Tak, można użyć kopii zapasowych wykonanych przed migracją dysków z niezarządzanych do zarządzanych.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak mogę przywrócić maszynę wirtualną do punktu przywracania, zanim maszyna wirtualna została zmigrowana do dysków zarządzanych?

Proces przywracania pozostaje taki sam. Jeśli punkt odzyskiwania jest punktem w czasie, gdy maszyna wirtualna miała dyski niezarządzane, można [przywrócić dyski jako niezarządzane](tutorial-restore-disk.md#unmanaged-disks-restore). Jeśli maszyna wirtualna miała dyski zarządzane, można [przywrócić dyski jako dyski zarządzane](tutorial-restore-disk.md#managed-disk-restore). Następnie można [utworzyć maszynę wirtualną z tych dysków](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Dowiedz się więcej](backup-azure-vms-automation.md#restore-an-azure-vm) na ten temat w programie PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Czy mogę przywrócić maszynę wirtualną, która została usunięta?

Tak. Nawet jeśli usuniesz maszynę wirtualną, można przejść do odpowiedniego elementu kopii zapasowej w przechowalni i przywrócić z punktu odzyskiwania.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Jak przywrócić maszynę wirtualną do tych samych zestawów dostępności?

W przypadku maszyn wirtualnych platformy Azure na dysku zarządzanym przywracanie zestawów dostępności jest włączone przez podanie opcji w szablonie podczas przywracania jako dysków zarządzanych. Ten szablon ma parametr wejściowy o nazwie **Zestawy dostępności**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak szybciej przywrócić występy?

[Funkcja natychmiastowego przywracania](backup-instant-restore-capability.md) pomaga w szybszych kopiach zapasowych i natychmiastowych przywracania z migawek.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Co się stanie, gdy zmienimy ustawienia magazynu kluczy dla zaszyfrowanej maszyny Wirtualnej?

Po zmianie ustawień magazynu kluczy dla zaszyfrowanej maszyny Wirtualnej kopie zapasowe będą nadal działać z nowym zestawem szczegółów. Jednak po przywróceniu z punktu odzyskiwania przed zmianą, należy przywrócić wpisy tajne w magazynie kluczy, zanim będzie można utworzyć maszynę wirtualną z niego. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret).

Operacje takie jak klucz tajny/klucz roll-over nie wymagają tego kroku i ten sam KeyVault może być używany po przywróceniu.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Czy mogę uzyskać dostęp do maszyny Wirtualnej po przywróceniu z powodu zerwania relacji z kontrolerem domeny?

Tak, dostęp do maszyny Wirtualnej po przywróceniu z powodu maszyny Wirtualnej, która zerwała relację z kontrolerem domeny. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co się stanie, jeśli zmodyfikuję zasady tworzenia kopii zapasowych?

Kopia zapasowa maszyny Wirtualnej jest archiwiza w tworzenie kopii zapasowej przy użyciu ustawień harmonogramu i przechowywania w zmodyfikowanych lub nowych zasad.

- Jeśli przechowywanie zostanie rozszerzone, istniejące punkty odzyskiwania są oznaczane i przechowywane zgodnie z nowymi zasadami.
- Jeśli retencja zostanie zmniejszona, punkty odzyskiwania są oznaczane do przycinania w następnym zadaniu oczyszczania, a następnie usuwane.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Jak przenieść maszynę wirtualną, z pomocą roboczej kopii zapasowej w usłudze Azure Backup do innej grupy zasobów?

1. Tymczasowo zatrzymaj kopię zapasową i zachowaj dane kopii zapasowej.
2. Przenoszenie maszyny Wirtualnej do docelowej grupy zasobów.
3. Ponownie włącz tworzenie kopii zapasowych w tym samym lub nowym magazynie.

Maszynę Wirtualną można przywrócić z dostępnych punktów przywracania, które zostały utworzone przed operacją przenoszenia.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Czy istnieje limit liczby maszyn wirtualnych, które mogą być skojarzone z tymi samymi zasadami tworzenia kopii zapasowych?

Tak, istnieje limit 100 maszyn wirtualnych, które mogą być skojarzone z tymi samymi zasadami tworzenia kopii zapasowych z portalu. Zaleca się, aby dla ponad 100 maszyn wirtualnych utworzyć wiele zasad tworzenia kopii zapasowych z tym samym harmonogramem lub innym harmonogramem.
