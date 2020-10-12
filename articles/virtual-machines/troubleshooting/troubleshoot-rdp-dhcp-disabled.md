---
title: Nie można nawiązać zdalnego połączenia z usługą Azure Virtual Machines, ponieważ serwer DHCP jest wyłączony | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z łącznością RDP, która jest spowodowana przez usługę klienta DHCP, jest wyłączona w Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 945f8896a844e7a73107df44d03abc7290f4e3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999142"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nie można nawiązać połączenia RDP z platformą Azure Virtual Machines, ponieważ usługa klienta DHCP jest wyłączona

W tym artykule opisano problem polegający na tym, że nie można uzyskać zdalnego pulpitu do usługi Azure Windows Virtual Machines (maszyny wirtualne) po wyłączeniu usługi klienta DHCP na maszynie wirtualnej.


## <a name="symptoms"></a>Objawy
Nie można nawiązać połączenia RDP z maszyną wirtualną na platformie Azure, ponieważ usługa klienta DHCP jest wyłączona na maszynie wirtualnej. Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w Azure Portal zobaczysz, że maszyna wirtualna jest uruchamiana normalnie i czeka na poświadczenia na ekranie logowania. Dzienniki zdarzeń w maszynie wirtualnej można wyświetlić zdalnie przy użyciu Podgląd zdarzeń. Zobaczysz, że usługa klienta DHCP nie jest uruchomiona lub nie można jej uruchomić. Następujący przykładowy dziennik:

**Nazwa dziennika**: system </br>
**Źródło**: Menedżer sterowania usługami </br>
**Data**: 12/16/2015 11:19:36 am </br>
**Identyfikator zdarzenia**: 7022 </br>
**Kategoria zadania**: brak </br>
**Poziom**: błąd </br>
**Słowa kluczowe**: klasyczne</br>
**Użytkownik**: nie dotyczy </br>
**Komputer**: MyVM.cosotos.com</br>
**Opis**: usługa klienta DHCP zawiesiła się podczas uruchamiania.</br>

W przypadku maszyn wirtualnych Menedżer zasobów można użyć funkcji konsoli dostępu szeregowego do wykonywania zapytań dotyczących dzienników zdarzeń 7022 przy użyciu następującego polecenia:

```console
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

W przypadku klasycznych maszyn wirtualnych trzeba będzie korzystać z trybu OFFLINE i zebrać dzienniki ręcznie.

## <a name="cause"></a>Przyczyna

Usługa klienta DHCP nie jest uruchomiona na maszynie wirtualnej.

> [!NOTE]
> Ten artykuł ma zastosowanie tylko w przypadku usługi klienta DHCP, a nie serwera DHCP.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemu operacyjnego, którego dotyczy dana maszyna wirtualna, jako kopii zapasowej. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć kontrolki serial do włączenia protokołu DHCP lub [zresetowania interfejsu sieciowego](reset-network-interface.md) dla maszyny wirtualnej.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowej

1. Połącz się z [konsolą szeregową i Otwórz wystąpienie programu Cmd](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
2. Sprawdź, czy usługa DHCP jest wyłączona w interfejsie sieciowym:

    ```console
    sc query DHCP
    ```

3. Jeśli usługa DHCP jest zatrzymana, spróbuj uruchomić usługę

    ```console
    sc start DHCP
    ```

4. Wykonaj ponownie zapytanie dotyczące usługi, aby upewnić się, że usługa została uruchomiona pomyślnie.

    ```console
    sc query DHCP
    ```

    Spróbuj nawiązać połączenie z maszyną wirtualną i sprawdź, czy problem został rozwiązany.
5. Jeśli usługa nie zostanie uruchomiona, użyj następującego odpowiedniego rozwiązania na podstawie otrzymanego komunikatu o błędzie:

    | Błąd  |  Rozwiązanie |
    |---|---|
    | 5 — ODMOWA DOSTĘPU  | Sprawdź, [czy usługa klienta DHCP została zatrzymana z powodu błędu odmowy dostępu](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 — ERROR_SERVICE_REQUEST_TIMEOUT   | Zobacz [awarię lub zawieszenie usługi klienta DHCP](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 — ERROR_SERVICE_DISABLED  | Zobacz [Usługa klienta DHCP jest wyłączona](#dhcp-client-service-is-disabled).  |
    | 1059 — ERROR_CIRCULAR_DEPENDENCY  |[Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.   |
    | 1067 — ERROR_PROCESS_ABORTED |Zobacz [awarię lub zawieszenie usługi klienta DHCP](#dhcp-client-service-crashes-or-hangs).   |
    |1068 — ERROR_SERVICE_DEPENDENCY_FAIL   | [Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.  |
    |1069 — ERROR_SERVICE_LOGON_FAILED   |  Nie można wyświetlić [usługi klienta DHCP z powodu niepowodzenia logowania](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 — ERROR_SERVICE_START_HANG  | Zobacz [awarię lub zawieszenie usługi klienta DHCP](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 — ERROR_SERVICE_NEVER_STARTED  | Zobacz [Usługa klienta DHCP jest wyłączona](#dhcp-client-service-is-disabled).  |
    |1079 — ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.  |
    |1053 | [Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Usługa klienta DHCP została zatrzymana z powodu błędu odmowy dostępu

1. Połącz się z [konsolą szeregową](serial-console-windows.md) i Otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzie Monitor procesu, uruchamiając następujący skrypt:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Teraz uruchom śledzenie **ProcMon** :

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Odtwórz problem przez uruchomienie usługi generującej komunikat **odmowy dostępu** :

   ```
   sc start DHCP
   ```

   Gdy to się nie powiedzie, Zakończ śledzenie monitora procesu:

   ```
   procmon /Terminate
   ```
5. Zbierz plik **c:\temp\ProcMonTrace.PML** :

    1. [Dołącz dysk z danymi do maszyny wirtualnej](../windows/attach-managed-disk-portal.md
).
    2. Użyj konsoli szeregowej można skopiować plik na nowy dysk. Na przykład `copy C:\temp\ProcMonTrace.PML F:\`. W tym poleceniu F jest literą sterownika dołączonego dysku danych. Zastąp literę odpowiednio poprawną wartością.
    3. Odłącz dysk danych, a następnie dołącz go do działającej maszyny wirtualnej z zainstalowanym monitorem procesu ubstakke.

6. Otwórz **ProcMonTrace. PML** przy użyciu Monitora procesów na działającej maszynie wirtualnej. Następnie filtrowanie według **wyniku jest odmowa dostępu**, jak pokazano na poniższym zrzucie ekranu:

    ![Filtruj według wyniku w monitorze procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Naprawianie kluczy rejestru, folderów lub plików, które znajdują się w danych wyjściowych. Zazwyczaj ten problem jest spowodowany tym, że konto logowania używane w usłudze nie ma uprawnienia ACL do uzyskiwania dostępu do tych obiektów. Aby określić odpowiednie uprawnienia ACL dla konta logowania, można zaewidencjonować odpowiednią maszynę wirtualną.

#### <a name="dhcp-client-service-is-disabled"></a>Usługa klienta DHCP jest wyłączona

1. Przywróć domyślną wartość uruchamiania usługi:

   ```
   sc config DHCP start= auto
   ```

2. Uruchom usługę:

   ```
   sc start DHCP
   ```

3. Wykonaj ponownie zapytanie o stan usługi, aby upewnić się, że jest on uruchomiony:

   ```
   sc query DHCP
   ```

4. Spróbuj połączyć się z maszyną wirtualną za pomocą Pulpit zdalny.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Usługa klienta DHCP kończy się niepowodzeniem z powodu niepowodzenia logowania

1. Ponieważ ten problem występuje, jeśli konto uruchamiania tej usługi zostało zmienione, Przywróć konto do stanu domyślnego:

    ```console
    sc config DHCP obj= 'NT Authority\Localservice'
    ```

2. Uruchom usługę:

    ```console
    sc start DHCP
    ```

3. Spróbuj połączyć się z maszyną wirtualną za pomocą Pulpit zdalny.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Awaria lub zawieszenie usługi klienta DHCP

1. Jeśli stan usługi jest zablokowany w stanie **uruchamiania** lub **zatrzymywania** , spróbuj zatrzymać usługę:

    ```console
    sc stop DHCP
    ```

2. Wyodrębnij usługę do własnego kontenera "svchost":

    ```console
    sc config DHCP type= own
    ```

3. Uruchom usługę:

    ```console
    sc start DHCP
    ```

4. Jeśli nadal nie można uruchomić usługi, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).
2. Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania. Upewnij się, że dołączony dysk jest oznaczony jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3.  Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**). Następnie uruchom poniższy skrypt. Ten skrypt zakłada, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to **F**. Zamień literę odpowiednio do wartości w maszynie wirtualnej.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Odłącz dysk systemu operacyjnego i Utwórz ponownie maszynę wirtualną](./troubleshoot-recovery-disks-portal-windows.md). Następnie sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby rozwiązać problem.
