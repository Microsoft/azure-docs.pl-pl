---
title: Błąd zatrzymania systemu Windows — stan braku pamięci
description: W tym artykule przedstawiono procedurę rozwiązywania problemów z niepowodzeniem uruchamiania systemu Windows i wyświetlania stanu "stan braku pamięci".
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005909"
---
# <a name="windows-stop-error---status-no-memory"></a>Błąd zatrzymania systemu Windows — stan braku pamięci

W tym artykule przedstawiono kroki rozwiązywania problemów z niepowodzeniem uruchamiania systemu Windows i wyświetlania stanu lub kodu błędu #0xC0000017, znanego również jako "stan braku pamięci".

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej (VM) zobaczysz, że zrzut ekranu wyświetla kod błędu: `0xC0000017` . W zależności od używanej wersji systemu Windows można zobaczyć ten kod w **Menedżerze rozruchu systemu Windows** lub na **ekranie odzyskiwania**.

   **Menedżer rozruchu systemu Windows**

   ![Menedżer rozruchu systemu Windows stwierdzający, że nie można uruchomić systemu Windows. Przyczyną może być niedawna zmiana sprzętu lub oprogramowania. Przewiń w dół, zobaczysz kod stanu "0xC0000017", a także informacje o tym, że nie można załadować aplikacji lub systemu operacyjnego, ponieważ brakuje wymaganego pliku lub zawiera błędy ".](./media/troubleshoot-windows-stop-error/1.png)

   **Ekran odzyskiwania**
 
   ![Ekran odzyskiwania z informacją o tym, że "komputer/urządzenie musi zostać naprawione. Za mało dostępnej pamięci, aby utworzyć urządzenie Ramdisk. Powinien być również widoczny kod błędu "0xC0000017".](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Przyczyna

Dysk systemu operacyjnego jest pełny, zbyt pofragmentowany lub system operacyjny (OS) nie może uzyskać dostępu do pamięci lub pliku stronicowania.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu:

1. Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu
1. Zwolnij miejsce na dysku
1. Wyczyść nieprawidłową pamięć z magazynu BCD
1. Przywróć domyślną lokalizację pliku stronicowania
1. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci
1. Kompiluj ponownie maszynę wirtualną

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Aby rozwiązać ten problem, nastąpi Rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
1. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>W przypadku maszyn wirtualnych 2. generacji Przypisz literę do partycji Extensible Firmware Interface (EFI):

Jeśli używasz maszyny wirtualnej generacji 2, partycja EFI dołączonego dysku może nie mieć przypisanej litery. Należy wykonać poniższe kroki, aby przypisać literę do partycji przed kontynuowaniem pracy z tym przewodnikiem rozwiązywania problemów.

1. W obszarze wyszukiwania systemu Windows wprowadź `diskmgmt` i Otwórz **konsolę Zarządzanie dyskami**.
1. Zidentyfikuj uszkodzony dysk dołączony do maszyny wirtualnej naprawy. Zazwyczaj ten dysk jest wyświetlany jako ostatni w konsoli programu i ma największą wartość liczbową.
1. Należy pamiętać, że na tym dysku znajduje się partycja zawierająca **partycję systemową EFI**, która nie ma przypisanej litery, takiej jak dysk *F:*). Jeśli wszystkie partycje są przypisane, możesz przejść od razu do zwolnienia miejsca na dysku. W przeciwnym razie Kontynuuj, aby przypisać literę do tego dysku.

   ![Konsola zarządzania dyskami z dołączonym dyskiem "dysk 2", a także przypisaną partycją o rozmiarze 100 MB i jest "partycją systemową EFI".](./media/troubleshoot-windows-stop-error/3.png)

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień jako administrator i wprowadź, `diskpart` Aby uruchomić narzędzie **diskpart** .
1. Wprowadź następujące polecenia:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - W poleceniu Zastąp `<NUMBER OF THE ATTACHED DISK>` wartość numerem dysku zidentyfikowanym w kroku 2.
   - W poleceniu Zastąp ciąg `<NUMBER OF THE SYSTEM PARTION>` numerem partycji partycji systemowej EFI. Ta partycja nie ma jeszcze przypisanej litery, ale powinna być w **systemie** typów i mieć rozmiar o rozmiarze 100 MB.

   > [!NOTE]
   > Porównanie partycji w konsoli zarządzania dyskami z tymi wymienionymi w narzędziu DISKPART ułatwia określenie, który numer partycji odpowiada partycji systemowej EFI na dołączonym dysku.

1. Zamknij okno wiersza polecenia.

### <a name="free-up-space-on-the-disk"></a>Zwolnij miejsce na dysku

Po dołączeniu uszkodzonego dysku do maszyny wirtualnej naprawy należy sprawdzić, czy system operacyjny na tym dysku ma wystarczającą ilość miejsca do prawidłowego działania. 

1. Sprawdź, czy dysk jest zapełniony przez kliknięcie prawym przyciskiem myszy dysku podłączonego dysku i wybranie pozycji **Właściwości**.
1. Jeśli dysk ma **mniej niż 300 MB wolnego miejsca**, [rozwiń go maksymalnie 1 TB przy użyciu programu PowerShell](../windows/expand-os-disk.md).
1. Gdy rozmiar dysku wynosi **1 TB**, należy przeprowadzić Oczyszczanie dysku. Możesz użyć narzędzia do [oczyszczania dysku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) , aby zwolnić miejsce.
1. Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator) i wykonaj defragmentację na dysku:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - W zależności od poziomu fragmentacji może upłynąć kilka godzin.
   - W poleceniu Zastąp ciąg `<LETTER ASSIGNED TO THE OS DISK>` literą dysku systemu operacyjnego (na przykład dysk *F:*).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Wyczyść nieprawidłową pamięć z magazynu Dane konfiguracji rozruchu (BCD)

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
1. Wykonaj zapytanie dotyczące pliku konfiguracji rozruchu dla nieprawidłowych flag pamięci za pomocą następującego polecenia:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - W poleceniu Zastąp ciąg `<LETTER ASSIGNED TO THE OS DISK>` literą dysku systemu operacyjnego (na przykład dysk *F:*).

