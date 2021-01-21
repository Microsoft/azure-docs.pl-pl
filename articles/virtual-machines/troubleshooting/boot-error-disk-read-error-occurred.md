---
title: Rozwiązywanie problemów z błędem rozruchu — Wystąpił błąd odczytu dysku
description: W tym artykule przedstawiono procedurę rozwiązywania problemów, w których nie można odczytać dysku na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: 75d1cf8638f922bb0275322568eb1399db4f49e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629729"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Rozwiązywanie problemów z błędem rozruchu — Wystąpił błąd odczytu dysku

W tym artykule przedstawiono procedurę rozwiązywania problemów, w których nie można odczytać dysku na maszynie wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) do wyświetlania zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla monit o komunikat "Wystąpił błąd odczytu dysku. Naciśnij klawisze Ctrl+Alt+Del, aby uruchomić ponownie”.

   ![Komunikat o błędzie: Wystąpił błąd odczytu dysku. Naciśnij kombinację klawiszy Ctrl + Alt + Del, aby ponownie uruchomić system.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Przyczyna

Ten komunikat o błędzie wskazuje, że struktura dysku jest uszkodzona i nie można jej odczytać. Jeśli używasz maszyny wirtualnej generacji 1, możliwe jest również, że partycja dysku zawierająca dane konfiguracji rozruchowej nie jest ustawiona jako **aktywna**.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Wybierz rozwiązanie:
   - [Ustaw stan partycji na aktywny](#set-partition-status-to-active)
   - [Napraw partycję dysku](#fix-the-disk-partition)
1. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
1. Skompiluj ponownie maszynę wirtualną.

> [!NOTE]
> W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Aby rozwiązać ten problem, nastąpi Rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
1. Korzystając z Podłączanie pulpitu zdalnego, Połącz się z maszyną wirtualną naprawy.

### <a name="set-partition-status-to-active"></a>Ustaw stan partycji na aktywny

Maszyny wirtualne generacji 1 powinny najpierw sprawdzić, czy partycja systemu operacyjnego z magazynem BCD jest oznaczona jako **aktywna**. Jeśli masz maszynę wirtualną generacji 2, przejdź z wyprzedzeniem, aby [naprawić partycję dysku](#fix-the-disk-partition), ponieważ Flaga stanu była przestarzała w późniejszej generacji.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień (cmd.exe).
1. Wprowadź polecenie **diskpart** , aby uruchomić narzędzie **diskpart** .
1. Wprowadź **dysk listy** , aby wyświetlić listę dysków w systemie i zidentyfikować podłączony wirtualny dysk twardy systemu operacyjnego (VHD).
1. Po zlokalizowaniu wirtualnego dysku twardego systemu operacyjnego wprowadź **wartość SEL Disk #** , aby wybrać dysk. Na poniższym obrazie przedstawiono przykład miejsca, w którym dysk 1 jest dołączonym wirtualnym dyskiem twardym systemu operacyjnego.

   ![Okno Diskpart z danymi wyjściowymi polecenia * * List Disk * *, gdzie Disk 0 i Disk 1 są wyświetlane w tabeli. W oknie są również wyświetlane dane wyjściowe polecenia * * SEL Disk 1 * *, gdzie dysk 1 jest wybranym dyskiem](./media/disk-read-error-occurred/2.png)

1. Po wybraniu dysku wprowadź **partycję listy** , aby wyświetlić listę partycji wybranego dysku.
1. Po zidentyfikowaniu partycji rozruchowej wprowadź **wartość w polu Nr partycji SEL** , aby wybrać partycję. Partycja rozruchowa ma zwykle rozmiar około 350 MB.  Na przykład na poniższej ilustracji znajduje się partycja rozruchowa Partition 1.

   ![Okno Diskpart z danymi wyjściowymi polecenia * * Partition list * *, gdzie Partition 1 i Partition 2 są wyświetlane w tabeli. W oknie są również wyświetlane dane wyjściowe polecenia * * SEL Partition 1 * *, gdzie Partition 1 jest wybranym dyskiem.](./media/disk-read-error-occurred/3.png)

1. Wprowadź **partycję szczegółów** , aby sprawdzić stan partycji. Zobacz następujące zrzuty ekranu, aby poznać przykłady partycji ustawionej jako **aktywne: nie** lub **aktywne: tak**.

   **Aktywne: nie**

   ![Okno Diskpart z danymi wyjściowymi polecenia * * detail partition * *, gdzie Partition 1 jest ustawiona na * * Active: nie * *.](./media/disk-read-error-occurred/4.png)

   **Aktywne: tak**

   ![Okno Diskpart z danymi wyjściowymi polecenia * * detail partition * *, gdzie Partition 1 jest ustawiona na * * Active: Yes * *.](./media/disk-read-error-occurred/5.png)

1. Jeśli partycja nie jest ustawiona na wartość **Active**, wprowadź wartość **Active** , aby zmienić aktywną flagę.
1. Wprowadź **partycję szczegółową** , aby sprawdzić, czy zmiana stanu została ukończona prawidłowo, i sprawdź, czy dane wyjściowe obejmują **wartość Active: tak**. 
1. Wprowadź **Exit** , aby zamknąć narzędzie Diskpart i zapisać zmiany w konfiguracji.

### <a name="fix-the-disk-partition"></a>Napraw partycję dysku

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień (cmd.exe).
1. Użyj następującego polecenia, aby uruchomić **program CHKDSK** na dysku i wykonać poprawki błędów:

   `chkdsk <DRIVE LETTER>: /f`

   Dodanie polecenia **/f** spowoduje usunięcie wszystkich błędów na dysku. Pamiętaj, aby zastąpić **< literę dysku >** literą dołączonego wirtualnego dysku twardego systemu operacyjnego.

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
