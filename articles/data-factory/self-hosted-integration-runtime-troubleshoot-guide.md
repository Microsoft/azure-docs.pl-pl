---
title: Rozwiązywanie problemów z samodzielnym współzaczajnym czasem pracy integracji w usłudze Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z samodzielnym hostowaniem środowiska wykonawczego integracji w usłudze Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414894"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Rozwiązywanie problemów z samodzielnym współzaczajnym środowiskom działania integracji

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano typowe metody rozwiązywania problemów dla środowiska wykonawczego integracji hostowanego samodzielnie w usłudze Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Common errors and resolutions (Typowe błędy i rozwiązania)

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Komunikat o błędzie: Środowisko wykonawcze integracji hostowanego samodzielnie nie może połączyć się z usługą w chmurze

![Problem z samodzielnym połączeniem podczerwony](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna 

Środowisko uruchomieniowe integracji hostowanego samodzielnie nie może połączyć się z usługą data factory (zaplecze). Ten problem jest zazwyczaj spowodowany przez ustawienia sieciowe w zaporze.

#### <a name="resolution"></a>Rozwiązanie

1. Sprawdź, czy usługa środowiska wykonawczego integracji jest uruchomiona.
    
   ![Stan uruchomienia usługi podczerwony](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Jeśli usługa jest uruchomiona, przejdź do kroku 3.

1. Jeśli w środowisku uruchomieniowym integracji hostowanego samodzielnie (które jest ustawieniem domyślnym), nie ma skonfigurowanych serwerów proxy, uruchom następujące polecenie Programu PowerShell na komputerze, na którym jest zainstalowane środowisko uruchomieniowe integracji hostowanego samodzielnie:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adres URL usługi może się różnić w zależności od lokalizacji fabryki danych. Adres URL usługi można znaleźć w obszarze Środowiska**wykonawcze** >  > **integracji**połączeń **interfejsu użytkownika usługi ADF** > Edit**Self-hosted IR** > **Nodes** > **View Service URL .**
            
    Poniżej przedstawiono oczekiwaną odpowiedź:
            
    ![Odpowiedź polecenia programu PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Jeśli nie otrzymasz oczekiwanej odpowiedzi, użyj jednej z następujących metod, stosownie do sytuacji:
            
    * Jeśli zostanie wyświetlony komunikat "Nie można rozpoznać nazwy zdalnej", występuje problem z systemem nazw domen (DNS). Skontaktuj się z zespołem sieciowym, aby rozwiązać ten problem.
    * Jeśli zostanie wyświetlony komunikat "Ssl/tls cert is not trusted", sprawdź, czy certyfikat jest https://wu2.frontend.clouddatahub.net/ zaufany na komputerze, a następnie zainstaluj certyfikat publiczny przy użyciu Menedżera certyfikatów. Ta akcja powinna złagodzić problem.
    * Przejdź do przeglądarki zdarzeń **systemu Windows** > **(dzienniki)** > Środowisko**uruchomieniowe aplikacji** i usług dzienników i sprawdź, czy nie ma**błędów** > spowodowanych przez system DNS, regułę zapory lub ustawienia sieci firmy. (Jeśli znajdziesz taką awarię, na przymusowo zamknij połączenie). Ponieważ każda firma ma dostosowane ustawienia sieciowe, skontaktuj się z zespołem sieciowym, aby rozwiązać te problemy.

1. Jeśli "serwer proxy" został skonfigurowany w czasie wykonywania integracji hostowanego samodzielnie, sprawdź, czy serwer proxy może uzyskać dostęp do punktu końcowego usługi. Aby uzyskać przykładowe polecenie, zobacz [Program PowerShell, żądania sieci web i serwery proxy](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

Poniżej przedstawiono oczekiwaną odpowiedź:
            
![Odpowiedź polecenia programu Powershell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Uwagi dotyczące serwera proxy:
> *    Sprawdź, czy serwer proxy musi zostać umieszczony na liście Bezpieczni adresaci. Jeśli tak, upewnij się, że [te domeny](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) znajdują się na liście Bezpieczni adresaci.
> *    Sprawdź, czy certyfikat TLS/SSL "wu2.frontend.clouddatahub.net/" jest zaufany na serwerze proxy.
> *    Jeśli używasz uwierzytelniania usługi Active Directory na serwerze proxy, zmień konto usługi na konto użytkownika, które może uzyskać dostęp do serwera proxy jako "Usługa środowiska uruchomieniowego integracji".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Komunikat o błędzie: Węzeł środowiska uruchomieniowego integracji hostowanej/ logiczny moduł SHIR jest w stanie Nieaktywne/ "Uruchomiony (ograniczony)"

#### <a name="cause"></a>Przyczyna 

Własny hostowany zintegrowany węzeł środowiska uruchomieniowego może mieć stan **nieaktywny,** jak pokazano na poniższym zrzucie ekranu:

![Nieaktywny, samodzielnie hostowany węzeł podczerwony](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

To zachowanie występuje, gdy węzły nie mogą komunikować się ze sobą.

#### <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do maszyny Wirtualnej hostowanej przez węzeł. W obszarze Środowisko**uruchomieniowe integracji** **aplikacji i usług dzienniki** > otwórz Podgląd zdarzeń i przefiltruj wszystkie dzienniki błędów.

1. Sprawdź, czy dziennik błędów zawiera następujący błąd: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