1. Jeśli zapytanie nie zawiera żadnych nieprawidłowych bloków pamięci, przejdź do następnego zadania. W przeciwnym razie przejdź do kroku 4.
1. W przypadku zidentyfikowania nieprawidłowych bloków pamięci te bloki uniemożliwiają utworzenie RAMDisk i należy usunąć przy użyciu następującego polecenia:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - W poleceniu Zastąp ciąg `<LETTER ASSIGNED TO THE OS DISK>` literą dysku systemu operacyjnego (na przykład dysk *F:*).

### <a name="restore-the-page-file-to-its-default-location"></a>Przywróć domyślną lokalizację pliku stronicowania

Plik stronicowania przechowuje dane, które nie mogą być przechowywane przez pamięć RAM komputera w postaci przepełnienia/kopii zapasowej. Istnieje możliwość, że plik jest hostowany na dysku VHD, a nie w katalogu tymczasowym, który jest domyślną lokalizacją platformy Azure. Jeśli wartość jest równa true, plik może być niedostępny i powinien zostać przywrócony do lokalizacji domyślnej.

Przed podjęciem jakichkolwiek kroków należy utworzyć kopię folderu **\Windows\System32\Config** na dysku w dobrej kondycji. Ten krok zapewnia, że może cofnąć wszelkie niepożądane zmiany. Będziesz pracować nad ważnymi plikami systemowymi, dlatego zdecydowanie zalecane jest to.

1. W obszarze wyszukiwania systemu Windows wprowadź **regedit** i Otwórz aplikację Edytor rejestru.
1. W Edytorze rejestru zaznacz klucz **HKEY_LOCAL_MACHINE** i wybierz pozycję **plik > Załaduj gałąź Hive...** z menu.

   ![Menu ładowanie programu Hive w Edytorze rejestru.](./media/troubleshoot-windows-stop-error/4.png)

