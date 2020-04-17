---
title: Tabela obsługi usługi Azure Backup
description: Zawiera podsumowanie ustawień obsługi i ograniczeń dotyczących usługi Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: b87430c9ff7d806fd1fc74d5d2a8270f9db43b53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537325"
---
# <a name="support-matrix-for-azure-backup"></a>Macierz pomocy technicznej dla usługi Azure Backup

Za pomocą [usługi Azure Backup](backup-overview.md) można wykonać kopię zapasową danych na platformie chmurowej platformy Microsoft Azure. W tym artykule podsumowano ogólne ustawienia pomocy technicznej i ograniczenia dotyczące scenariuszy i wdrożeń usługi Azure Backup.

Dostępne są inne matryce pomocy technicznej:

- Macierz obsługi kopii [zapasowej maszyny wirtualnej platformy Azure](backup-support-matrix-iaas.md)
- Macierz obsługi kopii zapasowej przy użyciu [menedżera ochrony danych systemu Center (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Macierz obsługi kopii zapasowej przy użyciu [agenta usług microsoft azure recovery services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Obsługa magazynu

Usługa Azure Backup używa magazynów usług odzyskiwania do organizowania kopii zapasowych i zarządzania nimi. Używa również magazynów do przechowywania kopii zapasowych danych.

W poniższej tabeli opisano funkcje magazynów usług Recovery Services:

**Funkcja** | **Szczegóły**
--- | ---
**Przechowalnia w ramach subskrypcji** | Do 500 magazynów usługi Recovery Services w ramach jednej subskrypcji.
**Maszyny w magazynie** | Do 1000 maszyn wirtualnych platformy Azure w jednym magazynie.<br/><br/> W jednym magazynie można zarejestrować do 50 serwerów MABS.
**Źródła danych** | Maksymalny rozmiar pojedynczego [źródła danych](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) to 54 400 GB. Ten limit nie dotyczy kopii zapasowych maszyn wirtualnych platformy Azure. Nie ma zastosowania limity do całkowitej ilości danych, które można złożyć kopii zapasowej w magazynie.
**Wykonywanie kopii zapasowych w magazynie** | **Maszyny wirtualne platformy Azure:** Raz dziennie.<br/><br/>**Maszyny chronione przez DPM/MABS:** Dwa razy dziennie.<br/><br/> **Maszyny kopii zapasowej bezpośrednio za pomocą agenta MARS:** Trzy razy dziennie.
**Kopie zapasowe między magazynami** | Kopia zapasowa znajduje się w obrębie regionu.<br/><br/> Potrzebujesz magazynu w każdym regionie platformy Azure, który zawiera maszyny wirtualne, które chcesz wykonać kopii zapasowej. Nie można przywrócić kopii zapasowej w innym regionie.
**Przenoszenie przechowalni** | Można [przenosić magazyny](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) między subskrypcjami lub między grupami zasobów w tej samej subskrypcji. Jednak przenoszenie przechowalni między regionami nie jest obsługiwane.
**Przenoszenie danych między magazynami** | Przenoszenie kopii zapasowych danych między magazynami nie jest obsługiwane.
**Modyfikowanie typu magazynu przechowalni** | Przed przechowywaniem kopii zapasowych można zmodyfikować typ replikacji magazynu (magazyn geograficznie nadmiarowy lub magazyn lokalnie nadmiarowy) dla magazynu. Po rozpoczęciu wykonywania kopii zapasowych w magazynie nie można zmienić typu replikacji.

## <a name="on-premises-backup-support"></a>Obsługa lokalnych kopii zapasowych

Oto, co jest obsługiwane, jeśli chcesz mieć zapas kopii zapasowych na komputerach lokalnych:

**Maszyna** | **Co jest archiwizowane** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Bezpośrednia kopia zapasowa komputera z systemem Windows za pomocą agenta MARS** | Pliki, foldery, stan systemu | Aby przejść do magazynu usług odzyskiwania, wrócę do magazynu usług odzyskiwania. | 3 razy dziennie<br/><br/> Brak kopii zapasowej obsługującej aplikację<br/><br/> Przywracanie pliku, folderu, woluminu
**Bezpośrednia kopia zapasowa komputera Linux z agentem MARS** | Kopia zapasowa nie jest obsługiwana
**Powrót do programu DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Śmiwal kopii zapasowej do lokalnej pamięci masowej programu DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełna szczegółowość do tworzenia kopii zapasowych i odzyskiwania<br/><br/> Linux obsługiwany dla maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Oracle nie jest obsługiwane
**Zapasowe do mabs** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Świększ do lokalnej pamięci masowej MABS. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełna szczegółowość do tworzenia kopii zapasowych i odzyskiwania<br/><br/> Linux obsługiwany dla maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Oracle nie jest obsługiwane

## <a name="azure-vm-backup-support"></a>Obsługa kopii zapasowych maszyn wirtualnych platformy Azure

### <a name="azure-vm-limits"></a>Limity maszyn wirtualnych platformy Azure

**Limit** | **Szczegóły**
--- | ---
**Dyski z danymi maszyn wirtualnych platformy Azure** | Limit 16 <br> Aby skorzystać z prywatnej wersji zapoznawczej maszyn wirtualnych z ponad 16 dyskami (do 32 dysków), wyślij wiadomość na adres AskAzureBackupTeam@microsoft.com
**Rozmiar dysku z danymi maszyn wirtualnych platformy Azure** | Rozmiar dysku indywidualnego może wynosić do 32 TB i maksymalnie 256 TB łącznie dla wszystkich dysków na maszynie wirtualnej.

### <a name="azure-vm-backup-options"></a>Opcje kopii zapasowych maszyn wirtualnych platformy Azure

Oto, co jest obsługiwane, jeśli chcesz zrobić zamieść kopii zapasowej maszyn wirtualnych platformy Azure:

**Maszyna** | **Co jest archiwizowane** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej** | Cała maszyna wirtualna | Wróc do skarbca. | Rozszerzenie zainstalowane po włączeniu kopii zapasowej maszyny Wirtualnej.<br/><br/> Śmiuj zapasy raz dziennie.<br/><br/> Kopia zapasowa obsługujących aplikacje dla maszyn wirtualnych z systemem Windows; kopia zapasowa spójna z plikami dla maszyn wirtualnych z systemem Linux. Spójność aplikacji dla komputerów z systemem Linux można skonfigurować przy użyciu skryptów niestandardowych.<br/><br/> Przywracanie maszyny Wirtualnej lub dysku.<br/><br/> Nie można wyw kopii zapasowej maszyny Wirtualnej platformy Azure w lokalizacji lokalnej.
**Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu agenta MARS** | Pliki, foldery, stan systemu | Wróc do skarbca. | 3 razy dziennie.<br/><br/> Jeśli chcesz zrobić kopie zapasowe określonych plików lub folderów, a nie całej maszyny Wirtualnej, agent MARS może działać obok rozszerzenia maszyny Wirtualnej.
**Maszyna wirtualna platformy Azure z programem DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Aby przejść do magazynu lokalnego maszyny Wirtualnej platformy Azure, która jest uruchomiona programem DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki obsługujące aplikację.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna wirtualna platformy Azure z usługą MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji | Aby przejść do magazynu lokalnego maszyny Wirtualnej platformy Azure, która jest uruchomiona mabs. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki obsługujące aplikację.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.

## <a name="linux-backup-support"></a>Obsługa kopii zapasowych systemu Linux

Oto, co jest obsługiwane, jeśli chcesz zrobić kopii zapasowej maszyn z systemem Linux:

**Typ kopii zapasowej** | **Linux (zatwierdzony przez Azure)**
--- | ---
**Bezpośrednia kopia zapasowa komputera lokalnego z systemem Linux** | Bez pomocy technicznej. Agenta MARS można zainstalować tylko na komputerach z systemem Windows.
**Używanie rozszerzenia agenta do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure z systemem Linux** | Tworzenie kopii zapasowej spójnej z aplikacją przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Odzyskiwanie na poziomie plików.<br/><br/> Przywracanie przez utworzenie maszyny wirtualnej z punktu odzyskiwania lub dysku.
**Tworzenie kopii zapasowych na komputerach lokalnych z systemem Linux za pomocą programu DPM** | Spójna z plikami kopia zapasowa maszyn wirtualnych z systemem Linux dla gości na technologiach Hyper-V i VMWare.<br/><br/> Przywracanie maszyn wirtualnych maszyn wirtualnych z programami V-V i VMWare Linux dla gości.
**Tworzenie kopii zapasowych na komputerach lokalnych z systemem Linux za pomocą usługi MABS** | Spójna z plikami kopia zapasowa maszyn wirtualnych z systemem Linux dla gości na technologiach Hyper-V i VMWare.<br/><br/> Przywracanie maszyn wirtualnych maszyn wirtualnych z programami V-V i VMWare Linux dla gości.
**Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z systemem Linux za pomocą programu MABS lub DPM** | Bez pomocy technicznej.

## <a name="daylight-saving-time-support"></a>Obsługa czasu letniego

Usługa Azure Backup nie obsługuje automatycznego dostosowywania zegara dla czasu letniego dla kopii zapasowych maszyn wirtualnych platformy Azure. Nie przesuwa godziny kopii zapasowej do przodu lub do tyłu. Aby upewnić się, że kopia zapasowa jest uruchamiana w żądanym czasie, zmodyfikuj zasady tworzenia kopii zapasowych ręcznie zgodnie z wymaganiami.

## <a name="disk-deduplication-support"></a>Obsługa deduplikacji dysków

Obsługa deduplikacji dysku jest następująca:

- Deduplikacja dysku jest obsługiwana lokalnie, gdy program DPM lub MABS jest używany do 300 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 System Windows Server wykonuje deduplikację danych (na poziomie hosta) na wirtualnych dyskach twardych (VHD), które są podłączone do maszyny Wirtualnej jako magazyn kopii zapasowych.
- Funkcja deduplikacji nie jest obsługiwana na platformie Azure dla żadnego składnika usługi Backup. Gdy program DPM i MABS są wdrażane na platformie Azure, dysków magazynu dołączonych do maszyny Wirtualnej nie można deduplikować.

## <a name="security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i w spoczynku.

### <a name="network-traffic-to-azure"></a>Ruch sieciowy na platformę Azure

- Ruch kopii zapasowych z serwerów do magazynu usług odzyskiwania jest szyfrowany przy użyciu standardu zaawansowanego szyfrowania 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- Dane kopii zapasowej są przechowywane w magazynie usług odzyskiwania w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

    > [!WARNING]
    > Po skonfigurowaniu magazynu tylko Ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

### <a name="data-security"></a>Bezpieczeństwo danych

- Podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure należy skonfigurować szyfrowanie *na* maszynie wirtualnej.
- Usługa Azure Backup obsługuje usługę Azure Disk Encryption, która używa funkcji BitLocker na maszynach wirtualnych z systemem Windows, a programu **dm-crypt** na maszynach wirtualnych z systemem Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md)Service , która chroni dane w stanie spoczynku.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
**Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS** | ![Tak][green] | ![Tak][green]
**Maszyny wirtualne platformy Azure** | ![Tak][green] | ![Tak][green]
**Lokalne maszyny z systemem Windows lub maszyny wirtualne platformy Azure z programem DPM** | ![Tak][green] | ![Tak][green]
**Lokalne maszyny z systemem Windows lub maszyny wirtualne platformy Azure z usługą MABS** | ![Tak][green] | ![Tak][green]

## <a name="compression-support"></a>Obsługa kompresji

Kopia zapasowa obsługuje kompresję ruchu kopii zapasowej, jak podsumowano w poniższej tabeli.

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny Wirtualnej odczytuje dane bezpośrednio z konta magazynu platformy Azure za pośrednictwem sieci magazynu, więc nie jest konieczne skompresowanie tego ruchu.
- Jeśli używasz programu DPM lub MABS, możesz zaoszczędzić przepustowość, ściskając dane przed utworzeniem kopii zapasowej.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresowanie do przechowalni (HTTPS)**
--- | --- | ---
**Bezpośrednia kopia zapasowa lokalnych komputerów z systemem Windows** | Nie dotyczy | ![Tak][green]
**Tworzenie kopii zapasowej maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej** | Nie dotyczy | Nie dotyczy
**Tworzenie kopii zapasowych na komputerach lokalnych/platformy Azure przy użyciu usługi MABS/DPM** | ![Tak][green] | ![Tak][green]

## <a name="retention-limits"></a>Limity przechowywania

**Ustawienie** | **Limity**
--- | ---
**Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (komputer lub obciążenie)** | 9,999
**Maksymalny czas wygaśnięcia punktu odzyskiwania** | Bez ograniczeń
**Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS** | Co 15 minut dla programu SQL Server<br/><br/> Raz na godzinę w przypadku innych obciążeń
**Maksymalna częstotliwość wykonywania kopii zapasowych w magazynie** | **Lokalne maszyny z systemem Windows lub maszyny wirtualne platformy Azure z systemem MARS:** Trzy dziennie<br/><br/> **DPM/MABS:** Dwa dziennie<br/><br/> **Kopia zapasowa maszyny wirtualnej platformy Azure:** Jeden dziennie
**Retencja punktów odzyskiwania** | Codziennie, co tydzień, co miesiąc, co rok
**Maksymalny okres przechowywania** | Zależnie od częstotliwości wykonywania kopii zapasowych
**Punkty odzyskiwania na dysku programu DPM/usługi MABS** | 64 dla serwerów plików; 448 dla serwerów aplikacji <br/><br/>Nieograniczona liczba punktów odzyskiwania taśm dla lokalnego programu DPM

## <a name="cross-region-restore"></a>Przywracanie między regionami

Usługa Azure Backup dodała funkcję Przywracania między regionami, aby zwiększyć dostępność danych i możliwości odporności, dając klientom pełną kontrolę nad przywracaniem danych do regionu pomocniczego. Aby skonfigurować tę funkcję, odwiedź [artykuł Ustaw przywracanie regionu krzyżowego.](backup-create-rs-vault.md#set-cross-region-restore). Ta funkcja jest obsługiwana dla następujących typów zarządzania:

| Typ zarządzania kopiami zapasowymi | Obsługiwane                                                    |  Obsługiwane regiony |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Maszyna wirtualna platformy Azure               | Tak.   Obsługiwane dla zaszyfrowanych maszyn wirtualnych i maszyn wirtualnych z dyskami mniejszymi niż 4 TB | Wszystkie regiony publiczne platformy Azure.  |
| Mars Agent/W środowisku lokalnym | Nie                                                           | Nie dotyczy               |
| SQL /SAP HANA          | Nie                                                           | Nie dotyczy               |
| Afs                    | Nie                                                           | Nie dotyczy               |

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj macierz pomocy technicznej](backup-support-matrix-iaas.md) dla kopii zapasowej maszyny Wirtualnej platformy Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
