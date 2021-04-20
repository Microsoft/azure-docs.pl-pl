---
title: Informacje o kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak usługa Azure Backup kopii zapasowej maszyn wirtualnych platformy Azure oraz jak stosować najlepsze rozwiązania.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ce76f64093bab362d62afcc3f94d07f7ee7883d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718453"
---
# <a name="an-overview-of-azure-vm-backup"></a>Omówienie kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano [sposób Azure Backup kopii](./backup-overview.md) zapasowej maszyn wirtualnych platformy Azure.

Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi funkcjami zarządzania punktami odzyskiwania. Konfiguracja i skalowanie są proste, kopie zapasowe są optymalizowane i w razie potrzeby można je łatwo przywrócić.

W ramach procesu tworzenia [](#snapshot-creation)kopii zapasowej jest nosowana migawka, a dane są przesyłane do magazynu usługi Recovery Services bez wpływu na obciążenia produkcyjne. Migawka zapewnia różne poziomy spójności, zgodnie z opisem w [tym miejscu.](#snapshot-consistency)

Azure Backup oferuje również wyspecjalizowane oferty dla obciążeń baz danych, takich jak [SQL Server](backup-azure-sql-database.md) i [SAP HANA,](sap-hana-db-about.md) które są świadome obciążeń, oferują 15-minutowy cel punktu odzyskiwania (cel punktu odzyskiwania) oraz umożliwiają tworzenie kopii zapasowych i przywracanie poszczególnych baz danych.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Usługa Azure Backup wykonuje kopię zapasową maszyn wirtualnych platformy Azure w następujący sposób:

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="encryption-of-azure-vm-backups"></a>Szyfrowanie kopii zapasowych maszyn wirtualnych platformy Azure

W przypadku kopii zapasowej maszyn wirtualnych platformy Azure za pomocą Azure Backup maszyny wirtualne są szyfrowane w spoczynku przy użyciu szyfrowanie usługi Storage (SSE). Azure Backup można również utworzyć kopię zapasową maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu Azure Disk Encryption.

**Szyfrowanie** | **Szczegóły** | **Pomoc techniczna**
--- | --- | ---
**Sse** | Dzięki funkcjom SSE usługa Azure Storage zapewnia szyfrowanie magazynowane przez automatyczne szyfrowanie danych przed ich zapisaniem. Usługa Azure Storage odszyfrowuje również dane przed ich pobieraniem. Azure Backup obsługuje kopie zapasowe maszyn wirtualnych z dwoma typami szyfrowanie usługi Storage:<li> **Szyfrowanie SSE z kluczami zarządzanymi przez** platformę: to szyfrowanie jest domyślnie włączone dla wszystkich dysków maszyn wirtualnych. Więcej informacji można [znaleźć tutaj.](../virtual-machines/disk-encryption.md#platform-managed-keys)<li> **SSE z kluczami zarządzanymi przez klienta.** Kluczami cmk zarządza się kluczami używanymi do szyfrowania dysków. Więcej informacji można [znaleźć tutaj.](../virtual-machines/disk-encryption.md#customer-managed-keys) | Azure Backup szyfrowania danych magazynowych maszyn wirtualnych platformy Azure przy użyciu szyfrowania SSE.
**Usługa Azure Disk Encryption** | Azure Disk Encryption szyfruje dyski systemu operacyjnego i danych dla maszyn wirtualnych platformy Azure.<br/><br/> Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEK), które są zabezpieczane w magazynie kluczy jako wpisy tajne. Azure Disk Encryption integruje się również z kluczami szyfrowania kluczy Azure Key Vault (KEK). | Azure Backup obsługuje tworzenie kopii zapasowych zarządzanych i niezaładowanych maszyn wirtualnych platformy Azure zaszyfrowanych tylko za pomocą szyfrowania BEK lub za pomocą szyfrowania BEK razem z kEK.<br/><br/> Zarówno kEK, jak i KEK, są kopiami zapasową i szyfrowane.<br/><br/> Ponieważ tworzyć kopię zapasową kluczy KEK i kluczy szyfrowania kluczy, użytkownicy z niezbędnymi uprawnieniami mogą w razie potrzeby przywrócić klucze i wpisy tajne do magazynu kluczy. Ci użytkownicy mogą również odzyskać zaszyfrowaną maszynę wirtualną.<br/><br/> Zaszyfrowane klucze i wpisy tajne nie mogą być odczytywane przez nieautoryzowanych użytkowników ani przez platformę Azure.

W przypadku zarządzanych i niezaładowanych maszyn wirtualnych platformy Azure usługa Backup obsługuje obie maszyny wirtualne zaszyfrowane tylko za pomocą szyfrowania bek lub maszyny wirtualne zaszyfrowane za pomocą bek razem z keksami.

Zapasowe klucze SZYFROWANIA (wpisy tajne) i KEK (klucze) kopii zapasowej są szyfrowane. Mogą być odczytywane i używane tylko wtedy, gdy zostaną przywrócone z powrotem do magazynu kluczy przez autoryzowanych użytkowników. Nieautoryzowani użytkownicy ani platforma Azure nie mogą odczytywać ani używać kluczy ani wpisów tajnych kopii zapasowej.

Back up to also beks are back up. Dlatego w przypadku zgubień kluczy BEK autoryzowani użytkownicy mogą przywrócić te elementy do magazynu kluczy i odzyskać zaszyfrowane maszyny wirtualne. Tylko użytkownicy z wymaganym poziomem uprawnień mogą tworzyć i przywracać zaszyfrowane maszyny wirtualne lub klucze i wpisy tajne.

## <a name="snapshot-creation"></a>Tworzenie migawki

Azure Backup tworzyć migawki zgodnie z harmonogramem tworzenia kopii zapasowych.

- **Maszyny wirtualne z systemem Windows:** W przypadku maszyn wirtualnych z systemem Windows usługa Backup koordynuje się z usługą VSS w celu utworzenia spójnej na poziomie aplikacji migawki dysków maszyn wirtualnych.  Domyślnie program Azure Backup pełną kopię zapasową usługi VSS (obcina dzienniki aplikacji, takie jak SQL Server w czasie tworzenia kopii zapasowej, aby uzyskać spójną kopię zapasową na poziomie aplikacji).  Jeśli używasz bazy danych usługi SQL Server kopii zapasowej maszyny wirtualnej platformy Azure, możesz zmodyfikować ustawienie, aby wykonać kopię zapasową usługi VSS (aby zachować dzienniki). Więcej informacji znajduje się w [tym artykule](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Maszyny wirtualne z systemem Linux:** Aby tworzyć migawki maszyn wirtualnych z systemem Linux spójne z aplikacją, użyj struktury skryptów wstępnych i po skryptach systemu Linux, aby napisać własne skrypty niestandardowe w celu zapewnienia spójności.

  - Azure Backup wywołuje tylko skrypty wstępne/post napisane przez Ciebie.
  - Jeśli skrypty wstępne i skrypty wykonywane po operacji są wykonywane pomyślnie, Azure Backup punkt odzyskiwania jest oznaczany jako spójny z aplikacją. Jednak w przypadku korzystania ze skryptów niestandardowych ostatecznie ponosisz odpowiedzialność za spójność aplikacji.
  - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) na temat konfigurowania skryptów.

## <a name="snapshot-consistency"></a>Spójność migawek

W poniższej tabeli wyjaśniono różne typy spójności migawek:

**Snapshot** | **Szczegóły** | **Odzyskiwania** | **Kwestie do rozważenia**
--- | --- | --- | ---
**Spójność na aplikacji** | Kopie zapasowe spójne z aplikacją przechwytują zawartość pamięci i oczekujące operacje we/wy. Migawki spójne z aplikacją używają zapisu usługi VSS (lub skryptów wstępnych/post dla systemu Linux), aby zapewnić spójność danych aplikacji przed tworzeniem kopii zapasowej. | W przypadku odzyskiwania maszyny wirtualnej za pomocą migawki spójnej z aplikacją maszyna wirtualna jest uruchamiana. Nie ma uszkodzeń ani utraty danych. Aplikacje rozpoczynają się w spójnym stanie. | Windows: wszystkie autorzy usługi VSS zakończyli pomyślnie<br/><br/> Linux: skrypty wstępne/post są skonfigurowane i zakończyły się powodzeniem
**Spójność na system plików** | Kopie zapasowe spójne na poziomie systemu plików zapewniają spójność przez tworzenie migawki wszystkich plików w tym samym czasie.<br/><br/> | W przypadku odzyskiwania maszyny wirtualnej za pomocą migawki spójnej z systemem plików maszyna wirtualna jest uruchamiana. Nie ma uszkodzeń ani utraty danych. Aplikacje muszą implementować własny mechanizm "naprawiania", aby zapewnić spójność przywróconych danych. | Windows: niektóre autorzy usługi VSS nie powiodli się <br/><br/> Linux: wartość domyślna (jeśli skrypty wstępne/post nie są skonfigurowane lub zakończyły się niepowodzeniem)
**Spójność na awariach** | Migawki spójne na poziomie awarii zwykle występują, jeśli maszyna wirtualna platformy Azure zostanie zamknięta w momencie tworzenia kopii zapasowej. Przechwytywane i kopie zapasowe są przechwytywane i tworzyć kopie zapasowe tylko tych danych, które już istnieją na dysku. | Rozpoczyna się od procesu rozruchu maszyny wirtualnej, po którym następuje sprawdzenie dysku w celu naprawienia błędów uszkodzenia. Wszelkie operacje zapisu lub danych w pamięci, które nie zostały przeniesione na dysk przed awarią. Aplikacje implementują własną weryfikację danych. Na przykład aplikacja bazy danych może używać swojego dziennika transakcji do weryfikacji. Jeśli dziennik transakcji zawiera wpisy, które nie są w bazie danych, oprogramowanie bazy danych wycofuje transakcje do momentu spójności danych. | Maszyna wirtualna jest w stanie zamknięcia (zatrzymania/cofniania alokacji).

>[!NOTE]
> Jeśli aprowizowanie zakończy **się** pomyślnie, program Azure Backup kopie zapasowe spójne z systemem plików. Jeśli stan aprowowania jest **niedostępny** lub **nie powiódł się,** są podejmowane kopie zapasowe spójne na awarie. Jeśli stan aprowizowania  tworzy lub **usuwa**, oznacza to, Azure Backup ponawia próbę wykonania operacji.

## <a name="backup-and-restore-considerations"></a>Zagadnienia dotyczące tworzenia kopii zapasowych i przywracania

**Kwestie do rozważenia** | **Szczegóły**
--- | ---
**Dysk** | Tworzenie kopii zapasowej dysków maszyn wirtualnych jest równoległe. Jeśli na przykład maszyna wirtualna ma cztery dyski, usługa Backup próbuje utworzyć kopię zapasową wszystkich czterech dysków równolegle. Kopia zapasowa jest przyrostowa (tylko zmienione dane).
**Planowanie** |  Aby zmniejszyć ruch kopii zapasowych, należy tworzyć kopie zapasowe różnych maszyn wirtualnych o różnych godzinach dnia i upewnić się, że te czasy nie nakładają się na siebie. Tworzenie kopii zapasowych maszyn wirtualnych w tym samym czasie powoduje korki.
**Przygotowywanie kopii zapasowych** | Należy pamiętać o czasie potrzebnym na przygotowanie kopii zapasowej. Czas przygotowania obejmuje instalowanie lub aktualizowanie rozszerzenia kopii zapasowej oraz wyzwolenie migawki zgodnie z harmonogramem tworzenia kopii zapasowych.
**Transfer danych** | Należy wziąć pod uwagę czas potrzebny Azure Backup zidentyfikowania zmian przyrostowych z poprzedniej kopii zapasowej.<br/><br/> W przyrostowej kopii zapasowej usługa Azure Backup określa zmiany, obliczając sumę kontrolną bloku. Jeśli blok ulegnie zmianie, zostanie oznaczony do przesłania do magazynu. Usługa analizuje zidentyfikowane bloki i próbuje jeszcze bardziej zminimalizować ilość danych do przesłania. Po dokonaniu oceny wszystkich zmienionych bloków usługa Azure Backup przesyła zmiany do magazynu.<br/><br/> Może wystąpić opóźnienie między tworzeniem migawki i kopiowaniem jej do magazynu. W godzinach szczytu przesyłanie migawek do magazynu może potrwać do ośmiu godzin. Czas wykonywania kopii zapasowej maszyny wirtualnej będzie krótszy niż 24 godziny w przypadku codziennie wykonywanej kopii zapasowej.
**Początkowa kopia zapasowa** | Mimo że łączny czas wykonywania przyrostowych kopii zapasowych jest krótszy niż 24 godziny, może to nie dotyczyć pierwszej kopii zapasowej. Czas wymagany na utworzenie początkowej kopii zapasowej będzie zależeć od rozmiaru danych i terminu przetwarzania kopii zapasowej.
**Przywracanie kolejki** | Azure Backup przetwarza zadania przywracania z wielu kont magazynu w tym samym czasie i umieszcza żądania przywracania w kolejce.
**Przywracanie kopii** | Podczas procesu przywracania dane są kopiowane z magazynu na konto magazynu.<br/><br/> Łączny czas przywracania zależy od liczby operacji we/wy na sekundę (IOPS) i przepływności konta magazynu.<br/><br/> Aby skrócić czas kopiowania, wybierz konto magazynu, które nie jest ładowane z innymi zapisami i odczytami aplikacji.

### <a name="backup-performance"></a>Wydajność kopii zapasowych

Te typowe scenariusze mogą mieć wpływ na łączny czas wykonywania kopii zapasowej:

- **Dodawanie nowego dysku do chronionej maszyny wirtualnej platformy Azure:** Jeśli maszyna wirtualna jest w trakcie tworzenia przyrostowej kopii zapasowej i dodawany jest nowy dysk, czas tworzenia kopii zapasowej się zwiększy. Łączny czas wykonywania kopii zapasowej może trwać dłużej niż 24 godziny z powodu replikacji początkowej nowego dysku oraz replikacji różnicowej istniejących dysków.
- **Dyski pofragmentowane:** Operacje tworzenia kopii zapasowej są szybsze, gdy zmiany dysku są ciągłe. Jeśli zmiany zostaną rozłożone i pofragmentowane na dysku, tworzenie kopii zapasowej będzie wolniejsze.
- **Rezygnacja z dysku:** Jeśli chronione dyski, które są objęte przyrostową kopią zapasową, mają dzienny przyrost danych o pojemności większej niż 200 GB, tworzenie kopii zapasowej może zająć dużo czasu (ponad osiem godzin).
- **Wersje kopii zapasowych:** Najnowsza wersja usługi Backup (znana jako wersja natychmiastowego przywracania) używa bardziej zoptymalizowanego procesu niż porównanie sumy kontrolnej do identyfikowania zmian. Jeśli jednak używasz funkcji natychmiastowego przywracania i usunięto migawkę kopii zapasowej, kopia zapasowa przełącza się na porównanie sumy kontrolnej. W takim przypadku operacja tworzenia kopii zapasowej przekroczy 24 godziny (lub nie powiedzie się).

### <a name="restore-performance"></a>Wydajność przywracania

Te typowe scenariusze mogą mieć wpływ na łączny czas przywracania:

- Łączny czas przywracania zależy od liczby operacji wejścia/wyjścia na sekundę (IOPS) i przepływności konta magazynu.
- Łączny czas przywracania może mieć wpływ na załadowanie docelowego konta magazynu z innymi operacjami odczytu i zapisu aplikacji. Aby ulepszyć operację przywracania, wybierz konto magazynu, które nie jest ładowane z innymi danymi aplikacji.

## <a name="best-practices"></a>Najlepsze rozwiązania

Podczas konfigurowania kopii zapasowych maszyn wirtualnych sugerujemy następujące rozwiązania:

- Zmodyfikuj domyślne godziny harmonogramu ustawiane w ramach zasad. Na przykład jeśli domyślna godzina w zasadach to 12:00, zwiększ czas o kilka minut, aby zoptymalizować użycie zasobów.
- Jeśli przywracasz maszyny wirtualne z jednego magazynu, zdecydowanie zalecamy używanie różnych kont magazynu ogólnego przeznaczenia w wersji [2,](../storage/common/storage-account-upgrade.md) aby upewnić się, że docelowe konto magazynu nie zostanie ograniczone. Na przykład każda maszyna wirtualna musi mieć inne konto magazynu. Jeśli na przykład zostanie przywróconych 10 maszyn wirtualnych, użyj 10 różnych kont magazynu.
- W przypadku tworzenia kopii zapasowych maszyn wirtualnych używających magazynu w chmurze Premium z natychmiastowym przywracaniem zalecamy  przydzielenie *50%* wolnego miejsca w całkowitej przydzielonej przestrzeni dyskowej, co jest wymagane tylko w przypadku pierwszej kopii zapasowej. 50% wolnego miejsca nie jest wymagane do tworzenia kopii zapasowych po zakończeniu tworzenia pierwszej kopii zapasowej
- Limit liczby dysków na konto magazynu jest określany względem tego, w jakim stopniu aplikacje działające na maszynie wirtualnej w modelu infrastruktura jako usługa (IaaS) uzyskują dostęp do dysków. Zgodnie z ogólną praktyką, jeśli na jednym koncie magazynu znajduje się od 5 do 10 dysków lub więcej, należy zrównoważyć obciążenie przez przeniesienie niektórych dysków do oddzielnych kont magazynu.
- Aby przywrócić maszyny wirtualne z dyskami zarządzanymi przy użyciu programu PowerShell, podaj dodatkowy parametr ***TargetResourceGroupName,*** aby określić grupę zasobów, do której zostaną przywrócone dyski zarządzane. Dowiedz się [więcej tutaj.](./backup-azure-vms-automation.md#restore-managed-disks)

## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowych

Maszyny wirtualne platformy Azure, których kopię zapasową Azure Backup, podlegają Azure Backup [cenom.](https://azure.microsoft.com/pricing/details/backup/)

Rozliczanie nie rozpoczyna się, dopóki nie zakończy się tworzenie pierwszej pomyślnej kopii zapasowej. W tym momencie rozpocznie się rozliczanie zarówno magazynu, jak i chronionych maszyn wirtualnych. Rozliczanie jest kontynuowane, o ile wszystkie dane kopii zapasowej maszyny wirtualnej są przechowywane w magazynie. Jeśli zatrzymasz ochronę maszyny wirtualnej, ale dane kopii zapasowej maszyny wirtualnej istnieją w magazynie, rozliczenia będą kontynuowane.

Rozliczanie określonej maszyny wirtualnej jest zatrzymywane tylko wtedy, gdy ochrona zostanie zatrzymana i wszystkie dane kopii zapasowej zostaną usunięte. Gdy ochrona zostaje zatrzymana i nie ma aktywnych zadań tworzenia kopii zapasowej, rozmiar ostatniej pomyślnej kopii zapasowej maszyny wirtualnej staje się rozmiarem wystąpienia chronionego używanym na rachunku miesięcznym.

Obliczanie rozmiaru chronionego wystąpienia jest oparte na *rzeczywistym* rozmiarze maszyny wirtualnej. Rozmiar maszyny wirtualnej jest sumą wszystkich danych na maszynie wirtualnej, z wyjątkiem magazynu tymczasowego. Ceny są oparte na rzeczywistych danych przechowywanych na dyskach danych, a nie na maksymalnym obsługiwanym rozmiarze dla każdego dysku danych dołączonego do maszyny wirtualnej.

Podobnie rachunek za magazyn kopii zapasowych jest oparty na ilości danych przechowywanych w magazynie Azure Backup, która jest sumą rzeczywistych danych w każdym punkcie odzyskiwania.

Na przykład weźmy maszynę wirtualną o rozmiarze A2 w warstwie Standardowa, która ma dwa dodatkowe dyski danych o maksymalnym rozmiarze 32 TB każdy. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

**Dysk** | **Maksymalny rozmiar** | **Obecne rzeczywiste dane**
--- | --- | ---
Dysk systemu operacyjnego | 32 TB | 17 GB
Dysk lokalny/tymczasowy | 135 GB | 5 GB (nie obejmuje kopii zapasowej)
Dysk danych 1 | 32 TB| 30 GB
Dysk danych 2 | 32 TB | 0 GB

Rzeczywisty rozmiar maszyny wirtualnej w tym przypadku wynosi 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronionego wystąpienia (47 GB) staje się podstawą miesięcznego rachunku. W miarę wzrostu ilości danych na maszynie wirtualnej rozmiar chronionego wystąpienia używany do zmiany rozliczeń zmienia się w celu dopasowania.

## <a name="next-steps"></a>Następne kroki

- [Przygotuj się do tworzenia kopii zapasowej maszyny wirtualnej platformy Azure.](backup-azure-arm-vms-prepare.md)