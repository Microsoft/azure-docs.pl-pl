---
title: Maszyna wirtualna nie odpowiada podczas stosowania zasad konfiguracji zasad inspekcji
description: Ten artykuł zawiera kroki rozwiązywania problemów, w których maszyna wirtualna (VM) przestaje odpowiadać podczas stosowania zasad konfiguracji zasad inspekcji, co uniemożliwia rozruch maszyny wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: f286542c91ba473d13595d8e8299b1bbd8c93856
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632609"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>Maszyna wirtualna nie odpowiada podczas stosowania zasad konfiguracji zasad inspekcji

Ten artykuł zawiera kroki rozwiązywania problemów, w których maszyna wirtualna (VM) przestaje odpowiadać podczas stosowania zasad konfiguracji zasad inspekcji, co uniemożliwia rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zostanie wyświetlony komunikat zrzut ekranu przedstawiający, że system operacyjny (OS) nie odpowiadał podczas rozruchu z **zastosowaniem zasad konfiguracji zasad inspekcji**.

  ![Rozruch systemu operacyjnego z komunikatem: "stosowanie zasad konfiguracji zasad inspekcji"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Rozruch systemu operacyjnego w systemie Windows Server 2012 z komunikatem: "stosowanie zasad konfiguracji zasad inspekcji"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Przyczyna

Istnieją blokady powodujące konflikty, gdy zasady próbują oczyścić stare profile użytkowników.

> [!NOTE]
> Dotyczy to tylko systemów Windows Server 2012 i Windows Server 2012 R2.

Poniżej przedstawiono zasady problematyczne: *komputery szablony, \ System/użytkownik Profiles\Delete profile użytkowników starsze niż określona liczba dni po ponownym uruchomieniu systemu.*

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
1. Wyłącz zasady.
1. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
1. Skompiluj ponownie maszynę wirtualną.
1. Zbierz plik zrzutu pamięci i Prześlij bilet pomocy technicznej.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
1. Korzystając z Podłączanie pulpitu zdalnego, Połącz się z maszyną wirtualną naprawy.

### <a name="disable-the-policy"></a>Wyłączanie zasad

1. Na stronie Naprawa maszyny wirtualnej Otwórz **Edytor rejestru**.
1. Znajdź klucz **HKEY_LOCAL_MACHINE** i wybierz pozycję **plik > Załaduj gałąź** z menu.

   ![Nawigowanie w Edytorze rejestru w celu załadowania gałęzi.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Aby załadować klucze rejestru z systemu w trybie offline, można użyć funkcji ładowania Hive. W takim przypadku system jest uszkodzonym dyskiem podłączonym do maszyny wirtualnej naprawy.
   - Ustawienia całego systemu są przechowywane w **HKEY_LOCAL_MACHINE** i mogą być skracane jako **HKLM**.

1. Na dołączonym dysku Otwórz `\windows\system32\config\SOFTWARE` plik.

   - Gdy zostanie wyświetlony monit o podanie nazwy, wprowadź **BROKENSOFTWARE**.
   - Aby sprawdzić, czy **BROKENSOFTWARE** został załadowany, rozwiń **HKEY_LOCAL_MACHINE** i poszukaj dodanego klucza **BROKENSOFTWARE** .

1. Przejdź do **BROKENSOFTWARE** i sprawdź, czy klucz **CleanupProfiles** istnieje w załadowanym elemencie Hive.

   - Jeśli klucz istnieje, zasady **CleanupProfiles** są ustawione. Jego wartość reprezentuje zasady przechowywania mierzone w dniach.
   - Jeśli klucz nie istnieje, zasady **CleanupProfiles** nie są ustawione. W tej sytuacji przejdź do przodu, aby [przesłać bilet pomocy technicznej za pomocą pliku zrzutu pamięci](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Usuń klucz **CleanupProfiles** za pomocą tego polecenia:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Zwolnij gałąź **BROKENSOFTWARE** za pomocą tego polecenia:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

**Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci przez uruchomienie następującego skryptu:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień jako administrator.
1. Utwórz listę danych magazynu BCD i określ identyfikator modułu ładującego rozruchu, który będzie używany w następnym kroku.

   1. W przypadku maszyny wirtualnej 1. generacji wprowadź następujące polecenie i zanotuj identyfikator wymieniony poniżej:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - W poleceniu Zamień na `<BOOT PARTITON>` literę partycji na dysku dołączonym, który zawiera folder rozruchowy.

        ![Rysunek 4 przedstawia dane wyjściowe dotyczące wyświetlania listy magazynów BCD na maszynie wirtualnej generacji 1, która zawiera listę w obszarze ładujący rozruchu systemu Windows numer identyfikacyjny.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. W przypadku maszyny wirtualnej 2. generacji wprowadź następujące polecenie i zanotuj wymieniony identyfikator:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - W poleceniu Zastąp `<LETTER OF THE EFI SYSTEM PARTITION>` literą partycji systemowej EFI.
      - Pomocne może być uruchomienie konsoli zarządzania dyskami w celu zidentyfikowania odpowiedniej partycji systemowej oznaczonej jako **partycja systemowa EFI**.
      - Identyfikator może być unikatowym identyfikatorem GUID lub może być domyślnym **menedżerem Bootmgr**.

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
   
### <a name="rebuild-the-virtual-machine"></a>Kompiluj ponownie maszynę wirtualną

1. Aby skompilować ponownie maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

1. Sprawdź, czy maszyna wirtualna uruchamia się normalnie, aby sprawdzić, czy problem został rozwiązany.

   - Jeśli problem nie został naprawiony, Kontynuuj [zbieranie pliku zrzutu i przesyłanie biletu pomocy technicznej](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Jeśli problem został rozwiązany, nie trzeba wykonywać żadnych dalszych kroków.

Jeśli problem został rozwiązany, zasady są teraz wyłączone lokalnie. W przypadku stałego rozwiązania nie należy używać zasad CleanupProfiles na maszynach wirtualnych, ponieważ spowoduje to automatyczne usunięcie profilów użytkowników. Użyj innej metody do przeprowadzenia oczyszczania profilu, na przykład zaplanowanego zadania lub skryptu.

**Nie używaj tych zasad:** 
 *Machine\Admin Administracyjne\system\profile użytkownika Profiles\Delete profile użytkowników starsze niż określona liczba dni po ponownym uruchomieniu systemu.*

### <a name="the-issue-should-now-be-fixed"></a>Problem powinien teraz zostać rozwiązany

Przetestuj maszynę wirtualną, aby upewnić się, że działa jak normalne. Jeśli nadal występują problemy, możesz przejść do następnej sekcji, aby uzyskać dalszą pomoc.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Zbierz plik zrzutu pamięci i Prześlij bilet pomocy technicznej

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii, a następnie skontaktować się z pomocą techniczną przy użyciu pliku zrzutu pamięci. Aby zebrać plik zrzutu, wykonaj następujące kroki:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować nową maszynę wirtualną naprawy.
1. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na stronie napraw maszynę wirtualną przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika przypisana do dołączonego dysku systemu operacyjnego jest oznaczona jako *F*, należy przejść do `F:\Windows` .
1. Znajdź `memory.dmp` plik, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu pamięci.
1. Jeśli masz problemy z lokalizowaniem `memory.dmp` pliku, zamiast tego użyj [wywołań przerwań, które nie są MASKOWANE (NMI) w konsoli szeregowej](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Postępuj zgodnie z przewodnikiem, aby [wygenerować plik zrzutu awaryjnego za pomocą wywołań NMI w tym miejscu](/windows/client-management/generate-kernel-or-complete-crash-dump).