---
title: Informacje o kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak usługa Azure Backup wykonuje kopie zapasowe maszyn wirtualnych platformy Azure oraz jak postępować zgodnie z najlepszymi rozwiązaniami.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 30d27f3f9c559fd149bd45f303127e0eec40b878
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173854"
---
# <a name="an-overview-of-azure-vm-backup"></a>Omówienie kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano, jak [usługa Azure Backup](./backup-overview.md) wykonuje kopie zapasowe maszyn wirtualnych platformy Azure.

Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi funkcjami zarządzania punktami odzyskiwania. Konfiguracja i skalowanie są proste, kopie zapasowe są optymalizowane i w razie potrzeby można je łatwo przywrócić.

W ramach procesu tworzenia kopii zapasowej [jest wykonywana migawka](#snapshot-creation), a dane są przesyłane do magazynu Recovery Services bez wpływu na obciążenia produkcyjne. Migawka zawiera różne poziomy spójności, zgodnie z opisem w [tym miejscu](#snapshot-consistency).

Azure Backup również oferuje wyspecjalizowane oferty dla obciążeń bazy danych, takich jak [SQL Server](backup-azure-sql-database.md) i [SAP HANA](sap-hana-db-about.md) , które są oparte na obciążeniu, oferują 15-minutowy cel punktu odzyskiwania, a także umożliwia wykonywanie kopii zapasowych i przywracanie poszczególnych baz danych.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Usługa Azure Backup wykonuje kopię zapasową maszyn wirtualnych platformy Azure w następujący sposób:

1. W przypadku maszyn wirtualnych platformy Azure wybranych do utworzenia kopii zapasowej Azure Backup uruchamia zadanie tworzenia kopii zapasowej zgodnie z określonym harmonogramem tworzenia kopii zapasowych.
1. Podczas pierwszej kopii zapasowej, rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest zainstalowane [rozszerzenie VMSnapshot](../virtual-machines/extensions/vmsnapshot-windows.md) .
    - W przypadku maszyn wirtualnych z systemem Linux jest zainstalowane [rozszerzenie VMSnapshotLinux](../virtual-machines/extensions/vmsnapshot-linux.md) .
1. W przypadku maszyn wirtualnych z systemem Windows, które są uruchomione, współpracują z systemem Windows Usługa kopiowania woluminów w tle (VSS) w celu utworzenia migawki maszyny wirtualnej spójnej na poziomie aplikacji.
    - Domyślnie kopia zapasowa pobiera pełne kopie zapasowe usługi VSS.
    - Jeśli kopia zapasowa nie może pobrać migawki spójnej na poziomie aplikacji, zostanie przeprowadzona spójna z plikiem migawka magazynu (ponieważ nie ma żadnych zapisów aplikacji podczas zatrzymania maszyny wirtualnej).
1. W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa pobiera kopię zapasową spójną na poziomie plików. W przypadku migawek spójnych z aplikacjami należy ręcznie dostosować skrypty poprzedzające i końcowe.
1. Gdy kopia zapasowa pobiera migawkę, przesyła dane do magazynu.
    - Operacja tworzenia kopii zapasowej jest zoptymalizowana tak, aby kopie zapasowe poszczególnych dysków maszyny wirtualnej były tworzone równolegle.
    - W przypadku każdego dysku, którego kopia zapasowa jest tworzona, usługa Azure Backup odczytuje bloki danych na dysku, a następnie identyfikuje i przenosi tylko te z nich, które uległy zmianie (przyrost) od czasu utworzenia poprzedniej kopii zapasowej.
    - Dane migawki mogą nie być natychmiast kopiowane do magazynu. W godzinach szczytu może upłynąć kilka godzin. W przypadku zasad codziennego tworzenia kopii zapasowych łączny czas tworzenia kopii zapasowej maszyny wirtualnej jest krótszy niż 24 godziny.
1. Zmiany wprowadzone do maszyny wirtualnej z systemem Windows po włączeniu Azure Backup są następujące:
    - Pakiet redystrybucyjny Microsoft Visual C++ 2013 (x64) — 12.0.40660 jest zainstalowany na maszynie wirtualnej
    - Typ uruchamiania usługi kopiowania woluminów w tle (VSS) zmieniony na automatyczny przy użyciu instrukcji Manual
    - Usługa IaaSVmProvider systemu Windows jest dodawana

1. Po zakończeniu transferu danych migawka zostanie usunięta i zostanie utworzony punkt odzyskiwania.

![Architektura kopii zapasowej maszyny wirtualnej platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Szyfrowanie kopii zapasowych maszyny wirtualnej platformy Azure

Podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z Azure Backup maszyny wirtualne są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE). Azure Backup może również tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu Azure Disk Encryption.

**Szyfrowanie** | **Szczegóły** | **Pomoc techniczna**
--- | --- | ---
**SSE** | W przypadku używania instrukcji SSE usługa Azure Storage zapewnia szyfrowanie w spoczynku przez automatyczne szyfrowanie danych przed ich zapisaniem. Usługa Azure Storage odszyfrowuje również dane przed ich pobraniem. Azure Backup obsługuje kopie zapasowe maszyn wirtualnych z dwoma typami szyfrowanie usługi Storage:<li> **SSE z kluczami zarządzanymi na platformie**: to szyfrowanie jest domyślnie przeznaczone dla wszystkich dysków w maszynach wirtualnych. Zobacz więcej [tutaj](../virtual-machines/windows/disk-encryption.md#platform-managed-keys).<li> **SSE z kluczami zarządzanymi przez klienta**. Program CMK umożliwia zarządzanie kluczami używanymi do szyfrowania dysków. Zobacz więcej [tutaj](../virtual-machines/windows/disk-encryption.md#customer-managed-keys). | Azure Backup używa instrukcji SSE do szyfrowania w czasie spoczynku maszyn wirtualnych platformy Azure.
**Usługa Azure Disk Encryption** | Azure Disk Encryption szyfruje zarówno dyski systemu operacyjnego, jak i danych dla maszyn wirtualnych platformy Azure.<br/><br/> Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEKs), które są chronione w magazynie kluczy jako wpisy tajne. Azure Disk Encryption integruje się również z kluczami szyfrowania klucza Azure Key Vault (KEKs). | Azure Backup obsługuje tworzenie kopii zapasowych zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure szyfrowanych tylko za pomocą BEKs lub z BEKs razem z KEKs.<br/><br/> BEKs i KEKs są archiwizowane i szyfrowane.<br/><br/> Ponieważ kopie zapasowe KEKs i BEKs są tworzone, użytkownicy z niezbędnymi uprawnieniami mogą przywrócić klucze i wpisy tajne z powrotem do magazynu kluczy, jeśli jest to konieczne. Ci użytkownicy mogą również odzyskać zaszyfrowaną maszynę wirtualną.<br/><br/> Zaszyfrowane klucze i wpisy tajne nie mogą być odczytywane przez nieautoryzowanych użytkowników lub platformę Azure.

W przypadku zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure usługa Backup obsługuje zarówno maszyny wirtualne zaszyfrowane za pomocą BEKs, jak i maszyny wirtualne zaszyfrowane za pomocą usługi BEKs oraz KEKs.

Kopie zapasowe BEKs (Secret) i KEKs (klucze) są szyfrowane. Mogą być odczytywane i używane tylko wtedy, gdy są przywracane do magazynu kluczy przez autoryzowanych użytkowników. Żaden z nieautoryzowanych użytkowników lub platforma Azure mogą odczytywać lub używać kopii zapasowych kluczy lub wpisów tajnych.

BEKs również kopie zapasowe. Dlatego jeśli BEKs zostaną utracone, autoryzowani użytkownicy mogą przywrócić BEKs do magazynu kluczy i odzyskać zaszyfrowane maszyny wirtualne. Tylko użytkownicy z wymaganym poziomem uprawnień mogą tworzyć kopie zapasowe zaszyfrowanych maszyn wirtualnych lub kluczy i wpisów tajnych oraz przywracać je.

## <a name="snapshot-creation"></a>Tworzenie migawki

Azure Backup wykonuje migawki zgodnie z harmonogramem tworzenia kopii zapasowych.

- **Maszyny wirtualne z systemem Windows:** W przypadku maszyn wirtualnych z systemem Windows usługa tworzenia kopii zapasowych koordynuje się z usługą VSS w celu utworzenia migawki dysków maszyn wirtualnych spójnej na poziomie aplikacji.  Domyślnie usługa Azure Backup pobiera pełną kopię zapasową VSS (obcina Dzienniki aplikacji, takie jak SQL Server w momencie tworzenia kopii zapasowej, aby uzyskać kopię zapasową spójną na poziomie aplikacji).  Jeśli używasz bazy danych SQL Server w kopii zapasowej maszyny wirtualnej platformy Azure, możesz zmodyfikować to ustawienie, aby wykonać kopię zapasową usługi VSS (w celu zachowania dzienników). Aby uzyskać więcej informacji, zobacz [ten artykuł](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Maszyny wirtualne z systemem Linux:** Aby tworzyć migawki maszyn wirtualnych z systemem Linux spójne z aplikacjami, należy użyć skryptów przedskryptowych i skryptów po skrypcie dla systemu Linux do pisania własnych skryptów niestandardowych w celu zapewnienia spójności.

  - Azure Backup wywołuje tylko skrypty poprzedzające lub gotowe.
  - Jeśli skrypty wstępne i po wykonaniu zostały wykonane pomyślnie, Azure Backup oznacza punkt odzyskiwania jako spójny dla aplikacji. Jeśli jednak używasz skryptów niestandardowych, jesteś w końcu odpowiedzialny za spójność aplikacji.
  - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) o konfigurowaniu skryptów.

## <a name="snapshot-consistency"></a>Spójność migawek

W poniższej tabeli objaśniono różne typy spójności migawek:

**Snapshot** | **Szczegóły** | **Odzyskiwania** | **Zagadnienie**
--- | --- | --- | ---
**Spójna na poziomie aplikacji** | Kopie zapasowe spójne z aplikacjami przechwytują zawartość pamięci i oczekujące operacje we/wy. Migawki spójne z aplikacjami używają składnika zapisywania usługi VSS (lub skryptów pre/post dla systemu Linux), aby zapewnić spójność danych aplikacji przed wystąpieniem kopii zapasowej. | Podczas odzyskiwania maszyny wirtualnej za pomocą migawki spójnej na poziomie aplikacji maszyna wirtualna jest uruchamiana. Nie występują uszkodzenia ani utrata danych. Aplikacje są uruchamiane w spójnym stanie. | System Windows: wszystkie składniki zapisywania usługi VSS zostały pomyślnie zakończone<br/><br/> Linux: skrypty poprzedzające i końcowe zostały skonfigurowane i zakończyły się powodzeniem
**Spójny system plików** | Spójne kopie zapasowe systemu plików zapewniają spójność, pobierając migawkę wszystkich plików w tym samym czasie.<br/><br/> | Podczas odzyskiwania maszyny wirtualnej za pomocą migawki spójnej z systemem plików, maszyna wirtualna jest uruchamiana. Nie występują uszkodzenia ani utrata danych. Aplikacje muszą implementować własny mechanizm naprawy, aby upewnić się, że przywrócone dane są spójne. | System Windows: niepowodzenie niektórych składników zapisywania usługi VSS <br/><br/> Linux: wartość domyślna (Jeśli skrypty pre/post nie są skonfigurowane lub zakończyły się niepowodzeniem)
**Spójny na poziomie awarii** | Migawki spójne z awarią są zwykle wykonywane, gdy maszyna wirtualna platformy Azure jest zamykana w momencie tworzenia kopii zapasowej. Przechwytywane i tworzone są kopie zapasowe tylko danych istniejących na dysku w momencie tworzenia kopii zapasowej. | Rozpoczyna się od procesu rozruchu maszyny wirtualnej, po którym następuje sprawdzenie dysku, aby naprawić błędy uszkodzeń. Wszelkie operacje dotyczące danych w pamięci lub zapisu, które nie zostały przekazane do dysku przed utratą awarii. Aplikacje implementują własne weryfikacje danych. Na przykład aplikacja bazy danych może użyć dziennika transakcji do weryfikacji. Jeśli w dzienniku transakcji znajdują się wpisy, które nie znajdują się w bazie danych, oprogramowanie bazy danych przenosi transakcje z powrotem do momentu spójności danych. | Maszyna wirtualna jest w stanie zamykania (zatrzymano/cofnięto przydział).

>[!NOTE]
> W przypadku **pomyślnego**stanu aprowizacji Azure Backup pobiera spójne kopie zapasowe systemu plików. Jeśli stan aprowizacji jest **niedostępny** lub **zakończył się niepowodzeniem**, tworzone są kopie zapasowe spójne z awarią. Jeśli stan aprowizacji jest **tworzony** lub **usuwany**, oznacza to, że Azure Backup próbuje wykonać operację ponownie.

## <a name="backup-and-restore-considerations"></a>Zagadnienia dotyczące tworzenia kopii zapasowych i przywracania

**Zagadnienie** | **Szczegóły**
--- | ---
**Dysk** | Tworzenie kopii zapasowych dysków maszyny wirtualnej jest równoległe. Na przykład jeśli maszyna wirtualna ma cztery dyski, usługa tworzenia kopii zapasowych próbuje wykonać kopię zapasową wszystkich czterech dysków równolegle. Kopia zapasowa jest przyrostowa (dotyczy tylko zmienionych danych).
**Planowanie** |  Aby zmniejszyć ruch kopii zapasowych, wykonaj kopię zapasową różnych maszyn wirtualnych w różnych porach dnia i upewnij się, że czasy nie nakładają się na siebie. Tworzenie kopii zapasowych maszyn wirtualnych w tym samym czasie powoduje korki.
**Przygotowywanie kopii zapasowych** | Należy pamiętać o czasie potrzebnym do przygotowania kopii zapasowej. Czas przygotowania obejmuje instalowanie lub aktualizowanie rozszerzenia kopii zapasowej oraz wyzwolenie migawki zgodnie z harmonogramem tworzenia kopii zapasowych.
**Transfer danych** | Należy wziąć pod uwagę czas wymagany do Azure Backup identyfikować przyrostowe zmiany z poprzedniej kopii zapasowej.<br/><br/> W przyrostowej kopii zapasowej usługa Azure Backup określa zmiany, obliczając sumę kontrolną bloku. Jeśli blok ulegnie zmianie, zostanie oznaczony do przesłania do magazynu. Usługa analizuje zidentyfikowane bloki i próbuje jeszcze bardziej zminimalizować ilość danych do przesłania. Po dokonaniu oceny wszystkich zmienionych bloków usługa Azure Backup przesyła zmiany do magazynu.<br/><br/> Może wystąpić opóźnienie między tworzeniem migawki i kopiowaniem jej do magazynu. W godzinach szczytu przekazanie migawek do magazynu może trwać do ośmiu godzin. Czas wykonywania kopii zapasowej maszyny wirtualnej będzie krótszy niż 24 godziny w przypadku codziennie wykonywanej kopii zapasowej.
**Początkowa kopia zapasowa** | Mimo że łączny czas wykonywania przyrostowych kopii zapasowych jest krótszy niż 24 godziny, może to nie dotyczyć pierwszej kopii zapasowej. Czas wymagany na utworzenie początkowej kopii zapasowej będzie zależeć od rozmiaru danych i terminu przetwarzania kopii zapasowej.
**Przywróć kolejkę** | Azure Backup przetwarza zadania przywracania z wielu kont magazynu w tym samym czasie i umieszcza żądania przywracania w kolejce.
**Przywróć kopię** | Podczas przywracania dane są kopiowane z magazynu do konta magazynu.<br/><br/> Łączny czas przywracania zależy od operacji we/wy na sekundę (IOPS) i przepływności konta magazynu.<br/><br/> Aby skrócić czas kopiowania, wybierz konto magazynu, które nie jest załadowane z innymi zapisami i odczytami aplikacji.

### <a name="backup-performance"></a>Wydajność kopii zapasowej

Te typowe scenariusze mogą mieć wpływ na łączny czas wykonywania kopii zapasowej:

- **Dodawanie nowego dysku do chronionej maszyny wirtualnej platformy Azure:** Jeśli maszyna wirtualna jest w trakcie przyrostowej kopii zapasowej i zostanie dodany nowy dysk, zwiększy się czas tworzenia kopii zapasowej. Łączny czas wykonywania kopii zapasowej może trwać ponad 24 godziny z powodu początkowej replikacji nowego dysku wraz z replikacją różnicową istniejących dysków.
- **Pofragmentowane dyski:** Operacje tworzenia kopii zapasowych są szybsze, gdy zmiany dysku są ciągłe. Jeśli zmiany zostaną rozłożone i pofragmentowane na dysku, kopia zapasowa będzie wolniejsza.
- Zmiany **dysku:** W przypadku dysków chronionych, które przechodzą przyrostową kopię zapasową, można wykonać codzienne zmiany o ponad 200 GB, a wykonywanie kopii zapasowej może trwać długo (więcej niż osiem godzin).
- **Wersje kopii zapasowej:** Najnowsza wersja kopii zapasowej (znana jako wersja natychmiastowego przywracania) używa bardziej zoptymalizowanego procesu niż porównanie sum kontrolnych w celu identyfikowania zmian. Ale jeśli używasz natychmiastowego przywracania i usunięto migawkę kopii zapasowej, kopia zapasowa przełączy się do porównania sum kontrolnych. W takim przypadku operacja tworzenia kopii zapasowej będzie przekroczyć 24 godziny (lub zakończyć się niepowodzeniem).

### <a name="restore-performance"></a>Przywracanie wydajności

Te typowe scenariusze mogą mieć wpływ na łączny czas przywracania:

- Łączny czas przywracania zależy od operacji wejścia/wyjścia na sekundę (IOPS) oraz przepływności konta magazynu.
- Łączny czas przywracania może być narażony, jeśli docelowe konto magazynu zostanie załadowane z innymi operacjami odczytu i zapisu aplikacji. Aby ulepszyć operację przywracania, wybierz konto magazynu, które nie zostało załadowane z innymi danymi aplikacji.

## <a name="best-practices"></a>Najlepsze rozwiązania

Podczas konfigurowania kopii zapasowych maszyn wirtualnych sugerujemy następujące rozwiązania:

- Zmodyfikuj domyślne godziny harmonogramu ustawiane w ramach zasad. Na przykład jeśli domyślna godzina w zasadach to 12:00, zwiększ czas o kilka minut, aby zoptymalizować użycie zasobów.
- Jeśli przywracasz maszyny wirtualne z jednego magazynu, zdecydowanie zalecamy użycie różnych [kont magazynu ogólnego przeznaczenia w wersji 2](../storage/common/storage-account-upgrade.md) , aby upewnić się, że docelowe konto magazynu nie zostanie ograniczone. Na przykład każda maszyna wirtualna musi mieć inne konto magazynu. Na przykład jeśli zostaną przywrócone 10 maszyn wirtualnych, użyj 10 różnych kont magazynu.
- W przypadku tworzenia kopii zapasowych maszyn wirtualnych korzystających z usługi Premium Storage z natychmiastowym przywracaniem zalecamy alokowanie *50%* wolnego miejsca w łącznym przydzielonym miejscu do magazynowania, które jest wymagane **tylko** dla pierwszej kopii zapasowej. Ilość wolnego miejsca na 50% nie jest wymagana w przypadku kopii zapasowych po wykonaniu pierwszej kopii zapasowej
- Limit liczby dysków na konto magazynu jest określany względem tego, w jakim stopniu aplikacje działające na maszynie wirtualnej w modelu infrastruktura jako usługa (IaaS) uzyskują dostęp do dysków. Zgodnie z ogólną praktyką, jeśli na jednym koncie magazynu znajduje się od 5 do 10 dysków lub więcej, należy zrównoważyć obciążenie przez przeniesienie niektórych dysków do oddzielnych kont magazynu.

## <a name="backup-costs"></a>Koszty kopii zapasowych

Maszyny wirtualne platformy Azure z kopiami zapasowymi Azure Backup podlegają [Azure Backup cenniku](https://azure.microsoft.com/pricing/details/backup/).

Rozliczanie nie rozpocznie się, dopóki nie zostanie ukończona pierwsza kopia zapasowa. W tym momencie rozpocznie się rozliczanie zarówno dla magazynu, jak i chronionych maszyn wirtualnych. Rozliczanie jest kontynuowane, o ile wszystkie dane kopii zapasowej maszyny wirtualnej są przechowywane w magazynie. Jeśli zatrzymasz ochronę dla maszyny wirtualnej, ale dane kopii zapasowej dla maszyny wirtualnej istnieją w magazynie, rozliczenia będą kontynuowane.

Rozliczanie dla określonej maszyny wirtualnej zatrzymuje się tylko wtedy, gdy ochrona została zatrzymana i wszystkie dane kopii zapasowej zostaną usunięte. Po zatrzymaniu ochrony, gdy nie ma aktywnych zadań kopii zapasowej, rozmiar ostatniej pomyślnej kopii zapasowej maszyny wirtualnej to rozmiar chronionego wystąpienia użyty dla rachunku miesięcznego.

Obliczanie rozmiaru chronionego wystąpienia jest zależne od *rzeczywistego* rozmiaru maszyny wirtualnej. Rozmiar maszyny wirtualnej to suma wszystkich danych w maszynie wirtualnej, z wyłączeniem magazynu tymczasowego. Cennik jest oparty na rzeczywistych danych przechowywanych na dyskach danych, a nie na maksymalnym obsługiwanym rozmiarze dla każdego dysku danych dołączonego do maszyny wirtualnej.

Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych przechowywanych w Azure Backup, która jest sumą rzeczywistych danych w poszczególnych punktach odzyskiwania.

Załóżmy na przykład, że maszyna wirtualna o rozmiarze a2 ma dwa dodatkowe dyski z danymi o maksymalnym rozmiarze 32 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

**Dysk** | **Maksymalny rozmiar** | **Rzeczywiste dane obecne**
--- | --- | ---
Dysk systemu operacyjnego | 32 TB | 17 GB
Dysk lokalny/tymczasowy | 135 GB | 5 GB (nie uwzględniono w kopii zapasowej)
Dysk danych 1 | 32 TB| 30 GB
Dysk danych 2 | 32 TB | 0 GB

Rzeczywistą wielkością maszyny wirtualnej w tym przypadku jest 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronionego wystąpienia (47 GB) stanowi podstawę dla rachunku miesięcznego. Wraz ze wzrostem ilości danych w maszynie wirtualnej rozmiar chronionego wystąpienia używany do zmiany rozliczeń jest zgodny.

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do tworzenia kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-arm-vms-prepare.md).