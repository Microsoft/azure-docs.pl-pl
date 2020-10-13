---
title: Błąd zatrzymania systemu Windows — 0x00000074 nieprawidłowe informacje o konfiguracji systemu
description: W tym artykule przedstawiono kroki rozwiązywania problemów, w których system Windows nie może przeprowadzić rozruchu i musi zostać ponownie uruchomiony z powodu nieprawidłowych informacji o konfiguracji systemu na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 4f2b338b8629209363acb7bbe0533831a089fe6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447315"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Błąd zatrzymania systemu Windows — 0x00000074 nieprawidłowe informacje o konfiguracji systemu

W tym artykule przedstawiono kroki rozwiązywania problemów, w których system Windows nie może przeprowadzić rozruchu i musi zostać ponownie uruchomiony z powodu nieprawidłowych informacji o konfiguracji systemu na maszynie wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla kod zatrzymania systemu Windows **#0x00000074** lub **BAD_SYSTEM_CONFIG_INFO**.

*Komputer napotkał problem i wymaga ponownego uruchomienia. Można uruchomić ponownie.* 
 *Aby uzyskać więcej informacji na temat tego problemu i możliwych poprawek http://windows.com/stopcode , odwiedź stronę* 
 *Jeśli skontaktujesz się z osobą odpowiedzialną za pomoc techniczną, przekaż im następujące informacje:* 
 *Kod zatrzymania: BAD_SYSTEM_CONFIG_INFO*

  ![0x00000074 kod zatrzymania systemu Windows, który jest również pokazywany jako "BAD_SYSTEM_CONFIG_INFO". System Windows informuje użytkownika, że jego komputer napotkał problem i wymaga ponownego uruchomienia.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>Przyczyna

Kod zatrzymania **BAD_SYSTEM_CONFIG_INFO** występuje, jeśli gałąź rejestru **systemu** jest uszkodzona. Ten błąd może być spowodowany jedną z następujących przyczyn:

- Gałąź rejestru nie została prawidłowo ZAMKNIĘTA.
- Gałąź rejestru jest uszkodzona.
- Brak kluczy lub wartości rejestru.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu:

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Sprawdź uszkodzenie Hive.
1. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
1. Skompiluj ponownie maszynę wirtualną.

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Aby rozwiązać ten problem, należy przeprowadzić rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) , aby przygotować maszynę wirtualną naprawy.
1. Sprawdź uszkodzenie Hive.
1. Użyj Podłączanie pulpitu zdalnego, aby nawiązać połączenie z maszyną wirtualną naprawy.
1. Skopiuj `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` folder i Zapisz go w odpowiedniej partycji dysku lub w innej bezpiecznej lokalizacji. Utwórz kopię zapasową tego folderu jako środek zapobiegawczy, ponieważ będziesz edytować krytyczne pliki rejestru. 

> [!NOTE]
> Utwórz kopię `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` folderu jako kopie zapasowe w przypadku konieczności wycofania wszelkich zmian wprowadzonych w rejestrze.

### <a name="check-for-hive-corruption"></a>Sprawdź uszkodzenie Hive

Poniższe instrukcje ułatwią ustalenie, czy przyczyną jest uszkodzenie Hive lub czy gałąź nie została poprawnie zamknięta. Jeśli gałąź nie została prawidłowo ZAMKNIĘTA, będzie można odblokować plik i naprawić maszynę wirtualną.

1. Na maszynie naprawczej należy otworzyć aplikację **Edytor rejestru** . Wpisz "regedit" na pasku wyszukiwania systemu Windows, aby go znaleźć.
1. W Edytorze rejestru wybierz pozycję **HKEY_LOCAL_MACHINE** , aby ją wyróżnić, a następnie wybierz pozycję **plik > Załaduj gałąź rejestru...** z menu.
1. Przejdź do `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` i wybierz pozycję **Otwórz**.
1. Po wyświetleniu monitu o wprowadzenie nazwy wprowadź **BROKENSYSTEM**.

   1. Jeśli nie można otworzyć programu Hive lub jest on pusty, gałąź jest uszkodzona. Jeśli gałąź została uszkodzona, [Otwórz bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

     ![Wystąpił błąd informujący, że Edytor rejestru nie może załadować gałęzi.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Jeśli gałąź zostanie otwarta normalnie, gałąź nie została prawidłowo ZAMKNIĘTA. Przejdź do kroku 5.

1. Aby naprawić gałąź, która nie została prawidłowo ZAMKNIĘTA, podświetl pozycję **BROKENSYSTEM** , a następnie wybierz pozycję **File > Unload Hive...** w celu odblokowania pliku.

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

Aby skompilować ponownie maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .
