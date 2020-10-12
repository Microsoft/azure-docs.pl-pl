---
title: Nie można uruchomić maszyny wirtualnej systemu Windows z powodu Menedżera rozruchu systemu Windows
description: W tym artykule przedstawiono procedurę rozwiązywania problemów, w których Menedżer rozruchu systemu Windows uniemożliwia rozruch maszyny wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 2457952051f575306de46e3e8145cc26678a1ef8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526543"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Maszyna wirtualna z systemem Windows nie może zostać uruchomiona z powodu Menedżera rozruchu systemu Windows

W tym artykule przedstawiono procedurę rozwiązywania problemów, w których Menedżer rozruchu systemu Windows uniemożliwia rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

Maszyna wirtualna jest w stanie oczekiwania na monitowanie użytkownika i nie zostanie uruchomiona, chyba że zostanie ona przetestowana ręcznie.

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla Menedżera rozruchu systemu Windows z komunikatem *Wybierz system operacyjny do uruchomienia lub naciśnij klawisz Tab, aby wybrać narzędzie:*.

Rysunek 1.
 
![Menedżer rozruchu systemu Windows przedstawiający "Wybierz system operacyjny do uruchomienia lub naciśnij klawisz TAB, aby wybrać narzędzie:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany *DISPLAYBOOTMENU* flagi BCD w Menedżerze rozruchu systemu Windows. Gdy flaga jest włączona, Menedżer rozruchu systemu Windows wyświetli z pytaniem użytkownika, podczas procesu uruchamiania, aby wybrać moduł ładujący, który ma zostać uruchomiony, powodując opóźnienie rozruchu. Na platformie Azure ta funkcja może zostać dodana do czasu potrzebnego do rozruchu maszyny wirtualnej.

## <a name="solution"></a>Rozwiązanie

Przegląd procesu:

1. Skonfiguruj program w celu skrócenia czasu rozruchu przy użyciu konsoli szeregowej.
2. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
3. Skonfiguruj krótszy czas rozruchu na naprawianej maszynie wirtualnej.
4. **Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
5. Skompiluj ponownie maszynę wirtualną.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Skonfiguruj, aby przyspieszyć czas rozruchu przy użyciu konsoli szeregowej

Jeśli masz dostęp do konsoli szeregowej, istnieją dwa sposoby przyspieszenia rozruchu. Zmniejsz czas oczekiwania *DISPLAYBOOTMENU* lub Usuń flagę całkowicie.

1. Postępuj zgodnie z instrukcjami, aby uzyskać dostęp do konsoli programu [Azure serial Console dla systemu Windows](./serial-console-windows.md) .

   > [!NOTE]
   > Jeśli nie możesz uzyskać dostępu do konsoli szeregowej, przejdź dalej, aby [utworzyć naprawczą maszynę wirtualną i uzyskać do niej dostęp](#create-and-access-a-repair-vm).

2. **Opcja A**: skrócenie czasu oczekiwania

   a. Czas oczekiwania jest domyślnie ustawiony na 30 sekund, ale można go zmienić na szybszy (np. 5 sekund).

   b. Użyj następującego polecenia w konsoli szeregowej, aby dostosować wartość limitu czasu:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opcja B**: usuwanie flagi BCD

   a. Aby uniemożliwić całkowite wyświetlenie menu rozruchu, wprowadź następujące polecenie:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Jeśli nie można użyć konsoli szeregowej w celu skonfigurowania krótszego czasu rozruchu w powyższych krokach, można wykonać następujące czynności. Teraz będziesz rozwiązywać problemy w trybie offline, aby rozwiązać ten problem.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
2. Użyj Podłączanie pulpitu zdalnego połączyć się z maszyną wirtualną naprawy.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurowanie na potrzeby krótszego czasu rozruchu na naprawianej maszynie wirtualnej

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Aby włączyć DisplayBootMenu, wprowadź następujące elementy:

   Użyj tego polecenia dla **maszyn wirtualnych 1. generacji**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Użyj tego polecenia dla **maszyn wirtualnych 2. generacji**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Zastąp elementy większe niż lub mniejsze niż symbole oraz tekst w nich, np. "< tekst tutaj >".

3. Zmień wartość limitu czasu na 5 sekund:

   Użyj tego polecenia dla **maszyn wirtualnych 1. generacji**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Użyj tego polecenia dla **maszyn wirtualnych 2. generacji**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Zastąp elementy większe niż lub mniejsze niż symbole oraz tekst w nich, np. "< tekst tutaj >".

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   Włącz konsolę seryjną

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Zastąp elementy większe niż lub mniejsze niż symbole oraz tekst w nich, np. "< tekst tutaj >".

3. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest taka sama jak rozmiar pamięci (RAM) na maszynie wirtualnej.

   Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odwołać się do dowolnego dysku danych dołączonego do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zastąp "% główny_katalog_systemowy%" literą dysku (na przykład "F:") dysku danych w poniższych poleceniach.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Sugerowana konfiguracja do włączenia zrzutu systemu operacyjnego

**Załaduj przerwany dysk systemu operacyjnego**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Włącz w ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Włącz w ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Zwolnij przerwany dysk systemu operacyjnego:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Odbuduj oryginalną maszynę wirtualną

Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
