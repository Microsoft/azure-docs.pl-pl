---
title: Odpowiedzi na często zadawane pytania
description: 'Odpowiedzi na typowe pytania dotyczące funkcji usługi Azure Backup, w tym magazynów usług Recovery Services, elementów, których kopie zapasowe można tworzyć, sposobu działania, szyfrowania i ograniczeń. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: d85866e490b2c56abb7de1e94cd0ffaa8f714615
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327155"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Backup.

## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Czy istnieje ograniczenie liczby magazynów, które można utworzyć w poszczególnych subskrypcjach platformy Azure?

Tak. Maksymalnie można utworzyć 500 magazynów usługi Recovery Services na obsługiwany region usługi Azure Backup dla każdej subskrypcji. Jeśli potrzebna jest większa liczba magazynów, należy utworzyć dodatkową subskrypcję.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Czy istnieją ograniczenia dotyczące liczby serwerów/maszyn, które można zarejestrować w każdym magazynie?

W magazynie można zarejestrować maksymalnie 1000 maszyn wirtualnych platformy Azure. Jeśli używasz agenta Microsoft Azure Backup, możesz zarejestrować do 50 agentów MARS na magazyn. Można też 50 zarejestrować serwery serwera usługi MAB Server/serwery programu DPM w magazynie.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Ile źródeł danych / elementów można objąć ochroną w magazynie?

Można chronić maksymalnie 2000 źródeł danych/elementów we wszystkich obciążeniach (takich jak IaaS VM, SQL, AFS) w magazynie.
Jeśli na przykład masz już chronione 500 maszyn wirtualnych i 400 Azure Files w magazynie, możesz chronić tylko do 1100 baz danych SQL.

### <a name="how-many-policies-can-i-create-per-vault"></a>Ile zasad można utworzyć dla danego magazynu?

Dla danego magazynu można mieć maksymalnie 200 zasad.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Jeśli organizacja ma jeden magazyn, to w jaki sposób można odizolować dane z różnych serwerów w magazynie podczas przywracania danych?

Dane serwera, które mają zostać odzyskane, powinny używać tego samego hasła podczas konfigurowania kopii zapasowej. Jeśli chcesz odizolować odzyskiwanie do określonego serwera lub serwerów, użyj hasła tylko dla tego serwera lub serwerów. Na przykład serwery zarządzania zasobami ludzkimi mogą korzystać z jednego hasła szyfrowania, serwery księgowości z drugiego, a serwery pamięci masowej z trzeciego.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Czy można przenieść magazyn między subskrypcjami?

