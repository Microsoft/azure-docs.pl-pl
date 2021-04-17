---
title: Omówienie architektury
description: Zawiera omówienie architektury, składników i procesów używanych przez usługę Azure Backup service.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 6ecf01838b8fe3104626f8ada5f832c3f52dc378
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515910"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup architektury i składników

Możesz użyć usługi [Azure Backup,](backup-overview.md) aby utworzyć kopię zapasową danych na Microsoft Azure platformie w chmurze. Ten artykuł zawiera podsumowanie Azure Backup, składników i procesów.

## <a name="what-does-azure-backup-do"></a>Co Azure Backup zrobić?

Azure Backup kopii zapasowej danych, stanu maszyny i obciążeń uruchomionych na maszynach lokalnych i wystąpieniach maszyn wirtualnych platformy Azure. Istnieje wiele różnych Azure Backup scenariuszy.

## <a name="how-does-azure-backup-work"></a>Jak działa Azure Backup?

Kopię zapasową maszyn i danych można utworzyć przy użyciu kilku metod:

- **Kopii zapasowej maszyn lokalnych:**
  - Możesz utworzyć kopię zapasową lokalnych maszyn z systemem Windows bezpośrednio na platformie Azure przy użyciu agenta Azure Backup Microsoft Azure Recovery Services (MARS). Maszyny z systemem Linux nie są obsługiwane.
  - Kopie zapasowe maszyn lokalnych można tworzyć na serwerze kopii zapasowych — System Center Data Protection Manager (DPM) lub Microsoft Azure Backup Server (MABS). Następnie możesz utworzyć kopię zapasową serwera kopii zapasowych w magazynie usługi Recovery Services na platformie Azure.

- **Kopię zapasową maszyn wirtualnych platformy Azure:**
  - Możesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure bezpośrednio. Azure Backup instaluje rozszerzenie kopii zapasowej dla agenta maszyny wirtualnej platformy Azure uruchomionego na maszynie wirtualnej. To rozszerzenie zawiera kopię zapasową całej maszyny wirtualnej.
  - Możesz utworzyć kopię zapasową określonych plików i folderów na maszynie wirtualnej platformy Azure, uruchamiając agenta MARS.
  - Możesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure w usłudze MABS działającej na platformie Azure, a następnie utworzyć kopię zapasową usługi MABS w magazynie usługi Recovery Services.

