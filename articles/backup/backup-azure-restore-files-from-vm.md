---
title: Odzyskiwanie plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak odzyskiwać pliki i foldery z punktu odzyskiwania maszyny wirtualnej platformy Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79273308"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure

Azure Backup oferuje możliwość przywracania [maszyn wirtualnych platformy Azure i dysków](./backup-azure-arm-restore-vms.md) z kopii zapasowych maszyny wirtualnej platformy Azure, znanych również jako punkty odzyskiwania. W tym artykule wyjaśniono, jak odzyskiwać pliki i foldery z kopii zapasowej maszyny wirtualnej platformy Azure. Przywracanie plików i folderów jest dostępne tylko dla maszyn wirtualnych platformy Azure wdrożonych przy użyciu modelu Menedżer zasobów i chronionych do magazynu usługi Recovery Services.

> [!NOTE]
> Ta funkcja jest dostępna dla maszyn wirtualnych platformy Azure wdrożonych przy użyciu modelu Menedżer zasobów i chronionych z magazynem Recovery Services.
> Odzyskiwanie plików z kopii zapasowej zaszyfrowanej maszyny wirtualnej nie jest obsługiwane.
>

## <a name="mount-the-volume-and-copy-files"></a>Zainstaluj wolumin i skopiuj pliki

Aby przywrócić pliki lub foldery z punktu odzyskiwania, przejdź do maszyny wirtualnej i wybierz żądany punkt odzyskiwania.

