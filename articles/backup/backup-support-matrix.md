---
title: Tabela obsługi usługi Azure Backup
description: Zawiera podsumowanie ustawień obsługi i ograniczeń dotyczących usługi Azure Backup.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5c74a34efe8075ab7a34fab4570d9513900b3f81
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517423"
---
# <a name="support-matrix-for-azure-backup"></a>Macierz obsługi dla Azure Backup

Za pomocą [Azure Backup](backup-overview.md) kopii zapasowej danych na platformie Microsoft Azure w chmurze. Ten artykuł zawiera podsumowanie ogólnych ustawień pomocy technicznej i ograniczeń dotyczących Azure Backup scenariuszy i wdrożeń.

Dostępne są inne macierze pomocy technicznej:

- Macierz obsługi kopii [zapasowych maszyn wirtualnych platformy Azure](backup-support-matrix-iaas.md)
- Macierz obsługi kopii zapasowych przy [użyciu System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Macierz obsługi kopii zapasowych przy użyciu [agenta Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Obsługa magazynu

Azure Backup korzysta z magazynów usługi Recovery Services do organizowania kopii zapasowych następujących typów obciążeń i zarządzania nimi — maszyn wirtualnych platformy Azure, bazy danych SQL na platformie Azure, usługi SAP HANA na maszynach wirtualnych platformy Azure, udziałów plików platformy Azure i obciążeń lokalnych przy użyciu programu Azure Backup Agent, Azure Backup Server i System Center DPM. Magazyny usługi Recovery Services są również używane do przechowywania danych kopii zapasowej dla tych obciążeń.

W poniższej tabeli opisano funkcje magazynów usługi Recovery Services:

**Funkcja** | **Szczegóły**
--- | ---
**Magazyny w subskrypcji** | Do 500 magazynów usługi Recovery Services w ramach jednej subskrypcji.
**Maszyny w magazynie** | W jednym magazynie można chronić do 2000 źródeł danych we wszystkich obciążeniach (takich jak maszyny wirtualne platformy Azure, maszyny wirtualne SQL Server, serwery MABS itp.).<br><br>Maksymalnie 1000 maszyn wirtualnych platformy Azure w jednym magazynie.<br/><br/> W jednym magazynie można zarejestrować maksymalnie 50 serwerów USŁUGI MABS.
**Źródła danych** | Maksymalny rozmiar pojedynczego [źródła danych](./backup-azure-backup-faq.yml#how-is-the-data-source-size-determined-) to 54 400 GB. Ten limit nie dotyczy kopii zapasowych maszyn wirtualnych platformy Azure. Limity nie dotyczą całkowitej ilości danych, których kopię zapasową można utworzyć w magazynie.
**Wykonywanie kopii zapasowych w magazynie** | **Maszyny wirtualne platformy Azure:** Raz dziennie.<br/><br/>**Maszyny chronione przez program DPM/serwer MABS:** Dwa razy dziennie.<br/><br/> **Maszyny, dla których dano kopię zapasową bezpośrednio przy użyciu agenta MARS:** Trzy razy dziennie.
**Kopie zapasowe między magazynami** | Kopia zapasowa znajduje się w regionie.<br/><br/> Potrzebujesz magazynu w każdym regionie świadczenia usługi Azure, który zawiera maszyny wirtualne, których kopię zapasową chcesz wrócić. Nie można utworzyć kopii zapasowej w innym regionie.
**Przenoszenie magazynów** | Magazyny [można przenosić między](./backup-azure-move-recovery-services-vault.md) subskrypcjami lub między grupami zasobów w tej samej subskrypcji. Przenoszenie magazynów między regionami nie jest jednak obsługiwane.
**Przenoszenie danych między magazynami** | Przenoszenie danych kopii zapasowej między magazynami nie jest obsługiwane.
**Modyfikowanie typu magazynu** | Przed zapisaniem kopii zapasowych można zmodyfikować typ replikacji magazynu (magazyn geograficznie nadmiarowy lub magazyn lokalnie nadmiarowy) dla magazynu. Po rozpoczęciu wykonywania kopii zapasowych w magazynie nie można zmienić typu replikacji.
**Magazyn strefowo nadmiarowy (ZRS)** | Dostępne w regionach Południowe Zjednoczone Królestwo (UKS) i South Azja Wschodnia (SEA).
**Prywatne punkty końcowe** | Zapoznaj [się z tą sekcją,](./private-endpoints.md#before-you-start) aby uzyskać informacje o wymaganiach dotyczących tworzenia prywatnych punktów końcowych dla magazynu usługi odzyskiwania.  

## <a name="on-premises-backup-support"></a>Obsługa lokalnych kopii zapasowych

Oto, co jest obsługiwane, jeśli chcesz wrócić do kopii zapasowej maszyn lokalnych:

**Maszyna** | **Co jest kopii zapasowej** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Bezpośrednie tworzenie kopii zapasowej maszyny z systemem Windows za pomocą agenta MARS** | Pliki, foldery, stan systemu | Kopii zapasowej w magazynie usługi Recovery Services. | 3 razy dziennie<br/><br/> Brak kopii zapasowej z aplikacją<br/><br/> Przywracanie pliku, folderu, woluminu
**Bezpośrednie tworzenie kopii zapasowej maszyny z systemem Linux przy użyciu agenta MARS** | Kopia zapasowa nie jest obsługiwana
**Back up to DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Kopii zapasowej w lokalnym magazynie programu DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny poziom szczegółowości dla kopii zapasowych i odzyskiwania<br/><br/> System Linux obsługiwany dla maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Program Oracle nie jest obsługiwany
**Back up to MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Kopii zapasowej w magazynie lokalnym usługi MABS. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny poziom szczegółowości dla kopii zapasowych i odzyskiwania<br/><br/> System Linux obsługiwany dla maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Program Oracle nie jest obsługiwany

## <a name="azure-vm-backup-support"></a>Obsługa kopii zapasowych maszyn wirtualnych platformy Azure

### <a name="azure-vm-limits"></a>Limity maszyn wirtualnych platformy Azure

**Limit** | **Szczegóły**
--- | ---
**Dyski z danymi maszyn wirtualnych platformy Azure** | Zobacz macierz obsługi [kopii zapasowych maszyn wirtualnych platformy Azure.](./backup-support-matrix-iaas.md#vm-storage-support)
**Rozmiar dysku z danymi maszyn wirtualnych platformy Azure** | Rozmiar pojedynczego dysku może być do 32 TB i maksymalnie 256 TB łącznie dla wszystkich dysków na maszynie wirtualnej.

### <a name="azure-vm-backup-options"></a>Opcje kopii zapasowych maszyn wirtualnych platformy Azure

Oto, co jest obsługiwane, jeśli chcesz wrócić do kopii zapasowej maszyn wirtualnych platformy Azure:

**Maszyna** | **Co jest kopii zapasowej** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu rozszerzenia maszyny wirtualnej** | Cała maszyna wirtualna | Kopii zapasowej w magazynie. | Rozszerzenie jest instalowane po włączeniu kopii zapasowej maszyny wirtualnej.<br/><br/> Kopii zapasowej raz dziennie.<br/><br/> Kopia zapasowa z app-aware dla maszyn wirtualnych z systemem Windows; kopia zapasowa spójna na plikach dla maszyn wirtualnych z systemem Linux. Spójność aplikacji dla maszyn z systemem Linux można skonfigurować przy użyciu skryptów niestandardowych.<br/><br/> Przywróć maszynę wirtualną lub dysk.<br/><br/>[Obsługiwane jest tworzenie kopii zapasowych i przywracanie kontrolerów domeny usługi Active Directory.](active-directory-backup-restore.md)<br><br> Nie można utworzyć kopii zapasowej maszyny wirtualnej platformy Azure w lokalizacji lokalnej.
**Tworzenie kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu agenta MARS** | Pliki, foldery, stan systemu | Kopii zapasowej w magazynie. | Back up three times a day (3 razy dziennie).<br/><br/> Jeśli chcesz utworzyć kopię zapasową określonych plików lub folderów, a nie całej maszyny wirtualnej, agent MARS może działać razem z rozszerzeniem maszyny wirtualnej.
**Maszyna wirtualna platformy Azure z programem DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Kopii zapasowej w lokalnym magazynie maszyny wirtualnej platformy Azure z uruchomionym programem DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z świadomością aplikacji.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna wirtualna platformy Azure z usługą MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Kopii zapasowej w lokalnym magazynie maszyny wirtualnej platformy Azure z uruchomionym usługą MABS. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z świadomością aplikacji.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.

## <a name="linux-backup-support"></a>Obsługa kopii zapasowych systemu Linux

Oto, co jest obsługiwane, jeśli chcesz wrócić do kopii zapasowej maszyn z systemem Linux:

**Typ kopii zapasowej** | **Linux (zatwierdzony przez Azure)**
--- | ---
**Bezpośrednie tworzenie kopii zapasowej maszyny lokalnej z systemem Linux** | Nieobsługiwane. Agenta MARS można zainstalować tylko na maszynach z systemem Windows.
**Używanie rozszerzenia agenta do tworzenia kopii zapasowej maszyny wirtualnej platformy Azure z systemem Linux** | Spójna na aplikacji kopia zapasowa przy użyciu [skryptów niestandardowych.](backup-azure-linux-app-consistent.md)<br/><br/> Odzyskiwanie na poziomie plików.<br/><br/> Przywracanie przez utworzenie maszyny wirtualnej z punktu odzyskiwania lub dysku.
**Używanie programu DPM do obsługi kopii zapasowej maszyn lokalnych z systemem Linux** | Spójna na plikach kopia zapasowa maszyn wirtualnych gościa z systemem Linux w funkcjach Hyper-V i VMware.<br/><br/> Przywracanie maszyn wirtualnych gościa funkcji Hyper-V i programu VMware Linux.
**Używanie usługi MABS do obsługi kopii zapasowej maszyn lokalnych z systemem Linux** | Spójna na plikach kopia zapasowa maszyn wirtualnych gościa z systemem Linux w funkcjach Hyper-V i VMware.<br/><br/> Przywracanie maszyn wirtualnych gościa funkcji Hyper-V i programu VMware Linux.
**Używanie usługi MABS lub programu DPM do kopii zapasowej maszyn wirtualnych platformy Azure z systemem Linux** | Nieobsługiwane.

## <a name="daylight-saving-time-support"></a>Obsługa czasu letniego

Azure Backup nie obsługuje automatycznego korekty zegara dla czasu letniego dla kopii zapasowych maszyn wirtualnych platformy Azure. Nie przesuwa godziny tworzenia kopii zapasowej do przodu ani do tyłu. Aby zapewnić, że kopia zapasowa zostanie uruchomiona w odpowiednim czasie, zmodyfikuj zasady tworzenia kopii zapasowych ręcznie zgodnie z potrzebami.

## <a name="disk-deduplication-support"></a>Obsługa deduplikacji dysku

Obsługa deduplikacji dysku jest następująca:

- Deduplikacja dysku jest obsługiwana lokalnie w przypadku używania programu DPM lub usługi MABS do kopii zapasowej maszyn wirtualnych funkcji Hyper-V z systemem Windows. System Windows Server wykonuje deduplikację danych (na poziomie hosta) na wirtualnych dyskach twardych (VHD), które są dołączone do maszyny wirtualnej jako magazyn kopii zapasowych.
- Funkcja deduplikacji nie jest obsługiwana na platformie Azure dla żadnego składnika usługi Backup. Po wdrożeniu programu DPM i usługi MABS na platformie Azure nie można deduplikować dysków magazynu dołączonych do maszyny wirtualnej.

## <a name="security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania

Azure Backup obsługuje szyfrowanie danych podczas przesyłania i przechowywania.

### <a name="network-traffic-to-azure"></a>Ruch sieciowy do platformy Azure

- Ruch kopii zapasowej z serwerów do magazynu usługi Recovery Services jest szyfrowany przy użyciu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.

### <a name="data-security"></a>Bezpieczeństwo danych

- Dane kopii zapasowej są przechowywane w magazynie usługi Recovery Services w postaci zaszyfrowanej.
- Gdy kopię zapasową danych jest pobierana z serwerów lokalnych za pomocą agenta MARS, dane są szyfrowane przy użyciu hasła przed przekazaniem do usługi Azure Backup i odszyfrowywać tylko po ich pobraniu z Azure Backup.
- Podczas kopii zapasowej maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *na maszynie* wirtualnej.
- Usługa Azure Backup obsługuje usługę Azure Disk Encryption, która używa funkcji BitLocker na maszynach wirtualnych z systemem Windows, a programu **dm-crypt** na maszynach wirtualnych z systemem Linux.
- Na zaposłudze Azure Backup [azure szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md), która chroni dane magazynowe.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
**Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS** | ![Tak][green] | ![Tak][green]
**Maszyny wirtualne platformy Azure** | ![Tak][green] | ![Tak][green]
**Lokalne maszyny z systemem Windows lub maszyny wirtualne platformy Azure z programem DPM** | ![Tak][green] | ![Tak][green]
**Lokalne maszyny z systemem Windows lub maszyny wirtualne platformy Azure z usługą MABS** | ![Tak][green] | ![Tak][green]

## <a name="compression-support"></a>Obsługa kompresji

Kopia zapasowa obsługuje kompresję ruchu kopii zapasowych, zgodnie z podsumowaniem w poniższej tabeli.

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta usługi Azure Storage za pośrednictwem sieci magazynu, więc kompresja tego ruchu nie jest konieczna.
- Jeśli używasz programu DPM lub usługi MABS, możesz zaoszczędzić przepustowość, kompresując dane przed ich kopią zapasową.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresowanie do magazynu (HTTPS)**
--- | --- | ---
**Bezpośrednie tworzenie kopii zapasowych lokalnych maszyn z systemem Windows** | Nie dotyczy | ![Yes][green]
**Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej** | Nie dotyczy | NA
**Tworzenie kopii zapasowej na maszynach lokalnych/na platformie Azure przy użyciu usługi MABS/programu DPM** | ![Tak][green] | ![Tak][green]

## <a name="retention-limits"></a>Limity przechowywania

**Ustawienie** | **Limity**
--- | ---
**Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (maszyna lub obciążenie)** | 9,999
**Maksymalny czas wygaśnięcia punktu odzyskiwania** | Bez ograniczeń
**Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS** | Co 15 minut dla programu SQL Server<br/><br/> Raz na godzinę dla innych obciążeń
**Maksymalna częstotliwość wykonywania kopii zapasowych w magazynie** | **Lokalne maszyny z systemem Windows lub maszyny wirtualne platformy Azure z usługą MARS:** Trzy dziennie<br/><br/> **DpM/MABS:** Dwie dziennie<br/><br/> **Kopia zapasowa maszyny wirtualnej platformy Azure:** Jedna dziennie
**Przechowywanie punktów odzyskiwania** | Codziennie, co tydzień, co miesiąc, co rok
**Maksymalny okres przechowywania** | Zależnie od częstotliwości wykonywania kopii zapasowych
**Punkty odzyskiwania na dysku programu DPM/usługi MABS** | 64 dla serwerów plików; 448 dla serwerów aplikacji <br/><br/>Nieograniczone punkty odzyskiwania na taśmie dla lokalnego programu DPM

## <a name="cross-region-restore"></a>Przywracanie między regionami

Azure Backup dodano funkcję przywracania między regionami, aby zwiększyć dostępność danych i odporność, zapewniając pełną kontrolę nad przywracaniem danych do regionu pomocniczego. Aby skonfigurować tę funkcję, odwiedź [artykuł Ustawianie przywracania między regionami.](backup-create-rs-vault.md#set-cross-region-restore). Ta funkcja jest obsługiwana w przypadku następujących typów zarządzania:

| Typ zarządzania kopiami zapasowymi | Obsługiwane                                                    | Obsługiwane regiony |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Maszyna wirtualna platformy Azure               | Obsługiwane w przypadku maszyn wirtualnych platformy Azure (w tym zaszyfrowanych maszyn wirtualnych platformy Azure) z dyskami zarządzanymi i nieza zarządzanymi. Nie jest obsługiwane w przypadku klasycznych maszyn wirtualnych. | Dostępne we wszystkich regionach publicznych platformy Azure i suwerennych regionach z wyjątkiem Francja Środkowa, Australia Środkowa, Północna Republika Południowej Afryki, Północne Zjednoczone Emiraty Zjednoczone, Holandia Północna, Niemcy Zachodnio-środkowe, Azja Wschodnia, UG IOWA i UG Wirginia. <br>Aby uzyskać informacje na temat użycia w tych regionach, skontaktuj się z [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL /SAP HANA | W wersji zapoznawczej                                                      | Dostępne we wszystkich regionach publicznych platformy Azure i suwerennych regionach z wyjątkiem Francja Środkowa, Australia Środkowa, Północna Republika Południowej Afryki, Północne Zjednoczone Emiraty Zjednoczone, Holandia Północna, Niemcy Zachodnio-środkowe, Kanada Wschodnia, UG IOWA i UG Wirginia. <br>Aby uzyskać informacje na temat użycia w tych regionach, skontaktuj się z [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| Agent MARS/lokalnie  | Nie                                                           | Nie dotyczy               |
| AFS (udziały plików platformy Azure)                 | Nie                                                           | Nie dotyczy               |

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z macierzą obsługi](backup-support-matrix-iaas.md) kopii zapasowych maszyn wirtualnych platformy Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png