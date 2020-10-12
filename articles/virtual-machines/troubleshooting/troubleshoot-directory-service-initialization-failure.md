---
title: Rozwiązywanie problemów z błędem zatrzymania systemu Windows — niepowodzenie inicjowania usługi katalogowej
description: Rozwiązywanie problemów, w których maszyna wirtualna Active Directory kontrolera domeny na platformie Azure jest zablokowana w pętli, co wymaga ponownego uruchomienia.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028366"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Rozwiązywanie problemów z błędem zatrzymania systemu Windows — niepowodzenie inicjowania usługi katalogowej

W tym artykule przedstawiono kroki rozwiązywania problemów, w których maszyna wirtualna Active Directory kontroler domeny (VM) na platformie Azure zablokowała pętlę i Stany wymagane do ponownego uruchomienia.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) do wyświetlania zrzutu ekranu maszyny wirtualnej zrzut ekranu pokazuje, że maszyna wirtualna musi zostać ponownie uruchomiona z powodu błędu, wyświetlając kod zatrzymania **0XC00002E1** w systemie Windows Server 2008 R2 lub **0xC00002E2** w systemie Windows Server 2012 lub nowszym.

![Stany ekranu startowego systemu Windows Server 2012 "komputer napotkał problem i wymaga ponownego uruchomienia. Właśnie zbieramy pewne informacje o błędzie, a następnie będziemy ponownie uruchamiać system ".](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Przyczyna

Kod błędu **0xC00002E2** reprezentuje **STATUS_DS_INIT_FAILURE**, a kod błędu **0xC00002E1** reprezentuje **STATUS_DS_CANT_START**. Oba błędy występują w przypadku wystąpienia problemu z usługą katalogową.

Podczas rozruchu systemu operacyjnego wymuszone ponowne uruchomienie komputera przez lokalny serwer uwierzytelniania zabezpieczeń (**LSASS.exe**), który uwierzytelnia logowania użytkowników. Nie można przeprowadzić uwierzytelniania, gdy system operacyjny na maszynie wirtualnej jest kontrolerem domeny, który nie ma dostępu do odczytu/zapisu do swojej lokalnej Active Directory bazy danych. Ze względu na brak dostępu do **Active Directory (AD)** nie można uwierzytelnić LSASS.exe i wymuszono ponowne uruchomienie systemu operacyjnego.

Przyczyną tego błędu może być dowolny z następujących warunków:

- Nie ma dostępu do dysku przechowującego lokalną bazę danych usługi AD (**NTDS. Edytuj**).
- Dysk przechowujący lokalną bazę danych usługi AD (NTDS. Za mało wolnego miejsca.
- Lokalna baza danych usługi AD (NTDS. Plik DIT) nie istnieje.
- Maszyna wirtualna ma wiele dysków, a zasady sieci magazynowania (SAN) są nieprawidłowo skonfigurowane. Zasady sieci SAN nie są ustawione na **ONLINEALL**, a dyski inne niż OS są dołączone w trybie offline w Menedżerze dysków.
- Lokalna baza danych usługi AD (NTDS. Plik DIT) jest uszkodzony.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu:

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Wolne miejsce na dysku.
1. Sprawdź, czy dysk zawierający bazę danych usługi AD jest dołączony.
1. Włącz tryb przywracania usług katalogowych.
1. **Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
1. Skompiluj ponownie maszynę wirtualną.
1. Skonfiguruj ponownie zasady sieci SAN.

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Aby rozwiązać ten problem, nastąpi Rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) , aby przygotować maszynę wirtualną naprawy.
1. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="free-up-space-on-disk"></a>Zwolnij miejsce na dysku

Ponieważ dysk jest teraz dołączony do naprawianej maszyny wirtualnej, sprawdź, czy na dysku, na którym znajduje się Active Directory wewnętrzna baza danych, jest wystarczająca ilość miejsca do poprawnego wykonania.

1. Sprawdź, czy dysk jest pełny, klikając go prawym przyciskiem myszy i wybierając polecenie **Właściwości**.
1. Jeśli dysk ma mniej niż 300 MB wolnego miejsca, [rozwiń go maksymalnie 1 TB przy użyciu programu PowerShell](../windows/expand-os-disk.md).
1. Jeśli dysk osiągnie 1 TB zajętego miejsca, wykonaj Oczyszczanie dysku.

   1. Użyj programu PowerShell [, aby odłączyć dysk danych](../windows/detach-disk.md#detach-a-data-disk-using-powershell) od USZKODZONEJ maszyny wirtualnej.
   1. Po odłączeniu od uszkodzonej maszyny wirtualnej [Podłącz dysk danych](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm) do działającej maszyny wirtualnej.
   1. Za pomocą [Narzędzia do oczyszczania dysku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) Zwolnij dodatkowe miejsce.

1. **Opcjonalnie** — Jeśli potrzebujesz więcej miejsca, Otwórz wystąpienie programu CMD i wprowadź polecenie, `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` Aby wykonać defragmentację na dysku:

  * W poleceniu Zastąp ciąg `<LETTER ASSIGNED TO THE OS DISK>` literą dysku systemu operacyjnego. Na przykład, jeśli litera dysku to `F:` , to polecenie będzie `defrag F: /u /x /g` .

  * W zależności od poziomu fragmentacji może upłynąć kilka godzin.

Jeśli na dysku jest wystarczająca ilość miejsca, przejdź do następnego zadania.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Sprawdź, czy dysk zawierający Active Directoryą bazę danych jest dołączony

1. Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   1. Załaduj plik rejestru:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      W wyznaczeniu `f:` przyjęto założenie, że dysk jest dyskiem `F:` . Użyj litery dysku należącej do dysku zawierającego dysk systemu operacyjnego.

   1. Określ literę dysku i folder usługi **NTDS. Edytuj**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Zwolnij plik rejestru:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Przy użyciu Azure Portal upewnij się, że dysk, na którym NTDS. Program dytuj jest skonfigurowany do maszyny wirtualnej.
1. Za pomocą konsoli Zarządzanie dyskami w systemie operacyjnym gościa Sprawdź, czy dysk zawierający konsolę NTDS. Edytuj w trybie online.
   1. Narzędzie do zarządzania dyskami znajduje się w **narzędziach administracyjnych > zarządzania komputerem > magazynu**lub można uzyskać do nich dostęp przy użyciu `diskmgmt.msc` polecenia w wystąpieniu programu Cmd.
1. Jeśli dysk nie jest dołączony do maszyny wirtualnej, należy ponownie dołączyć dysk danych w celu rozwiązania problemu.

   Jeśli dysk został podłączony normalnie, przejdź do następnego zadania.

### <a name="enable-directory-services-restore-mode"></a>Włącz tryb przywracania usług katalogowych

Skonfiguruj maszynę wirtualną tak, aby uruchamiała się w trybie **przywracania usług katalogowych (DSRM)** , aby pominąć sprawdzanie istnienia NTDS. Plik DIT podczas rozruchu.

1. Przed kontynuowaniem sprawdź, czy zostały wykonane poprzednie zadania w celu dołączenia dysku do maszyny wirtualnej naprawy i określ, który dysk ma być obiektem NTDS. Plik DIT znajduje się w lokalizacji.
1. Używając wystąpienia programu CMD z podwyższonym poziomem uprawnień, Wyświetl informacje o partycji rozruchowej w tym magazynie, aby znaleźć identyfikator z aktywnej partycji:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Zamień na `< Drive Letter >` literę określoną w poprzednich krokach.

   ![Zrzut ekranu przedstawia wystąpienie CMD z podwyższonym poziomem uprawnień po wprowadzeniu polecenia "bcdedit/Store <litera dysku>: \boot\bcd/enum", które powoduje wyświetlenie Menedżera rozruchu systemu Windows z identyfikatorem.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Włącz `safeboot DsRepair` flagę na partycji rozruchowej:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Zamień `< Drive Letter >` i `< Identifier >` na wartości określone w poprzednich krokach.

1. Wykonaj ponownie zapytanie dotyczące opcji rozruchowych, aby upewnić się, że zmiana została prawidłowo ustawiona.

   ![Zrzut ekranu przedstawia wystąpienie CMD z podwyższonym poziomem uprawnień po włączeniu flagi DsRepair SAFEBOOT.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę szeregową, uruchom następujący skrypt, otwierając sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator) i uruchamiając następujące polecenia.

1. Włącz konsolę seryjną:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest co najmniej równa rozmiarowi pamięci (RAM) na maszynie wirtualnej.

  1. Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odnieść się do dowolnego dysku danych dołączonego do maszyny wirtualnej z wystarczającą ilością wolnego miejsca.

     Aby zmienić lokalizację, Zamień na `%SystemRoot%` literę dysku (na przykład `F:` ) dysku danych w następujących poleceniach.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Poniższa konfiguracja została zasugerowana do włączenia zrzutu systemu operacyjnego:

  **Załaduj przerwany dysk systemu operacyjnego**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **Włącz w ControlSet001**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Włącz w ControlSet002**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Zwolnij przerwany dysk systemu operacyjnego**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

1. Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

### <a name="reconfigure-the-storage-area-network-policy"></a>Skonfiguruj ponownie zasady sieci magazynowania

1. Podczas rozruchu w trybie DSRM jedynym użytkownikiem dostępnym do logowania jest administrator odzyskiwania, który został użyty podczas awansowania maszyny wirtualnej do kontrolera domeny. Wszyscy inni użytkownicy będą mogli wyświetlić błąd uwierzytelniania.

   1. Jeśli żaden inny kontroler domeny nie jest dostępny, należy zalogować się lokalnie przy użyciu `.\administrator` `machinename\administrator` hasła lub w trybie DSRM.

1. Skonfiguruj zasady sieci SAN, aby wszystkie dyski były w trybie online.

   1. Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień i wprowadź `DISKPART` .
   1. Zapytanie o listę dysków.

      `DISKPART> list disk`

   1. Wprowadź następujące polecenia, aby wybrać dysk, który ma zostać przełączony w tryb online i zmienić zasady sieci SAN:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. Po naprawieniu problemu upewnij się, że flaga `DsRepair safeboot` została usunięta:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Uruchom ponownie maszynę wirtualną.

   > [!NOTE]
   > Jeśli maszyna wirtualna była właśnie migrowana z lokalnego i chcesz migrować więcej kontrolerów domeny z lokalnego na platformę Azure, należy rozważyć wykonanie kroków opisanych w artykule poniżej, aby zapobiec wystąpieniu tego problemu w przyszłości migracji:
   >
   > [Jak przekazać istniejące lokalne kontrolery domeny funkcji Hyper-V do platformy Azure za pomocą Azure PowerShell](https://support.microsoft.com/help/2904015)
