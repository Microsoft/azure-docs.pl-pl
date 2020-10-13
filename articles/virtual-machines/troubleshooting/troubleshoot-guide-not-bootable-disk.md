---
title: Błąd rozruchu — "to nie jest dysk rozruchowy"
description: W tym artykule przedstawiono kroki rozwiązywania problemów, w których dysk nie jest rozruchowy na maszynie wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509039"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Błąd rozruchu — to nie jest dysk rozruchowy

W tym artykule przedstawiono procedurę rozwiązywania problemów, w których dysk nie jest rozruchowy na maszynie wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla monit z komunikatem "nie jest to dysk rozruchowy. Włóż dyskietkę rozruchową i naciśnij dowolny klawisz, aby ponowić próbę... ".

   Rysunek 1.

   ![Rysunek 1 pokazuje komunikat * "to nie jest dysk rozruchowy. Włóż dyskietkę rozruchową i naciśnij dowolny klawisz, aby ponowić próbę... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Przyczyna

Ten komunikat o błędzie oznacza, że proces rozruchu systemu operacyjnego nie może zlokalizować aktywnej partycji systemowej. Ten błąd może również oznaczać, że brakuje odwołania do magazynu Dane konfiguracji rozruchu (BCD), uniemożliwiając jego lokalizowanie partycji systemu Windows.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
2. Ustaw stan partycji na aktywny.
3. Napraw partycję dysku.
4. **Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
5. Odbuduj oryginalną maszynę wirtualną.

   > [!NOTE]
   > W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Aby rozwiązać ten problem, nastąpi Rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
2. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="set-partition-status-to-active"></a>Ustaw stan partycji na aktywny

Maszyny wirtualne generacji 1 powinny najpierw sprawdzić, czy partycja systemu operacyjnego, która zawiera magazyn BCD, jest oznaczona jako *aktywna*. Jeśli masz maszynę wirtualną generacji 2, przejdź z wyprzedzeniem, aby [naprawić partycję dysku](#fix-the-disk-partition), ponieważ flaga *stanu* była przestarzała w późniejszej generacji.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień *(cmd.exe)*.
2. Wprowadź polecenie *diskpart* , aby uruchomić narzędzie Diskpart.
3. Wprowadź *dysk listy* , aby wyświetlić listę dysków w systemie i zidentyfikować dołączony wirtualny dysk twardy systemu operacyjnego.
4. Po zlokalizowaniu wirtualnego dysku twardego systemu operacyjnego wprowadź *wartość SEL Disk #* , aby wybrać dysk.  Zobacz rysunek 2, gdzie dysk 1 jest dołączonym wirtualnym dyskiem systemu operacyjnego.

   Rysunek 2

   ![Rysunek 2 przedstawia okno programu * DISKPART * przedstawiające dane wyjściowe polecenia Wyświetl dysk, dysk 0 i dysk 1 wyświetlane w tabeli.  Pokazuje również dane wyjściowe polecenia SEL Disk 1, dysk 1 jest wybranym dyskiem](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Po wybraniu dysku wprowadź *partycję listy* , aby wyświetlić listę partycji wybranego dysku.
6. Po zidentyfikowaniu partycji rozruchowej wprowadź *wartość w polu Nr partycji SEL* , aby wybrać partycję.  Zazwyczaj partycja rozruchowa będzie o rozmiarze około 350 MB.  Zobacz rysunek 3, gdzie partycja 1 to partycja rozruchowa.

   Rysunek 3

   ![Rysunek 3 przedstawia okno * DISKPART * z danymi wyjściowymi polecenia * Partition list *. W tabeli są wyświetlane partycje 1 i 2. Wyświetla również dane wyjściowe polecenia * SEL Partition 1 *, gdy partycja 1 jest wybranym dyskiem.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Wprowadź "detail partition", aby sprawdzić stan partycji. Zobacz rysunek 4, gdzie partycja jest *aktywna: nie*, lub rysunek 5, gdzie partycja to "Active: Yes".

   Rysunek 4

   ![Rysunek 4 przedstawia okno * DISKPART * z danymi wyjściowymi polecenia * Partition-detail *, gdy partycja 1 jest ustawiona na * Active: nie *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Rysunek 5

   ![Rysunek 5 przedstawia okno * DISKPART * z danymi wyjściowymi polecenia * Partition-detail *, gdy partycja 1 jest ustawiona na * Active: Yes *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Jeśli partycja **nie jest aktywna**, wprowadź wartość *Active* , aby zmienić *aktywną* flagę.
9. Sprawdź, czy zmiana stanu została wykonana prawidłowo, wpisując polecenie *detail partition*.

   Rysunek 6.

   ![Rysunek 6 przedstawia okno narzędzia Diskpart z danymi wyjściowymi * detail partition * polecenie, gdy partycja 1 jest ustawiona na * Active: Yes *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Wprowadź *Exit* , aby zamknąć narzędzie Diskpart i zapisać zmiany w konfiguracji.

### <a name="fix-the-disk-partition"></a>Napraw partycję dysku

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień (cmd.exe).
2. Użyj następującego polecenia, aby uruchomić *program CHKDSK* na dyskach i naprawić błędy:

   `chkdsk <DRIVE LETTER>: /f`

   Dodanie opcji polecenia "/f" spowoduje naprawienie wszelkich błędów na dysku. Pamiętaj o zamianie na <DRIVE LETTER> literę dołączonego wirtualnego dysku twardego systemu operacyjnego.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   Włącz konsolę seryjną

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
