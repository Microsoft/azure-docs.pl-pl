---
title: Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z nieobsługiwanym środowiskiem Integration Runtime w Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315146"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Common errors and resolutions (Typowe błędy i rozwiązania)

### <a name="error-message"></a>Komunikat o błędzie: 

`Self-hosted integration runtime can't connect to cloud service`

![Problem z nieobsługiwanym połączeniem IR](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna 

Własne środowisko Integration Runtime nie może nawiązać połączenia z usługą Data Factory (zaplecza). Ten problem jest zwykle spowodowany przez ustawienia sieci w zaporze.

#### <a name="resolution"></a>Rozwiązanie

1. Sprawdź, czy usługa Integration Runtime jest uruchomiona.
    
   ![Stan działania usługi IR samoobsługowego](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Jeśli usługa jest uruchomiona, przejdź do kroku 3.

1. Jeśli nie ma skonfigurowanego serwera proxy w ramach własnego środowiska Integration Runtime (jest to ustawienie domyślne), uruchom następujące polecenie programu PowerShell na komputerze, na którym zainstalowano własne środowisko Integration Runtime:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adres URL usługi może się różnić w zależności od lokalizacji Data Factory. Adres URL usługi można znaleźć w obszarze **połączenia interfejsu użytkownika funkcji ADF**  >  **Connections**  >  **środowisko Integration Runtimes**  >  **Edycja samohostowanych**  >  **węzłów**IR  >  **przeglądanie adresów URL usług**.
            
    Oczekiwana jest następująca odpowiedź:
            
    ![Odpowiedź polecenia programu PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Jeśli nie otrzymasz oczekiwanej odpowiedzi, użyj jednej z następujących metod odpowiednio do swojej sytuacji:
            
    * Jeśli zostanie wyświetlony komunikat "nie można rozpoznać nazwy zdalnej", występuje problem z systemem nazw domen (DNS). Aby rozwiązać ten problem, skontaktuj się z zespołem sieci.
    * Jeśli zostanie wyświetlony komunikat "certyfikat SSL/TLS nie jest zaufany", sprawdź, czy certyfikat dla programu https://wu2.frontend.clouddatahub.net/ jest zaufany na komputerze, a następnie Zainstaluj certyfikat publiczny za pomocą Menedżera certyfikatów. Ta akcja powinna wyeliminować problem.
    * Przejdź do pozycji Podgląd zdarzeń **systemu Windows**  >  **(dzienniki)**  >  **Dzienniki aplikacji i usług**  >  **Integration Runtime** i sprawdź, czy wystąpił błąd spowodowany przez system DNS, regułę zapory lub ustawienia sieci firmowej. (W przypadku wystąpienia takiego błędu należy wymusić zamknięcie połączenia). Ponieważ każda firma ma dostosowane ustawienia sieci, należy skontaktować się z zespołem sieci, aby rozwiązać te problemy.

1. Jeśli "serwer proxy" został skonfigurowany w ramach własnego środowiska Integration Runtime, sprawdź, czy serwer proxy ma dostęp do punktu końcowego usługi. Przykładowe polecenie można znaleźć w temacie [PowerShell, żądania sieci Web i serwery proxy](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Oczekiwana jest następująca odpowiedź:
            
![Odpowiedź polecenia programu PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Zagadnienia dotyczące serwerów proxy:
> *    Sprawdź, czy serwer proxy należy umieścić na liście bezpiecznych adresatów. Jeśli tak, upewnij się, że [te domeny](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) znajdują się na liście bezpiecznych adresatów.
> *    Sprawdź, czy certyfikat TLS/SSL "wu2.frontend.clouddatahub.net/" jest zaufany na serwerze proxy.
> *    Jeśli używasz uwierzytelniania Active Directory na serwerze proxy, Zmień konto usługi na konto użytkownika, które ma dostęp do serwera proxy jako "usługa Integration Runtime".

### <a name="error-message"></a>Komunikat o błędzie: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Przyczyna 

Samoobsługowy, zintegrowany węzeł środowiska uruchomieniowego może mieć **nieaktywny** stan, jak pokazano na poniższym zrzucie ekranu:

![Nieaktywny samoobsługowy węzeł IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

To zachowanie występuje, gdy węzły nie mogą komunikować się ze sobą.

#### <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do maszyny wirtualnej hostowanej na węźle. W obszarze **Dzienniki aplikacji i usług**  >  **Integration Runtime**Otwórz Podgląd zdarzeń i przefiltruj wszystkie dzienniki błędów.

1. Sprawdź, czy dziennik błędów zawiera następujący błąd: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Jeśli ten błąd jest wyświetlany, uruchom następujące polecenie w wierszu polecenia: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Jeśli wystąpi następujący błąd, skontaktuj się z działem IT w celu uzyskania pomocy w rozwiązaniu tego problemu. Po pomyślnym zalogowaniu się w programie Telnet skontaktuj się z firmą pomoc techniczna firmy Microsoft, jeśli nadal występują problemy ze stanem węzła środowiska uruchomieniowego integracyjne.
        
   ![Błąd wiersza polecenia](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Sprawdź, czy dziennik błędów zawiera następujące elementy:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Aby rozwiązać ten problem, wypróbuj jedną lub obie następujące metody:
    - Umieść wszystkie węzły w tej samej domenie.
    - Dodaj adres IP do mapowania hosta we wszystkich plikach hosta hostowanej maszyny wirtualnej.


## <a name="troubleshoot-connectivity-issue"></a>Rozwiązywanie problemów z łącznością

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Rozwiązywanie problemów z łącznością między własnym hostowanym środowiskiem IR i Data Factory lub własnym środowiskiem IR i źródłem danych/ujściam

Aby rozwiązać problem z łącznością sieciową, należy wiedzieć, jak [zebrać ślad sieci](#how-to-collect-netmon-trace), zrozumieć, jak z niego korzystać i [analizować ślad netmon](#how-to-analyze-netmon-trace) przed zastosowaniem narzędzi netmon w rzeczywistych przypadkach z własnego środowiska IR.

Czasami w przypadku rozwiązywania problemów z łącznością, takich jak poniżej, między obsługą środowiska IR i Data Factory: 

![Żądanie HTTP nie powiodło się](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Lub jeden między środowiskiem samoobsługowym środowiska IR a źródłem danych/ujściam, wystąpią następujące błędy:

**Komunikat o błędzie:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Komunikat o błędzie:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Rozwiązanie:** W przypadku wystąpienia powyższych problemów zapoznaj się z poniższymi instrukcjami, aby uzyskać dalsze informacje:

Wykonaj śledzenie netmon i Przeanalizuj je ponownie.
- Po pierwsze, można ustawić filtr, aby wyświetlić wszystkie z serwera po stronie klienta. W poniższym przykładzie można zobaczyć, że po stronie serwera jest Data Factory Server.

    ![Serwer fabryki danych](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Po otrzymaniu pakietu resetowania można znaleźć konwersację według następującego protokołu TCP.

    ![Znajdowanie konwersacji](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Następnie można uzyskać konwersję między klientem a Data Factory Server poniżej, usuwając filtr.

    ![Pobierz konwersację](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Na podstawie zebranych śladów netmon możemy stwierdzić, że wartość czasu wygaśnięcia (TimeToLive) wynosi 64. Zgodnie z **domyślnymi wartościami limitu czasu wygaśnięcia i przeskoków** wymienionymi w [tym artykule](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (jak wyodrębnione poniżej) można zobaczyć, że jest to system Linux, który resetuje pakiet i powoduje odłączenie.

    Domyślne wartości czasu wygaśnięcia i przeskoków różnią się między różnymi systemami operacyjnymi, poniżej przedstawiono ustawienia domyślne dla kilku:
    - Jądro systemu Linux 2,4 (około 2001): 255 dla protokołów TCP, UDP i ICMP
    - Jądro systemu Linux 4,10 (2015): 64 dla protokołów TCP, UDP i ICMP
    - Windows XP (2001): 128 dla protokołów TCP, UDP i ICMP
    - Windows 10 (2015): 128 dla protokołów TCP, UDP i ICMP
    - Windows Server 2008:128 dla protokołów TCP, UDP i ICMP
    - Windows Server 2019 (2018): 128 dla protokołów TCP, UDP i ICMP
    - macOS (2001): 64 dla protokołów TCP, UDP i ICMP

    ![CZAS WYGAŚNIĘCIA 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Jednak jest on wyświetlany jako 61 zamiast 64 w powyższym przykładzie, ponieważ to, kiedy pakiet sieciowy dociera do miejsca docelowego, musi przechodzić przez różne przeskoki, takie jak routery/urządzenia sieciowe. Liczba routerów/urządzeń sieciowych zostanie potrącona w końcowym czasie TTL.
    W takim przypadku można zobaczyć, że Resetowanie może być wysyłane z systemu Linux z wartością TTL 64.

- Musimy sprawdzić czwarty przeskok z własnego środowiska IR, aby upewnić się, że może to być urządzenie resetowania.
 
    *Pakiet sieciowy z systemu Linux system A z parametrem TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 własne środowisko IR*

- W idealnej sytuacji czas wygaśnięcia będzie wynosił 128, co oznacza, że system Windows działa w naszym Data Factory. Jak pokazano w poniższym przykładzie, *128 – 107 = 21 przeskoków*, co oznacza, że 21 przeskoków dla pakietu zostało wysłanych z Data Factory do samodzielnego środowiska IR podczas uzgadniania TCP 3.
 
    ![CZAS WYGAŚNIĘCIA 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    W związku z tym należy skontaktować się z zespołem sieci, aby sprawdzić, co czwarty przeskok pochodzi z samodzielnego środowiska IR. Jeśli jest to Zapora jako system Linux, sprawdź wszystkie dzienniki, dlaczego urządzenie resetuje pakiet po uzgodnieniu protokołu TCP 3. Jednakże jeśli nie masz pewności, gdzie należy przeprowadzić badanie, spróbuj uzyskać ślad netmon z własnego środowiska IR i zapory w czasie, aby ustalić, które urządzenie może zresetować ten pakiet i spowodować odłączenie. W takim przypadku należy również skontaktować się z zespołem sieci, aby przejść do przodu.

### <a name="how-to-collect-netmon-trace"></a>Jak zbierać ślady netmon

1.  Pobierz narzędzia netmon z [tej witryny sieci Web](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)i zainstaluj je na komputerze serwera (dowolnego serwera z problemem) i klienta (takiego jak samoobsługowe środowisko IR).

2.  Utwórz folder, na przykład w następującej ścieżce: *D:\netmon*. Upewnij się, że ma wystarczająco dużo miejsca, aby zapisać dziennik.

3.  Przechwyć informacje o adresie IP i porcie. 
    1. Uruchom wiersz polecenia.
    2. Wybierz pozycję Uruchom jako administrator i uruchom następujące polecenie:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Przechwyć śledzenie netmon (pakiet sieciowy).
    1. Uruchom wiersz polecenia.
    2. Wybierz pozycję Uruchom jako administrator i uruchom następujące polecenie:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Aby przechwycić stronę sieciową, można użyć trzech różnych poleceń:
        - Opcja A: RoundRobin File Command (spowoduje to przechwycenie tylko jednego pliku i zastąpienie starych dzienników).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Opcja B: plik łańcucha polecenie (spowoduje to utworzenie nowego pliku w przypadku osiągnięcia 200 MB).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Opcja C: zaplanowana plik polecenia.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Naciśnij **klawisze CTRL + C** , aby zatrzymać przechwytywanie śladu netmon.
 
> [!NOTE]
> Jeśli śledzenie netmon można zbierać na komputerze klienckim, należy uzyskać adres IP serwera, aby ułatwić analizowanie śledzenia.

### <a name="how-to-analyze-netmon-trace"></a>Jak analizować śledzenie netmon

Gdy próbujesz wykonać polecenie telnet **8.8.8.8 888** z powyżej zebranych śladów netmon, zobaczysz następujący ślad:

![Śledzenie netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Śledzenie netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Oznacza to, że nie można nawiązać połączenia TCP ze stroną serwera **8.8.8.8** w oparciu o port **888**, dlatego w tym miejscu widoczne są dwa **SynReTransmit** dodatkowe pakiety. Ze względu na to, że źródło **host2** nie może nawiązać połączenia z **8.8.8.8em** w pierwszym pakiecie, będzie nadal mieć połączenie.

> [!TIP]
> - Możesz kliknąć pozycję Filtr **obciążenia**filtr  ->  **Standardowy**  ->  **adresy**  ->  **adresy IPv4**.
> - Wprowadź wartość **IPv4. Address = = 8.8.8.8** jako filtr, a następnie kliknij przycisk **Zastosuj**. Następnie zostanie wyświetlona tylko komunikacja z komputera lokalnego do lokalizacji docelowej **8.8.8.8**.

![Filtruj adresy 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Filtruj adresy 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Poniższy przykład pokazuje, jak wygląda dobry scenariusz. 

- Jeśli program Telnet **8.8.8.8 53** działa prawidłowo bez jakiegokolwiek problemu, można zobaczyć UZGADNIANIE protokołu TCP 3, a następnie sesja kończy się z UZGADNIANIEM protokołu TCP 4.

    ![przykład dobrego scenariusza 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![dobry przykład scenariusza 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- W oparciu o powyższe uzgadnianie TCP 3 można zobaczyć Poniższy przepływ pracy:

    ![Przepływ pracy uzgadniania TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Uzgadnianie protokołu TCP 4 do zakończenia sesji i przepływ pracy będzie pokazane w następujący sposób:

    ![Uzgadnianie protokołu TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Przepływ pracy uzgadniania TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik wydajności dotyczący mapowania przepływów danych](concepts-data-flow-performance.md)