1. W oknie dialogowym Załaduj gałąź rejestru wybierz pozycję **\windows\system32\config\SYSTEM** , a następnie kliknij pozycję Otwórz.
   1. Zostanie wyświetlony monit o podanie nazwy, którą należy wprowadzić **BROKENSYSTEM**. Ta nazwa pomoże rozróżnić powiązane gałęzie podczas rozwiązywania problemów.
   1. Rozwiń węzeł **HKEY_LOCAL_MACHINE** , aby wyświetlić nowy klucz BROKENSYSTEM, który został dodany.
1. Korzystając z edytora rejestru, ustal, z którego ControlSet maszyna jest uruchamiana.
   1. Przejdź do **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> zaznacz**.
   1. Na liście klucze Zwróć uwagę na wartość danych Current. Na przykład, jeśli ta wartość jest równa **1** lub **0x00000001 (1)**, wówczas zestaw kontrolek będzie ControlSet001.
1. Sprawdź lokalizację, w której skonfigurowano Tworzenie pliku stronicowania.
   1. W HKEY_LOCAL_MACHINE\BROKENSYSTEM rozwiń katalog pasujący do numeru ControlSet, który został zidentyfikowany w kroku 4, na przykład **ControlSet001**.
   1. Przejdź do pozycji **kontrola >> Menedżer sesji >> zarządzanie pamięcią** i zanotuj lokalizację klucza **ExistingPageFiles** .
   1. Ten klucz powinien znajdować się w domyślnej lokalizacji platformy Azure na dysku tymczasowym. Jeśli tak nie jest, a znajduje się na dysku VHD w innej lokalizacji, na przykład na dysku danych lub dysku systemu operacyjnego, należy usunąć ten plik.
   1. Przejdź do tej lokalizacji w Eksploratorze plików, a następnie usuń plik **pagefile.sys** .

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

**Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci przez uruchomienie następującego skryptu:

Aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień jako administrator.
1. Utwórz listę danych magazynu BCD i określ identyfikator modułu ładującego rozruchu, który będzie używany w następnym kroku.

   1. W przypadku maszyny wirtualnej 1. generacji wprowadź następujące polecenie i zanotuj identyfikator wymieniony poniżej:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - W poleceniu Zamień na `<BOOT PARTITON>` literę partycji na dysku dołączonym, który zawiera folder rozruchowy.

      ![Dane wyjściowe dotyczące wyświetlania magazynu BCD na maszynie wirtualnej generacji 1, która zawiera listę w obszarze ładujący rozruchu systemu Windows numer identyfikacyjny.](./media/troubleshoot-windows-stop-error/5.png)

   1. W przypadku maszyny wirtualnej 2. generacji wprowadź następujące polecenie i zanotuj wymieniony identyfikator:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - W poleceniu Zastąp `<LETTER OF THE EFI SYSTEM PARTITION>` literą partycji systemowej EFI.
   - Pomocne może być uruchomienie konsoli zarządzania dyskami w celu zidentyfikowania odpowiedniej partycji systemowej oznaczonej jako **partycja systemowa EFI**.
   - Identyfikator może być unikatowym identyfikatorem GUID lub może być domyślnym **menedżerem Bootmgr**.

1. Uruchom następujące polecenia, aby włączyć konsolę szeregową:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - W poleceniu Zamień na `<VOLUME LETTER WHERE THE BCD FOLDER IS>` literę folderu BCD.
   - W poleceniu Zastąp `<BOOT LOADER IDENTIFIER>` wartość identyfikatorem znalezionym w poprzednim kroku.

1. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest większa niż rozmiar pamięci (RAM) na maszynie wirtualnej.

   Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odnieść tę lokalizację do dowolnego dysku z danymi dołączonym do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zamień **% główny_katalog_systemowy%** na literę dysku danych, na przykład dysk **F:**, w poniższych poleceniach.

   Sugerowana konfiguracja do włączenia zrzutu systemu operacyjnego:

   **Załaduj gałąź rejestru ze złamanego dysku systemu operacyjnego:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Włącz w ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Włącz w ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Zwolnij przerwany dysk systemu operacyjnego:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

Aby skompilować ponownie maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