Tak. Aby przenieść magazyn usług Recovery Services, należy zapoznać się z tym [artykułem](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Czy można przenieść dane kopii zapasowej do innego magazynu?

Nie. Danych kopii zapasowej przechowywanych w magazynie nie można przenieść do innego magazynu.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Czy mogę zmienić ustawienie nadmiarowości magazynu po utworzeniu kopii zapasowej?

Domyślnie typ replikacji magazynu to magazyn Geograficznie nadmiarowy (GRS). Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona i nie można jej zmienić.

![Typ replikacji magazynu](./media/backup-azure-backup-faq/storage-replication-type.png)

Jeśli kopia zapasowa została już skonfigurowana i musi zostać przeniesiona z GRS do LRS, zobacz [jak zmienić z GRS na LRS po skonfigurowaniu kopii zapasowej](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup).

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Czy można wykonać przywracanie na poziomie elementu (ILR) dla maszyn wirtualnych, których kopia zapasowa jest wykonywana w magazynie usług Recovery Services?

- Przywracanie na poziomie elementu jest obsługiwane w przypadku maszyn wirtualnych platformy Azure, których kopia zapasowa jest wykonywana przez kopię zapasową maszyny wirtualnej platformy Azure. Więcej informacji znajduje się w tym [artykule](backup-azure-restore-files-from-vm.md)
- ILR nie jest obsługiwana dla punktów odzyskiwania online lokalnych maszyn wirtualnych, których kopia zapasowa jest wykonywana przez Azure Backup Server (serwera usługi MAB) lub System Center DPM.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Jak przenieść dane z magazynu Recovery Services do lokalnego?

Eksportowanie danych bezpośrednio z magazynu Recovery Services do lokalnego przy użyciu urządzenie Data Box nie jest obsługiwane. Dane muszą zostać przywrócone do konta magazynu, a następnie można je przenieść do lokalizacji lokalnej za pośrednictwem [urządzenie Data Box](../databox/data-box-overview.md) lub [zaimportować/wyeksportować](../storage/common/storage-import-export-service.md).

## <a name="azure-backup-agent"></a>Agent usługi Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Gdzie można znaleźć często zadawane pytania dotyczące agenta usługi Azure Backup dla kopii zapasowej maszyny wirtualnej platformy Azure?

- W przypadku agenta działającego na maszynach wirtualnych platformy Azure przeczytaj te [często zadawane pytania](backup-azure-vm-backup-faq.md).
- W przypadku agenta służącego do tworzenia kopii zapasowych folderów plików platformy Azure, przeczytaj te [często zadawane pytania](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Ogólne tworzenie kopii zapasowych

### <a name="are-there-limits-on-backup-scheduling"></a>Czy istnieją ograniczenia dotyczące planowania tworzenia kopii zapasowych?

Tak.

- Kopię zapasową maszyn z systemami Windows Server lub Windows można tworzyć maksymalnie 3 razy dziennie. W zasadach planowania można ustawić dzienne lub tygodniowe harmonogramy.
- Kopię zapasową programu DPM można tworzyć maksymalnie dwa razy dziennie. W zasadach planowania można ustawić dzienne, tygodniowe, miesięczne lub roczne harmonogramy.
- Kopie zapasowe maszyn wirtualnych platformy Azure wykonuje się codziennie.

### <a name="what-operating-systems-are-supported-for-backup"></a>Które systemy operacyjne są obsługiwane na potrzeby kopii zapasowych?

Usługa Azure Backup obsługuje następujące systemy operacyjne w przypadku tworzenia kopii zapasowych plików i folderów oraz aplikacji chronionych przy użyciu serwera usługi Azure Backup i programu DPM.

**System operacyjny** | **SKU** | **Szczegóły**
--- | --- | ---
Stacja robocza | |
Windows 10 (wersja 64-bitowa) | Enterprise, Pro, Home | Na maszynach powinny być uruchomione najnowsze dodatki Service Pack i aktualizacje.
Windows 8.1 (wersja 64-bitowa) | Enterprise, Pro | Na maszynach powinny być uruchomione najnowsze dodatki Service Pack i aktualizacje.
Windows 8 (wersja 64-bitowa) | Enterprise, Pro | Na maszynach powinny być uruchomione najnowsze dodatki Service Pack i aktualizacje.
Windows 7 (wersja 64-bitowa) | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Na maszynach powinny być uruchomione najnowsze dodatki Service Pack i aktualizacje.
Serwer | |
Windows Server 2019 (wersja 64-bitowa) | Standard, Datacenter, Essentials | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Server 2016 (wersja 64-bitowa) | Standard, Datacenter, Essentials | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Server 2012 R2 (wersja 64-bitowa) | Standard, Datacenter, Foundation | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Server 2012 (wersja 64-bitowa) | Datacenter, Foundation, Standard | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Storage Server 2016 (wersja 64-bitowa) | Standard, Workgroup | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Storage Server 2012 R2 (wersja 64-bitowa) | Standard, Workgroup, Essential | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Storage Server 2012 (wersja 64-bitowa) | Standard, Workgroup | Z najnowszymi dodatkami Service Pack / aktualizacjami.
Windows Server 2008 R2 z dodatkiem SP1 (wersja 64-bitowa) | Standard, Enterprise, Datacenter, Foundation | Z najnowszymi aktualizacjami.
Windows Server 2008 (wersja 64-bitowa) | Standard, Enterprise, Datacenter | Z najnowszymi aktualizacjami.

Usługa Azure Backup nie obsługuje 32-bitowych systemów operacyjnych.

W przypadku kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux usługa Azure Backup obsługuje [następujące dystrybucje zalecane dla platformy Azure](../virtual-machines/linux/endorsed-distros.md), z wyjątkiem systemu Core OS Linux i 32-bitowych systemów operacyjnych. W przypadku pozostałych dystrybucji opartych na modelu „Bring Your Own Linux” usługa Azure Backup powinna działać, jeśli na maszynie wirtualnej jest dostępny agent maszyny wirtualnej oraz jest obsługiwany język Python.

### <a name="are-there-size-limits-for-data-backup"></a>Czy istnieją limity dotyczące rozmiaru danych kopii zapasowej?

Limity dotyczące rozmiaru są następujące:

System operacyjny / maszyna | Limit rozmiaru źródła danych
--- | ---
Windows 8 lub nowszy | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 lub nowszy | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Maszyna wirtualna platformy Azure | Zobacz [Macierz obsługi kopii zapasowej maszyny wirtualnej platformy Azure](./backup-support-matrix-iaas.md#vm-storage-support)

### <a name="how-is-the-data-source-size-determined"></a>W jaki sposób jest określany rozmiar źródła danych?

W poniższej tabeli opisano sposób ustalania rozmiaru dla każdego źródła danych.

**Źródło danych** | **Szczegóły**
--- | ---
Wolumin |Ilość danych kopii zapasowej z jednego woluminu maszyny wirtualnej, którego kopia zapasowa jest wykonywana.
Baza danych programu SQL Server |Rozmiar pojedynczej bazy danych, której kopia zapasowa jest tworzona.
SharePoint | Suma baz danych z zawartością i danymi konfiguracyjnymi w farmie programu SharePoint, której kopia zapasowa jest wykonywana.
Exchange |Suma wszystkich baz danych programu Exchange w serwerze Exchange, którego kopia zapasowa jest wykonywana.
Stan systemu/BMR |Każda pojedyncza kopia BMR lub stanu systemu komputera, którego kopia zapasowa jest wykonywana.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Czy istnieje ograniczenie ilości danych kopii zapasowej utworzonej przy użyciu magazynu usługi Recovery Services?

Nie ma limitu łącznej ilości danych, których kopię zapasową można utworzyć przy użyciu magazynu Recovery Servicesowego. Poszczególne źródła danych (inne niż maszyny wirtualne platformy Azure) mogą mieć maksymalnie 54 400 GB pamięci. Aby uzyskać więcej informacji na temat limitów, zobacz [sekcję limity magazynu w macierzy obsługi](./backup-support-matrix.md#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Dlaczego rozmiar danych przesyłanych do magazynu usługi Recovery Services jest mniejszy niż rozmiar danych wybranych do utworzenia kopii zapasowej?

Dane, których kopie zapasowe są wykonywane przy użyciu agenta usługi Azure Backup, programu DPM lub serwera usługi Azure Backup, są przed przesłaniem kompresowane i szyfrowane. Po zastosowaniu kompresji i szyfrowania dane w magazynie są mniejsze o 30–40%.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Czy można usunąć poszczególne pliki z punktu odzyskiwania w magazynie?

Nie, usługa Azure Backup nie obsługuje usuwania ani czyszczenia poszczególnych elementów z przechowywanych kopii zapasowych.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Czy w przypadku anulowania uruchomionego zadania tworzenia kopii zapasowej przesyłane dane zostaną usunięte?

Nie. Wszystkie dane, które przesłano do magazynu przed momentem anulowania zadania utworzenia kopii zapasowej, pozostaną w magazynie.

- Usługa Azure Backup używa mechanizmu, który podczas tworzenia kopii zapasowej co pewien czas dodaje punkty kontrolne do danych kopii zapasowej.
- Ponieważ w danych kopii zapasowej umieszczone są punkty kontrolne, następny proces kopii zapasowej może sprawdzić integralność plików.
- Następnym zadaniem kopii zapasowej będzie przyrostowa kopia zapasowa tworzona w oparciu o wcześniej utworzoną kopię zapasową danych. Przyrostowe kopie zapasowe przesyłają tylko nowe lub zmienione dane, dzięki czemu zapewnia się lepsze wykorzystanie przepustowości.

Jeśli anulujesz zadanie kopii zapasowej dla maszyny wirtualnej platformy Azure, wszelkie przesłane dane zostaną zignorowane. Następne zadanie kopii zapasowej przesyła przyrostowe dane z ostatniego wykonanego zadania kopii zapasowej.

## <a name="retention-and-recovery"></a>Przechowywanie i odzyskiwanie

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Czy zasady przechowywania dla maszyn z programem DPM oraz z systemem Windows i bez programu DPM są takie same?

Tak, oba rodzaje maszyn mają dzienne, tygodniowe, miesięczne i roczne zasady przechowywania.

### <a name="can-i-customize-retention-policies"></a>Czy można dostosować zasady przechowywania?

Tak, zasady można dostosować. Można na przykład skonfigurować wymagania dotyczące przechowywania tygodniowego i dziennego, ale nie rocznego i miesięcznego.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Czy można używać różnych godzin dla zasad przechowywania i planowania kopii zapasowych?

Nie. Zasady przechowywania mogą być stosowane wyłącznie w punktach kopii zapasowej. Na przykład ten obraz przedstawia zasady przechowywania dla kopii zapasowych wykonywanych o godz. 0:00 i 18:00.

![Harmonogram tworzenia kopii zapasowej i przechowywania](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Czy w przypadku, gdy kopia zapasowa przechowywana jest przez długi czas, odzyskanie danych z wcześniejszego punktu danych trwa dłużej?

Nie. Czas odzyskania najstarszego i najnowszego punktu jest taki sam. Każdy punkt odzyskiwania zachowuje się jak pełny punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Skoro każdy punkt odzyskiwania jest traktowany jak pełny punkt, czy ma to wpływ na całkowitą fakturowaną przestrzeń dyskową dla kopii zapasowych?

Typowe produkty punktów długoterminowego przechowywania przechowują dane kopii zapasowych jako pełne punkty.

- Pełne punkty są *nieefektywne* z punktu widzenia magazynu, ale są łatwiejsze i szybsze do przywrócenia.
- Przyrostowe kopie są *efektywne* z punktu widzenia magazynu, ale wymagają przywrócenia łańcucha danych, co wpływa na czas odzyskiwania

Architektura magazynu usługi Azure Backup oferuje zalety obu rozwiązań dzięki optymalnemu przechowywaniu danych, umożliwiającemu szybkie ich przywrócenie, oraz niskim kosztom magazynowania. Takie podejście zapewnia efektywne wykorzystanie przepustowości ruchu przychodzącego i wychodzącego. Ilość pamięci masowej oraz czas potrzebny do odzyskania danych są ograniczone do minimum. Dowiedz się więcej o [przyrostowych kopiach zapasowych](backup-architecture.md#backup-types).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Czy istnieje ograniczenie liczby punktów odzyskiwania, które można utworzyć?

Możesz utworzyć maksymalnie 9999 punktów odzyskiwania na każde pojedyncze chronione wystąpienie. Chronione wystąpienie to komputer, serwer (fizyczny lub wirtualny) lub obciążenie, którego kopia zapasowa jest wykonywana na platformie Azure.

- Dowiedz się więcej o [wykonywaniu kopii zapasowej i przechowywaniu](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Ile razy można odzyskać dane, których kopia zapasowa jest wykonywana na platformie Azure?

Nie ma żadnego limitu liczby operacji odzyskiwania z Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Czy podczas przywracania danych należy zapłacić za ruch wychodzący z platformy Azure?

Nie. Odzyskiwanie jest bezpłatne i nie są naliczane opłaty za ruch wyjściowy.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co się stanie po zmianie zasad tworzenia kopii zapasowych?

Po zastosowaniu nowych zasad będzie obowiązywał harmonogram i okres przechowywania określony przez te nowe zasady.

- Jeśli przechowywanie zostanie rozszerzone, istniejące punkty odzyskiwania zostaną oznaczone, aby zachować je zgodnie z nowymi zasadami.
- W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Jak długo dane są zachowywane podczas zatrzymywania tworzenia kopii zapasowych, ale wybierając opcję zachowania danych kopii zapasowej?

Gdy kopie zapasowe są zatrzymane, a dane są zachowywane, istniejące reguły zasad na potrzeby oczyszczania danych przestaną obowiązywać, a dane będą przechowywane w nieskończoność do momentu zainicjowania przez administratora do usunięcia.

## <a name="encryption"></a>Szyfrowanie

### <a name="is-the-data-sent-to-azure-encrypted"></a>Czy dane wysyłane do platformy Azure są szyfrowane?

Tak. Dane są szyfrowane na maszynie lokalnej przy użyciu standardu AES256. Dane są przesyłane za pośrednictwem bezpiecznego linku HTTPS. Dane przesyłane w chmurze są chronione za pośrednictwem linku HTTPS tylko między magazynem a usługą odzyskiwania. Protokół iSCSI zabezpiecza dane przesyłane między usługą odzyskiwania a maszyną użytkownika. Kanał iSCSI jest chroniony przy użyciu bezpiecznego tunelowania.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Czy dane kopii zapasowej na platformie Azure są również szyfrowane?

Tak. Dane na platformie Azure są szyfrowane w spoczynku.

- W przypadku kopii zapasowych w środowisku lokalnym szyfrowanie danych w spoczynku odbywa się przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure.
- W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w spoczynku przy użyciu szyfrowania usługi Storage (SSE).

Firma Microsoft nie odszyfrowuje danych kopii zapasowej w dowolnym momencie.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Jaka jest minimalna długość klucza szyfrowania używanego do szyfrowania danych kopii zapasowej?

Klucz szyfrowania używany przez agenta Microsoft Azure Recovery Services (MARS) pochodzi od hasła, którego długość powinna wynosić co najmniej 16 znaków. W przypadku maszyn wirtualnych platformy Azure nie ma limitu długości kluczy używanych przez usługę Azure Keys.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co się stanie, jeśli klucz szyfrowania zostanie zgubiony przez użytkownika? Czy można odzyskać dane? Czy firma Microsoft może odzyskać dane?

Klucz używany do szyfrowania danych kopii zapasowej znajduje się tylko w Twojej lokacji. Firma Microsoft nie zachowuje kopii na platformie Azure i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

- [Często zadawane pytania](backup-azure-vm-backup-faq.md) dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.
- [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup