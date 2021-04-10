---
title: Omówienie architektury
description: Zawiera omówienie architektury, składników i procesów używanych przez usługę Azure Backup.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1e5a61bd4e3287c1100ff1f54fda797c1add438b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466415"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup architektura i składniki

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe danych na platformie Microsoft Azure w chmurze. Ten artykuł zawiera podsumowanie architektury, składników i procesów Azure Backup.

## <a name="what-does-azure-backup-do"></a>Co robią Azure Backup?

Azure Backup kopii zapasowych danych, stanu komputera i obciążeń uruchomionych na maszynach lokalnych i wystąpieniach maszyn wirtualnych platformy Azure. Istnieje kilka Azure Backup scenariuszy.

## <a name="how-does-azure-backup-work"></a>Jak działa Azure Backup?

Można utworzyć kopię zapasową maszyn i danych przy użyciu szeregu metod:

- **Tworzenie kopii zapasowych maszyn lokalnych**:
  - Można utworzyć kopię zapasową lokalnych maszyn z systemem Windows bezpośrednio na platformie Azure przy użyciu agenta Azure Backup Microsoft Azure Recovery Services (MARS). Maszyny z systemem Linux nie są obsługiwane.
  - Można tworzyć kopie zapasowe maszyn lokalnych na serwerze kopii zapasowych — w programie System Center Data Protection Manager (DPM) lub Microsoft Azure Backup Server (serwera usługi MAB). Następnie można wykonać kopię zapasową serwera kopii zapasowej w magazynie Recovery Services na platformie Azure.

- **Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure**:
  - Możesz bezpośrednio tworzyć kopie zapasowe maszyn wirtualnych platformy Azure. Azure Backup instaluje rozszerzenie kopii zapasowej dla agenta maszyny wirtualnej platformy Azure uruchomionego na maszynie wirtualnej. To rozszerzenie tworzy kopię zapasową całej maszyny wirtualnej.
  - Można utworzyć kopię zapasową określonych plików i folderów na maszynie wirtualnej platformy Azure, uruchamiając agenta MARS.
  - Można utworzyć kopię zapasową maszyn wirtualnych platformy Azure w serwera usługi MAB, który działa na platformie Azure, a następnie utworzyć kopię zapasową serwera usługi MAB do magazynu Recovery Services.

