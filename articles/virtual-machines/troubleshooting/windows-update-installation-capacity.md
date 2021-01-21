---
title: Rozwiązywanie problemów z uruchamianiem systemu operacyjnego — Windows Update pojemności instalacji
description: Kroki rozwiązywania problemów, w których Windows Update (KB) pobierają błąd i nie odpowiadają na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 0c0ec45eee86031e1533b97ccf352de0ecf70e38
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633158"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Rozwiązywanie problemów z uruchamianiem systemu operacyjnego — Windows Update pojemności instalacji

Ten artykuł zawiera kroki rozwiązywania problemów z maszyną wirtualną platformy Azure, gdzie Windows Update (KB) pobiera błąd i przestaje odpowiadać.

## <a name="symptom"></a>Objaw

W przypadku korzystania z diagnostyki rozruchu w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla Windows Update (KB) w toku, ale kończy się niepowodzeniem z kodem błędu: **C01A001D**. Na poniższej ilustracji przedstawiono Windows Update (KB) zablokowała komunikat "błąd C01A001D zastosowania operacji aktualizacji # # # # # z # # # # # (# # # # # #)":

![Windows Update (KB) jest zablokowany komunikatem "błąd C01A001D stosowania operacji aktualizacji X z Y (Z)".](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Przyczyna

W takiej sytuacji system operacyjny (OS) nie może zakończyć instalacji Windows Update (KB), ponieważ nie można utworzyć pliku podstawowego w systemie plików. Wnioskując z tego kodu błędu, system operacyjny nie może zapisać żadnych plików na dysku.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu:

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Wolne miejsce na dysku.
1. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
1. Skompiluj ponownie maszynę wirtualną.

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Rozwiąż ten problem w trybie offline, aby rozwiązać ten problem.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
1. Korzystając z Podłączanie pulpitu zdalnego, Połącz się z maszyną wirtualną naprawy.

### <a name="free-up-space-on-the-disk"></a>Zwolnij miejsce na dysku

Aby rozwiązać ten problem:

- Zmień rozmiar dysku do 1 TB, jeśli nie jest jeszcze w maksymalnym rozmiarze wynoszącym 1 TB.
- Wykonaj Oczyszczanie dysku.
- Usuń fragment dysku.

1. Sprawdź, czy dysk jest pełny. Jeśli rozmiar dysku jest mniejszy niż 1 TB, rozwiń go maksymalnie 1 TB [przy użyciu programu PowerShell](../windows/expand-os-disk.md).
1. Jeśli dysk ma już 1 TB, należy przeprowadzić Oczyszczanie dysku.
   1. Za pomocą [Narzędzia do oczyszczania dysku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) Zwolnij miejsce.
1. Po zmianie rozmiarów i oczyszczeniu należy usunąć fragment dysku przy użyciu następującego polecenia:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Defragmentacja może potrwać kilka godzin, w zależności od poziomu fragmentacji.

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

    **Załaduj przerwany dysk systemu operacyjnego:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
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

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

Aby skompilować ponownie maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