1. Zaloguj się do [Azure Portal](https://portal.Azure.com) i w lewym okienku kliknij pozycję **Virtual Machines (maszyny wirtualne**). Z listy maszyn wirtualnych wybierz maszynę wirtualną, aby otworzyć pulpit nawigacyjny maszyny wirtualnej.

2. W menu maszyny wirtualnej kliknij pozycję **kopia zapasowa** , aby otworzyć pulpit nawigacyjny kopii zapasowych.

    ![Otwórz element kopii zapasowej magazynu Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. W menu pulpitu nawigacyjnego kopia zapasowa kliknij pozycję **odzyskiwanie plików**.

    ![Przycisk odzyskiwania plików](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Zostanie otwarte menu **odzyskiwanie plików** .

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z menu rozwijanego **Wybieranie punktu odzyskiwania** wybierz punkt odzyskiwania, w którym znajdują się odpowiednie pliki. Domyślnie jest już wybrany najnowszy punkt odzyskiwania.

5. Aby pobrać oprogramowanie używane do kopiowania plików z punktu odzyskiwania, kliknij przycisk **Pobierz plik wykonywalny** (dla maszyn wirtualnych z systemem Windows Azure) lub **Pobierz skrypt** (w przypadku maszyn wirtualnych z systemem Linux Azure zostanie wygenerowany skrypt w języku Python).

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Platforma Azure pobiera plik wykonywalny lub skrypt na komputer lokalny.

    ![Pobierz komunikat dla pliku wykonywalnego lub skryptu](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Aby uruchomić plik wykonywalny lub skrypt jako administrator, sugerujemy zapisanie pobranego pliku na komputerze.

6. Plik wykonywalny lub skrypt jest chroniony hasłem i wymaga hasła. W menu **odzyskiwanie plików** kliknij przycisk Kopiuj, aby załadować hasło do pamięci.

    ![Wygenerowane hasło](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. W lokalizacji pobierania (zazwyczaj folder pobierania) kliknij prawym przyciskiem myszy plik wykonywalny lub skrypt, a następnie uruchom go z poświadczeniami administratora. Po wyświetleniu monitu wpisz hasło lub wklej hasło z pamięci, a następnie naciśnij klawisz **Enter**. Po wprowadzeniu prawidłowego hasła skrypt nawiązuje połączenie z punktem odzyskiwania.

    ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. W przypadku maszyn z systemem Linux jest generowany skrypt języka Python. Jeden z nich musi pobrać skrypt i skopiować go do odpowiedniego/zgodnego serwera z systemem Linux. Może być konieczne zmodyfikowanie uprawnień, aby wykonać je w ```chmod +x <python file name>```programie. Następnie uruchom plik Python przy użyciu ```./<python file name>```programu.

Zapoznaj się z sekcją [wymagania dostępu](#access-requirements) , aby upewnić się, że skrypt został pomyślnie uruchomiony.

### <a name="identifying-volumes"></a>Identyfikowanie woluminów

#### <a name="for-windows"></a>W przypadku systemu Windows

Po uruchomieniu pliku wykonywalnego system operacyjny instaluje nowe woluminy i przypisuje litery dysku. Aby przeglądać te dyski, można użyć Eksploratora Windows lub Eksploratora plików. Litery dysku przypisane do woluminów mogą nie być takie same jak oryginalna maszyna wirtualna. Jednak nazwa woluminu jest zachowywana. Na przykład jeśli wolumin na oryginalnej maszynie wirtualnej to "dysk danych (E:`\`)", wolumin ten można dołączyć na komputerze lokalnym jako "dysk danych (" dowolna litera ":`\`). Przeglądaj wszystkie woluminy wymienione w danych wyjściowych skryptu do momentu znalezienia plików lub folderu.  

   ![Menu odzyskiwania plików](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>W przypadku systemu Linux

W systemie Linux woluminy punktu odzyskiwania są instalowane do folderu, w którym skrypt jest uruchamiany. Odpowiednio są wyświetlane dołączone dyski, woluminy i odpowiednie ścieżki instalacji. Te ścieżki instalacji są widoczne dla użytkowników, którzy mają dostęp na poziomie głównym. Przeglądaj woluminy wymienione w danych wyjściowych skryptu.

  ![Menu odzyskiwania plików systemu Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Zamykanie połączenia

Po zidentyfikowaniu plików i skopiowaniu ich do lokalizacji magazynu lokalnego należy usunąć (lub odinstalować) dodatkowe dyski. Aby odinstalować dyski, w menu **odzyskiwanie plików** w Azure Portal kliknij polecenie **odinstalowania dysków**.

![Odinstalowywanie dysków](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Po odinstalowaniu dysków zostanie wyświetlony komunikat. Odświeżenie połączenia może potrwać kilka minut, aby można było je usunąć.

W systemie Linux po poważnym nawiązaniu połączenia z punktem odzyskiwania system operacyjny nie usuwa automatycznie odpowiednich ścieżek instalacji. Ścieżki instalacji istnieją jako woluminy oddzielone i są widoczne, ale zgłaszają błąd podczas uzyskiwania dostępu/zapisu plików. Można je usunąć ręcznie. Skrypt, gdy jest uruchamiany, identyfikuje wszystkie woluminy istniejące z poprzednich punktów odzyskiwania i czyści je po udzieleniu zgody.

## <a name="special-configurations"></a>Konfiguracje specjalne

### <a name="dynamic-disks"></a>Dyski dynamiczne

Jeśli chroniona maszyna wirtualna platformy Azure ma woluminy o jednej lub obu następujących cechach, nie można uruchomić skryptu wykonywalnego na tej samej maszynie wirtualnej.

- Woluminy obejmujące wiele dysków (woluminy łączone i rozłożone)
- Woluminy odporne na uszkodzenia (woluminy dublowane i RAID-5) na dyskach dynamicznych

Zamiast tego należy uruchomić skrypt wykonywalny na dowolnym innym komputerze z zgodnym systemem operacyjnym.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania w systemie Windows

Funkcja miejsca do magazynowania systemu Windows to technologia systemu Windows, która umożliwia wirtualizację magazynu. Funkcja miejsca do magazynowania systemu Windows umożliwia grupowanie standardowych dysków w Pule magazynów. Następnie można użyć dostępnego miejsca w tych pulach magazynów do tworzenia dysków wirtualnych nazywanych miejscami do magazynowania.

Jeśli chroniona maszyna wirtualna platformy Azure korzysta z funkcji miejsca do magazynowania systemu Windows, nie można uruchomić skryptu wykonywalnego na tej samej maszynie wirtualnej. Zamiast tego Uruchom skrypt wykonywalny na każdej innej maszynie z zgodnym systemem operacyjnym.

### <a name="lvmraid-arrays"></a>Macierze LVM/RAID

W systemie Linux macierze Menedżera woluminów logicznych (LVM) i/lub RAID oprogramowania są używane do zarządzania woluminami logicznymi za pośrednictwem wielu dysków. Jeśli chroniona maszyna wirtualna z systemem Linux korzysta z tablic LVM i/lub RAID, nie można uruchomić skryptu na tej samej maszynie wirtualnej. Skrypt należy uruchomić na dowolnej innej maszynie z zgodnym systemem operacyjnym, który obsługuje system plików chronionej maszyny wirtualnej.

Następujące dane wyjściowe skryptu przedstawiają dyski macierzy LVM i/lub RAID oraz woluminy z typem partycji.

   ![Menu wyjściowe LVM systemu Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Aby przenieść te partycje w tryb online, uruchom polecenia w poniższych sekcjach.

#### <a name="for-lvm-partitions"></a>Dla partycji LVM

Aby wyświetlić listę nazw grup woluminów w woluminie fizycznym:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Aby wyświetlić listę wszystkich woluminów logicznych, nazw i ich ścieżek w grupie woluminów:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Aby zainstalować woluminy logiczne do wybranej ścieżki:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Macierze RAID

Następujące polecenie wyświetla szczegółowe informacje o wszystkich dyskach RAID:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Odpowiedni dysk RAID jest wyświetlany jako`/dev/mdm/<RAID array name in the protected VM>`

Użyj polecenia Instaluj, jeśli dysk RAID ma woluminy fizyczne:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Jeśli dysk RAID ma w nim skonfigurowany inny LVM, Użyj poprzedniej procedury dla partycji LVM, ale Użyj nazwy woluminu zamiast nazwy dysku RAID.

## <a name="system-requirements"></a>Wymagania systemowe

### <a name="for-windows-os"></a>System operacyjny Windows

W poniższej tabeli przedstawiono zgodność między systemami operacyjnymi serwera i komputerów. Podczas odzyskiwania plików nie można przywrócić plików do poprzedniej lub przyszłej wersji systemu operacyjnego. Na przykład nie można przywrócić pliku z maszyny wirtualnej z systemem Windows Server 2016 do systemu Windows Server 2012 lub Windows 8. Można przywrócić pliki z maszyny wirtualnej do tego samego serwera systemu operacyjnego lub do zgodnego systemu operacyjnego klienta.

|System operacyjny serwera | Zgodny system operacyjny klienta  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

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
> Znaleźliśmy pewne problemy z uruchamianiem skryptu odzyskiwania plików na maszynach z systemem operacyjnym SLES 12 SP4 i badamy z zespołem SLES.
> Obecnie uruchomienie skryptu odzyskiwania plików działa na komputerach z SLES 12 SP2 i wersjami systemu operacyjnego z dodatkiem SP3.
>

Skrypt wymaga również, aby składniki Python i bash były bezpiecznie wykonywane i połączone z punktem odzyskiwania.

|Składnik | Wersja  |
| --------------- | ---- |
| bash | 4 i nowsze |
| python | 2.6.6 i nowsze  |
| TLS | 1,2 powinna być obsługiwana  |

## <a name="access-requirements"></a>Wymagania dotyczące uzyskiwania dostępu

W przypadku uruchamiania skryptu na komputerze z ograniczonym dostępem upewnij się, że masz dostęp do:

- `download.microsoft.com`
- Adresy URL usługi odzyskiwania (nazwa geograficzna odnosi się do regionu, w którym znajduje się magazyn usługi odzyskiwania)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(W przypadku usługi Azure Public Georegiony)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(W przypadku platformy Azure — Chiny)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Dla instytucji rządowych USA platformy Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(W przypadku platformy Azure (Niemcy)
- Porty wychodzące 53 (DNS), 443, 3260

> [!NOTE]
>
> - Pobrana nazwa pliku skryptu będzie miała **nazwę geograficzną** , która ma zostać wypełniona w adresie URL. Dla exampple: pobrana Nazwa skryptu rozpoczyna \'się\'\_\'od VMName\'\'geoname\'_ GUID, na przykład *ContosoVM_wcus_12345678*
> - Adres URL miałaby wartość <https://pod01-rec2.wcus.backup.windowsazure.com>"
>

W przypadku systemu Linux skrypt wymaga składników "Open-iSCSI" i "lshw", aby nawiązać połączenie z punktem odzyskiwania. Jeśli składniki nie istnieją na komputerze, na którym skrypt jest uruchamiany, skrypt monituje o zgodę na zainstalowanie składników programu. Wyrażanie zgody na zainstalowanie niezbędnych składników.

Dostęp do `download.microsoft.com` programu jest wymagany do pobierania składników używanych do tworzenia bezpiecznego kanału między komputerem, na którym skrypt jest uruchamiany, a danymi w punkcie odzyskiwania.

Skrypt można uruchomić na dowolnym komputerze, który ma ten sam (lub zgodny) system operacyjny co maszyna wirtualna z kopią zapasową. Zapoznaj się z [tabelą zgodną](backup-azure-restore-files-from-vm.md#system-requirements) z systemem operacyjnym w przypadku zgodnych systemów operacyjnych. Jeśli chroniona maszyna wirtualna platformy Azure korzysta z funkcji miejsca do magazynowania systemu Windows (dla maszyn wirtualnych z systemem Windows Azure) lub tablic LVM/RAID (dla maszyn wirtualnych z systemem Linux), nie można uruchomić pliku wykonywalnego ani skryptu na tej samej maszynie wirtualnej. Zamiast tego Uruchom plik wykonywalny lub skrypt na dowolnej innej maszynie z zgodnym systemem operacyjnym.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Odzyskiwanie plików z kopii zapasowych maszyn wirtualnych z dużymi dyskami

W tej sekcji wyjaśniono, jak przeprowadzić odzyskiwanie plików z kopii zapasowych maszyn wirtualnych platformy Azure z więcej niż 16 dyskami, a każdy dysk ma rozmiar większy niż 32 TB.

Ponieważ proces odzyskiwania plików dołącza wszystkie dyski z kopii zapasowej, w przypadku użycia dużej liczby dysków (>16) lub dużych dysków (> 32 TB każdego) są zalecane następujące punkty akcji:

- Zachowaj oddzielny serwer przywracania (maszyny wirtualne D2v3 maszyny wirtualnej platformy Azure) do odzyskiwania plików. Można go użyć tylko do odzyskiwania plików, a następnie zamknąć go, gdy nie jest to wymagane. Przywracanie na oryginalnej maszynie nie jest zalecane, ponieważ ma znaczący wpływ na maszynę wirtualną.
- Następnie uruchom skrypt jeden raz, aby sprawdzić, czy operacja odzyskiwania plików powiodła się.
- Jeśli proces odzyskiwania plików zawiesza się (dyski nigdy nie są zainstalowane lub są zainstalowane, ale nie są wyświetlane woluminy), wykonaj następujące czynności.
  - Jeśli serwer przywracania jest maszyną wirtualną z systemem Windows:
    - Upewnij się, że system operacyjny jest w wersji WS 2012 lub nowszej.
    - Upewnij się, że klucze rejestru zostały ustawione zgodnie z sugerowaną poniżej na serwerze przywracania, i upewnij się, że serwer jest ponownie uruchamiany. Liczba obok identyfikatora GUID może być z zakresu od 0001-0005. W poniższym przykładzie jest to 0,004. Przejdź przez ścieżkę klucza rejestru do sekcji parametry.

    ![iSCSI-reg-Key-Changes. png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Jeśli serwer przywracania jest maszyną wirtualną z systemem Linux:
  - W pliku/etc/iSCSI/iscsid.conf Zmień ustawienie z:
    - Node. poł. [0]. Timeo. noop_out_timeout = 5 do węzła. poł. [0]. Timeo. noop_out_timeout = 30
- Po wprowadzeniu zmiany należy ponownie uruchomić skrypt. Po wprowadzeniu tych zmian bardzo prawdopodobne jest, że odzyskiwanie plików powiedzie się.
- Za każdym razem, gdy użytkownik pobiera skrypt, Azure Backup inicjuje proces przygotowywania punktu odzyskiwania do pobrania. W przypadku dużych dysków ten proces zajmie dużo czasu. W przypadku kolejnych serii żądań przygotowanie docelowe przejdzie do spirali pobierania. Dlatego zaleca się pobranie skryptu z portalu/programu PowerShell/interfejsu wiersza polecenia, odczekanie przez 20-30 minut (heurystyka), a następnie uruchomienie go. W tym czasie obiekt docelowy powinien być gotowy do połączenia ze skryptem.
- Po odzyskaniu plików upewnij się, że Wróć do portalu i kliknij opcję **odinstalowania dysków** dla punktów odzyskiwania, w których nie można było zainstalować woluminów. Zasadniczo ten krok spowoduje wyczyszczenie wszystkich istniejących procesów/sesji i zwiększenie możliwości odzyskania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy podczas odzyskiwania plików z maszyn wirtualnych, zapoznaj się z poniższą tabelą, aby uzyskać dodatkowe informacje.

| Komunikat o błędzie/scenariusz | Prawdopodobna przyczyna | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Wyjście exe: *Przechwycono wyjątek podczas łączenia z miejscem docelowym* | Skrypt nie może uzyskać dostępu do punktu odzyskiwania.    | Sprawdź, czy maszyna spełnia [poprzednie wymagania dotyczące dostępu](#access-requirements). |  
| Wyjściowy plik exe: *element docelowy został już zalogowany za pośrednictwem sesji iSCSI.* | Skrypt został już wykonany na tym samym komputerze, a dyski zostały podłączone | Woluminy punktu odzyskiwania zostały już dołączone. Mogą nie być zainstalowane z tymi samymi literami dysków oryginalnej maszyny wirtualnej. Przejrzyj wszystkie dostępne woluminy w Eksploratorze plików dla danego pliku. |
| Wyjściowy plik exe: *ten skrypt jest nieprawidłowy, ponieważ dyski zostały odinstalowane za pośrednictwem portalu/przekroczyły limit 12 godzin. Pobierz nowy skrypt z portalu.* |    Dyski zostały odinstalowane z portalu lub Przekroczono limit 12 godzin | Ten konkretny plik exe jest teraz nieprawidłowy i nie można go uruchomić. Jeśli chcesz uzyskać dostęp do plików tego punktu odzyskiwania, odwiedź portal dla nowego pliku exe.|
| Na komputerze, na którym jest uruchamiany plik wykonywalny: nowe woluminy nie są odinstalowywane po kliknięciu przycisku Odinstaluj | Inicjator iSCSI na komputerze nie odpowiada/nie odświeża połączenia z elementem docelowym i utrzymuje pamięć podręczną. |  Po kliknięciu przycisku **Odinstaluj**odczekaj kilka minut. Jeśli nowe woluminy nie zostaną odinstalowane, przejrzyj wszystkie woluminy. Przeglądanie wszystkich woluminów zmusza inicjatora do odświeżenia połączenia, a wolumin zostanie odinstalowany z komunikatem o błędzie, że dysk nie jest dostępny.|
| Wyjściowy plik exe: skrypt jest uruchamiany pomyślnie, ale "nowe woluminy dołączone" nie są wyświetlane w danych wyjściowych skryptu |    Jest to błąd przejściowy    | Woluminy będą już dołączone. Otwórz Eksploratora, aby przeglądać. Jeśli używasz tego samego komputera do uruchamiania skryptów za każdym razem, Rozważ ponowne uruchomienie komputera, a lista powinna zostać wyświetlona w kolejnych uruchomieniach programu exe. |
| Specyficzne dla systemu Linux: nie można wyświetlić żądanych woluminów | System operacyjny maszyny, na której jest uruchamiany skrypt, może nie rozpoznać podstawowego systemu plików chronionej maszyny wirtualnej | Sprawdź, czy punkt odzyskiwania jest spójny pod kątem awarii lub spójny z plikiem. Jeśli plik jest spójny, uruchom skrypt na innym komputerze, którego system operacyjny rozpoznaje chronioną system plików maszyny wirtualnej. |
| Specyficzne dla systemu Windows: nie można wyświetlić żądanych woluminów | Dyski mogły zostać dołączone, ale nie skonfigurowano woluminów | Na ekranie Zarządzanie dyskami Zidentyfikuj dodatkowe dyski związane z punktem odzyskiwania. Jeśli którykolwiek z tych dysków znajduje się w stanie offline, spróbuj przełączyć je w tryb online, klikając dysk prawym przyciskiem myszy, a następnie kliknij pozycję **online**.|

## <a name="security"></a>Zabezpieczenia

W tej sekcji omówiono różne miary zabezpieczeń związane z wdrażaniem odzyskiwania plików z kopii zapasowych maszyn wirtualnych platformy Azure.

### <a name="feature-flow"></a>Przepływ funkcji

Ta funkcja została skompilowana w celu uzyskania dostępu do danych maszyny wirtualnej bez konieczności przywracania całej maszyny wirtualnej lub dysku maszyny wirtualnej i minimalnej liczby kroków. Dostęp do danych maszyny wirtualnej jest udostępniany przez skrypt (który instaluje wolumin odzyskiwania w przypadku uruchomienia, jak pokazano poniżej) i stanowi podstawę wszystkich implementacji zabezpieczeń:

  ![Przepływ funkcji zabezpieczeń](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementacje zabezpieczeń

#### <a name="select-recovery-point-who-can-generate-script"></a>Wybierz punkt odzyskiwania (który może generować skrypt)

Skrypt zapewnia dostęp do danych maszyny wirtualnej, dlatego ważne jest, aby określić, kto może generować w pierwszej kolejności. Musisz zalogować się do Azure Portal i mieć [autoryzację RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) do wygenerowania skryptu.

Odzyskiwanie plików wymaga tego samego poziomu autoryzacji zgodnie z wymaganiami przywracania maszyn wirtualnych i przywracania dysków. Innymi słowy, tylko autoryzowani użytkownicy mogą wyświetlać dane maszyn wirtualnych mogą generować skrypt.

Wygenerowany skrypt jest podpisany za pomocą oficjalnego certyfikatu firmy Microsoft dla usługi Azure Backup. Wszelkie naruszenia skryptu oznacza, że podpis jest uszkodzony, a każda próba uruchomienia skryptu zostanie wyróżniona jako potencjalne ryzyko przez system operacyjny.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Zainstaluj wolumin odzyskiwania (który może uruchamiać skrypt)

Tylko administrator może uruchomić skrypt i powinien działać w trybie podniesionych uprawnień. Skrypt uruchamia tylko wstępnie wygenerowany zestaw kroków i nie akceptuje danych wejściowych ze źródła zewnętrznego.

Aby uruchomić skrypt, wymagane jest hasło, które jest wyświetlane tylko dla autoryzowanego użytkownika w czasie generowania skryptu w Azure Portal lub PowerShell/CLI. Ma to na celu upewnienie się, że autoryzowany użytkownik, który pobiera ten skrypt, jest również odpowiedzialny za uruchamianie skryptu.

#### <a name="browse-files-and-folders"></a>Przeglądanie plików i folderów

Aby przeglądać pliki i foldery, skrypt używa inicjatora iSCSI na komputerze i łączy się z punktem odzyskiwania skonfigurowanym jako obiekt docelowy iSCSI. Tutaj można wyobrazić scenariusze, w których jeden próbuje imitację/fałszowanie/wszystkie składniki.

Używamy mechanizmu uwierzytelniania wzajemnego CHAP, aby każdy składnik uwierzytelniał inne. Oznacza to, że niezwykle trudne jest, aby fałszywy inicjator łączył się z obiektem docelowym iSCSI i dla fałszywego obiektu docelowego był połączony z maszyną, na której uruchomiono skrypt.

Przepływ danych między usługą odzyskiwania i komputerem jest chroniony przez utworzenie bezpiecznego tunelu protokołu TLS za pośrednictwem protokołu TCP (protokół[tls 1,2 powinien być obsługiwany](#system-requirements) na komputerze, na którym jest uruchamiany skrypt).

Wszystkie pliki Access Control List (ACL) obecne w nadrzędnej/kopii zapasowej maszyny wirtualnej są zachowane również w zainstalowanym systemie plików.

Skrypt zapewnia dostęp tylko do odczytu do punktu odzyskiwania i jest prawidłowy tylko przez 12 godzin. Jeśli chcesz usunąć dostęp wcześniej, zaloguj się do witryny Azure Portal/programu PowerShell/interfejsu wiersza polecenia i przeprowadź **dezinstalację dysków** dla danego punktu odzyskiwania. Skrypt zostanie natychmiast unieważniony.

## <a name="next-steps"></a>Następne kroki

- Wszelkie problemy podczas przywracania plików można znaleźć w sekcji [Rozwiązywanie problemów](#troubleshooting) .
- Dowiedz się, jak [przywrócić pliki za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Dowiedz się, jak [przywrócić pliki za pośrednictwem interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- Po przywróceniu maszyny wirtualnej Dowiedz się, jak [zarządzać kopiami zapasowymi](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