Dowiedz się więcej o [możliwościach tworzenia kopii zapasowych](backup-overview.md) i o [obsługiwanych scenariuszach tworzenia kopii zapasowych](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Gdzie są tworzone kopie zapasowe danych?

Azure Backup przechowuje kopie zapasowe danych w magazynach — Recovery Services magazyny i magazyny kopii zapasowych. Magazyn jest jednostką online magazynu na platformie Azure, która jest używana do przechowywania danych, takich jak kopie zapasowe, punkty odzyskiwania i zasady tworzenia kopii zapasowych.

Magazyny mają następujące funkcje:

- Magazyny ułatwiają organizowanie danych kopii zapasowej, przy jednoczesnym zminimalizowaniu obciążeń związanych z zarządzaniem.
- Możesz monitorować elementy kopii zapasowej w magazynie, w tym maszyn wirtualnych platformy Azure i maszynach lokalnych.
- Możesz zarządzać dostępem do magazynu za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
- Określ sposób replikowania danych w magazynie w celu zapewnienia nadmiarowości:
  - **Magazyn lokalnie nadmiarowy (LRS)**: aby chronić przed awarią w centrum danych, można użyć LRS. LRS replikuje dane do jednostki skalowania magazynu. [Dowiedz się więcej](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Magazyn Geograficznie nadmiarowy (GRS)**: aby chronić przed awarią całego regionu, możesz użyć GRS. GRS replikuje dane do regionu pomocniczego. [Dowiedz się więcej](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Magazyn strefowo nadmiarowy (ZRS)**: replikuje dane w [strefach dostępności](../availability-zones/az-overview.md#availability-zones), gwarantując miejsce zamieszkania i odporność danych w tym samym regionie. [Dowiedz się więcej](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Domyślnie magazyny Recovery Services używają GRS.

Recovery Services magazyny mają następujące dodatkowe funkcje:

- W każdej subskrypcji platformy Azure można utworzyć maksymalnie 500 magazynów.

## <a name="backup-agents"></a>Agenci tworzenia kopii zapasowych

Azure Backup udostępnia różnych agentów kopii zapasowych, w zależności od tego, jakiego typu maszyny jest tworzona kopia zapasowa:

**Odczynnik** | **Szczegóły**
--- | ---
**Agent MARS** | <ul><li>Działa na pojedynczych lokalnych maszynach z systemem Windows Server, aby utworzyć kopię zapasową plików, folderów i stanu systemu.</li> <li>Działa na maszynach wirtualnych platformy Azure, aby utworzyć kopię zapasową plików, folderów i stanu systemu.</li> <li>Działa na serwerach DPM/serwera usługi MAB, aby utworzyć kopię zapasową lokalnego dysku magazynu programu DPM/serwera usługi MAB na platformie Azure.</li></ul>
**Rozszerzenie maszyny wirtualnej platformy Azure** | Działa na maszynach wirtualnych platformy Azure w celu tworzenia kopii zapasowych w magazynie.

## <a name="backup-types"></a>Typy kopii zapasowych

W poniższej tabeli objaśniono różne typy kopii zapasowych i używane:

**Typ kopii zapasowej** | **Szczegóły** | **Użycie**
--- | --- | ---
**Pełne** | Pełna kopia zapasowa zawiera całe źródło danych. Zwiększa przepustowość sieci niż różnicowe lub przyrostowe kopie zapasowe. | Używany do początkowej kopii zapasowej.
**Różnicy** |  Różnicowa kopia zapasowa przechowuje bloki, które uległy zmianie od początkowej pełnej kopii zapasowej. Program używa mniejszej ilości sieci i magazynu i nie zachowuje nadmiarowych kopii niezmienionych danych.<br/><br/> Niewydajne, ponieważ bloki danych, które nie są zmieniane między nowszymi kopiami zapasowymi, są transferowane i przechowywane. | Nieużywane przez Azure Backup.
**Przyrostowy** | Przyrostowa kopia zapasowa przechowuje tylko te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej. Wysoka wydajność magazynu i sieci. <br/><br/> Dzięki przyrostowym kopiom zapasowym nie trzeba uzupełniać z pełnymi kopiami zapasowymi. | Używane przez program DPM/serwera usługi MAB do tworzenia kopii zapasowych na dyskach i używane we wszystkich kopiach zapasowych na platformie Azure. Nieużywany do SQL Server kopii zapasowej.

## <a name="sql-server-backup-types"></a>SQL Server typy kopii zapasowych

W poniższej tabeli objaśniono różne typy kopii zapasowych używanych dla SQL Server baz danych i częstotliwości ich używania:

**Typ kopii zapasowej** | **Szczegóły** | **Użycie**
--- | --- | ---
**Pełna kopia zapasowa** | pełna kopia zapasowa bazy danych powoduje utworzenie kopii zapasowej całej bazy danych. Zawiera wszystkie dane z konkretnej bazy danych lub zestawu grup plików lub plików. Pełna kopia zapasowa zawiera również wystarczającą liczbę dzienników do odzyskania tych danych. | Można wyzwalać maksymalnie jedną pełną kopię zapasową dziennie.<br/><br/> Można utworzyć pełną kopię zapasową w ciągu dziennym lub tygodniowym.
**Różnicowa kopia zapasowa** | Różnicowa kopia zapasowa jest oparta na najnowszej, poprzedniej kopii zapasowej danych pełnych.<br/><br/> Przechwytuje tylko te dane, które zostały zmienione od czasu pełnej kopii zapasowej. |  Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.<br/><br/> Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej w tym samym dniu.
**Kopia zapasowa dziennika transakcji** | kopia zapasowa dziennika umożliwia przywrócenie do określonego punktu w czasie z dokładnością do sekundy. | Maksymalnie można skonfigurować kopie zapasowych dziennika transakcji co 15 minut.

### <a name="comparison-of-backup-types"></a>Porównanie typów kopii zapasowych

Użycie magazynu, cel czasu odzyskiwania (RTO) i użycie sieci różnią się w zależności od typu kopii zapasowej. Na poniższej ilustracji przedstawiono porównanie typów kopii zapasowych:

- Źródło danych A składa się z 10 bloków magazynu, A1-A10, których kopia zapasowa jest wykonywana miesięcznie.
- Bloki A2, A3, A4 i A9 zmieniają się w trakcie pierwszego miesiąca, a blok A5 zmienia się w następnym miesiącu.
- W przypadku różnicowych kopii zapasowych w drugim miesiącu tworzone są kopie zapasowe bloków a2, A3, A4 i A9. W trzecim miesiącu jest ponownie wykonywana kopia zapasowa tych samych bloków, a dodatkowo zmienionego bloku A5. Kopie zapasowe zmienionych bloków będą ciągle wykonywane aż do czasu wykonania kolejnej pełnej kopii zapasowej.
- W przypadku przyrostowych kopii zapasowych w drugim miesiącu bloki a2, A3, A4 i A9 są oznaczane jako zmienione i transferowane. W trzecim miesiącu jest oznaczany i transferowany jedynie zmieniony blok A5.

![Obraz przedstawiający Porównanie metod tworzenia kopii zapasowej](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funkcje kopii zapasowej

Poniższa tabela zawiera podsumowanie obsługiwanych funkcji dla różnych typów kopii zapasowych:

**Funkcja** | **Bezpośrednie tworzenie kopii zapasowych plików i folderów (przy użyciu agenta MARS)** | **Kopia zapasowa maszyny wirtualnej platformy Azure** | **Maszyny lub aplikacje z programem DPM/serwera usługi MAB**
--- | --- | --- | ---
Tworzenie kopii zapasowej w magazynie | ![Tak][green] | ![Tak][green] | ![Tak][green]
Utwórz kopię zapasową na dysku programu DPM/serwera usługi MAB, a następnie na platformie Azure | | | ![Tak][green]
Kompresuj dane wysyłane do kopii zapasowej | ![Tak][green] | Podczas przesyłania danych nie jest używana kompresja. Magazyn jest nieco nieznacznie napompowany, ale przywracanie jest szybsze.  | ![Tak][green]
Uruchom przyrostową kopię zapasową |![Tak][green] |![Tak][green] |![Tak][green]
Tworzenie kopii zapasowej deduplikowanych dysków | | | ![Częściowo][yellow]<br/><br/> W przypadku serwerów DPM/serwera usługi MAB wdrożonych tylko lokalnie.

![Klucz tabeli](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Podstawy zasad tworzenia kopii zapasowych

- Tworzone są zasady tworzenia kopii zapasowych na magazyn.
- Zasady tworzenia kopii zapasowych można utworzyć dla kopii zapasowych następujących obciążeń: maszyny wirtualne platformy Azure, SQL na maszynach wirtualnych platformy Azure, SAP HANA na maszynach wirtualnych platformy Azure i udziały plików platformy Azure. Zasady tworzenia kopii zapasowych plików i folderów przy użyciu agenta MARS są określone w konsoli MARS.
  - Udział plików platformy Azure
- Zasady mogą być przypisane do wielu zasobów. Zasady kopii zapasowych maszyny wirtualnej platformy Azure mogą służyć do ochrony wielu maszyn wirtualnych platformy Azure.
- Zasady składają się z dwóch składników
  - Harmonogram: Kiedy należy wykonać kopię zapasową
  - Przechowywanie: czas przechowywania poszczególnych kopii zapasowych.
- Harmonogram może być definiowany jako "dzienny" lub "tygodniowy" w określonym punkcie czasu.
- Przechowywanie można zdefiniować dla punktów kopii zapasowych "dziennych", "cotygodniowych", "Monthly", "rocznie".
  - "tydzień" oznacza kopię zapasową w określonym dniu tygodnia
  - "miesięcznie" oznacza kopię zapasową w określonym dniu miesiąca
  - "rocznie" oznacza kopię zapasową w określonym dniu roku
- Przechowywanie punktów kopii zapasowych "miesięcznie", "rocznie" jest określane jako długoterminowe przechowywanie (LTR)
- Po utworzeniu magazynu tworzony jest również "DefaultPolicy" i może służyć do tworzenia kopii zapasowych zasobów.
- Wszelkie zmiany wprowadzone w okresie przechowywania zasad tworzenia kopii zapasowych będą stosowane z mocą wstecz do wszystkich starszych punktów odzyskiwania od nowych.

### <a name="impact-of-policy-change-on-recovery-points"></a>Wpływ zmiany zasad w punktach odzyskiwania

- **Zwiększony/zmniejszany czas przechowywania:** Gdy czas przechowywania zostanie zmieniony, nowy czas przechowywania jest stosowany również do istniejących punktów odzyskiwania. W związku z tym niektóre punkty odzyskiwania zostaną oczyszczone. Jeśli okres przechowywania zostanie zwiększony, istniejące punkty odzyskiwania również będą miały większe przechowywanie.
- **Zmieniono codziennie na cotygodniowo:** Gdy zaplanowane kopie zapasowe są zmieniane codziennie na cotygodniowo, istniejące codzienne punkty odzyskiwania są czyszczone.
- **Zmieniono od tygodnia do dziennie:** Istniejące cotygodniowe kopie zapasowe zostaną zachowane na podstawie liczby dni pozostałej zgodnie z bieżącymi zasadami przechowywania.

### <a name="additional-reference"></a>Dodatkowa dokumentacja

- Maszyna wirtualna platformy Azure: jak [tworzyć](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) i [modyfikować](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) zasady.
- SQL Server bazy danych na maszynie wirtualnej platformy Azure: jak [tworzyć](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) i [modyfikować](./manage-monitor-sql-database-backup.md#modify-policy) zasady.
- Udział plików platformy Azure: jak [tworzyć](./backup-afs.md) i [modyfikować](./manage-afs-backup.md#modify-policy) zasady.
- SAP HANA: jak [tworzyć](./backup-azure-sap-hana-database.md#create-a-backup-policy) i [modyfikować](./sap-hana-db-manage.md#change-policy) zasady.
- MARS: [Tworzenie](./backup-windows-with-mars-agent.md#create-a-backup-policy) i [Modyfikowanie](./backup-azure-manage-mars.md#modify-a-backup-policy) zasad.
- [Czy istnieją jakieś ograniczenia dotyczące planowania tworzenia kopii zapasowych na podstawie typu obciążenia?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [Co się stanie z istniejącymi punktami odzyskiwania, jeśli zmienię zasady przechowywania?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>Architektura: Wbudowana kopia zapasowa maszyny wirtualnej platformy Azure

1. Po włączeniu kopii zapasowej dla maszyny wirtualnej platformy Azure kopia zapasowa jest uruchamiana zgodnie z określonym harmonogramem.
1. Podczas pierwszej kopii zapasowej, rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest instalowane rozszerzenie VMSnapshot.
    - W przypadku maszyn wirtualnych z systemem Linux jest zainstalowane rozszerzenie VMSnapshot Linux.
1. Rozszerzenie pobiera migawkę na poziomie magazynu.
    - W przypadku maszyn wirtualnych z systemem Windows, które są uruchomione, Współrzędna usługi Kopia zapasowa z Usługa kopiowania woluminów w tle systemu Windows (VSS) w celu przetworzenia migawki maszyny wirtualnej spójnej na poziomie aplikacji. Domyślnie kopia zapasowa pobiera pełne kopie zapasowe usługi VSS. Jeśli kopia zapasowa nie może wykonać migawki spójnej na poziomie aplikacji, pobiera migawkę spójną na poziomie plików.
    - W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa pobiera migawkę spójną na poziomie plików. W przypadku migawek spójnych z aplikacjami należy ręcznie dostosować skrypty poprzedzające i końcowe.
    - Kopia zapasowa jest zoptymalizowana przez utworzenie kopii zapasowej każdego dysku maszyny wirtualnej równolegle. Dla każdego dysku, którego kopia zapasowa jest tworzona, Azure Backup odczytuje bloków na dysku i zapisuje tylko zmienione dane.
1. Po wykonaniu migawki dane są przesyłane do magazynu.
    - Kopiowane są tylko bloki danych, które uległy zmianie od momentu utworzenia ostatniej kopii zapasowej.
    - Dane nie są szyfrowane. Azure Backup można utworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które zostały zaszyfrowane przy użyciu Azure Disk Encryption.
    - Dane migawki mogą nie być natychmiast kopiowane do magazynu. W godzinach szczytu kopia zapasowa może trwać kilka godzin. W przypadku zasad codziennego tworzenia kopii zapasowych łączny czas tworzenia kopii zapasowej maszyny wirtualnej jest krótszy niż 24 godziny.
1. Po wysłaniu danych do magazynu zostanie utworzony punkt odzyskiwania. Domyślnie migawki są zachowywane przez dwa dni przed ich usunięciem. Ta funkcja umożliwia przywracanie z tych migawek, a tym samym wycinanie czasów przywracania. Skraca czas wymagany do przekształcania i kopiowania danych z magazynu. Zobacz [Azure Backup możliwości przywracania natychmiastowego](./backup-instant-restore-capability.md).

Nie musisz jawnie zezwalać na połączenia z Internetem, aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure.

![Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektura: bezpośrednie tworzenie kopii zapasowych lokalnych maszyn z systemem Windows Server lub plików lub folderów maszyny wirtualnej platformy Azure

1. Aby skonfigurować ten scenariusz, należy pobrać i zainstalować agenta MARS na komputerze. Następnie wybierz, co chcesz zrobić z kopią zapasową, gdy kopie zapasowe będą wykonywane i jak długo będą przechowywane na platformie Azure.
1. Początkowa kopia zapasowa jest uruchamiana zgodnie z ustawieniami kopii zapasowej.
1. Agent MARS korzysta z usługi VSS, aby wykonać migawkę woluminów wybranych do utworzenia kopii zapasowej w danym momencie.
    - Agent MARS używa tylko operacji zapisu systemu Windows, aby przechwycić migawkę.
    - Ponieważ Agent nie używa żadnych składników zapisywania usługi VSS dla aplikacji, nie przechwytuje migawek spójnych na poziomie aplikacji.
1. Po przeprowadzeniu migawki przy użyciu usługi VSS Agent MARS tworzy wirtualny dysk twardy (VHD) w folderze pamięci podręcznej określonym podczas konfigurowania kopii zapasowej. Agent przechowuje również sumy kontrolne dla każdego bloku danych. Są one później używane do wykrywania zmienionych bloków dla kolejnych przyrostowych kopii zapasowych.
1. Przyrostowe kopie zapasowe są uruchamiane zgodnie z określonym harmonogramem, chyba że zostanie uruchomiona kopia zapasowa na żądanie.
1. W przyrostowych kopiach zapasowych zostały zidentyfikowane zmienione pliki i zostanie utworzony nowy wirtualny dysk twardy. Wirtualny dysk twardy jest kompresowany i szyfrowany, a następnie wysyłany do magazynu.
1. Po zakończeniu przyrostowej kopii zapasowej nowy wirtualny dysk twardy zostanie scalony z dyskiem VHD utworzonym po replikacji początkowej. Ten scalony wirtualny dysk twardy zawiera najnowszy stan, który będzie używany do porównywania ciągłej kopii zapasowej.

![Tworzenie kopii zapasowych lokalnych maszyn z systemem Windows Server przy użyciu agenta MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektura: Tworzenie kopii zapasowej w programie DPM/serwera usługi MAB

1. Należy zainstalować agenta ochrony programu DPM lub serwera usługi MAB na maszynach, które mają być chronione. Następnie należy dodać maszyny do grupy ochrony programu DPM.
    - Aby chronić maszyny lokalne, serwer programu DPM lub serwera usługi MAB musi znajdować się w środowisku lokalnym.
    - Aby chronić maszyny wirtualne platformy Azure, serwer serwera usługi MAB musi znajdować się na platformie Azure i działać jako maszyna wirtualna platformy Azure.
    - Za pomocą programu DPM/serwera usługi MAB można chronić woluminy, udziały, pliki i foldery kopii zapasowej. Istnieje również możliwość ochrony stanu systemu maszyny (od zera) i można chronić konkretne aplikacje za pomocą ustawień kopii zapasowych obsługujących aplikacje.
1. Po skonfigurowaniu ochrony dla komputera lub aplikacji w programie DPM/serwera usługi MAB należy wybrać opcję tworzenia kopii zapasowej na dysku lokalnym programu serwera usługi MAB/DPM do przechowywania krótkoterminowego oraz do platformy Azure w celu ochrony w trybie online. Należy również określić, kiedy ma być uruchomione tworzenie kopii zapasowej lokalnego magazynu programu DPM/serwera usługi MAB i kiedy ma zostać uruchomiona kopia zapasowa online na platformie Azure.
1. Kopia zapasowa dysku chronionego obciążenia jest tworzona na lokalnym dysku serwera usługi MAB/DPM zgodnie z określonym harmonogramem.
1. Kopie zapasowe dysków DPM/serwera usługi MAB są tworzone w magazynie przez agenta MARS uruchomionego na serwerze DPM/serwera usługi MAB.

![Tworzenie kopii zapasowych maszyn i obciążeń chronionych przez program DPM lub serwera usługi MAB](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Magazyn maszyny wirtualnej platformy Azure

Maszyny wirtualne platformy Azure używają dysków do przechowywania swoich systemów operacyjnych, aplikacji i danych. Każda maszyna wirtualna platformy Azure ma co najmniej dwa dyski: dysk systemu operacyjnego i dysk tymczasowy. Maszyny wirtualne platformy Azure mogą także mieć dyski danych dla danych aplikacji. Dyski są przechowywane jako wirtualne dyski twarde.

- Wirtualne dyski twarde są przechowywane jako stronicowe obiekty blob na kontach magazynu w warstwie Standardowa lub Premium na platformie Azure:
  - **Magazyn w warstwie Standardowa:** Niezawodna, tańsza Obsługa dysków dla maszyn wirtualnych korzystających z obciążeń, które nie są wrażliwe na opóźnienia. W przypadku magazynu w warstwie Standardowa można korzystać ze standardowych dysków SSD lub dysków twardych (dysk twardy).
  - Usługa **Premium Storage:** Obsługa dysków o wysokiej wydajności. Używa dysków SSD w warstwie Premium.
- Istnieją różne warstwy wydajności dla dysków:
  - **HDD w warstwie Standardowa dysk:** Obsługiwane przez HDD i używane do oszczędnego magazynowania.
  - **SSD w warstwie Standardowa dysk:** Łączy elementy dysków SSD Premium i standardowych dysków DYSKowych. Oferuje bardziej spójną wydajność i niezawodność niż dysk twardy, ale wciąż opłacalne.
  - **SSD w warstwie Premium dysk:** Obsługiwane przez dysków SSD oraz zapewnia wysoką wydajność i małe opóźnienia dla maszyn wirtualnych, na których są uruchomione obciążenia intensywnie korzystające z operacji we/wy.
- Dyski mogą być zarządzane lub niezarządzane:
  - **Dyski niezarządzane:** Tradycyjny typ dysków używanych przez maszyny wirtualne. Dla tych dysków utworzysz własne konto magazynu i określisz je podczas tworzenia dysku. Następnie należy poznać sposób maksymalizowania zasobów magazynu dla maszyn wirtualnych.
  - **Dyski zarządzane:** Platforma Azure tworzy konta magazynu i zarządza nimi. Należy określić rozmiar dysku i warstwę wydajności, a platforma Azure utworzy dyski zarządzane. Podczas dodawania dysków i skalowania maszyn wirtualnych platforma Azure obsługuje konta magazynu.

Aby uzyskać więcej informacji o magazynie dyskowym i dostępnych typach dysków dla maszyn wirtualnych, zobacz następujące artykuły:

- [Azure Managed disks dla maszyn wirtualnych z systemem Linux](../virtual-machines/managed-disks-overview.md)
- [Dostępne typy dysków dla maszyn wirtualnych](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure przy użyciu usługi Premium Storage

Można utworzyć kopię zapasową maszyn wirtualnych platformy Azure przy użyciu usługi Premium Storage z Azure Backup:

- Podczas tworzenia kopii zapasowych maszyn wirtualnych w usłudze Premium Storage usługa Backup tworzy tymczasową lokalizację przejściową o nazwie *AzureBackup-*, na koncie magazynu. Rozmiar lokalizacji przejściowej jest równy rozmiarowi migawki punktu odzyskiwania.
- Upewnij się, że na koncie magazynu w warstwie Premium jest wystarczająca ilość wolnego miejsca, aby pomieścić tymczasową lokalizację przemieszczania. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności dla kont usługi BLOB Storage na stronie Premium](../storage/blobs/scalability-targets-premium-page-blobs.md). Nie należy modyfikować lokalizacji tymczasowej.
- Po zakończeniu zadania tworzenia kopii zapasowej lokalizacja tymczasowa zostanie usunięta.
- Cena magazynu używana w lokalizacji tymczasowej jest spójna z [cenami usługi Premium Storage](../virtual-machines/disks-types.md#billing).

Podczas przywracania maszyn wirtualnych platformy Azure przy użyciu usługi Premium Storage można przywrócić je do magazynu w warstwie Premium lub standardowa. Zazwyczaj można je przywrócić do magazynu w warstwie Premium. Ale jeśli potrzebujesz tylko podzbioru plików z maszyny wirtualnej, może być ekonomiczne przywrócenie do magazynu w warstwie Standardowa.

### <a name="back-up-and-restore-managed-disks"></a>Tworzenie kopii zapasowej i przywracanie dysków zarządzanych

Można tworzyć kopie zapasowe maszyn wirtualnych platformy Azure z dyskami zarządzanymi:

- Tworzenie kopii zapasowych maszyn wirtualnych z dyskami zarządzanymi odbywa się tak samo, jak w przypadku każdej innej maszyny wirtualnej platformy Azure. Można utworzyć kopię zapasową maszyny wirtualnej bezpośrednio z ustawień maszyny wirtualnej lub można włączyć tworzenie kopii zapasowych maszyn wirtualnych w magazynie Recovery Services.
- Kopie zapasowe maszyn wirtualnych możesz tworzyć na dyskach zarządzanych za pomocą kolekcji RestorePoint tworzonych na tych dyskach.
- Azure Backup obsługuje również tworzenie kopii zapasowych maszyn wirtualnych z dyskami zarządzanymi, które zostały zaszyfrowane przy użyciu Azure Disk Encryption.

W przypadku przywracania maszyn wirtualnych z dyskami zarządzanymi można przywrócić do kompletnej maszyny wirtualnej z dyskami zarządzanymi lub kontem magazynu:

- W trakcie procesu przywracania platforma Azure obsługuje dyski zarządzane. Jeśli używasz opcji konta magazynu, możesz zarządzać kontem magazynu, które zostało utworzone podczas procesu przywracania.
- Jeśli przywracasz zaszyfrowaną zarządzaną maszynę wirtualną, przed rozpoczęciem procesu przywracania upewnij się, że klucze i wpisy tajne maszyny wirtualnej znajdują się w magazynie kluczy.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z matrycą pomocy technicznej, aby [poznać obsługiwane funkcje i ograniczenia dotyczące scenariuszy tworzenia kopii zapasowych](backup-support-matrix.md).
- Skonfiguruj kopię zapasową dla jednego z następujących scenariuszy:
  - [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md).
  - [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
  - [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
  - [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
