---
title: Rozwiązywanie problemów z błędem Menedżera rozruchu systemu Windows-0xC0000225 "nie znaleziono stanu"
description: Kroki rozwiązywania problemów z kodem błędu 0xC0000225 na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: a91add684d7b7aaee67e7dd4f4f2d0c6df078132
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632228"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Rozwiązywanie problemów z błędem Menedżera rozruchu systemu Windows-0xC0000225 "nie znaleziono stanu"
 
W tym artykule przedstawiono procedurę rozwiązywania problemów w przypadku wystąpienia błędu 0xC0000225 na maszynie wirtualnej platformy Azure. Ten błąd wskazuje, że nie można odnaleźć stanu lub obiektu.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zostanie wyświetlony komunikat o błędzie nie można uruchomić systemu Windows z kodem stanu *0xc0000225*.

Plik skojarzony z tym kodem błędu zostanie poinformowany o krokach, które należy podjąć w celu rozwiązania problemu. Zlokalizuj tekst sekcji **:** sekcja, aby określić odpowiedni sposób działania.

### <a name="drivers-os-related-or-third-party"></a>Sterowniki, powiązane z systemem operacyjnym lub inne firmy

Jeśli plik jest obecny, ale odwołuje się do sterownika (jak pokazano) lub dotyczy systemu operacyjnego lub innej firmy, wykonaj kroki opisane w sekcji [Naprawa pliku systemowego](#repair-the-system-file).
 
Na poniższej ilustracji nie udało się uruchomić systemu Windows Stany Menedżera rozruchu systemu Windows. Przyczyną może być niedawna zmiana sprzętu lub oprogramowania ". Na obrazie jest również wyświetlany stan "0xc0000225", **plik:** AS `\windows\System32\drivers\atapi.sys` , i **informacje:** "nie można załadować systemu operacyjnego z powodu braku krytycznych sterowników systemu lub zawiera błędy".

![Uruchamianie systemu Windows Stany Menedżera rozruchu systemu Windows nie powiodło się. Przyczyną może być Ostatnia zmiana sprzętu lub oprogramowania. Przedstawia on również stan jako "0xc0000225", plik jako "\windows\System32\drivers\atapi.sys" i w sekcji informacji: "nie można załadować systemu operacyjnego z powodu braku krytycznych sterowników systemu lub zawiera błędy".](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Brak pliku

Jeśli jest wyświetlany z kodem stanu, ale nie jest wyświetlany żaden plik, postępuj zgodnie z instrukcjami w sekcji [Dodawanie zmiennej OSDEVICE](#add-the-osdevice-variable).

Na poniższej ilustracji nie udało się uruchomić systemu Windows Stany Menedżera rozruchu systemu Windows. Przyczyną może być niedawna zmiana sprzętu lub oprogramowania ". Obraz zawiera również stan "0xc0000225", a **informacje:** as "nie można wybrać rozruchu, ponieważ wymagane urządzenie jest niedostępne".

![Uruchamianie systemu Windows Stany Menedżera rozruchu systemu Windows nie powiodło się. Przyczyną może być Ostatnia zmiana sprzętu lub oprogramowania. Jest w nim także wyświetlany stan "0xc0000225" i w sekcji informacji stan: "zaznaczenie rozruchu nie powiodło się, ponieważ wymagane urządzenie jest niedostępne".](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Plik rejestru

Jeśli odwołuje się do dowolnego pliku rejestru, takiego jak \Windows\System32\Config\System, wykonaj kroki opisane w sekcji [Tworzenie biletu pomocy technicznej](#contact-support).
 
Na poniższej ilustracji nie udało się uruchomić systemu Windows Stany Menedżera rozruchu systemu Windows. Przyczyną może być niedawna zmiana sprzętu lub oprogramowania ". Obraz zawiera również stan "0xc0000225", plik AS `\windows\System32\config\system` i **info:** as "nie można załadować systemu operacyjnego z powodu braku pliku rejestru systemowego lub zawiera błędy".

![Uruchamianie systemu Windows Stany Menedżera rozruchu systemu Windows nie powiodło się. Przyczyną może być Ostatnia zmiana sprzętu lub oprogramowania. Przedstawia on również stan jako "0xc0000225", plik jako "\windows\System32\config\system", a w sekcji informacji: "nie można załadować systemu operacyjnego z powodu braku pliku rejestru systemowego lub zawierającego błędy".](./media/troubleshoot-boot-error-status-not-found/3.png)

Na poniższej ilustracji stan ekranu odzyskiwania to "komputer/urządzenie musi zostać naprawione. Nie można załadować systemu operacyjnego, ponieważ brakuje pliku rejestru systemowego lub zawiera błędy. Obraz również pokazuje kod błędu jako "0xc0000225" i plik jako `\windows\System32\config\system` .

![Stan ekranu odzyskiwania to komputer/urządzenie, które należy naprawić. Nie można załadować systemu operacyjnego, ponieważ brakuje pliku rejestru systemowego lub zawiera on błędy. Zawiera również kod błędu jako "0xc0000225" i plik jako "\windows\System32\config\system".](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Przyczyny

### <a name="missing-binary"></a>Brak danych binarnych

W pliku System **(. sys)** może wystąpić nieobecny lub uszkodzony plik binarny.

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Uszkodzenie BCD lub niewłaściwa migracja wirtualnego dysku twardego

W takim przypadku **dane konfiguracji rozruchu (BCD)** jest uszkodzony lub **wirtualny dysk twardy (VHD)** został zmigrowany z lokalnego, ale nie został poprawnie przygotowany. Wynikiem jest brak zmiennej **OSDEVICE** i należy ją dodać.

### <a name="registry-hive-corruption"></a>Uszkodzenie gałęzi rejestru

Uszkodzenie gałęzi rejestru może być spowodowane:

- Wystąpił błąd gałęzi
- Instalacja programu Hive, ale jest pusta
- Gałąź nie została prawidłowo ZAMKNIĘTA
## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Wybierz rozwiązanie:
   - [Naprawa pliku systemowego](#repair-the-system-file)
   - [Dodawanie zmiennej OSDevice](#add-the-osdevice-variable)
   - [Tworzenie biletu pomocy technicznej](#contact-support)
1. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
1. Skompiluj ponownie maszynę wirtualną.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
1. Korzystając z Podłączanie pulpitu zdalnego, Połącz się z maszyną wirtualną naprawy.

### <a name="select-a-solution"></a>Wybierz rozwiązanie

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
1. Na podstawie wskazanego wcześniej objawu postępuj zgodnie z instrukcjami w odpowiednim rozwiązaniu. Możesz pominąć kroki z innych rozwiązań, ponieważ nie będą one miały zastosowania do problemu:

- [Naprawa pliku systemowego](#repair-the-system-file)
- [Dodawanie zmiennej OSDevice](#add-the-osdevice-variable)
- [Tworzenie biletu pomocy technicznej](#contact-support)

### <a name="repair-the-system-file"></a>Naprawa pliku systemowego

1. Korzystając z dołączonego wirtualnego dysku twardego, przejdź do lokalizacji pliku danych binarnych wyświetlanych na zrzucie ekranu maszyny wirtualnej.
1. Kliknij prawym przyciskiem myszy plik, wybierz polecenie **Właściwości**, a następnie wybierz kartę **szczegóły** , aby wyświetlić informacje o pliku.
   1. Zanotuj wersję pliku, jak pokazano na poniższej ilustracji:

      ![Okno właściwości dla pliku "cng.sys" z wyróżnioną wersją pliku.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Zmień nazwę pliku na **< BINARY.SYS >. old**, zastępując **< BINARY.SYS >** nazwą pliku.

   W przypadku obrazu w powyższym kroku **cng.sys** zmienić nazwy pliku na **cng.sys. old**

   > [!NOTE]
   > Jeśli spróbujesz zmienić nazwę pliku i otrzymasz komunikat "plik jest uszkodzony i nie można go odczytać", [skontaktuj się z pomocą techniczną w celu uzyskania pomocy](https://azure.microsoft.com/support/create-ticket/), ponieważ to rozwiązanie nie będzie działało.

1. Teraz, gdy zmieniono nazwę uszkodzonego pliku, napraw plik, przywracając go z jego wewnętrznego repozytorium.
   1. Uruchom sesję **cmd** .
   1. Przejdź do **\windows\winsxs**.

   1. Wyszukaj plik binarny znajdujący się na początku tej sekcji przy użyciu następującego polecenia:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      To polecenie wyświetla listę wszystkich wersji pliku, które zawiera komputer, co zapewnia historię ścieżki tego składnika.

      Na przykład **katalog cng.sys** zostałby zmieniona na dir **cng.sys/s**

   1. Wybierz najnowszą wersję pliku na liście (lub dowolną preferowaną) i skopiuj plik do folderu **Windows\System32** przy użyciu poprzedniej ścieżki i następującego polecenia:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Jeśli najnowsza wartość binarna nie zadziałała, Wypróbuj wersję przed nią lub, z której wiesz, że istnieje stabilny plik, taki jak wersja przed poprawką.

      Jeśli na przykład plik binarny, którego szukasz, jest **cmimcext.sys**, uszkodzony dysk jest dyskiem **F:**, a właśnie uruchomiono wyszukiwanie w najnowszej wersji, zobaczysz następujący obraz, gdzie zapytanie w wierszu polecenia `dir cmim* /s` lokalizowanie najnowszej wersji pliku cmimcext.sys.

      ![Zapytanie w wierszu polecenia "dir cmim */s" w celu zlokalizowania najnowszej wersji pliku cmimcext.sys.](./media/troubleshoot-boot-error-status-not-found/6.png)

      Na przykładowym obrazie powyżej zapytanie zostało wykonane na dysku **C:**, podczas gdy litera dysku powinna mieć uszkodzony dysk, **F:**, czyli dysk systemu operacyjnego dołączony jako dysk z danymi na maszynie wirtualnej naprawy.

      Wynikiem polecenia kopiowania pliku będzie: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

Po zakończeniu tego zadania, Kontynuuj, aby [włączyć konsolę szeregową i kolekcję zrzutów pamięci](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>Dodawanie zmiennej OSDEVICE

Zbierz bieżące informacje Instalatora dotyczące rozruchu i zanotuj identyfikator aktywnej partycji. Następnie użyj tych informacji, aby dodać zmienną **OSDEVICE** , postępując zgodnie ze wskazówkami dotyczącymi generacji maszyny wirtualnej.

Jeśli ta kolekcja informacji zawiera błąd, w którym nie ma pliku **\boot\bcd** , zamiast tego należy użyć instrukcji w temacie [Naprawa pliku systemowego](#repair-the-system-file) . 

1. W przypadku maszyn wirtualnych 1. generacji Otwórz wiersz polecenia z podwyższonym poziomem uprawnień jako administrator i wprowadź następujące polecenie:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Ten obraz przedstawia moduł ładujący rozruchu systemu Windows na maszynie wirtualnej generacji 1 z wyróżnionym atrybutem identyfikatora. Wyróżniony atrybut identyfikatora zawiera unikatowy ciąg alfanumeryczny.

   ![Program ładujący rozruchu systemu Windows wyświetlany w maszynie wirtualnej generacji 1 z wyróżnionym atrybutem identyfikatora. Wyróżniony atrybut identyfikatora zawiera unikatowy ciąg alfanumeryczny.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Zanotuj identyfikator modułu ładującego rozruchu systemu Windows, którego ścieżką jest **\windows\system32\winload.exe**.

1. W przypadku maszyn wirtualnych 2. generacji Sprawdź, czy dysk systemu operacyjnego jest w trybie online i czy litery dysku partycji zostały przypisane. Po zweryfikowaniu należy zebrać informacje o konfiguracji rozruchu.
   1. W obszarze **wyszukiwania systemu Windows** wpisz **Zarządzanie dyskami** i Otwórz konsolę Zarządzanie dyskami. Za pomocą tej konsoli Zidentyfikuj numer dysku dołączony do maszyny wirtualnej naprawy i partycję Extensible Firmware Interface (EFI), która zawiera magazyn danych konfiguracji rozruchu.

   Na poniższej ilustracji dysk 2 jest numerem dysku podłączonym do maszyny wirtualnej naprawy. Obraz zawiera również partycję systemową EFI na dysku 2 o rozmiarze 100 MB i nie ma przypisanej litery.

   ![Dysk 2 jest pokazywany jako numer dysku dołączony do maszyny wirtualnej naprawy. Pokazuje również partycję systemową EFI na dysku 2, który jest 100 MB i nie ma przypisanej litery.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień jako administrator i wprowadź następujące polecenia:
      1. Otwórz **narzędzie Diskpart** przy użyciu polecenia `diskpart` .
      1. Wyświetl listę wszystkich dysków, a następnie wybierz dołączony dysk zidentyfikowany w poprzednim kroku:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         Na poniższej ilustracji przedstawiono wyniki tworzenia listy i wybierania dysku. Dysk 0 (127 GB/online), dysk 1 (32 GB/online) i dysk 2 (127 GB/online) są wyświetlane z wybranym dyskiem 2 przy użyciu polecenia `sel disk 2` .

         ![Wyniki listy, a następnie wybranie dysku. Dysk 0 (127 GB | Online), dysk 1 (32 GB | Online) i Disk 2 (127 GB | Online) są wyświetlane z wybranym dyskiem 2.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Utwórz listę partycji i wybierz partycję systemową EFI zidentyfikowaną w poprzednim kroku:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         Na poniższej ilustracji przedstawiono wyniki listy i wyboru partycji. Partycja 1 (zarezerwowana/16 MB), partycja 2 (system/100 MB) i partycja 3 (podstawowa/126 GB) są wyświetlane z partycją 2 wybraną przy użyciu polecenia `sel part 2` .

         ![Wyniki listy, a następnie wybrania partycji. Partycja 1 (zarezerwowana | 16 MB), partycja 2 (system | 100 MB) i partycja 3 (podstawowa | 126 GB) są wyświetlane z wybraną partycją 2.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Przypisz literę do partycji EFI przy użyciu polecenia `assign` .

         Na poniższej ilustracji `assign` polecenie i nowy **system dysków (F:)** są widoczne w Eksploratorze plików.

         ![Przypisanie polecenia i nowego dysku SYSTEMowego (F:) są widoczne w Eksploratorze plików.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Utwórz listę danych magazynu BCD przy użyciu następującego polecenia:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Na poniższej ilustracji moduł ładujący rozruchu systemu Windows znajduje się w maszynie wirtualnej generacji 2 z wyróżnionym atrybutem identyfikatora. Wyróżniony atrybut identyfikatora ma wartość **{default}**.

         ![Program ładujący rozruchu systemu Windows jest wyświetlany w maszynie wirtualnej generacji 2 z wyróżnionym atrybutem identyfikatora. Wyróżniony atrybut identyfikatora pokazuje jako wartość domyślną.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Zanotuj identyfikator modułu ładującego rozruchu systemu Windows, którego ścieżką jest **\windows\system32\winload.EFI**.

1. Zwróć uwagę, że brakuje zmiennej OSDEVICE na aktywnej partycji:

   ![Atrybuty Menedżera rozruchu systemu Windows i modułu ładującego rozruchu systemu Windows są wymienione w wierszu polecenia z brakującym atrybutem urządzenia systemu operacyjnego.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   Na tym obrazie atrybuty Menedżera rozruchu systemu Windows i modułu ładującego rozruchu systemu Windows są wymienione w wierszu polecenia, ale brakuje atrybutu OSDEVICE.

1. Dodaj zmienną OSDEVICE na podstawie następujących informacji:

   W przypadku dysków systemu operacyjnego z jedną partycją Dodaj `BOOT` .

   > [!NOTE]
   > Folder z możliwością rozruchu będzie znajdować się na tej samej partycji co folder Windows folder **\Windows**.
   > - Folder rozruchowy dla maszyn wirtualnych 1. generacji to **(folder \boot\bcd)**.
   > - Folder rozruchowy dla maszyn wirtualnych 2. generacji to **EFI\Microsoft\boot\bcd**.

   W przypadku maszyn wirtualnych 1. generacji wprowadź następujące polecenie:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   W przypadku maszyn wirtualnych 2. generacji wprowadź następujące polecenie:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   W przypadku wielu dysków systemu operacyjnego partycji Dodaj `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > Folder możliwy do rozruchowy najprawdopodobniej znajduje się na innej partycji niż **folder \Windows** folderu systemu Windows.
   > - Folder rozruchowy dla maszyn wirtualnych 1. generacji to **(folder \boot\bcd)**.
   > - Folder rozruchowy dla maszyn wirtualnych 2. generacji to **EFI\Microsoft\boot\bcd**.

   W przypadku maszyn wirtualnych 1. generacji wprowadź następujące polecenie:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   W przypadku maszyn wirtualnych 2. generacji wprowadź następujące polecenie:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Po zakończeniu tego zadania, Kontynuuj, aby [włączyć konsolę szeregową i kolekcję zrzutów pamięci](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Kontakt z pomocą techniczną

Błąd **pliku rejestru** ma rozwiązanie, ale konieczne będzie [utworzenie biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/) w celu uzyskania dalszej pomocy.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

**Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci przez uruchomienie następującego skryptu:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień jako administrator.
1. Uruchom następujące polecenia:

   **Włącz konsolę seryjną**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest większa niż rozmiar pamięci (RAM) na maszynie wirtualnej.

   Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odnieść tę lokalizację do dowolnego dysku z danymi dołączonym do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zamień **% główny_katalog_systemowy%** na literę dysku danych, na przykład **F:**, w poniższych poleceniach.

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
