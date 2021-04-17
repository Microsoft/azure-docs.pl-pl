---
title: Co nowego w usłudze Azure Backup
description: Dowiedz się więcej o nowych funkcjach Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 68e0e5cc0876840c30ab9e428a2b96bd7d667756
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516335"
---
# <a name="whats-new-in-azure-backup"></a>Co nowego w usłudze Azure Backup

Azure Backup stale ulepsza i udostępnia nowe funkcje, które zwiększają ochronę danych na platformie Azure. Te nowe funkcje rozszerzają ochronę danych o nowe typy obciążeń, zwiększają bezpieczeństwo i zwiększają dostępność danych kopii zapasowych. Dodają również nowe możliwości zarządzania, monitorowania i automatyzacji.

Aby dowiedzieć się więcej na temat nowych wersji, dodaj zakładkę do tej strony lub [subskrybując aktualizacje tutaj.](https://azure.microsoft.com/updates/?query=backup)

## <a name="updates-summary"></a>Podsumowanie aktualizacji

- Marzec 2021 r.
  - [Usługa Azure Disk Backup jest teraz ogólnie dostępna](#azure-disk-backup-is-now-generally-available)
  - [Centrum kopii zapasowych jest teraz ogólnie dostępne](#backup-center-is-now-generally-available)
  - [Obsługa warstwy Archiwum dla Azure Backup (w wersji zapoznawczej)](#archive-tier-support-for-azure-backup-in-preview)
- Luty 2021 r.
  - [Tworzenie kopii zapasowej obiektów blob platformy Azure (w wersji zapoznawczej)](#backup-for-azure-blobs-in-preview)
- Styczeń 2021 r.
  - [Azure Disk Backup (w wersji zapoznawczej)](#azure-disk-backup-in-preview)
  - [Szyfrowanie danych w spoczynku przy użyciu kluczy zarządzanych przez klienta (ogólna dostępność)](#encryption-at-rest-using-customer-managed-keys)
- Listopad 2020 r.
  - [Azure Resource Manager szablonu kopii zapasowej udziału plików platformy Azure (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Przyrostowe kopie zapasowe dla SAP HANA baz danych na maszyn wirtualnych platformy Azure (w wersji zapoznawczej)](#incremental-backups-for-sap-hana-databases-in-preview)
- Wrzesień 2020
  - [Centrum kopii zapasowych (w wersji zapoznawczej)](#backup-center-in-preview)
  - [Tworzenie Azure Database for PostgreSQL kopii zapasowej (w wersji zapoznawczej)](#backup-azure-database-for-postgresql-in-preview)
  - [Selektywne tworzenie i przywracanie kopii zapasowej dysku](#selective-disk-backup-and-restore)
  - [Przywracanie między regionami dla SQL Server i SAP HANA baz danych na maszyn wirtualnych platformy Azure (w wersji zapoznawczej)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Obsługa tworzenia kopii zapasowych maszyn wirtualnych z maksymalnie 32 dyskami (ogólna dostępność)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Uproszczone środowisko konfiguracji kopii zapasowych dla bazy danych SQL na platformie Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Tworzenie SAP HANA kopii zapasowych w usłudze Azure Virtual Machines RHEL (w wersji zapoznawczej)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Magazyn strefowo nadmiarowy (ZRS) dla danych kopii zapasowej (w wersji zapoznawczej)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Usuwanie nie softowe dla SQL Server i SAP HANA obciążeń na platformie Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Usługa Azure Disk Backup jest teraz ogólnie dostępna

Azure Backup zarządzanie cyklem życia migawek w usłudze Azure Dyski zarządzane przez automatyzację okresowego tworzenia migawek i ich zachowywanie przez skonfigurowane czasy trwania przy użyciu zasad kopii zapasowych.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Disk Backup.](disk-backup-overview.md)

## <a name="backup-center-is-now-generally-available"></a>Centrum kopii zapasowych jest teraz ogólnie dostępne

Centrum kopii zapasowych upraszcza zarządzanie ochroną danych na dużą skalę, umożliwiając odnajdywanie, zarządzanie, monitorowanie, obsługę i optymalizowanie zarządzania kopiami zapasowymi z jednej centralnej konsoli.

Aby uzyskać więcej informacji, zobacz [Overview of Backup Center](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Obsługa warstwy Archiwum dla Azure Backup (w wersji zapoznawczej)

Azure Backup umożliwia teraz zmniejszenie kosztów kopii zapasowych przechowywania długoterminowego dzięki dostępności warstwy Archiwum dla maszyn wirtualnych platformy Azure i SQL Server na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji, zobacz [Obsługa warstwy Archiwum (wersja zapoznawcza).](archive-tier-support.md)

## <a name="backup-for-azure-blobs-in-preview"></a>Tworzenie kopii zapasowej obiektów blob platformy Azure (w wersji zapoznawczej)

Operacyjna kopia zapasowa obiektów blob to zarządzane, lokalne rozwiązanie do ochrony danych, które umożliwia ochronę blokowych obiektów blob przed różnymi scenariuszami utraty danych, takich jak uszkodzenie, usunięcie obiektów blob i przypadkowe usunięcie konta magazynu. Dane są przechowywane lokalnie na źródłowym koncie magazynu i w razie potrzeby można je odzyskać do wybranego punktu w czasie. Dzięki temu zapewnia proste, bezpieczne i ekonomiczne sposoby ochrony obiektów blob.

Operacyjna kopia zapasowa obiektów blob integruje się z Centrum kopii zapasowych, a także z innymi możliwościami zarządzania kopiami zapasowymi, aby zapewnić pojedyncze okienko, które może ułatwić zarządzanie, monitorowanie, obsługę i analizowanie kopii zapasowych na dużą skalę.

Aby uzyskać więcej informacji, zobacz [Overview of operational backup for Azure Blobs (in preview) (Omówienie](blob-backup-overview.md)operacyjnej kopii zapasowej obiektów blob platformy Azure (w wersji zapoznawczej).

## <a name="azure-disk-backup-in-preview"></a>Usługa Azure Disk Backup (w wersji zapoznawczej)

Usługa Azure Disk Backup oferuje gotowe rozwiązanie, które zapewnia zarządzanie cyklem życia migawek dla usługi Azure Dyski zarządzane przez automatyzowanie [okresowego](../virtual-machines/managed-disks-overview.md) tworzenia migawek i zachowywanie ich przez skonfigurowany czas trwania przy użyciu zasad tworzenia kopii zapasowych. Migawkami dysków można zarządzać bez poniesienie kosztów infrastruktury i bez konieczności wykonywania skryptów niestandardowych ani jakichkolwiek narzutów na zarządzanie. Jest to rozwiązanie do tworzenia kopii zapasowych spójne na awarii, [](../virtual-machines/disks-incremental-snapshots.md) które pobiera kopię zapasową dysku zarządzanego w punkcie w czasie przy użyciu migawek przyrostowych z obsługą wielu kopii zapasowych dziennie. Jest to również rozwiązanie bez agenta i nie ma wpływu na wydajność aplikacji produkcyjnych. Obsługuje tworzenie kopii zapasowych i przywracanie dysków systemu operacyjnego i danych (w tym dysków udostępnionych), niezależnie od tego, czy są one aktualnie dołączone do uruchomionej maszyny wirtualnej platformy Azure.

Aby uzyskać więcej informacji, zobacz [Azure Disk Backup (w wersji zapoznawczej)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Szyfrowanie danych w spoczynku przy użyciu kluczy zarządzanych przez klienta

Obsługa szyfrowania danych w spoczynku przy użyciu kluczy zarządzanych przez klienta jest teraz ogólnie dostępna. Daje to możliwość szyfrowania danych kopii zapasowej w magazynach usługi Recovery Services przy użyciu własnych kluczy przechowywanych w usłudze Azure Key Vault. Klucz szyfrowania używany do szyfrowania kopii zapasowych w magazynie usługi Recovery Services może różnić się od klucza używanego do szyfrowania źródła. Dane są chronione przy użyciu klucza szyfrowania danych (DEK) opartego na standardach AES 256, który z kolei jest chroniony przy użyciu kluczy przechowywanych w Key Vault. W porównaniu z szyfrowaniem przy użyciu kluczy zarządzanych przez platformę (które jest domyślnie dostępne), zapewnia to większą kontrolę nad kluczami i może pomóc lepiej spełnić wymagania dotyczące zgodności.

Aby uzyskać więcej informacji, zobacz [Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta.](encryption-at-rest-with-cmk.md)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager szablonu kopii zapasowej usługi AFS

Azure Backup teraz obsługuje konfigurowanie kopii zapasowych dla istniejących udziałów plików platformy Azure przy użyciu szablonu usługi Azure Resource Manager (ARM). Szablon konfiguruje ochronę istniejącego udziału plików platformy Azure, określając odpowiednie szczegóły dla magazynu usługi Recovery Services i zasad tworzenia kopii zapasowych. Opcjonalnie tworzy nowy magazyn usługi Recovery Services i zasady tworzenia kopii zapasowych oraz rejestruje konto magazynu zawierające udział plików w magazynie usługi Recovery Services.

Aby uzyskać więcej informacji, [zobacz Azure Resource Manager szablonów dla Azure Backup](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Przyrostowe kopie zapasowe dla SAP HANA baz danych (w wersji zapoznawczej)

Azure Backup teraz obsługuje przyrostowe kopie zapasowe dla SAP HANA baz danych hostowanych na maszynach wirtualnych platformy Azure. Umożliwia to szybsze i bardziej ekonomiczne tworzenie kopii zapasowych danych SAP HANA danych.

Aby uzyskać więcej informacji, [zobacz różne opcje dostępne](/sap-hana-faq-backup-azure-vm.yml#policy) podczas tworzenia zasad kopii zapasowych i sposób tworzenia zasad tworzenia kopii zapasowych dla baz SAP HANA [danych.](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)

## <a name="backup-center-in-preview"></a>Centrum kopii zapasowych (w wersji zapoznawczej)

Azure Backup włączono nową natywną funkcję zarządzania do zarządzania całą kopią zapasową z konsoli centralnej. Centrum kopii zapasowych zapewnia możliwość monitorowania, obsługi, zarządzania i optymalizowania ochrony danych na dużą skalę w ujednolicony sposób zgodny z natywnymi środowiskami zarządzania platformy Azure.

Za pomocą centrum kopii zapasowych można uzyskać zagregowany widok spisu dla subskrypcji, lokalizacji, grup zasobów, magazynów, a nawet dzierżaw przy użyciu Azure Lighthouse. Centrum kopii zapasowych to centrum akcji, z którego można wyzwolić działania związane z tworzeniem kopii zapasowej, takie jak konfigurowanie kopii zapasowej, przywracanie, tworzenie zasad lub magazynów, a wszystko to z jednego miejsca. Ponadto dzięki bezproblemowej integracji z Azure Policy można teraz zarządzać środowiskiem i śledzić zgodność z perspektywy kopii zapasowej. Wbudowane zasady platformy Azure specyficzne dla Azure Backup umożliwiają również konfigurowanie kopii zapasowych na dużą skalę.

Aby uzyskać więcej informacji, zobacz [Overview of Backup Center](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Tworzenie Azure Database for PostgreSQL kopii zapasowych (w wersji zapoznawczej)

Azure Backup usług Azure Database Services zostały połączone w celu skompilowania rozwiązania klasy korporacyjnej do tworzenia kopii zapasowych dla usługi Azure PostgreSQL (obecnie w wersji zapoznawczej). Teraz możesz spełnić wymagania dotyczące ochrony danych i zgodności za pomocą kontrolowanych przez klienta zasad tworzenia kopii zapasowych, które umożliwiają przechowywanie kopii zapasowych przez maksymalnie 10 lat. Dzięki temu masz szczegółową kontrolę nad zarządzaniem operacjami tworzenia kopii zapasowych i przywracania na poziomie poszczególnych baz danych. Podobnie można łatwo przywrócić dane w różnych wersjach bazy danych PostgreSQL lub w magazynie obiektów blob.

Aby uzyskać więcej informacji, zobacz [Azure Database for PostgreSQL kopii zapasowej](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Selektywne tworzenie i przywracanie kopii zapasowej dysku

Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (systemu operacyjnego i danych) na maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyn wirtualnych. Teraz przy użyciu funkcji tworzenia kopii zapasowej i przywracania dysków selektywnych można utworzyć kopię zapasową podzestawu dysków danych na maszynie wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie na potrzeby tworzenia kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera tylko dyski uwzględnione w operacji tworzenia kopii zapasowej.

Aby uzyskać więcej informacji, zobacz [Selektywne tworzenie kopii zapasowych dysków i ich przywracanie dla maszyn wirtualnych platformy Azure.](selective-disk-backup-restore.md)

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Przywracanie między regionami dla SQL Server i SAP HANA (w wersji zapoznawczej)

Wraz z wprowadzeniem przywracania między regionami można teraz inicjować przywracanie w regionie pomocniczym w celu ograniczenia rzeczywistych problemów z przestojami w regionie podstawowym dla środowiska. Dzięki temu przywracanie w regionie pomocniczym jest całkowicie kontrolowane przez klienta. Azure Backup do takich przywracania używane są dane kopii zapasowej replikowane do regionu pomocniczego.

Teraz oprócz obsługi przywracania między regionami dla maszyn wirtualnych platformy Azure funkcja została rozszerzona o przywracanie baz danych SQL i SAP HANA na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji, zobacz [Przywracanie między regionami dla baz danych SQL](restore-sql-database-azure-vm.md#cross-region-restore) i Przywracanie między [regionami dla SAP HANA baz danych](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Obsługa tworzenia kopii zapasowych maszyn wirtualnych z maksymalnie 32 dyskami

Do tej pory Azure Backup 16 dysków zarządzanych na maszynę wirtualną. Teraz Azure Backup kopie zapasowe maksymalnie 32 dysków zarządzanych na maszynę wirtualną.

Aby uzyskać więcej informacji, zobacz macierz [obsługi magazynu maszyn wirtualnych](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Prostsza konfiguracja kopii zapasowej dla bazy danych SQL na platformie Azure

Konfigurowanie kopii zapasowych dla maszyny SQL Server na maszynach wirtualnych platformy Azure jest teraz jeszcze łatwiejsze dzięki wbudowanej konfiguracji kopii zapasowej zintegrowanej z okienkiem maszyny wirtualnej Azure Portal. W zaledwie kilku krokach można włączyć tworzenie kopii zapasowej bazy SQL Server, aby chronić wszystkie istniejące bazy danych, a także te, które zostaną dodane w przyszłości.

Aby uzyskać więcej informacji, zobacz Back up a SQL Server from the VM pane (SQL Server back up a vm pane (SQL Server [kopii zapasowej maszyny wirtualnej w okienku maszyny wirtualnej).](backup-sql-server-vm-from-vm-pane.md)

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Tworzenie kopii SAP HANA na maszynach wirtualnych platformy Azure z system RHEL (w wersji zapoznawczej)

Azure Backup to natywne rozwiązanie do tworzenia kopii zapasowych dla platformy Azure i ma certyfikat BackInt firmy SAP. Azure Backup dodano obsługę systemu Red Hat Enterprise Linux (RHEL), jednego z najczęściej używanych systemów operacyjnych Linux z systemem SAP HANA.

Aby uzyskać więcej informacji, zobacz [macierz obsługi SAP HANA kopii zapasowej bazy danych.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Magazyn strefowo nadmiarowy (ZRS) dla danych kopii zapasowej (w wersji zapoznawczej)

Usługa Azure Storage zapewnia doskonałą równowagę między wysoką wydajnością, wysoką dostępnością i wysoką odpornością danych dzięki zróżnicowanym opcjam nadmiarowości. Azure Backup umożliwia również rozszerzenie tych korzyści na dane kopii zapasowej z opcjami przechowywania kopii zapasowych w magazynie lokalnie nadmiarowym (LRS) i magazynie geograficznie nadmiarowym (GRS). Teraz dostępne są dodatkowe opcje trwałości z dodaną obsługą magazynu strefowo nadmiarowego (ZRS).

Aby uzyskać więcej informacji, zobacz [Ustawianie nadmiarowości magazynu dla magazynu usługi Recovery Services.](backup-create-rs-vault.md#set-storage-redundancy)

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Usuwanie nie softowe SQL Server i SAP HANA obciążeń

Rośnie liczba obaw dotyczących problemów z zabezpieczeniami, takich jak złośliwe oprogramowanie, oprogramowanie wymuszające okup i włamanie. Te problemy z zabezpieczeniami mogą być kosztowne, zarówno pod względem pieniędzy, jak i danych. Aby chronić przed takimi atakami, Azure Backup funkcje zabezpieczeń, które pomagają chronić dane kopii zapasowej nawet po usunięciu.

Jedną z takich funkcji jest usuwanie nie soft. W przypadku usuwania nie programowego, nawet jeśli złośliwy aktor usunie kopię zapasową (lub dane kopii zapasowej zostaną przypadkowo usunięte), dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co umożliwia odzyskiwanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14-dniowe przechowywanie danych kopii zapasowej w stanie "usuwanie nie soft" nie powoduje ponoszenia żadnych kosztów.

Teraz, oprócz obsługi usuwania nie softowego dla maszyn wirtualnych platformy Azure, obciążenia SQL Server i SAP HANA na platformie Azure są również chronione za pomocą usuwania nie softowego.

Aby uzyskać więcej informacji, zobacz Usuwanie nie softowe dla programu SQL Server na maszynie wirtualnej [platformy Azure SAP HANA w obciążeniach maszyn wirtualnych platformy Azure.](soft-delete-sql-saphana-in-azure-vm.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Backup i najlepsze rozwiązania](guidance-best-practices.md)