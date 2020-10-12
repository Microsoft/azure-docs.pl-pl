---
title: Usługi pulpitu zdalnego nie jest uruchamiane na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z Usługi pulpitu zdalnego po nawiązaniu połączenia z maszyną wirtualną | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 0b00785fed7708986885e9da9102e8f1b4fd4539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508886"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Usługi pulpitu zdalnego nie jest uruchamiane na maszynie wirtualnej platformy Azure

W tym artykule opisano sposób rozwiązywania problemów związanych z nawiązywaniem połączenia z maszyną wirtualną platformy Azure i Usługi pulpitu zdalnego, lub TermService, nie uruchamia się lub nie można uruchomić usługi.


## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną występują następujące scenariusze:

- Zrzut ekranu maszyny wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i oczekuje na poświadczenia.

    ![Zrzut ekranu przedstawiający stan maszyny wirtualnej](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Dzienniki zdarzeń w maszynie wirtualnej można wyświetlić zdalnie przy użyciu Podgląd zdarzeń. Zobaczysz, że Usługi pulpitu zdalnego, TermService, nie rozpoczyna się lub nie można uruchomić. Następujący dziennik jest przykładem:

    **Nazwa dziennika**: system </br>
    **Źródło**: Menedżer sterowania usługami </br>
    **Data**: 12/16/2017 11:19:36 am</br>
    **Identyfikator zdarzenia**: 7022</br>
    **Kategoria zadania**: brak</br>
    **Poziom**: błąd</br>
    **Słowa kluczowe**: klasyczne</br>
    **Użytkownik**: nie dotyczy</br>
    **Komputer**: VM.contoso.com</br>
    **Opis**: Usługa Usługi pulpitu zdalnego zawiesiła się podczas uruchamiania. 

    Aby wyszukać te błędy, można również użyć funkcji konsoli dostępu szeregowego: 

    ```console
   wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
    ```

## <a name="cause"></a>Przyczyna
 
Ten problem występuje, ponieważ Usługi pulpitu zdalnego nie jest uruchomiony na maszynie wirtualnej. Przyczyna może zależeć od następujących scenariuszy: 

- Usługa TermService jest **wyłączona**. 
- Usługa TermService uległa awarii lub nie odpowiada. 
- Nie można uruchomić platformy TermService z powodu nieprawidłowej konfiguracji.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj konsoli szeregowej. Lub w przeciwnym razie [napraw maszynę wirtualną w trybie offline](#repair-the-vm-offline) , dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny wirtualnej odzyskiwania.

### <a name="use-serial-console"></a>Korzystanie z konsoli szeregowej

1. Uzyskaj dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając pozycję **Obsługa & Rozwiązywanie problemów**  >  **konsola szeregowa**. Jeśli funkcja jest włączona na maszynie wirtualnej, można połączyć maszynę wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Wprowadź **polecenie cmd** w celu uruchomienia kanału i pobrania nazwy kanału.

3. Przejdź do kanału, na którym jest uruchomione wystąpienie CMD. W takim przypadku powinna to być kanał 1:

   ```
   ch -si 1
   ```

4. Wybierz pozycję **wprowadź** ponownie i Wprowadź prawidłową nazwę użytkownika i hasło, lokalny lub identyfikator domeny dla maszyny wirtualnej.

5. Zbadaj stan usługi TermService:

   ```
   sc query TermService
   ```

6. Jeśli stan usługi jest **zatrzymany**, spróbuj uruchomić usługę:

    ```
    sc start TermService
     ``` 

7. Wykonaj ponownie zapytanie dotyczące usługi, aby upewnić się, że usługa została uruchomiona pomyślnie:

   ```
   sc query TermService
   ```
8. Jeśli uruchomienie usługi nie powiedzie się, postępuj zgodnie z rozwiązaniem na podstawie otrzymanego błędu:

    |  Błąd |  Sugestia |
    |---|---|
    |5 — ODMOWA DOSTĘPU |Zobacz, [że Usługa TermService została zatrzymana z powodu błędu odmowy dostępu](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 — ERROR_SERVICE_REQUEST_TIMEOUT  |Zobacz [Usługa TermService jest wyłączona](#termservice-service-is-disabled).  |  
    |1058 — ERROR_SERVICE_DISABLED  |Zobacz [awarie usługi TermService lub zawiesza](#termservice-service-crashes-or-hangs)się.  |
    |1059 — ERROR_CIRCULAR_DEPENDENCY |[Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem.|
    |1067 — ERROR_PROCESS_ABORTED  |Zobacz [awarie usługi TermService lub zawiesza](#termservice-service-crashes-or-hangs)się.  |
    |1068 — ERROR_SERVICE_DEPENDENCY_FAIL|[Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem.|
    |1069 — ERROR_SERVICE_LOGON_FAILED  |Zobacz [Niepowodzenie usługi TermService z powodu niepowodzenia logowania](#termservice-service-fails-because-of-logon-failure) |
    |1070 — ERROR_SERVICE_START_HANG   | Zobacz [awarie usługi TermService lub zawiesza](#termservice-service-crashes-or-hangs)się. |
    |1077 — ERROR_SERVICE_NEVER_STARTED   | Zobacz [Usługa TermService jest wyłączona](#termservice-service-is-disabled).  |
    |1079 — ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem. |
    |1753   |[Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Usługa TermService została zatrzymana z powodu problemu z odmową dostępu

1. Połącz się z [konsolą szeregową](serial-console-windows.md) i Otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzie Monitor procesu, uruchamiając następujący skrypt:

   ```
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

4. Odtwórz problem, uruchamiając usługę, która zapewnia **odmowę dostępu**: 

   ```
   sc start TermService 
   ```

   Gdy to się nie powiedzie, Zakończ śledzenie monitora procesu:

   ```   
   procmon /Terminate 
   ```

5. Zbierz **c:\temp\ProcMonTrace.PML**pliku:

    1. [Dołącz dysk z danymi do maszyny wirtualnej](../windows/attach-managed-disk-portal.md
).
    2. Użyj konsoli szeregowej można skopiować plik na nowy dysk. Na przykład `copy C:\temp\ProcMonTrace.PML F:\`. W tym poleceniu F jest literą sterownika dołączonego dysku danych.
    3. Odłącz dysk danych i dołącz go do działającej maszyny wirtualnej z zainstalowanym monitorem procesu ubstakke.

6. Otwórz **ProcMonTrace. PML** przy użyciu monitorowania procesów działającej maszyny wirtualnej. Następnie filtrowanie według **wyniku jest odmowa dostępu**, jak pokazano na poniższym zrzucie ekranu:

    ![Filtruj według wyniku w monitorze procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Naprawianie kluczy rejestru, folderów lub plików, które znajdują się w danych wyjściowych. Zazwyczaj ten problem jest spowodowany tym, że konto logowania używane w usłudze nie ma uprawnienia ACL do uzyskiwania dostępu do tych obiektów. Aby uzyskać informacje na temat poprawnego uprawnienia ACL dla konta logowania, można zaewidencjonować odpowiednią maszynę wirtualną. 

#### <a name="termservice-service-is-disabled"></a>Usługa TermService jest wyłączona

1. Przywróć domyślną wartość uruchamiania usługi:

   ```
   sc config TermService start= demand 
   ```

2. Uruchom usługę:

   ```
   sc start TermService
   ```

3. Wykonaj zapytanie o jego stan ponownie, aby upewnić się, że usługa jest uruchomiona:

   ```
   sc query TermService 
   ```

4. Spróbuj połączyć się z maszyną wirtualną za pomocą Pulpit zdalny.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Usługa TermService kończy się niepowodzeniem z powodu niepowodzenia logowania

1. Ten problem występuje, jeśli konto uruchamiania tej usługi zostało zmienione. Zmieniono ją z powrotem na domyślną: 

    ```console
    sc config TermService obj= 'NT Authority\NetworkService'
    ```

2. Uruchom usługę:

    ```console
    sc start TermService
    ```

3. Spróbuj połączyć się z maszyną wirtualną za pomocą Pulpit zdalny.

#### <a name="termservice-service-crashes-or-hangs"></a>Awaria lub zawieszenie usługi TermService
1. Jeśli stan usługi jest zablokowany w trakcie **uruchamiania** lub **zatrzymywania**, spróbuj zatrzymać usługę: 

    ```console
    sc stop TermService
    ```

2. Wyodrębnij usługę do własnego kontenera "svchost":

    ```console
    sc config TermService type= own
    ```

3. Uruchom usługę:

    ```console
    sc start TermService
    ```

4. Jeśli uruchomienie usługi nadal kończy się niepowodzeniem, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).
2. Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania. Upewnij się, że dołączony dysk jest oznaczony jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3. Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**). Następnie uruchom poniższy skrypt. Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to **F**. Zastąp ją odpowiednią wartością na maszynie wirtualnej. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Odłącz dysk systemu operacyjnego i Utwórz ponownie maszynę wirtualną](./troubleshoot-recovery-disks-portal-windows.md). Następnie sprawdź, czy problem został rozwiązany.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby rozwiązać problem.