Dowiedz się więcej na [temat kopii zapasowych i](backup-overview.md) obsługiwanych [scenariuszy tworzenia kopii zapasowych.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Gdzie jest kopii zapasowej danych?

Azure Backup dane kopii zapasowej są przechowywane w magazynach — magazynach usługi Recovery Services i magazynach kopii zapasowych. Magazyn to jednostka magazynu online na platformie Azure używana do przechowywania danych, takich jak kopie zapasowe, punkty odzyskiwania i zasady tworzenia kopii zapasowych.

Magazyny mają następujące funkcje:

- Magazyny ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zminimalizowaniu narzutów związanych z zarządzaniem.
- Możesz monitorować elementy kopii zapasowej w magazynie, w tym maszyny wirtualne platformy Azure i maszyny lokalne.
- Dostępem do magazynu można zarządzać za pomocą kontroli dostępu na podstawie ról [(RBAC) platformy Azure.](../role-based-access-control/role-assignments-portal.md)
- Określasz sposób replikowania danych w magazynie w celu zapewnienia nadmiarowości:
  - **Magazyn lokalnie nadmiarowy (LRS):** aby chronić przed awariami w centrum danych, można użyć magazynu LRS. Magazyn LRS replikuje dane do jednostki skalowania magazynu. [Dowiedz się więcej](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Magazyn geograficznie nadmiarowy (GRS):** aby chronić się przed erudycją w całym regionie, można użyć magazynu GRS. GrS replikuje dane do regionu pomocniczego. [Dowiedz się więcej](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Magazyn strefowo nadmiarowy (ZRS):** replikuje dane w strefach [dostępności,](../availability-zones/az-overview.md#availability-zones)co gwarantuje rezydencję i odporność danych w tym samym regionie. [Dowiedz się więcej](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Domyślnie magazyny usługi Recovery Services używają magazynu GRS.

Magazyny usługi Recovery Services mają następujące dodatkowe funkcje:

- W każdej subskrypcji platformy Azure można utworzyć maksymalnie 500 magazynów.

## <a name="backup-agents"></a>Agenci tworzenia kopii zapasowych

Azure Backup udostępnia różnych agentów kopii zapasowych w zależności od typu maszyny, dla której jest dawana kopia zapasowa:

**Agenta** | **Szczegóły**
--- | ---
**Agent MARS** | <ul><li>Działa na poszczególnych lokalnych maszynach z systemem Windows Server w celu kopii zapasowej plików, folderów i stanu systemu.</li> <li>Działa na maszynach wirtualnych platformy Azure w celu kopii zapasowej plików, folderów i stanu systemu.</li> <li>Działa na serwerach DPM/MABS w celu kopii zapasowej lokalnego dysku magazynu DPM/MABS na platformie Azure.</li></ul>
**Rozszerzenie maszyny wirtualnej platformy Azure** | Działa na maszynach wirtualnych platformy Azure w celu ich kopii zapasowej w magazynie.

## <a name="backup-types"></a>Typy kopii zapasowych

W poniższej tabeli wyjaśniono różne typy kopii zapasowych oraz czas ich korzystania:

**Typ kopii zapasowej** | **Szczegóły** | **Użycie**
--- | --- | ---
**Pełne** | Pełna kopia zapasowa zawiera całe źródło danych. Przyjmuje większą przepustowość sieci niż różnicowe lub przyrostowe kopie zapasowe. | Służy do tworzenia początkowej kopii zapasowej.
**Różnicowe** |  Różnicowa kopia zapasowa przechowuje bloki, które uległy zmianie od czasu tworzenia początkowej pełnej kopii zapasowej. Używa mniejszej ilości sieci i magazynu i nie przechowywać nadmiarowych kopii niezmienionych danych.<br/><br/> Nieefektywne, ponieważ bloki danych, które są niezmienione między późniejszymi kopiami zapasami, są transferowane i przechowywane. | Nie są używane przez Azure Backup.
**Przyrostowy** | Przyrostowa kopia zapasowa przechowuje tylko te bloki danych, które uległy zmianie od czasu poprzedniej kopii zapasowej. Wysoka wydajność magazynu i sieci. <br/><br/> Dzięki przyrostowej kopii zapasowej nie trzeba uzupełniać pełnych kopii zapasowych. | Używany przez program DPM/usługę MABS do tworzenia kopii zapasowych dysków i używany we wszystkich kopiach zapasowych na platformie Azure. Nie jest używany do tworzenia SQL Server kopii zapasowej.

## <a name="sql-server-backup-types"></a>SQL Server kopii zapasowych

W poniższej tabeli opisano różne typy kopii zapasowych używanych SQL Server baz danych oraz ich często spotykane czasy:

**Typ kopii zapasowej** | **Szczegóły** | **Użycie**
--- | --- | ---
**Pełna kopia zapasowa** | pełna kopia zapasowa bazy danych powoduje utworzenie kopii zapasowej całej bazy danych. Zawiera wszystkie dane w określonej bazie danych lub w zestawie grup plików lub plików. Pełna kopia zapasowa zawiera również wystarczającą ilość dzienników do odzyskania tych danych. | Można wyzwalać maksymalnie jedną pełną kopię zapasową dziennie.<br/><br/> Pełną kopię zapasową można tworzyć codziennie lub co tydzień.
**Różnicowa kopia zapasowa** | Różnicowa kopia zapasowa jest oparta na najnowszej, poprzedniej pełnej kopii zapasowej danych.<br/><br/> Przechwytuje tylko te dane, które zostały zmienione od momentu tworzenia pełnej kopii zapasowej. |  Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.<br/><br/> Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej w tym samym dniu.
**Kopia zapasowa dziennika transakcji** | kopia zapasowa dziennika umożliwia przywrócenie do określonego punktu w czasie z dokładnością do sekundy. | Maksymalnie można skonfigurować kopie zapasowych dziennika transakcji co 15 minut.

### <a name="comparison-of-backup-types"></a>Porównanie typów kopii zapasowych

Zużycie magazynu, cel czasu odzyskiwania (RTO) i zużycie sieci różnią się dla każdego typu kopii zapasowej. Na poniższej ilustracji przedstawiono porównanie typów kopii zapasowych:

- Źródło danych A składa się z 10 bloków magazynu A1–A10, których kopię zapasową tworzy się co miesiąc.
- Bloki A2, A3, A4 i A9 zmieniają się w trakcie pierwszego miesiąca, a blok A5 zmienia się w następnym miesiącu.
- W przypadku różnicowych kopii zapasowych w drugim miesiącu są tworzyć kopie zapasowe zmienionych bloków A2, A3, A4 i A9. W trzecim miesiącu jest ponownie wykonywana kopia zapasowa tych samych bloków, a dodatkowo zmienionego bloku A5. Kopie zapasowe zmienionych bloków będą ciągle wykonywane aż do czasu wykonania kolejnej pełnej kopii zapasowej.
- W przypadku przyrostowych kopii zapasowych w drugim miesiącu bloki A2, A3, A4 i A9 są oznaczane jako zmienione i przesyłane. W trzecim miesiącu jest oznaczany i transferowany jedynie zmieniony blok A5.

![Obraz przedstawiający porównania metod tworzenia kopii zapasowych](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkcje kopii zapasowej

W poniższej tabeli przedstawiono podsumowanie obsługiwanych funkcji dla różnych typów kopii zapasowych:

**Funkcja** | **Bezpośrednie tworzenie kopii zapasowej plików i folderów (przy użyciu agenta MARS)** | **Kopia zapasowa maszyny wirtualnej platformy Azure** | **Maszyny lub aplikacje z programem DPM/usługą MABS**
--- | --- | --- | ---
Back up to vault (Back up to vault) | ![Tak][green] | ![Tak][green] | ![Tak][green]
Back up to DPM/MABS disk, then to Azure | | | ![Tak][green]
Kompresowanie danych wysyłanych do kopii zapasowej | ![Tak][green] | Kompresja nie jest używana podczas przesyłania danych. Magazyn jest nieco napompowany, ale przywracanie jest szybsze.  | ![Tak][green]
Uruchamianie przyrostowej kopii zapasowej |![Tak][green] |![Tak][green] |![Tak][green]
Back up deduplicated disks (Kopię zapasową deduplikowanych dysków) | | | ![Częściowo][yellow]<br/><br/> Dotyczy serwerów DPM/MABS wdrożonych tylko lokalnie.

![Klucz tabeli](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Podstawowe informacje o zasadach kopii zapasowych

- Zasady tworzenia kopii zapasowych są tworzone dla każdego magazynu.
- Na potrzeby tworzenia kopii zapasowych można utworzyć zasady tworzenia kopii zapasowych następujących obciążeń: maszyn wirtualnych platformy Azure, bazy danych SQL na platformie Azure, maszyn wirtualnych platformy Azure SAP HANA maszyn wirtualnych platformy Azure i udziałów plików platformy Azure. Zasady tworzenia kopii zapasowej plików i folderów przy użyciu agenta MARS są określone w konsoli usługi MARS.
  - Udział plików platformy Azure
- Zasady można przypisać do wielu zasobów. Zasady tworzenia kopii zapasowych maszyn wirtualnych platformy Azure mogą służyć do ochrony wielu maszyn wirtualnych platformy Azure.
- Zasady składają się z dwóch składników
  - Harmonogram: Kiedy należy wykonać kopię zapasową
  - Przechowywanie: czas przechowywania każdej kopii zapasowej.
- Harmonogram można zdefiniować jako "codziennie" lub "co tydzień" z określonym punktem czasu.
- Przechowywanie można zdefiniować dla punktów kopii zapasowych "codziennie", "co tydzień", "co miesiąc", "co rok".
  - "co tydzień" oznacza kopię zapasową w określonym dniu tygodnia
  - "miesięczny" oznacza kopię zapasową w określonym dniu miesiąca
  - "yearly" odnosi się do kopii zapasowej w określonym dniu roku
- Przechowywanie "miesięcznych", "rocznie" punktów kopii zapasowych jest określane jako długoterminowe przechowywanie (LTR)
- Po utworzeniu magazynu jest również tworzona wartość "DefaultPolicy", która może służyć do tworzenia kopii zapasowych zasobów.
- Wszelkie zmiany wprowadzone w okresie przechowywania zasad kopii zapasowych zostaną wstecznie zastosowane do wszystkich starszych punktów odzyskiwania oprócz nowych.

### <a name="impact-of-policy-change-on-recovery-points"></a>Wpływ zmiany zasad na punkty odzyskiwania

- **Wydłużono/skrócono czas przechowywania:** Po zmianie czasu przechowywania nowy czas przechowywania jest stosowany również do istniejących punktów odzyskiwania. W związku z tym niektóre punkty odzyskiwania zostaną wyczyszczone. Jeśli okres przechowywania zostanie zwiększony, istniejące punkty odzyskiwania również będą mieć zwiększony okres przechowywania.
- **Zmieniono z codziennie na co tydzień:** Gdy zaplanowane kopie zapasowe są zmieniane z codziennego na tygodniowy, istniejące codzienne punkty odzyskiwania są czyszczone.
- **Zmieniono z cotygodniowej na dzienną:** Istniejące cotygodniowe kopie zapasowe zostaną zachowane na podstawie liczby pozostałych dni zgodnie z bieżącymi zasadami przechowywania.

### <a name="additional-reference"></a>Dodatkowa dokumentacja

- Maszyna wirtualna platformy Azure: jak [tworzyć i](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) [modyfikować](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) zasady.
- SQL Server na maszynie wirtualnej platformy Azure: jak [tworzyć i](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) [modyfikować](./manage-monitor-sql-database-backup.md#modify-policy) zasady.
- Udział plików platformy Azure: jak [tworzyć i](./backup-afs.md) [modyfikować](./manage-afs-backup.md#modify-policy) zasady.
- SAP HANA: Jak [tworzyć i](./backup-azure-sap-hana-database.md#create-a-backup-policy) [modyfikować](./sap-hana-db-manage.md#change-policy) zasady.
- MARS: jak [tworzyć i](./backup-windows-with-mars-agent.md#create-a-backup-policy) [modyfikować](./backup-azure-manage-mars.md#modify-a-backup-policy) zasady.
- [Czy istnieją jakiekolwiek ograniczenia dotyczące planowania kopii zapasowej na podstawie typu obciążenia?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Co się stanie z istniejącymi punktami odzyskiwania, jeśli zmienię zasady przechowywania?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Architektura: wbudowana kopia zapasowa maszyny wirtualnej platformy Azure

1. Po włączeniu tworzenia kopii zapasowej dla maszyny wirtualnej platformy Azure kopia zapasowa jest uruchamiana zgodnie z harmonogramem.
1. Podczas tworzenia pierwszej kopii zapasowej rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest instalowane rozszerzenie VMSnapshot.
    - W przypadku maszyn wirtualnych z systemem Linux jest instalowane rozszerzenie VMSnapshot systemu Linux.
1. Rozszerzenie pobiera migawkę na poziomie magazynu.
    - W przypadku maszyn wirtualnych z systemem Windows, które są uruchomione, usługa Backup koordynuje się z usługą Windows Usługa kopiowania woluminów w tle (VSS), aby utworzyć spójną na aplikacji migawkę maszyny wirtualnej. Domyślnie kopia zapasowa pobiera pełne kopie zapasowe usługi VSS. Jeśli kopia zapasowa nie może utworzyć migawki spójnej z aplikacją, jest tworzyć migawkę spójną na plikach.
    - W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa pobiera migawkę spójną na plikach. W przypadku migawek spójnych na aplikacji należy ręcznie dostosować skrypty wykonywane przed i po.
    - Tworzenie kopii zapasowej jest optymalizowane przez równoległe tworzenie kopii zapasowych każdego dysku maszyny wirtualnej. Dla każdego dysku, dla Azure Backup dane odczytuje bloki na dysku i przechowuje tylko zmienione dane.
1. Po zakończeniu tworzenia migawki dane są przesyłane do magazynu.
    - Kopiowane są tylko bloki danych, które uległy zmianie od czasu ostatniej kopii zapasowej.
    - Dane nie są szyfrowane. Azure Backup kopii zapasowej maszyn wirtualnych platformy Azure, które zostały zaszyfrowane przy użyciu Azure Disk Encryption.
    - Dane migawki mogą nie być natychmiast kopiowane do magazynu. W godzinach szczytu tworzenie kopii zapasowej może potrwać kilka godzin. W przypadku zasad codziennego tworzenia kopii zapasowych łączny czas tworzenia kopii zapasowej maszyny wirtualnej jest krótszy niż 24 godziny.
1. Po wysłaniu danych do magazynu tworzony jest punkt odzyskiwania. Domyślnie migawki są przechowywane przez dwa dni, zanim zostaną usunięte. Ta funkcja umożliwia operację przywracania z tych migawek, co pozwala na ograniczenie czasu przywracania. Skraca to czas wymagany do przekształcenia i skopiowania danych z magazynu. Zobacz [Azure Backup funkcji natychmiastowego przywracania.](./backup-instant-restore-capability.md)

Nie musisz jawnie zezwalać na łączność z Internetem, aby wrócić do kopii zapasowej maszyn wirtualnych platformy Azure.

![Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: bezpośrednie tworzenie kopii zapasowych lokalnych maszyn z systemem Windows Server lub plików lub folderów maszyn wirtualnych platformy Azure

1. Aby skonfigurować scenariusz, należy pobrać i zainstalować agenta MARS na maszynie. Następnie wybierz, co należy utworzyć, kiedy będą uruchamiane kopie zapasowe i jak długo będą przechowywane na platformie Azure.
1. Początkowa kopia zapasowa jest uruchamiana zgodnie z ustawieniami kopii zapasowej.
1. Agent MARS używa usługi VSS do utworzenia migawki woluminów wybranych do utworzenia kopii zapasowej.
    - Agent MARS używa tylko operacji zapisu systemu Windows do przechwycenia migawki.
    - Ponieważ agent nie używa żadnych autorzy usługi VSS aplikacji, nie przechwytuje migawek spójnych z aplikacją.
1. Po zrobieniu migawki za pomocą usługi VSS agent MARS tworzy wirtualny dysk twardy (VHD) w folderze pamięci podręcznej określonym podczas konfigurowania kopii zapasowej. Agent przechowuje również sumy kontrolne dla każdego bloku danych. Są one później używane do wykrywania zmienionych bloków dla kolejnych przyrostowych kopii zapasowych.
1. Przyrostowe kopie zapasowe są uruchamiane zgodnie z harmonogramem, chyba że jest uruchamiana kopia zapasowa na żądanie.
1. W przyrostowych kopiach zapasowych są identyfikowane zmienione pliki i tworzony jest nowy dysk VHD. Wirtualny dysk twardy jest kompresowany i szyfrowany, a następnie wysyłany do magazynu.
1. Po zakończeniu tworzenia przyrostowej kopii zapasowej nowy wirtualny dysk twardy jest scalony z dyskiem VHD utworzonym po replikacji początkowej. Ten scalony wirtualny dysk twardy zapewnia najnowszy stan, który będzie używany do porównywania bieżących kopii zapasowych.

![Tworzenie kopii zapasowych lokalnych maszyn z systemem Windows Server za pomocą agenta MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: back up to DPM/MABS (Architektura: back up to DPM/MABS)

1. Program DPM lub agenta ochrony mabs należy zainstalować na maszynach, które mają być chronioną usługą. Następnie należy dodać maszyny do grupy ochrony programu DPM.
    - Aby chronić maszyny lokalne, serwer programu DPM lub usługi MABS musi znajdować się w środowisku lokalnym.
    - Aby chronić maszyny wirtualne platformy Azure, serwer USŁUGI MABS musi znajdować się na platformie Azure i działać jako maszyna wirtualna platformy Azure.
    - Za pomocą programu DPM/usługi MABS można chronić woluminy kopii zapasowych, udziały, pliki i foldery kopii zapasowych. Można również chronić stan systemu komputera (bez systemu operacyjnego), a określone aplikacje można chronić za pomocą ustawień kopii zapasowych z wykrywaniem aplikacji.
1. Po skonfigurowaniu ochrony maszyny lub aplikacji w programie DPM/usłudze MABS należy wybrać opcję kopii zapasowej dysku lokalnego usługi MABS/programu DPM w celu przechowywania krótkoterminowego i ochrony online na platformie Azure. Należy również określić, kiedy należy uruchomić tworzenie kopii zapasowej w lokalnym magazynie programu DPM/usługi MABS oraz kiedy należy uruchomić tworzenie kopii zapasowej online na platformie Azure.
1. Kopię zapasową dysku chronionego obciążenia należy wrócić do lokalnych dysków MABS/DPM zgodnie z określonym harmonogramem.
1. Kopię zapasową dysków programu DPM/USŁUGI MABS w magazynie jest backup agenta MARS uruchomionego na serwerze DPM/MABS.

![Tworzenie kopii zapasowych maszyn i obciążeń chronionych przez program DPM lub usługę MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Magazyn maszyn wirtualnych platformy Azure

Maszyny wirtualne platformy Azure przechowują swoje systemy operacyjne, aplikacje i dane za pomocą dysków. Każda maszyna wirtualna platformy Azure ma co najmniej dwa dyski: dysk systemu operacyjnego i dysk tymczasowy. Maszyny wirtualne platformy Azure mogą również mieć dyski danych dla danych aplikacji. Dyski są przechowywane jako wirtualne dyski twarde.

- Wirtualne dyski twarde są przechowywane jako stronicowe obiekty blob na kontach magazynu w witrynie Azure w chmurze w witrynie Azure:
  - **Magazyn w standardowych magazynach:** Niezawodna i tańsza obsługa dysków dla maszyn wirtualnych z uruchomionymi obciążeniami, które nie są wrażliwe na opóźnienia. Magazyn w warstwie Standardowa może używać dysków SSD lub HDD w warstwie Standardowa.
  - **Premium Storage:** Obsługa dysków o wysokiej wydajności. Używa dysków SSD w warstwie Premium.
- Istnieją różne warstwy wydajności dla dysków:
  - **HDD w warstwie Standardowa dysku:** Dzięki dyskom HDD i jest używany do ekonomicznego magazynowania.
  - **SSD w warstwie Standardowa dysku:** Łączy elementy dysków SSD w warstwie Premium i dysków HDD w warstwie Standardowa. Oferuje bardziej spójną wydajność i niezawodność niż hdd, ale nadal ekonomiczne.
  - **SSD w warstwie Premium dysku:** Dzięki dyskom SSD zapewnia wysoką wydajność i małe opóźnienia dla maszyn wirtualnych, na których są uruchomione obciążenia intensywnie korzystające z operacji we/wy.
- Dyskami można zarządzać lub nie zarządzać nimi:
  - **Dyski niezadawani:** Tradycyjny typ dysków używanych przez maszyny wirtualne. W przypadku tych dysków należy utworzyć własne konto magazynu i określić je podczas tworzenia dysku. Następnie należy ustalić, jak zmaksymalizować zasoby magazynu dla maszyn wirtualnych.
  - **Dyski zarządzane:** Platforma Azure tworzy konta magazynu i zarządza nimi. Należy określić rozmiar dysku i warstwę wydajności, a platforma Azure utworzy dyski zarządzane. Podczas dodawania dysków i skalowania maszyn wirtualnych platforma Azure obsługuje konta magazynu.

Aby uzyskać więcej informacji o magazynie dyskowym i dostępnych typach dysków dla maszyn wirtualnych, zobacz następujące artykuły:

- [Dyski zarządzane platformy Azure dla maszyn wirtualnych z systemem Linux](../virtual-machines/managed-disks-overview.md)
- [Dostępne typy dysków dla maszyn wirtualnych](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Back up and restore Azure VMs with Premium storage (Przechowywanie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu usługi Premium Storage)

Możesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure przy użyciu magazynu w chmurze w chmurze w Azure Backup:

- Podczas tworzenia kopii zapasowych maszyn wirtualnych w usłudze Premium Storage usługa Backup tworzy tymczasową lokalizację o nazwie *AzureBackup-,* na koncie magazynu. Rozmiar lokalizacji przejściowej jest równy rozmiarowi migawki punktu odzyskiwania.
- Upewnij się, że konto magazynu w chmurze Premium ma odpowiednie wolne miejsce do przechowywania tymczasowej lokalizacji przejściowej. Aby uzyskać więcej informacji, zobacz [Cele skalowalności dla kont magazynu stronicowych obiektów blob w chmurze](../storage/blobs/scalability-targets-premium-page-blobs.md)Premium. Nie należy modyfikować lokalizacji przejściowej.
- Po zakończeniu zadania tworzenia kopii zapasowej lokalizacja przemieszczania zostanie usunięta.
- Cena magazynu używanego dla lokalizacji przejściowej jest zgodna z [cennikiem usługi Premium Storage.](../virtual-machines/disks-types.md#billing)

Po przywróceniu maszyn wirtualnych platformy Azure przy użyciu magazynu w witrynie Premium Storage można przywrócić je do magazynu w chmurze Premium lub Standardowa. Zazwyczaj należy przywrócić je do magazynu w chmurze Premium. Jeśli jednak potrzebujesz tylko podzestawu plików z maszyny wirtualnej, przywrócenie ich do magazynu w standardowych magazynach może okazać się opłacalne.

### <a name="back-up-and-restore-managed-disks"></a>Kopii zapasowej i przywracania dysków zarządzanych

Możesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure przy użyciu dysków zarządzanych:

- Kopię zapasową maszyn wirtualnych z dyskami zarządzanymi można wykonać w taki sam sposób, jak każdą inną maszynę wirtualną platformy Azure. Kopię zapasową maszyny wirtualnej można utworzyć bezpośrednio z ustawień maszyny wirtualnej lub włączyć tworzenie kopii zapasowych maszyn wirtualnych w magazynie usługi Recovery Services.
- Kopie zapasowe maszyn wirtualnych możesz tworzyć na dyskach zarządzanych za pomocą kolekcji RestorePoint tworzonych na tych dyskach.
- Azure Backup obsługuje również kopię zapasową maszyn wirtualnych z dyskami zarządzanymi, które zostały zaszyfrowane przy użyciu Azure Disk Encryption.

Podczas przywracania maszyn wirtualnych za pomocą dysków zarządzanych można przywrócić do pełnej maszyny wirtualnej z dyskami zarządzanymi lub do konta magazynu:

- Podczas procesu przywracania platforma Azure obsługuje dyski zarządzane. Jeśli używasz opcji konta magazynu, możesz zarządzać kontem magazynu utworzonym podczas procesu przywracania.
- W przypadku przywracania zaszyfrowanej zarządzanej maszyny wirtualnej upewnij się, że klucze i wpisy tajne maszyny wirtualnej istnieją w magazynie kluczy przed rozpoczęciem procesu przywracania.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z macierzą [obsługi, aby dowiedzieć się więcej o obsługiwanych funkcjach i ograniczeniach dotyczących scenariuszy tworzenia kopii zapasowych.](backup-support-matrix.md)
- Skonfiguruj kopię zapasową dla jednego z tych scenariuszy:
  - [Kopię zapasową maszyn wirtualnych platformy Azure.](backup-azure-arm-vms-prepare.md)
  - [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
  - [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
  - [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
