---
title: Odzyskiwanie plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak odzyskiwać pliki i foldery z punktu odzyskiwania maszyny wirtualnej platformy Azure.
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: references_regions
ms.openlocfilehash: ed231a4870af7489d48ff54548be380c2cf0799c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864894"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure

Azure Backup oferuje możliwość przywracania [maszyn wirtualnych platformy Azure i dysków](./backup-azure-arm-restore-vms.md) z kopii zapasowych maszyny wirtualnej platformy Azure, znanych również jako punkty odzyskiwania. W tym artykule wyjaśniono, jak odzyskiwać pliki i foldery z kopii zapasowej maszyny wirtualnej platformy Azure. Przywracanie plików i folderów jest dostępne tylko dla maszyn wirtualnych platformy Azure wdrożonych przy użyciu modelu Menedżer zasobów i chronionych z magazynem Recovery Services.


> [!NOTE]
> Ta funkcja jest dostępna dla maszyn wirtualnych platformy Azure wdrożonych przy użyciu modelu Menedżer zasobów i chronionych z magazynem Recovery Services.
> Odzyskiwanie plików z kopii zapasowej zaszyfrowanej maszyny wirtualnej nie jest obsługiwane.
>

![Przepływ pracy odzyskiwania folderu plików](./media/backup-azure-restore-files-from-vm/file-recovery-1.png)

## <a name="step-1-generate-and-download-script-to-browse-and-recover-files"></a>Krok 1. Generowanie i pobieranie skryptu w celu przeglądania i odzyskiwania plików

Aby przywrócić pliki lub foldery z punktu odzyskiwania, przejdź do maszyny wirtualnej i wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.Azure.com) i w okienku po lewej stronie wybierz pozycję **maszyny wirtualne**. Z listy maszyn wirtualnych wybierz maszynę wirtualną, aby otworzyć pulpit nawigacyjny maszyny wirtualnej.

