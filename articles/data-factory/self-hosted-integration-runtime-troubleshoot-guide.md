---
title: Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z nieobsługiwanym środowiskiem Integration Runtime w Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 94e214c55a0109beb85cd08ce87303e5bd0f8016
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835431"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Common errors and resolutions (Typowe błędy i rozwiązania)

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Komunikat o błędzie: własne środowisko Integration Runtime nie może nawiązać połączenia z usługą w chmurze

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

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Komunikat o błędzie: własny Host Integration Runtime Node/Logical SHIR jest w stanie nieaktywny/"uruchomiony (ograniczony)"

#### <a name="cause"></a>Przyczyna 

Samoobsługowy, zintegrowany węzeł środowiska uruchomieniowego może mieć **nieaktywny** stan, jak pokazano na poniższym zrzucie ekranu:

![Nieaktywny samoobsługowy węzeł IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

To zachowanie występuje, gdy węzły nie mogą komunikować się ze sobą.

#### <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do maszyny wirtualnej hostowanej na węźle. W obszarze **Dzienniki aplikacji i usług**  >  **Integration Runtime**Otwórz Podgląd zdarzeń i przefiltruj wszystkie dzienniki błędów.

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
*  [Microsoft Q&A question page](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
