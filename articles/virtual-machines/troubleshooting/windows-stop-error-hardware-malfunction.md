---
title: Błąd zatrzymania systemu Windows — usterka sprzętowa
description: W tym artykule przedstawiono procedurę rozwiązywania problemów z awarią maszyn wirtualnych z systemem Windows Server 2008 i komunikatem o błędzie informującym o wystąpieniu usterki sprzętowej.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 18622d60f3a33658fadfd28c53c93a07b4b438a9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488650"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Błąd zatrzymania systemu Windows — usterka sprzętowa

W tym artykule przedstawiono procedurę rozwiązywania problemów z awarią maszyn wirtualnych z systemem Windows Server 2008 i komunikatem o błędzie informującym o wystąpieniu usterki sprzętowej.

## <a name="symptoms"></a>Objawy

Gdy używasz [diagnostyki rozruchu](./boot-diagnostics.md) do wyświetlania zrzutu ekranu maszyny wirtualnej, zobaczysz, że zrzut ekranu wyświetla niebieski ekran z komunikatem:

**\*\*\* Usterka sprzętowa**

**Skontaktuj się z dostawcą, aby uzyskać pomoc techniczną**

**\*\*\* System został zatrzymany \*\*\***

#### <a name="blue-screen"></a>Niebieski ekran

![Zrzut ekranu przedstawia awarię sprzętu niebieskiego ekranu.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Konsola szeregowa

![Zrzut ekranu przedstawia komunikat "Usterka sprzętowa" w konsoli szeregowej, jeśli konsola szeregowa została włączona.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Przyczyna

Ten ekran zostanie wyświetlony, gdy system operacyjny gościa nie został prawidłowo skonfigurowany i zostanie wysłane przerwanie z maską (NMI). Komunikat o błędzie wskazuje, że program trybu jądra wygenerował wyjątek, którego procedura obsługi nie przechwytuje. Można zidentyfikować, który wyjątek został wygenerowany przez zbieranie zrzutu pamięci.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu 

1. Konfigurowanie klucza rejestru przerwania bez maskowania (NMI) 
2. Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu 
3. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci 
4. Kompiluj ponownie maszynę wirtualną 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Konfigurowanie klucza rejestru przerwania bez maskowania (NMI)

1. Korzystając z Azure Portal, uruchom ponownie maszynę wirtualną, aby system operacyjny gościa był uruchamiany normalnie. 
2. Po przywróceniu pewnego dostępu do maszyny wirtualnej Otwórz wiersz polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator). 
3. Skonfiguruj klucz rejestru NMI za pomocą następującego polecenia:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Zobacz więcej informacji na temat polecenia REG ADD](/windows-server/administration/windows-commands/reg-add)
4. *(Opcjonalnie)* Konfiguracja zbierania zrzutów pamięci:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Opcjonalnie)* Skonfiguruj dostęp do konsoli szeregowej:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Wyświetl więcej informacji na temat polecenia BCDEDIT](/windows-server/administration/windows-commands/bcdedit)
6. Uruchom ponownie maszynę wirtualną za pomocą następującego polecenia:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Wyświetl dalsze informacje dotyczące polecenia SHUTDOWN](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> Problem powinien zostać rozwiązany.

> [!NOTE]
> Po ponownym uruchomieniu przetestuj maszynę wirtualną, aby upewnić się, że działa jak normalne. Jeśli nadal występują problemy, możesz przejść do następnej sekcji, aby uzyskać dalsze instrukcje.

> [!TIP]
> Zalecane jest skonfigurowanie klucza rejestru przerwania niemaskowanego (NMI) w powyższej sekcji, jednak jeśli maszyna wirtualna nie została przeprowadzona normalnie, wówczas planowane zmiany w rejestrze systemu operacyjnego gościa mogą nie zostać wykonane. W takim przypadku można wykonać poniższe instrukcje, aby ręcznie dodać ustawienia rejestru.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
2. Nawiąż połączenie z maszyną wirtualną naprawy przy użyciu Podłączanie pulpitu zdalnego.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Przed odbudowywaniem maszyny wirtualnej zaleca się włączenie zbierania zrzutów pamięci i konsoli szeregowej. Aby to zrobić, uruchom następujący skrypt: 

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator). 
2. Utwórz listę danych magazynu BCD i określ identyfikator modułu ładującego rozruchu, który będzie używany w następnym kroku. 
    1. W przypadku maszyny wirtualnej 1. generacji wprowadź następujące polecenie i zanotuj identyfikator wymieniony poniżej: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        W poleceniu Zamień na `<BOOT PARTITON>` literę partycji na dysku dołączonym, który zawiera folder rozruchowy. 

        ![Zrzut ekranu przedstawia dane wyjściowe z listą magazynu BCD na maszynie wirtualnej generacji 1, która zawiera listę w obszarze ładujący rozruchu systemu Windows numer identyfikacyjny.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. W przypadku maszyny wirtualnej 2. generacji wprowadź następujące polecenie i zanotuj wymieniony identyfikator:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * W poleceniu Zastąp `<LETTER OF THE EFI SYSTEM PARTITION>` literą partycji systemowej EFI.
        * Pomocne może być uruchomienie konsoli zarządzania dyskami w celu zidentyfikowania odpowiedniej partycji systemowej oznaczonej jako *partycja systemowa EFI*.
        * Identyfikator może być unikatowym identyfikatorem GUID lub może być domyślnym *menedżerem Bootmgr*.
3. Uruchom następujące polecenia, aby włączyć konsolę szeregową:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * W poleceniu Zamień na `<VOLUME LETTER WHERE THE BCD FOLDER IS>` literę folderu BCD.
    * W poleceniu Zastąp `<BOOT LOADER IDENTIFIER>` wartość identyfikatorem znalezionym w poprzednim kroku.
4. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest większa niż rozmiar pamięci (RAM) na maszynie wirtualnej. 
    1. Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci. Zamiast tworzyć plik na dysku systemu operacyjnego, można odwołać się do dowolnego innego dysku danych dołączonego do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zastąp wartość **% główny_katalog_systemowy%** literą dysku (na przykład **F:**) dysku danych w poleceniach wymienionych poniżej. 
    2. Wprowadź poniższe polecenia (zalecana konfiguracja zrzutu):

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
### <a name="rebuild-the-virtual-machine"></a>Kompiluj ponownie maszynę wirtualną

* Aby skompilować ponownie maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z rozruchem maszyn wirtualnych platformy Azure](./boot-error-troubleshoot.md)