2. W menu maszyny wirtualnej wybierz pozycję **kopia zapasowa** , aby otworzyć pulpit nawigacyjny kopii zapasowych.

    ![Otwórz element kopii zapasowej magazynu Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. W menu pulpitu nawigacyjnego kopii zapasowej wybierz pozycję **odzyskiwanie plików**.

    ![Wybierz odzyskiwanie plików](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)32

    Zostanie otwarte menu **odzyskiwanie plików** .

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z menu rozwijanego **Wybieranie punktu odzyskiwania** wybierz punkt odzyskiwania, w którym znajdują się odpowiednie pliki. Domyślnie jest już wybrany najnowszy punkt odzyskiwania.

5. Wybierz pozycję **Pobierz plik wykonywalny** (dla maszyn wirtualnych systemu Windows Azure) lub **Pobierz skrypt** (dla maszyn wirtualnych z systemem Linux Azure zostanie wygenerowany skrypt języka Python), aby pobrać oprogramowanie używane do kopiowania plików z punktu odzyskiwania.

    ![Pobierz plik wykonywalny](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Platforma Azure pobiera plik wykonywalny lub skrypt na komputer lokalny.

    ![Pobierz komunikat dla pliku wykonywalnego lub skryptu](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Aby uruchomić plik wykonywalny lub skrypt jako administrator, sugerujemy zapisanie pobranego pliku na komputerze.

6. Plik wykonywalny lub skrypt jest chroniony hasłem i wymaga hasła. W menu **odzyskiwanie plików** wybierz przycisk Kopiuj, aby załadować hasło do pamięci.

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/generated-pswd.png)


## <a name="step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script"></a>Krok 2. upewnienie się, że komputer spełnia wymagania przed wykonaniem skryptu

Po pomyślnym pobraniu skryptu upewnij się, że masz odpowiednią maszynę do wykonania tego skryptu. Maszyna wirtualna, na której planujesz wykonać skrypt, nie powinna mieć żadnej z następujących nieobsługiwanych konfiguracji. **Jeśli tak, wybierz komputer alternatywny najlepiej z tego samego regionu, który spełnia wymagania**.  

### <a name="dynamic-disks"></a>Dyski dynamiczne

Nie można uruchomić skryptu wykonywalnego na maszynie wirtualnej przy użyciu żadnej z następujących cech: Wybierz maszynę alternatywną

- Woluminy obejmujące wiele dysków (woluminy łączone i rozłożone).
- Woluminy odporne na uszkodzenia (woluminy dublowane i RAID-5) na dyskach dynamicznych.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania w systemie Windows

Nie można uruchomić pobranego pliku wykonywalnego na tej samej kopii zapasowej maszyny wirtualnej, jeśli kopia zapasowa maszyny wirtualnej zawiera funkcję miejsca do magazynowania systemu Windows. Wybierz komputer alternatywny.

### <a name="virtual-machine-backups-having-large-disks"></a>Kopie zapasowe maszyn wirtualnych z dużymi dyskami

Jeśli kopia zapasowa komputera ma dużą liczbę dysków (>16) lub duże dyski (> 4 TB), nie zaleca się wykonywania skryptu na tym samym komputerze na potrzeby przywracania, ponieważ będzie on miał znaczny wpływ na maszynę wirtualną. Zamiast tego zaleca się używanie oddzielnej maszyny wirtualnej tylko do odzyskiwania plików (maszyny wirtualne D2v3 maszyny wirtualnej platformy Azure), a następnie wyłączenie jej w razie braku potrzeby. 

Zobacz wymagania dotyczące przywracania plików z kopii zapasowych maszyn wirtualnych z dużą ilością dysku:<br>
[System operacyjny Windows](#for-backed-up-vms-with-large-disks-windows)<br>
[System operacyjny Linux](#for-backed-up-vms-with-large-disks-linux)

Po wybraniu odpowiedniej maszyny do uruchomienia skryptu ILR upewnij się, że spełnia [wymagania systemu operacyjnego](#step-3-os-requirements-to-successfully-run-the-script) i [wymagania dotyczące dostępu](#step-4-access-requirements-to-successfully-run-the-script). 

## <a name="step-3-os-requirements-to-successfully-run-the-script"></a>Krok 3. wymagania systemu operacyjnego umożliwiające pomyślne uruchomienie skryptu

Maszyna wirtualna, na której ma zostać uruchomiony pobrany skrypt, musi spełniać poniższe wymagania.

### <a name="for-windows-os"></a>System operacyjny Windows

W poniższej tabeli przedstawiono zgodność między systemami operacyjnymi serwera i komputerów. Podczas odzyskiwania plików nie można przywrócić plików do poprzedniej lub przyszłej wersji systemu operacyjnego. Na przykład nie można przywrócić pliku z maszyny wirtualnej z systemem Windows Server 2016 do systemu Windows Server 2012 lub Windows 8. Można przywrócić pliki z maszyny wirtualnej do tego samego serwera systemu operacyjnego lub do zgodnego systemu operacyjnego klienta.

|System operacyjny serwera | Zgodny system operacyjny klienta  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 z dodatkiem R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 z dodatkiem R2 | Windows 7   |

### <a name="for-linux-os"></a>Dla systemu operacyjnego Linux

W systemie Linux system operacyjny komputera służącego do przywracania plików musi obsługiwać systemy plików chronionej maszyny wirtualnej. Po wybraniu komputera do uruchomienia skryptu upewnij się, że komputer ma zgodny system operacyjny i korzysta z jednej z wersji zidentyfikowanych w poniższej tabeli:

|System operacyjny Linux | Wersje  |
| --------------- | ---- |
| Ubuntu | 12,04 i więcej |
| CentOS | 6,5 i więcej  |
| RHEL | 6,7 i więcej |
| Debian | 7 i nowsze |
| Oracle Linux | 6,4 i więcej |
| SLES | 12 i nowsze |
| openSUSE | 42,2 i więcej |

> [!NOTE]
> Znaleźliśmy problemy z uruchamianiem skryptu odzyskiwania plików na maszynach z systemem operacyjnym SLES 12 SP4 i badamy z zespołem SLES.
> Obecnie uruchomienie skryptu odzyskiwania plików działa na komputerach z SLES 12 SP2 i wersjami systemu operacyjnego z dodatkiem SP3.
>

Skrypt wymaga również, aby składniki Python i bash były bezpiecznie wykonywane i połączone z punktem odzyskiwania.

|Składnik | Wersja  |
| --------------- | ---- |
| bash | 4 i nowsze |
| python | 2.6.6 i nowsze  |
| .NET | 4.6.2 i nowsze |
| TLS | 1,2 powinna być obsługiwana  |

Upewnij się również, że masz odpowiednią [maszynę do wykonania skryptu ILR](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) i że spełnia on [wymagania dotyczące dostępu](#step-4-access-requirements-to-successfully-run-the-script).

## <a name="step-4-access-requirements-to-successfully-run-the-script"></a>Krok 4: wymagania dostępu do pomyślnie uruchomionego skryptu

W przypadku uruchamiania skryptu na komputerze z ograniczonym dostępem upewnij się, że masz dostęp do:

- `download.microsoft.com`
- Adresy URL usługi odzyskiwania (geograficzne nazwy odnoszą się do regionu, w którym znajduje się magazyn Recovery Services)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.com` (W przypadku regionów publicznych platformy Azure)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.cn` (W przypadku platformy Azure — Chiny)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.us` (Dla instytucji rządowych USA platformy Azure)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.de` (W przypadku platformy Azure (Niemcy)
- Porty wychodzące 53 (DNS), 443, 3260

> [!NOTE]
>
> Plik skryptu pobrany w [kroku 1 będzie](#step-1-generate-and-download-script-to-browse-and-recover-files) miał **nazwę geograficzną** w nazwie pliku. Użyj tej **nazwy geograficznej** , aby wypełnić adres URL. Nazwa pobranego skryptu rozpocznie się od: \' VMName \' \_ \' geoname \' _ \' GUID \' .<br><br>
> Na przykład jeśli nazwa pliku skryptu jest *ContosoVM_wcus_12345678*, **Nazwa geograficzna** to *wcus* , a adres URL to:<br> <https://pod01-rec2.wcus.backup.windowsazure.com>
>

W przypadku systemu Linux skrypt wymaga składników "Open-iSCSI" i "lshw", aby nawiązać połączenie z punktem odzyskiwania. Jeśli składniki nie istnieją na komputerze, na którym skrypt jest uruchamiany, skrypt monituje o zgodę na zainstalowanie składników programu. Wyrażanie zgody na zainstalowanie niezbędnych składników.

Dostęp do programu `download.microsoft.com` jest wymagany do pobierania składników używanych do tworzenia bezpiecznego kanału między komputerem, na którym skrypt jest uruchamiany, a danymi w punkcie odzyskiwania.

Upewnij się również, że masz odpowiednią [maszynę do wykonania skryptu ILR](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) i że spełnia on [wymagania systemu operacyjnego](#step-3-os-requirements-to-successfully-run-the-script).

## <a name="step-5-running-the-script-and-identifying-volumes"></a>Krok 5. Uruchamianie skryptu i identyfikowanie woluminów

### <a name="for-windows"></a>W przypadku systemu Windows

Po spełnieniu wszystkich wymagań wymienionych w [kroku 2](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script), [krok 3](#step-3-os-requirements-to-successfully-run-the-script) i [4](#step-4-access-requirements-to-successfully-run-the-script), skopiuj skrypt z pobranej lokalizacji (zazwyczaj folder pliki do pobrania), zobacz [krok 1, aby dowiedzieć się, jak wygenerować i pobrać skrypt](#step-1-generate-and-download-script-to-browse-and-recover-files). Kliknij prawym przyciskiem myszy plik wykonywalny i uruchom go z poświadczeniami administratora. Po wyświetleniu monitu wpisz hasło lub wklej hasło z pamięci, a następnie naciśnij klawisz ENTER. Po wprowadzeniu prawidłowego hasła skrypt nawiązuje połączenie z punktem odzyskiwania.

  ![Dane wyjściowe pliku wykonywalnego](./media/backup-azure-restore-files-from-vm/executable-output.png)


Po uruchomieniu pliku wykonywalnego system operacyjny instaluje nowe woluminy i przypisuje litery dysku. Aby przeglądać te dyski, można użyć Eksploratora Windows lub Eksploratora plików. Litery dysku przypisane do woluminów mogą nie być takie same jak oryginalna maszyna wirtualna. Jednak nazwa woluminu jest zachowywana. Na przykład jeśli wolumin na oryginalnej maszynie wirtualnej to "dysk danych (E: `\` )", wolumin ten można dołączyć na komputerze lokalnym jako "dysk danych (" dowolna litera ": `\` ). Przeglądaj wszystkie woluminy wymienione w danych wyjściowych skryptu do momentu znalezienia plików lub folderu.  

   ![Dołączone woluminy odzyskiwania](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-backed-up-vms-with-large-disks-windows"></a>Tworzenie kopii zapasowych maszyn wirtualnych z dużymi dyskami (system Windows)

Jeśli proces odzyskiwania plików zawiesza się po uruchomieniu skryptu przywracania pliku (na przykład jeśli dyski nigdy nie są zainstalowane lub są zainstalowane, ale nie są wyświetlane woluminy), wykonaj następujące czynności:
  
1. Upewnij się, że system operacyjny jest w wersji WS 2012 lub nowszej.
2. Upewnij się, że klucze rejestru zostały ustawione zgodnie z sugerowaną poniżej na serwerze przywracania, i upewnij się, że serwer jest ponownie uruchamiany. Liczba obok identyfikatora GUID może być z zakresu od 0001-0005. W poniższym przykładzie jest to 0,004. Przejdź przez ścieżkę klucza rejestru do sekcji parametry.

    ![Zmiany klucza rejestru](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

### <a name="for-linux"></a>W przypadku systemu Linux

Po spełnieniu wszystkich wymagań wymienionych w [kroku 2](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script), [krok 3](#step-3-os-requirements-to-successfully-run-the-script) i [4](#step-4-access-requirements-to-successfully-run-the-script), wygeneruj skrypt języka Python dla maszyn z systemem Linux. Zobacz [krok 1, aby dowiedzieć się, jak wygenerować i pobrać skrypt](#step-1-generate-and-download-script-to-browse-and-recover-files). Pobierz skrypt i skopiuj go do odpowiedniego/zgodnego serwera z systemem Linux. Może być konieczne zmodyfikowanie uprawnień, aby wykonać je w programie ```chmod +x <python file name>``` . Następnie uruchom plik Python przy użyciu programu ```./<python file name>``` .


W systemie Linux woluminy punktu odzyskiwania są instalowane do folderu, w którym skrypt jest uruchamiany. Odpowiednio są wyświetlane dołączone dyski, woluminy i odpowiednie ścieżki instalacji. Te ścieżki instalacji są widoczne dla użytkowników, którzy mają dostęp na poziomie głównym. Przeglądaj woluminy wymienione w danych wyjściowych skryptu.

  ![Menu odzyskiwania plików systemu Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)


#### <a name="for-backed-up-vms-with-large-disks-linux"></a>Tworzenie kopii zapasowych maszyn wirtualnych z dużymi dyskami (Linux) * *

Jeśli proces odzyskiwania plików zawiesza się po uruchomieniu skryptu przywracania pliku (na przykład jeśli dyski nigdy nie są zainstalowane lub są zainstalowane, ale nie są wyświetlane woluminy), wykonaj następujące czynności:

1. W pliku/etc/iSCSI/iscsid.conf Zmień ustawienie z:
    - `node.conn[0].timeo.noop_out_timeout = 5`  do `node.conn[0].timeo.noop_out_timeout = 120`
2. Po wprowadzeniu powyższych zmian ponownie uruchom skrypt. Jeśli występują błędy przejściowe, należy się upewnić, że między kolejnymi uruchomieniami występuje odstęp od 20 do 30 minut, aby uniknąć kolejnych serii żądań mających wpływ na przygotowanie docelowe. Ten interwał między ponownymi uruchomieniami zapewni, że obiekt docelowy będzie gotowy do połączenia ze skryptu.
3. Po odzyskaniu plików upewnij się, że Wróć do portalu i wybierz opcję **odinstalowania dysków** dla punktów odzyskiwania, w których nie można było zainstalować woluminów. Zasadniczo ten krok spowoduje wyczyszczenie wszystkich istniejących procesów/sesji i zwiększenie możliwości odzyskania.


#### <a name="lvmraid-arrays-for-linux-vms"></a>Macierze LVM/RAID (dla maszyn wirtualnych z systemem Linux)

W systemie Linux macierze Menedżera woluminów logicznych (LVM) i/lub RAID oprogramowania są używane do zarządzania woluminami logicznymi za pośrednictwem wielu dysków. Jeśli chroniona maszyna wirtualna z systemem Linux korzysta z tablic LVM i/lub RAID, nie można uruchomić skryptu na tej samej maszynie wirtualnej.<br>
Skrypt należy uruchomić na dowolnej innej maszynie z zgodnym systemem operacyjnym, który obsługuje system plików chronionej maszyny wirtualnej.<br>
Następujące dane wyjściowe skryptu przedstawiają dyski macierzy LVM i/lub RAID oraz woluminy z typem partycji.

   ![Menu wyjściowe LVM systemu Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Aby przenieść te partycje w tryb online, uruchom polecenia w poniższych sekcjach.

#### <a name="for-lvm-partitions"></a>Dla partycji LVM

Po uruchomieniu skryptu partycje LVM są instalowane w woluminach fizycznych/Disk określonych w danych wyjściowych skryptu. Proces ma

1. Pobieranie unikatowej listy nazw grup woluminów z woluminów fizycznych lub dysków
2. Następnie Wyświetl listę woluminów logicznych w tych grupach woluminów
3. Następnie zainstaluj woluminy logiczne do żądanej ścieżki.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Wyświetlanie listy nazw grup woluminów z woluminów fizycznych

Aby wyświetlić listę nazw grup woluminów:

```bash
pvs -o +vguuid
```

To polecenie spowoduje wyświetlenie listy wszystkich woluminów fizycznych (w tym tych, które są obecne przed uruchomieniem skryptu), odpowiadających im nazw grup woluminów i unikatowych identyfikatorów użytkowników w grupie woluminów (UUID). Poniżej przedstawiono przykładowe dane wyjściowe polecenia.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

Pierwsza kolumna (WA) przedstawia wolumin fizyczny, kolejne kolumny przedstawiają odpowiednią nazwę, format, atrybuty, rozmiar, wolne miejsce i unikatowy identyfikator grupy woluminów. W danych wyjściowych polecenia są wyświetlane wszystkie woluminy fizyczne. Zapoznaj się z danymi wyjściowymi skryptu i zidentyfikuj woluminy związane z kopią zapasową. W powyższym przykładzie w danych wyjściowych skryptu przedstawiono/dev/SDF i/dev/SDD. Tak więc Grupa woluminów *datavg_db* należy do skryptu, a grupa woluminów *Appvg_new* należy do maszyny. Ostatnim pomysłem jest upewnienie się, że unikatowa nazwa grupy woluminów powinna mieć jeden unikatowy identyfikator.

###### <a name="duplicate-volume-groups"></a>Zduplikowane grupy woluminów

Istnieją scenariusze, w których nazwy grup woluminów mogą mieć 2 UUID po uruchomieniu skryptu. Oznacza to, że nazwy grup woluminów na komputerze, na którym skrypt jest wykonywany, i w kopii zapasowej maszyny wirtualnej są takie same. Następnie należy zmienić nazwy grup woluminów maszyn wirtualnych z kopią zapasową. Zapoznaj się z poniższym przykładem.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

Dane wyjściowe skryptu byłyby pokazane/dev/SDG,/dev/SDH,/dev/sdm2 jako dołączone. Dlatego odpowiednie nazwy VG są Appvg_new i rootvg. Jednak te same nazwy znajdują się również na liście VG maszyny. Możemy sprawdzić, czy jedna nazwa VG ma dwa identyfikatory UUID.

Teraz musimy zmienić nazwy VG dla woluminów opartych na skryptach, na przykład:/dev/SDG,/dev/SDH,/dev/sdm2. Aby zmienić nazwę grupy woluminów, użyj następującego polecenia

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Teraz mamy wszystkie nazwy VG z unikatowymi identyfikatorami.

###### <a name="active-volume-groups"></a>Aktywne grupy woluminów

Upewnij się, że grupy woluminów odpowiadające woluminom skryptu są aktywne. Następujące polecenie służy do wyświetlania aktywnych grup woluminów. Sprawdź, czy na tej liście znajdują się grupy woluminów powiązane ze skryptem.

```bash
vgdisplay -a
```  

W przeciwnym razie Aktywuj grupę woluminów przy użyciu następującego polecenia.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Wyświetlanie listy woluminów logicznych w grupach woluminów

Po otrzymaniu unikatowej aktywnej listy VGs związanych ze skryptem woluminy logiczne występujące w tych grupach woluminów można wyświetlić za pomocą następującego polecenia.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

To polecenie wyświetla ścieżkę każdego woluminu logicznego jako "ścieżka LV".

##### <a name="mounting-logical-volumes"></a>Instalowanie woluminów logicznych

Aby zainstalować woluminy logiczne do wybranej ścieżki:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Nie używaj elementu "Mount-a". To polecenie służy do instalowania wszystkich urządzeń opisanych w temacie "/etc/fstab". Może to oznaczać, że duplikaty urządzeń mogą zostać zainstalowane. Dane można przekierowywać do urządzeń utworzonych za pomocą skryptu, które nie utrwalają danych, co może spowodować utratę danych.

#### <a name="for-raid-arrays"></a>Macierze RAID

Następujące polecenie wyświetla szczegółowe informacje o wszystkich dyskach RAID:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Odpowiedni dysk RAID jest wyświetlany jako `/dev/mdm/<RAID array name in the protected VM>`

Użyj polecenia Instaluj, jeśli dysk RAID ma woluminy fizyczne:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Jeśli dysk RAID ma w nim skonfigurowany inny LVM, Użyj poprzedniej procedury dla partycji LVM, ale Użyj nazwy woluminu zamiast nazwy dysku RAID.

## <a name="step-6-closing-the-connection"></a>Krok 6. zamykanie połączenia

Po zidentyfikowaniu plików i skopiowaniu ich do lokalizacji magazynu lokalnego należy usunąć (lub odinstalować) dodatkowe dyski. Aby odinstalować dyski, w menu **odzyskiwanie plików** w Azure Portal wybierz opcję **odinstalowania dysków**.

![Odinstalowywanie dysków](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Po odinstalowaniu dysków zostanie wyświetlony komunikat. Odświeżenie połączenia może potrwać kilka minut, aby można było je usunąć.

W systemie Linux po poważnym nawiązaniu połączenia z punktem odzyskiwania system operacyjny nie usuwa automatycznie odpowiednich ścieżek instalacji. Ścieżki instalacji istnieją jako woluminy oddzielone i są widoczne, ale zgłaszają błąd podczas uzyskiwania dostępu/zapisu plików. Można je usunąć ręcznie. Skrypt, gdy jest uruchamiany, identyfikuje wszystkie woluminy istniejące z poprzednich punktów odzyskiwania i czyści je po udzieleniu zgody.

> [!NOTE]
> Upewnij się, że połączenie jest zamknięte po przywróceniu wymaganych plików. Jest to ważne, szczególnie w scenariuszu, w którym maszyna, w której wykonywany jest skrypt, został również skonfigurowany do tworzenia kopii zapasowych. Jeśli połączenie jest nadal otwarte, kolejna kopia zapasowa może zakończyć się niepowodzeniem z powodu błędu "UserErrorUnableToOpenMount". Dzieje się tak, ponieważ zainstalowane dyski/woluminy są zakładane jako dostępne, a dostęp do nich może się nie powieść, ponieważ podstawowy magazyn, czyli serwer docelowy iSCSI, może być niedostępny. Czyszczenie połączenia spowoduje usunięcie tych dysków/woluminów, więc nie będą one dostępne podczas tworzenia kopii zapasowej.

## <a name="security"></a>Zabezpieczenia

W tej sekcji omówiono różne miary zabezpieczeń związane z wdrażaniem odzyskiwania plików z kopii zapasowych maszyn wirtualnych platformy Azure.

### <a name="feature-flow"></a>Przepływ funkcji

Ta funkcja została skompilowana w celu uzyskania dostępu do danych maszyny wirtualnej bez konieczności przywracania całej maszyny wirtualnej lub dysku maszyny wirtualnej i minimalnej liczby kroków. Dostęp do danych maszyny wirtualnej jest udostępniany przez skrypt (który instaluje wolumin odzyskiwania w przypadku uruchomienia, jak pokazano poniżej) i stanowi podstawę wszystkich implementacji zabezpieczeń:

  ![Przepływ funkcji zabezpieczeń](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementacje zabezpieczeń

#### <a name="select-recovery-point-who-can-generate-script"></a>Wybierz punkt odzyskiwania (który może generować skrypt)

Skrypt zapewnia dostęp do danych maszyny wirtualnej, dlatego ważne jest, aby określić, kto może generować w pierwszej kolejności. Musisz zalogować się do Azure Portal i mieć [autoryzację Azure RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) do wygenerowania skryptu.

Odzyskiwanie plików wymaga tego samego poziomu autoryzacji zgodnie z wymaganiami przywracania maszyn wirtualnych i przywracania dysków. Innymi słowy, tylko autoryzowani użytkownicy mogą wyświetlać dane maszyn wirtualnych mogą generować skrypt.

Wygenerowany skrypt jest podpisany za pomocą oficjalnego certyfikatu firmy Microsoft dla usługi Azure Backup. Wszelkie naruszenia skryptu oznacza, że podpis jest uszkodzony, a każda próba uruchomienia skryptu zostanie wyróżniona jako potencjalne ryzyko przez system operacyjny.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Zainstaluj wolumin odzyskiwania (który może uruchamiać skrypt)

Tylko administrator może uruchomić skrypt i powinien działać w trybie podniesionych uprawnień. Skrypt uruchamia tylko wstępnie wygenerowany zestaw kroków i nie akceptuje danych wejściowych ze źródła zewnętrznego.

Aby uruchomić skrypt, wymagane jest hasło, które jest wyświetlane tylko dla autoryzowanego użytkownika w czasie generowania skryptu w Azure Portal lub PowerShell/CLI. Ma to na celu upewnienie się, że autoryzowany użytkownik, który pobiera ten skrypt, jest również odpowiedzialny za uruchomienie skryptu.

#### <a name="browse-files-and-folders"></a>Przeglądanie plików i folderów

Aby przeglądać pliki i foldery, skrypt używa inicjatora iSCSI na komputerze i łączy się z punktem odzyskiwania skonfigurowanym jako obiekt docelowy iSCSI. Tutaj można wyobrazić scenariusze, w których jeden próbuje imitację/fałszowanie/wszystkie składniki.

Używamy mechanizmu uwierzytelniania wzajemnego CHAP, aby każdy składnik uwierzytelniał inne. Oznacza to, że niezwykle trudne jest, aby fałszywy inicjator łączył się z obiektem docelowym iSCSI i dla fałszywego obiektu docelowego był połączony z maszyną, na której uruchomiono skrypt.

Przepływ danych między usługą odzyskiwania i komputerem jest chroniony przez utworzenie bezpiecznego tunelu protokołu TLS za pośrednictwem protokołu TCP (protokół[tls 1,2 powinien być obsługiwany](#step-3-os-requirements-to-successfully-run-the-script) na komputerze, na którym jest uruchamiany skrypt).

Wszystkie pliki Access Control List (ACL), które znajdują się w nadrzędnym/kopii zapasowej maszyny wirtualnej, również są zachowywane w zainstalowanym systemie plików.

Skrypt zapewnia dostęp tylko do odczytu do punktu odzyskiwania i jest prawidłowy tylko przez 12 godzin. Jeśli chcesz usunąć dostęp wcześniej, zaloguj się do Azure Portal/PowerShell/CLI i przeprowadź **dezinstalację dysków** dla danego punktu odzyskiwania. Skrypt zostanie natychmiast unieważniony.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przywrócić pliki za pośrednictwem programu PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Dowiedz się, jak [przywrócić pliki za pośrednictwem interfejsu wiersza polecenia platformy Azure](./tutorial-restore-files.md)
- Po przywróceniu maszyny wirtualnej Dowiedz się, jak [zarządzać kopiami zapasowymi](./backup-azure-manage-vms.md)
