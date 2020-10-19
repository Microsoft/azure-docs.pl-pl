---
title: Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: f318d785fdfa5b72050bdd805ecfe801d307b9a7
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172830"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi [Azure Backup](./backup-overview.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Które obrazy maszyn wirtualnych można włączyć do tworzenia kopii zapasowych podczas ich tworzenia?

Podczas tworzenia maszyny wirtualnej można włączyć tworzenie kopii zapasowych dla maszyn wirtualnych z [obsługiwanymi systemami operacyjnymi](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="why-initial-backup-is-taking-lot-of-time-to-complete"></a>Dlaczego tworzenie początkowej kopii zapasowej trwa długo?

Początkowa kopia zapasowa to zawsze pełna kopia zapasowa, która będzie zależała od rozmiaru danych i czasu przetwarzania kopii zapasowej. <br>
Aby zwiększyć wydajność tworzenia kopii zapasowych, zobacz [najlepsze rozwiązania w zakresie tworzenia kopii zapasowych](./backup-azure-vms-introduction.md#best-practices). [Uwagi dotyczące tworzenia kopii zapasowych](./backup-azure-vms-introduction.md#backup-and-restore-considerations) i [wydajności kopii zapasowych](./backup-azure-vms-introduction.md#backup-performance)<br>
Mimo że łączny czas wykonywania przyrostowych kopii zapasowych jest krótszy niż 24 godziny, może to nie dotyczyć pierwszej kopii zapasowej.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Czy koszt kopii zapasowej obejmuje koszt maszyny wirtualnej?

Nie. Koszty kopii zapasowej są oddzielone od kosztów maszyny wirtualnej. Dowiedz się więcej o [cenach Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Które uprawnienia są wymagane, aby włączyć tworzenie kopii zapasowej maszyny wirtualnej?

Jeśli jesteś współautorem maszyny wirtualnej, możesz włączyć tworzenie kopii zapasowej na maszynie wirtualnej. Jeśli używasz roli niestandardowej, musisz mieć następujące uprawnienia, aby włączyć tworzenie kopii zapasowej na maszynie wirtualnej:

- Microsoft. RecoveryServices/magazyny/zapis
- Microsoft. RecoveryServices/magazyny/odczyt
- Microsoft. RecoveryServices/Locations/*
- Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/*/Read
- Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Read
- Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Write
- Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/zapis
- Microsoft. RecoveryServices/magazyny/backupPolicies/odczyt
- Microsoft. RecoveryServices/magazyny/backupPolicies/zapis

Jeśli magazyn Recovery Services i maszyna wirtualna mają różne grupy zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów dla magazynu Recovery Services.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Czy zadanie tworzenia kopii zapasowej na żądanie używa tego samego harmonogramu przechowywania co zaplanowane kopie zapasowe?

Nie. Określ zakres przechowywania dla zadania tworzenia kopii zapasowej na żądanie. Domyślnie jest ono przechowywane przez 30 dni, gdy jest wyzwalane z poziomu portalu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ostatnio na niektórych maszynach wirtualnych została włączona usługa Azure Disk Encryption. Czy operacje tworzenia kopii zapasowych będą nadal działać?

Podaj uprawnienia Azure Backup, aby uzyskać dostęp do Key Vault. Określ uprawnienia w programie PowerShell zgodnie z opisem w sekcji **Włączanie kopii zapasowej** w dokumentacji [programu Azure Backup PowerShell](backup-azure-vms-automation.md) .

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Dyski maszyny wirtualnej zostały zmigrowane do dysków zarządzanych. Czy operacje tworzenia kopii zapasowych będą nadal działać?

Tak, kopie zapasowe działają bezproblemowo. Nie trzeba ponownie konfigurować żadnych elementów.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Dlaczego nie widzę mojej maszyny wirtualnej w kreatorze konfigurowania kopii zapasowych?

W kreatorze są wyświetlane tylko maszyny wirtualne znajdujące się w tym samym regionie co magazyn, które nie są już tworzone w ramach kopii zapasowej.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Moja maszyna wirtualna jest wyłączona. Czy zostanie zaplanowana kopia zapasowa na żądanie, czy też

Tak. Kopie zapasowe są uruchamiane, gdy maszyna jest wyłączona. Punkt odzyskiwania jest oznaczony jako spójny pod kątem awarii.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Czy mogę anulować trwające zadanie tworzenia kopii zapasowej?

Tak. Zadanie tworzenia kopii zapasowej można anulować w stanie trwającej **migawki** . Nie można anulować zadania, jeśli transfer danych z migawki jest w toku.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Włączono blokadę grupy zasobów utworzonej przez usługę Azure Backup (na przykład `AzureBackupRG_<geo>_<number>` ). Czy operacje tworzenia kopii zapasowych będą nadal działać?

Jeśli zablokujesz grupę zasobów utworzoną przez usługę Azure Backup, kopie zapasowe rozpoczną się niepowodzeniem, ponieważ obowiązuje limit 18 punktów przywracania.

Usuń blokadę i wyczyść kolekcję punktów przywracania z tej grupy zasobów, aby umożliwić pomyślne tworzenie kopii zapasowych w przyszłości. [Wykonaj następujące kroki](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) , aby usunąć kolekcję punktów przywracania.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Czy Azure Backup obsługuje standardowe dyski zarządzane przez dysk SSD?

Tak, Azure Backup obsługuje [dyski zarządzane w warstwie Standardowa SSD](https://docs.microsoft.com/azure/virtual-machines/disks-types#standard-ssd).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Czy można utworzyć kopię zapasową maszyny wirtualnej z dyskiem z obsługą akcelerator zapisu (WA)?

Nie można wykonać migawek na dysku z obsługą WA. Jednak usługa Azure Backup może wykluczyć dysk z obsługą WA z kopii zapasowej.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mam maszynę wirtualną z dyskami akcelerator zapisu (WA) i zainstalowaną SAP HANA. Jak mogę utworzyć kopię zapasową?

Azure Backup nie może utworzyć kopii zapasowej dysku z obsługą WA, ale może go wykluczyć z kopii zapasowej. Jednak kopia zapasowa nie zapewni spójności bazy danych, ponieważ nie jest wykonywana kopia zapasowa informacji na dysku z obsługą WA. Można utworzyć kopię zapasową dysków przy użyciu tej konfiguracji, jeśli chcesz utworzyć kopię zapasową dysku systemu operacyjnego i utworzyć kopię zapasową dysków, które nie są włączone.

Azure Backup udostępnia rozwiązanie do tworzenia kopii zapasowych dla SAP HANA baz danych z celem punktu odzyskiwania wynoszącym 15 minut. Jest BACKINT certyfikowany przez SAP, aby zapewnić natywną obsługę kopii zapasowych, wykorzystującą natywne interfejsy API SAP HANA. Dowiedz się więcej [o tworzeniu kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Co to jest maksymalne opóźnienie, które może oczekiwać w czasie rozpoczęcia wykonywania kopii zapasowej z zaplanowanego czasu wykonywania kopii zapasowej ustawionej w ramach zasad kopii zapasowych maszyny wirtualnej?

Zaplanowana kopia zapasowa zostanie wyzwolona w ciągu 2 godzin od zaplanowanego czasu tworzenia kopii zapasowej. Jeśli na przykład maszyny wirtualne 100 mają czas rozpoczęcia wykonywania kopii zapasowej zaplanowane o godzinie 2:00, a następnie przez 4:00 AM na najnowszych wszystkich maszynach wirtualnych 100, zadanie tworzenia kopii zapasowej będzie w toku. Jeśli zaplanowane kopie zapasowe zostały wstrzymane ze względu na awarię i wznowienie lub ponowną próbę, kopia zapasowa może zostać uruchomiona poza zaplanowanym dwugodzinnym oknem.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Jaki jest minimalny dozwolony zakres przechowywania dla codziennego punktu kopii zapasowej?

Zasady tworzenia kopii zapasowych maszyn wirtualnych platformy Azure obsługują minimalny zakres przechowywania od siedmiu dni do 9999 dni. Wszelkie modyfikacje istniejących zasad tworzenia kopii zapasowych maszyn wirtualnych przy użyciu mniej niż siedmiu dni będą wymagały aktualizacji w celu spełnienia minimalnego zakresu przechowywania wynoszącego siedem dni.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Co się stanie, jeśli zmienię wielkość liter nazwy mojej maszyny wirtualnej lub grupy zasobów maszyny wirtualnej?

Jeśli zmienisz wielkość liter (na górną lub niższą) maszyny wirtualnej lub grupy zasobów maszyny wirtualnej, przypadek nazwy elementu kopii zapasowej nie ulegnie zmianie. Jest to jednak oczekiwane Azure Backup zachowanie. Zmiana wielkości liter nie zostanie wyświetlona w elemencie kopii zapasowej, ale zostanie zaktualizowana w zapleczu.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Czy można tworzyć kopie zapasowe lub przywracać dyski selektywne dołączone do maszyny wirtualnej?

Azure Backup teraz obsługuje selektywne tworzenie kopii zapasowych i przywracanie dysków przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [selektywne tworzenie kopii zapasowych i przywracanie dysków dla maszyn wirtualnych platformy Azure](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Czy zarządzane tożsamości są zachowywane w przypadku zmiany dzierżawy podczas tworzenia kopii zapasowej?

Jeśli nastąpi [zmiana dzierżawy](/azure/devops/organizations/accounts/change-azure-ad-connection) , musisz wyłączyć i ponownie włączyć [zarządzane tożsamości](../active-directory/managed-identities-azure-resources/overview.md) , aby kopie zapasowe działały ponownie.

## <a name="restore"></a>Przywracanie

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Jak mogę zdecydować, czy przywracać tylko dyski, czy pełną maszynę wirtualną?

Należy wziąć pod uwagę przywracanie maszyny wirtualnej jako opcję szybkiego tworzenia dla maszyny wirtualnej platformy Azure. Ta opcja umożliwia zmianę nazw dysków, kontenerów używanych przez dyski, publiczne adresy IP i nazwy interfejsów sieciowych. Zmiana zachowuje unikatowe zasoby podczas tworzenia maszyny wirtualnej. Maszyna wirtualna nie została dodana do zestawu dostępności.

Jeśli chcesz, możesz użyć opcji Przywróć dysk:

- Dostosuj utworzoną maszynę wirtualną. Na przykład zmień rozmiar.
- Dodaj ustawienia konfiguracji, które nie były w trakcie wykonywania kopii zapasowej.
- Kontrolowanie konwencji nazewnictwa dla utworzonych zasobów.
- Dodaj maszynę wirtualną do zestawu dostępności.
- Dodaj inne ustawienia, które należy skonfigurować przy użyciu programu PowerShell lub szablonu.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Czy można przywrócić kopie zapasowe niezarządzanych dysków maszyny wirtualnej po uaktualnieniu do dysków zarządzanych?

Tak, można użyć kopii zapasowych wykonanych przed migracją dysków z niezarządzanych do zarządzanych.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak mogę przywrócić maszynę wirtualną do punktu przywracania, zanim maszyna wirtualna została zmigrowana do dysków zarządzanych?

Proces przywracania pozostaje taki sam. Jeśli punkt odzyskiwania znajduje się w danym punkcie w czasie, gdy maszyna wirtualna ma dyski niezarządzane, można [przywrócić dyski jako niezarządzane](tutorial-restore-disk.md#unmanaged-disks-restore). Jeśli maszyna wirtualna ma dyski zarządzane, można [przywrócić dyski jako dyski zarządzane](tutorial-restore-disk.md#managed-disk-restore). Następnie można [utworzyć maszynę wirtualną na podstawie tych dysków](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Dowiedz się więcej](backup-azure-vms-automation.md#restore-an-azure-vm) o tym, jak to zrobić w programie PowerShell.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Jeśli podczas przywracania nie można utworzyć maszyny wirtualnej, co się dzieje z dyskami uwzględnionymi w przywracaniu?

W przypadku przywracania zarządzanej maszyny wirtualnej, nawet jeśli utworzenie maszyny wirtualnej nie powiedzie się, dyski nadal będą przywracane.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Czy można przywrócić maszynę wirtualną, która została usunięta?

Tak. Nawet jeśli usuniesz maszynę wirtualną, możesz przejść do odpowiedniego elementu kopii zapasowej w magazynie i przywrócić go z punktu odzyskiwania.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Jak mogę przywrócić maszynę wirtualną do tych samych zestawów dostępności?

W przypadku maszyn wirtualnych platformy Azure z dyskami zarządzanymi przywracanie do zestawów dostępności jest włączone przez udostępnienie opcji w szablonie podczas przywracania jako dyski zarządzane. Ten szablon zawiera parametr wejściowy o nazwie **zestawy dostępności**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak szybsze przywracanie wydajności?

Funkcja [natychmiastowego przywracania](backup-instant-restore-capability.md) ułatwia wykonywanie kopii zapasowych i natychmiastowe przywracanie z migawek.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Co się stanie w przypadku zmiany ustawień magazynu kluczy dla zaszyfrowanej maszyny wirtualnej?

Po zmianie ustawień magazynu kluczy dla zaszyfrowanej maszyny wirtualnej kopie zapasowe będą nadal współpracują z nowym zestawem szczegółów. Jednak po przywróceniu z punktu odzyskiwania przed zmianą należy przywrócić klucze tajne w magazynie kluczy, aby można było utworzyć maszynę wirtualną. Więcej informacji znajduje się w tym [artykule](./backup-azure-restore-key-secret.md).

Operacje, takie jak odzyskiwanie awaryjne/klucze, nie wymagają tego kroku, a ten sam magazyn kluczy może być używany po przywróceniu.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Czy mogę uzyskać dostęp do maszyny wirtualnej po jej przywróceniu, ponieważ maszyna wirtualna ma przerwane relacje z kontrolerem domeny?

Tak, można uzyskać dostęp do maszyny wirtualnej po jej przywróceniu, ponieważ maszyna wirtualna ma przerwane relacje z kontrolerem domeny. Aby uzyskać więcej informacji, zobacz ten [artykuł](./backup-azure-arm-restore-vms.md#post-restore-steps)

### <a name="why-restore-operation-is-taking-long-time-to-complete"></a>Dlaczego ukończenie operacji przywracania trwa długo?

Łączny czas przywracania zależy od operacji wejścia/wyjścia na sekundę (IOPS) oraz przepływności konta magazynu. Łączny czas przywracania może być narażony, jeśli docelowe konto magazynu zostanie załadowane z innymi operacjami odczytu i zapisu aplikacji. Aby ulepszyć operację przywracania, wybierz konto magazynu, które nie zostało załadowane z innymi danymi aplikacji.

## <a name="manage-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co się stanie w przypadku zmodyfikowania zasad tworzenia kopii zapasowych?

Utworzono kopię zapasową maszyny wirtualnej przy użyciu ustawień harmonogram i przechowywanie w zmodyfikowanych lub nowych zasadach.

- Jeśli przechowywanie zostanie rozszerzone, istniejące punkty odzyskiwania są oznaczane i przechowywane zgodnie z nowymi zasadami.
- W przypadku skrócenia okresu przechowywania punkty odzyskiwania są oznaczane do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usuwane.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Jak mogę przenieść maszyny wirtualnej, której kopia zapasowa została utworzona przez Azure Backup do innej grupy zasobów?

1. Tymczasowe zatrzymywanie tworzenia kopii zapasowej i zachowywanie danych kopii zapasowej.
2. Aby przenieść maszyny wirtualne skonfigurowane przy użyciu Azure Backup, wykonaj następujące czynności:

   1. Znajdź lokalizację maszyny wirtualnej.
   2. Znajdź grupę zasobów o następującym wzorcu nazewnictwa: `AzureBackupRG_<location of your VM>_1` . Na przykład *AzureBackupRG_westus2_1*
   3. W Azure Portal zaznacz opcję **Pokaż ukryte typy**.
   4. Znajdź zasób z typem **Microsoft. COMPUTE/restorePointCollections** , który ma wzorzec nazewnictwa `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Usuń ten zasób. Ta operacja usuwa tylko natychmiastowe punkty odzyskiwania, a nie kopię zapasową danych w magazynie.
   6. Po zakończeniu operacji usuwania można przenieść maszynę wirtualną.

3. Przenieś maszynę wirtualną do docelowej grupy zasobów.
4. Wznów tworzenie kopii zapasowej.

Można przywrócić maszynę wirtualną z dostępnych punktów przywracania utworzonych przed operacją przenoszenia.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Co się stanie po przeniesieniu maszyny wirtualnej do innej grupy zasobów?

Gdy maszyna wirtualna zostanie przeniesiona do innej grupy zasobów, jest to nowa maszyna wirtualna, o ile jest to konieczne Azure Backup.

Po przeniesieniu maszyny wirtualnej do nowej grupy zasobów można ponownie włączyć ochronę maszyny wirtualnej w tym samym magazynie lub w innym magazynie. Ponieważ jest to nowa maszyna wirtualna dla Azure Backup, opłaty są naliczane oddzielnie.

Punkty przywracania starych maszyn wirtualnych będą dostępne do przywracania w razie potrzeby. Jeśli te dane kopii zapasowej nie są potrzebne, możesz zatrzymać ochronę starej maszyny wirtualnej, usuwając dane.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Czy istnieje ograniczenie liczby maszyn wirtualnych, które można skojarzyć z tymi samymi zasadami tworzenia kopii zapasowych?

Tak, istnieje limit 100 maszyn wirtualnych, które mogą być skojarzone z tymi samymi zasadami tworzenia kopii zapasowych z poziomu portalu. Zalecamy, aby dla ponad 100 maszyn wirtualnych utworzyć wiele zasad tworzenia kopii zapasowych z tym samym harmonogramem lub innym harmonogramem.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Jak można wyświetlić ustawienia przechowywania kopii zapasowych?

Obecnie można wyświetlać ustawienia przechowywania na poziomie elementu kopii zapasowej (VM) na podstawie zasad tworzenia kopii zapasowych przypisanych do maszyny wirtualnej.

Jednym ze sposobów wyświetlania ustawień przechowywania kopii zapasowych jest przejście do [pulpitu nawigacyjnego](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) elementu kopii zapasowej maszyny wirtualnej w Azure Portal. Wybranie linku do zasad tworzenia kopii zapasowej ułatwia wyświetlanie czasu przechowywania wszystkich codziennych, cotygodniowych, miesięcznych i rocznych punktów przechowywania skojarzonych z maszyną wirtualną.

Możesz również użyć [Eksploratora kopii zapasowych](./monitor-azure-backup-with-backup-explorer.md) , aby wyświetlić ustawienia przechowywania dla wszystkich maszyn wirtualnych w jednym okienku szkła. Przejdź do Eksploratora kopii zapasowych z dowolnego magazynu Recovery Services, przejdź do karty **elementy kopii zapasowej** i wybierz widok zaawansowany, aby wyświetlić szczegółowe informacje o przechowywaniu dla każdej maszyny wirtualnej.