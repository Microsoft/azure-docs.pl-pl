---
title: Rozwiązywanie problemów z kondycją zaplecza na platformie Azure Application Gateway
description: Opisuje sposób rozwiązywania problemów z kondycją zaplecza dla usługi Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 531a7fd8547130b4897f3dad0900e1c27fb7fe9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132045"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Rozwiązywanie problemów z kondycją zaplecza w Application Gateway
==================================================

<a name="overview"></a>Omówienie
--------

Domyślnie usługa Azure Application Gateway sonduje serwery zaplecza, aby sprawdzić ich stan kondycji i sprawdzić, czy są one gotowe do obsłużenia żądań. Użytkownicy mogą również tworzyć niestandardowe sondy, aby wspominać o nazwie hosta, ścieżce do sondowania oraz kodów stanu, które mają być akceptowane w dobrej kondycji. W każdym przypadku, jeśli serwer zaplecza nie odpowie pomyślnie, Application Gateway oznacza, że serwer jest w złej kondycji i przestanie przekazywać przekazywanie żądań do serwera. Po pomyślnym rozpoczęciu odpowiedzi serwer Application Gateway wznawia przekazywanie żądań.

### <a name="how-to-check-backend-health"></a>Sprawdzanie kondycji zaplecza

Aby sprawdzić kondycję puli zaplecza, można użyć strony **kondycja zaplecza** na Azure Portal. Można też użyć [interfejsu API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), interfejsu [wiersza polecenia](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)lub protokołu REST.

Stan pobrany przez dowolną z tych metod może być jednym z następujących:

- Dobra kondycja

- Nieprawidłowy

- Nieznane

Jeśli kondycja zaplecza serwera jest w dobrej kondycji, oznacza to, że Application Gateway przekaże żądania do tego serwera. Jeśli jednak kondycja zaplecza dla wszystkich serwerów w puli zaplecza jest zła lub nieznana, podczas próby uzyskania dostępu do aplikacji mogą wystąpić problemy. W tym artykule opisano objawy, przyczynę i rozwiązanie dla każdego z wymienionych błędów.

<a name="backend-health-status-unhealthy"></a>Stan kondycji zaplecza: zła kondycja
-------------------------------

Jeśli kondycja zaplecza jest zła, widok portalu będzie wyglądać następująco:

![Kondycja zaplecza Application Gateway — zła kondycja](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Lub jeśli korzystasz Azure PowerShell z kwerendy interfejsu API REST platformy Azure, możesz uzyskać odpowiedź podobną do następującej:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Po otrzymaniu stanu serwera wewnętrznej bazy danych w złej kondycji dla wszystkich serwerów w puli zaplecza żądania nie są przekazywane do serwerów, a Application Gateway zwraca błąd "502 zła brama" do żądającego klienta. Aby rozwiązać ten problem, Sprawdź kolumnę **szczegóły** na karcie **kondycja zaplecza** .

Komunikat wyświetlany w kolumnie **szczegóły** zawiera bardziej szczegółowe informacje o problemie i na podstawie tych informacji można rozpocząć rozwiązywanie problemu.

> [!NOTE]
> Domyślne żądanie sondowania jest wysyłane w formacie \<protocol\> ://127.0.0.1: \<port\> /. Na przykład w http://127.0.0.1:80 przypadku sondy http na porcie 80. Tylko kody stanu HTTP od 200 do 399 są uznawane za zdrowe. Protokół i port docelowy są dziedziczone z ustawień HTTP. Jeśli chcesz, aby Application Gateway sondować różne protokoły, nazwy hostów lub ścieżki i rozpoznawać inny kod stanu jako dobry, skonfiguruj sondę niestandardową i skojarz ją z ustawieniami protokołu HTTP.

<a name="error-messages"></a>Komunikaty o błędach
------------------------
#### <a name="backend-server-timeout"></a>Limit czasu serwera wewnętrznej bazy danych

**Komunikat:** Czas reakcji zaplecza na \' sondę kondycji bramy aplikacji jest większy niż próg limitu czasu w ustawieniu sondy.

**Przyczyna:** Gdy Application Gateway wysyła żądanie sondy HTTP (S) do serwera wewnętrznej bazy danych, czeka na odpowiedź z serwera wewnętrznej bazy danych przez skonfigurowany okres. Jeśli serwer wewnętrznej bazy danych nie odpowiada w skonfigurowanym okresie (wartość limitu czasu), zostanie oznaczony jako nieprawidłowy do momentu, aż zacznie reagować w skonfigurowanym limicie czasu.

**Rozwiązanie:** Sprawdź, dlaczego serwer zaplecza lub aplikacja nie odpowiada przed upływem skonfigurowanego limitu czasu, a także sprawdź zależności aplikacji. Na przykład sprawdź, czy baza danych ma jakiekolwiek problemy, które mogą spowodować opóźnienie w odpowiedzi. Jeśli masz świadomość zachowania aplikacji i powinna ona reagować tylko po wartości limitu czasu, zwiększ wartość limitu czasu z ustawień sondy niestandardowej. Aby zmienić wartość limitu czasu, trzeba mieć niestandardową sondę. Informacje o sposobie konfigurowania sondy niestandardowej [znajdują się na stronie dokumentacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Aby zwiększyć wartość limitu czasu, wykonaj następujące kroki:

1.  Bezpośredni dostęp do serwera wewnętrznej bazy danych i sprawdź czas odpowiedzi serwera na Tę stronę. Możesz użyć dowolnego narzędzia, aby uzyskać dostęp do serwera wewnętrznej bazy danych, w tym przeglądarki przy użyciu narzędzi deweloperskich.

1.  Po pobraniu czasu na odpowiedź aplikacji wybierz kartę **sondy kondycji** , a następnie wybierz sondę, która jest skojarzona z ustawieniami protokołu HTTP.

1.  Wprowadź wartość limitu czasu, która jest większa niż czas odpowiedzi aplikacji (w sekundach).

1.  Zapisz ustawienia sondy niestandardowej i sprawdź, czy kondycja zaplecza jest teraz wyświetlana jako dobra kondycja.

#### <a name="dns-resolution-error"></a>Błąd rozpoznawania nazw DNS

**Komunikat:** Application Gateway nie może utworzyć sondy dla tego zaplecza. Dzieje się tak zazwyczaj wtedy, gdy nazwa FQDN zaplecza nie została wprowadzona prawidłowo. 

**Przyczyna:** Jeśli pula zaplecza jest typu adresu IP/FQDN lub App Service, Application Gateway jest rozpoznawana jako adres IP nazwy FQDN wprowadzonej za pośrednictwem systemu nazw domen (DNS) (niestandardowa lub Azure default) i próbuje nawiązać połączenie z serwerem na porcie TCP wymienionym w ustawieniach protokołu HTTP. Ale jeśli ten komunikat jest wyświetlany, sugeruje, że Application Gateway nie może pomyślnie rozpoznać adresu IP wprowadzonej nazwy FQDN.

**Rozwiązanie:**

1.  Sprawdź, czy nazwa FQDN wprowadzona w puli zaplecza jest poprawna i czy jest domeną publiczną, a następnie spróbuj rozwiązać ją z komputera lokalnego.

1.  W przypadku rozpoznania adresu IP może wystąpić problem z konfiguracją DNS w sieci wirtualnej.

1.  Sprawdź, czy sieć wirtualna jest skonfigurowana za pomocą niestandardowego serwera DNS. Jeśli tak jest, sprawdź serwer DNS, dlaczego nie można go rozpoznać jako adresu IP podanej nazwy FQDN.

1.  Jeśli używasz domyślnej usługi DNS platformy Azure, skontaktuj się z rejestratorem nazw domen, aby dowiedzieć się, czy zakończono poprawne mapowanie rekordów lub rekordu CNAME.

1.  Jeśli domena jest prywatna lub wewnętrzna, spróbuj ją rozpoznać z poziomu maszyny wirtualnej w tej samej sieci wirtualnej. Jeśli możesz rozwiązać ten problem, ponownie uruchom Application Gateway i sprawdź ponownie. Aby ponownie uruchomić Application Gateway, należy [zatrzymać](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) i [uruchomić](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) program przy użyciu poleceń programu PowerShell opisanych w tych połączonych zasobach.

#### <a name="tcp-connect-error"></a>Błąd połączenia TCP

**Komunikat:** Application Gateway nie może nawiązać połączenia z zapleczem.
Sprawdź, czy zaplecze reaguje na port używany dla sondy.
Sprawdź również, czy jakakolwiek sieciowej grupy zabezpieczeń/UDR/zapora blokuje dostęp do adresu IP i portu tego zaplecza

**Przyczyna:** Po fazie rozpoznawania nazw DNS Application Gateway próbuje nawiązać połączenie z serwerem zaplecza na porcie TCP, który jest skonfigurowany w ustawieniach protokołu HTTP. Jeśli Application Gateway nie może ustanowić sesji TCP na określonym porcie, sonda zostanie oznaczona jako zła w złej kondycji.

**Rozwiązanie:** Jeśli ten błąd wystąpi, wykonaj następujące kroki:

1.  Sprawdź, czy możesz nawiązać połączenie z serwerem zaplecza na porcie wymienionym w ustawieniach protokołu HTTP przy użyciu przeglądarki lub programu PowerShell. Na przykład uruchom następujące polecenie: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Jeśli wymieniony port nie jest pożądanym portem, wprowadź poprawny numer portu dla Application Gateway, aby nawiązać połączenie z serwerem wewnętrznej bazy danych

1.  Jeśli nie można nawiązać połączenia z portem na komputerze lokalnym, a następnie:

    a.  Sprawdź ustawienia sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) karty sieciowej i podsieci serwera wewnętrznej bazy danych oraz czy dozwolone są połączenia przychodzące ze skonfigurowanym portem. Jeśli nie, Utwórz nową regułę, aby zezwolić na połączenia. Aby dowiedzieć się, jak utworzyć reguły sieciowej grupy zabezpieczeń, [Zobacz stronę z dokumentacją](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Sprawdź, czy ustawienia sieciowej grupy zabezpieczeń podsieci Application Gateway zezwalają na ruch wychodzący publicznego i prywatnego, aby można było nawiązać połączenie. Aby dowiedzieć się więcej na temat tworzenia reguł sieciowej grupy zabezpieczeń, zapoznaj się ze stroną dokumentu dostarczoną w kroku 3a.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Sprawdź ustawienia tras zdefiniowanych przez użytkownika (UDR) Application Gateway i podsieci serwera wewnętrznej bazy danych dla wszelkich anomalii routingu. Upewnij się, że UDR nie kieruje ruchu z podsieci zaplecza. Na przykład sprawdź trasy do wirtualnych urządzeń sieciowych lub trasy domyślne anonsowane do podsieci Application Gateway za pośrednictwem usługi Azure ExpressRoute i/lub sieci VPN.

    d.  Aby sprawdzić obowiązujące trasy i reguły dla karty sieciowej, można użyć następujących poleceń programu PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Jeśli nie masz żadnych problemów z usługą sieciowej grupy zabezpieczeń lub UDR, sprawdź serwer zaplecza pod kątem problemów związanych z aplikacjami, które uniemożliwiają klientom nawiązywanie połączeń TCP na skonfigurowanych portach. Kilka kwestii do sprawdzenia:

    a.  Otwórz wiersz polecenia (Win + R- \> cmd), wprowadź `netstat` i wybierz ENTER.

    b.  Sprawdź, czy serwer nasłuchuje na skonfigurowanym porcie. Na przykład:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Jeśli nie nasłuchuje na skonfigurowanym porcie, sprawdź ustawienia serwera sieci Web. Na przykład: powiązania witryny w usługach IIS, bloku serwera w NGINX i hosta wirtualnego w Apache.

    d.  Sprawdź ustawienia zapory systemu operacyjnego, aby upewnić się, że ruch przychodzący do portu jest dozwolony.

#### <a name="http-status-code-mismatch"></a>Niezgodność kodu stanu HTTP

**Komunikat:** Kod stanu \' odpowiedzi HTTP zaplecza nie jest zgodny z ustawieniem sondy. Oczekiwano: {HTTPStatusCode0} odebrano: {HTTPStatusCode1}.

**Przyczyna:** Po nawiązaniu połączenia TCP i zakończeniu uzgadniania TLS (Jeśli protokół TLS jest włączony), Application Gateway wyśle sondę jako żądanie HTTP GET do serwera wewnętrznej bazy danych. Jak opisano wcześniej, domyślną sondą będzie \<protocol\> ://127.0.0.1: \<port\> /, a w przypadku próg 200 do 399 w dobrej kondycji są brane pod uwagę kody stanu odpowiedzi. Jeśli serwer zwróci inny kod stanu, zostanie oznaczony jako nieprawidłowy dla tej wiadomości.

**Rozwiązanie:** W zależności od kodu odpowiedzi serwera wewnętrznej bazy danych można wykonać następujące czynności. Poniżej przedstawiono kilka typowych kodów stanu:

| **Błąd** | **Akcje** |
| --- | --- |
| Niezgodność kodu stanu sondy: odebrane 401 | Sprawdź, czy serwer wewnętrznej bazy danych wymaga uwierzytelniania. Sondy Application Gateway nie mogą przekazywać poświadczeń w celu uwierzytelnienia. Zezwalaj na użycie \" protokołu HTTP 401 \" w kodzie stanu sondy lub sondy do ścieżki, w której serwer nie wymaga uwierzytelniania. | |
| Niezgodność kodu stanu sondy: odebrane 403 | Dostęp zabroniony. Sprawdź, czy dostęp do ścieżki jest dozwolony na serwerze wewnętrznej bazy danych. | |
| Niezgodność kodu stanu sondy: odebrane 404 | Nie znaleziono strony. Sprawdź, czy ścieżka nazwy hosta jest dostępna na serwerze wewnętrznej bazy danych. Zmień nazwę hosta lub parametr ścieżki na dostępną wartość. | |
| Niezgodność kodu stanu sondy: odebrane 405 | Dla Application Gateway jest używana metoda HTTP GET. Sprawdź, czy serwer zezwala na tę metodę. | |
| Niezgodność kodu stanu sondy: odebrane 500 | Wewnętrzny błąd serwera. Sprawdź kondycję serwera zaplecza i sprawdź, czy usługi są uruchomione. | |
| Niezgodność kodu stanu sondy: odebrane 503 | Usługa niedostępna. Sprawdź kondycję serwera zaplecza i sprawdź, czy usługi są uruchomione. | |

Lub, jeśli uważasz, że odpowiedź jest uzasadniona i chcesz, aby Application Gateway akceptowała inne kody stanu w dobrej kondycji, możesz utworzyć niestandardową sondę. Takie podejście jest przydatne w sytuacjach, gdy witryna sieci Web zaplecza wymaga uwierzytelniania. Ponieważ żądania sondowania nie mają żadnych poświadczeń użytkownika, nie powiodą się, a serwer wewnętrznej bazy danych zwróci kod stanu HTTP 401.

Aby utworzyć niestandardową sondę, wykonaj następujące [kroki](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Niezgodność treści odpowiedzi HTTP

**Komunikat:** Treść odpowiedzi HTTP zaplecza jest \' niezgodna z ustawieniem sondy. Odebrana treść odpowiedzi nie zawiera ciągu {String}.

**Przyczyna:** Podczas tworzenia niestandardowej sondy można oznaczyć serwer zaplecza jako zdrowy przez dopasowanie ciągu z treści odpowiedzi. Można na przykład skonfigurować Application Gateway tak, aby akceptował "nieautoryzowane" jako ciąg do dopasowania. Jeśli odpowiedź serwera wewnętrznej bazy danych dla żądania sondowania zawiera ciąg **nieautoryzowany**, zostanie oznaczony jako w dobrej kondycji. W przeciwnym razie zostanie ona oznaczona jako w złej kondycji za pomocą tej wiadomości.

**Rozwiązanie:** Aby rozwiązać ten problem, wykonaj następujące kroki:

1.  Uzyskaj dostęp do serwera wewnętrznej bazy danych lokalnie lub z komputera klienckiego w ścieżce sondy i sprawdź treść odpowiedzi.

1.  Sprawdź, czy treść odpowiedzi w konfiguracji sondy niestandardowej Application Gateway jest zgodna z konfiguracją.

1.  Jeśli nie są zgodne, Zmień konfigurację sondy tak, aby stała się wartością ciągu do zaakceptowania.

Dowiedz się więcej na temat [dopasowywania sond Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

>[!NOTE]
> W przypadku wszystkich komunikatów o błędach związanych z protokołem TLS aby dowiedzieć się więcej na temat zachowania SNI i różnic między jednostką SKU V1 i v2, należy sprawdzić stronę [Przegląd protokołu TLS](ssl-overview.md) .


#### <a name="backend-server-certificate-invalid-ca"></a>Nieprawidłowy urząd certyfikacji dla certyfikatu serwera wewnętrznej bazy danych

**Komunikat:** Certyfikat serwera używany przez zaplecze nie jest podpisany przez dobrze znany urząd certyfikacji (CA). Zezwól na zaplecze na Application Gateway, przekazując certyfikat główny certyfikatu serwera używanego przez zaplecze.

**Przyczyna:** Kompleksowy protokół SSL z Application Gateway v2 wymaga zweryfikowania certyfikatu serwera wewnętrznej bazy danych w celu uznania kondycji serwera.
Aby certyfikat TLS/SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi być wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji (na przykład jeśli został użyty certyfikat z podpisem własnym), użytkownicy powinni przekazać certyfikat wystawcy do Application Gateway.

**Rozwiązanie:** Wykonaj następujące kroki, aby wyeksportować i przekazać zaufany certyfikat główny do Application Gateway. (Te kroki są przeznaczone dla klientów z systemem Windows).

1.  Zaloguj się na komputerze, na którym jest hostowana aplikacja.

1.  Wybierz pozycję Win + R lub kliknij prawym przyciskiem myszy przycisk **Start** , a następnie wybierz polecenie **Uruchom**.

1.  Wprowadź `certmgr.msc` i wybierz ENTER. Możesz również wyszukać Menedżera certyfikatów w menu **Start** .

1.  Znajdź certyfikat, zazwyczaj w `\Certificates - Current User\\Personal\\Certificates\` i otwórz go.

1.  Wybierz certyfikat główny, a następnie wybierz pozycję **Wyświetl certyfikat**.

1.  We właściwościach certyfikatu wybierz kartę **szczegóły** .

1.  Na karcie **szczegóły** wybierz opcję **Kopiuj do pliku** i Zapisz plik w formacie X. 509 z kodowaniem Base-64 (. CER).

1.  Otwórz stronę **Ustawienia** protokołu HTTP Application Gateway w Azure Portal.

1. Otwórz ustawienia protokołu HTTP, wybierz pozycję **Dodaj certyfikat**i Znajdź plik certyfikatu, który został właśnie zapisany.

1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia protokołu HTTP.

Alternatywnie można wyeksportować certyfikat główny z komputera klienckiego, uzyskując bezpośrednio dostęp do serwera (pomijając Application Gateway) za pośrednictwem przeglądarki i eksportując certyfikat główny z przeglądarki.

Aby uzyskać więcej informacji na temat wyodrębniania i przekazywania zaufanych certyfikatów głównych w Application Gateway, zobacz [Eksportowanie zaufanego certyfikatu głównego (dla jednostki SKU w wersji 2)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Niezgodność zaufanych certyfikatów głównych

**Komunikat:** Certyfikat główny certyfikatu serwera używanego w zapleczu nie jest zgodny z zaufanym certyfikatem głównym dodanym do bramy aplikacji. Upewnij się, że dodano prawidłowy certyfikat główny, aby dozwolonych zaplecza

**Przyczyna:** Kompleksowy protokół SSL z Application Gateway v2 wymaga zweryfikowania certyfikatu serwera wewnętrznej bazy danych w celu uznania kondycji serwera.
Aby certyfikat TLS/SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji (na przykład został użyty certyfikat z podpisem własnym), użytkownicy powinni przekazać certyfikat wystawcy do Application Gateway.

Certyfikat przekazany do Application Gateway ustawień protokołu HTTP musi być zgodny z certyfikatem głównym certyfikatu serwera wewnętrznej bazy danych.

**Rozwiązanie:** Jeśli zostanie wyświetlony komunikat o błędzie, wystąpi niezgodność między certyfikatem przekazanym do Application Gateway a tym, który został przekazany do serwera wewnętrznej bazy danych.

Wykonaj kroki 1-11 w poprzedniej metodzie, aby przekazać prawidłowy zaufany certyfikat główny do Application Gateway.

Aby uzyskać więcej informacji na temat wyodrębniania i przekazywania zaufanych certyfikatów głównych w Application Gateway, zobacz [Eksportowanie zaufanego certyfikatu głównego (dla jednostki SKU w wersji 2)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Ten błąd może również wystąpić, jeśli serwer wewnętrznej bazy danych nie wymienia kompletnego łańcucha certyfikatu, łącznie z głównym > pośrednią (jeśli dotyczy) > liścia podczas uzgadniania TLS. Aby sprawdzić, można użyć poleceń OpenSSL z dowolnego klienta i połączyć się z serwerem zaplecza przy użyciu skonfigurowanych ustawień sondy Application Gateway.

Na przykład:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Jeśli dane wyjściowe nie zawierają pełnego łańcucha zwracanego certyfikatu, wyeksportuj certyfikat ponownie z pełnym łańcuchem, łącznie z certyfikatem głównym. Skonfiguruj ten certyfikat na serwerze wewnętrznej bazy danych. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nieprawidłowa nazwa pospolita certyfikatu zaplecza (CN)

**Komunikat:** Nazwa pospolita (CN) certyfikatu wewnętrznej bazy danych nie pasuje do nagłówka hosta sondy.

**Przyczyna:** Application Gateway sprawdza, czy nazwa hosta określona w ustawieniach protokołu HTTP zaplecza jest zgodna z identyfikatorem CN przedstawionym przez certyfikat TLS/SSL serwera wewnętrznej bazy danych. Jest to Standard_v2 i WAF_v2 zachowanie jednostki SKU (wersja 2). Wartość WAF (SNI) w warstwie Standardowa i (Oznaczanie nazwy serwera 1) jest ustawiana jako nazwa FQDN w adresie puli zaplecza. Aby uzyskać więcej informacji na temat zachowania SNI i różnic między jednostką SKU V1 i v2, zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](ssl-overview.md).

Jeśli w jednostce SKU v2 istnieje domyślna sonda (nie skonfigurowano żadnej niestandardowej sondy i skojarzono z nią), SNI zostanie ustawiona z nazwy hosta wymienionej w ustawieniach protokołu HTTP. Lub jeśli opcja "Wybierz nazwę hosta z adresu zaplecza" jest wymieniona w ustawieniach HTTP, gdzie Pula adresów zaplecza zawiera prawidłową nazwę FQDN, to ustawienie zostanie zastosowane.

Jeśli istnieje niestandardowa sonda skojarzona z ustawieniami HTTP, SNI zostanie ustawiona z nazwy hosta wymienionej w konfiguracji sondy niestandardowej. W przypadku wybrania w niestandardowej sondy **Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza** SNI zostanie ustawiona z nazwy hosta wymienionej w ustawieniach protokołu HTTP.

Jeśli w ustawieniach protokołu HTTP zostanie ustawiona **Nazwa hosta z adresu zaplecza** , Pula adresów zaplecza musi zawierać prawidłową nazwę FQDN.

Jeśli zostanie wyświetlony komunikat o błędzie, nazwa POSPOLITa certyfikatu wewnętrznej bazy danych nie jest zgodna z nazwą hosta skonfigurowaną w sondie niestandardowej lub w ustawieniach protokołu HTTP (Jeśli wybrano opcję **Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza** ). Jeśli używasz domyślnej sondy, nazwa hosta zostanie ustawiona jako **127.0.0.1**. Jeśli ta wartość nie jest wymagana, należy utworzyć niestandardową sondę i skojarzyć ją z ustawieniami protokołu HTTP.

**Rozwiązanie:**

Aby rozwiązać ten problem, wykonaj poniższe czynności.

W przypadku systemu Windows:

1.  Zaloguj się na komputerze, na którym jest hostowana aplikacja.

1.  Wybierz pozycję Win + R lub kliknij prawym przyciskiem myszy przycisk **Start** i wybierz polecenie **Uruchom**.

1.  Wprowadź **certmgr. msc** i wybierz klawisz ENTER. Możesz również wyszukać Menedżera certyfikatów w menu **Start** .

1.  Znajdź certyfikat (zazwyczaj w `\Certificates - Current User\\Personal\\Certificates` ), a następnie otwórz certyfikat.

1.  Na karcie **szczegóły** zapoznaj się z **tematem**certyfikatu.

1.  Sprawdź nazwę POSPOLITą certyfikatu ze szczegółowych informacji i wprowadź tę samą wartość w polu Nazwa hosta sondy niestandardowej lub w ustawieniach protokołu HTTP (Jeśli wybrano pozycję **Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza** ). Jeśli to nie jest wymagana nazwa hosta dla witryny sieci Web, należy uzyskać certyfikat dla tej domeny lub wprowadzić poprawną nazwę hosta w niestandardowej sondze lub konfiguracji ustawienia HTTP.

W przypadku systemu Linux przy użyciu OpenSSL:

1.  Uruchom to polecenie w OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Na wyświetlone właściwości Znajdź nazwę POSPOLITą certyfikatu i wprowadź tę samą wartość w polu Nazwa hosta w ustawieniach protokołu HTTP. Jeśli to nie jest wymagana nazwa hosta dla witryny sieci Web, należy uzyskać certyfikat dla tej domeny lub wprowadzić poprawną nazwę hosta w niestandardowej sondze lub konfiguracji ustawienia HTTP.

#### <a name="backend-certificate-is-invalid"></a>Certyfikat zaplecza jest nieprawidłowy

**Komunikat:** Certyfikat zaplecza jest nieprawidłowy. Bieżąca data nie należy \" \" do przedziału od prawidłowych od i \" do \" zakresu dat dla certyfikatu.

**Przyczyna:** Każdy certyfikat jest dostarczany z zakresem ważności, a połączenie HTTPS nie będzie bezpieczne, chyba że certyfikat TLS/SSL serwera jest prawidłowy. Bieżące dane muszą znajdować się w przedziale **od prawidłowym** **do** zakresu. Jeśli tak nie jest, certyfikat jest uznawany za nieprawidłowy i spowoduje to utworzenie problemu zabezpieczeń, w którym Application Gateway oznacza, że serwer zaplecza jest w złej kondycji.

**Rozwiązanie:** Jeśli certyfikat TLS/SSL wygasł, odnów certyfikat u dostawcy i zaktualizuj ustawienia serwera przy użyciu nowego certyfikatu. Jeśli jest to certyfikat z podpisem własnym, należy wygenerować prawidłowy certyfikat i przekazać certyfikat główny do Application Gateway ustawień protokołu HTTP. W tym celu wykonaj następujące kroki:

1.  Otwórz ustawienia protokołu HTTP Application Gateway w portalu.

1.  Wybierz ustawienie z wygasłym certyfikatem, wybierz pozycję **Dodaj certyfikat**i Otwórz nowy plik certyfikatu.

1.  Usuń stary certyfikat przy użyciu ikony **usuwania** obok certyfikatu, a następnie wybierz pozycję **Zapisz**.

#### <a name="certificate-verification-failed"></a>Weryfikacja certyfikatu nie powiodła się

**Komunikat:** Nie można zweryfikować ważności certyfikatu zaplecza. Aby sprawdzić przyczynę, sprawdź diagnostykę OpenSSL dla komunikatu skojarzonego z kodem błędu {errorCode}

**Przyczyna:** Ten błąd występuje, gdy Application Gateway nie może zweryfikować ważności certyfikatu.

**Rozwiązanie:** Aby rozwiązać ten problem, sprawdź, czy certyfikat na serwerze został utworzony prawidłowo. Na przykład można użyć [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) do zweryfikowania certyfikatu i jego właściwości, a następnie spróbuj ponownie przekazać certyfikat do Application Gateway ustawień http.

<a name="backend-health-status-unknown"></a>Stan kondycji zaplecza: nieznany
-------------------------------
Jeśli kondycja zaplecza jest wyświetlana jako nieznana, widok portalu będzie wyglądać następująco:

![Kondycja zaplecza Application Gateway — nieznany](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Takie zachowanie może wystąpić z następujących powodów:

1.  SIECIOWEJ grupy zabezpieczeń w podsieci Application Gateway blokuje dostęp przychodzący do portów 65503-65534 (SKU v1) lub 65200-65535 (wersja 2 SKU) z "Internet".
1.  UDR w podsieci Application Gateway jest ustawiona na trasę domyślną (0.0.0.0/0), a następny przeskok nie jest określony jako "Internet".
1.  Trasa domyślna jest anonsowana przez połączenie ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP.
1.  Niestandardowy serwer DNS jest skonfigurowany w sieci wirtualnej, która nie może rozpoznać nazw domen publicznych.
1.  Application Gateway jest w złej kondycji.

**Rozwiązanie:**

1.  Sprawdź, czy Twój sieciowej grupy zabezpieczeń blokuje dostęp do portów 65503-65534 (SKU v1) lub 65200-65535 (jednostka SKU v2) z **Internetu**:

    a.  Na karcie **przegląd** Application Gateway wybierz łącze **Virtual Network/podsieć** .

    b.  Na karcie **podsieci** w sieci wirtualnej wybierz podsieć, w której wdrożono Application Gateway.

    c.  Sprawdź, czy sieciowej grupy zabezpieczeń jest skonfigurowany.

    d.  Jeśli skonfigurowano sieciowej grupy zabezpieczeń, wyszukaj ten zasób sieciowej grupy zabezpieczeń na karcie **Wyszukiwanie** lub w obszarze **wszystkie zasoby**.

    e.  W sekcji **reguły ruchu przychodzącego** Dodaj regułę ruchu przychodzącego zezwalającą na zakres portów docelowych 65503-65534 dla jednostki SKU w wersji 1 lub 65200-65535 v2 z zestawem **źródłowym** ustawionym jako **dowolny** lub **internetowy**.

    f.  Wybierz pozycję **Zapisz** i sprawdź, czy zaplecze można wyświetlić w dobrej kondycji. Alternatywnie możesz to zrobić za poorednictwem [programu PowerShell/interfejsu wiersza polecenia](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Sprawdź, czy Twój UDR ma trasę domyślną (0.0.0.0/0) z następnym przeskokiem, który nie został ustawiony jako **Internet**:
    
    a.  Wykonaj kroki 1a i 1B, aby określić podsieć.

    b.  Sprawdź, czy nie ma skonfigurowanych UDR. Jeśli jest, wyszukaj zasób na pasku wyszukiwania lub w obszarze **wszystkie zasoby**.

    c.  Sprawdź, czy istnieją trasy domyślne (0.0.0.0/0) z następnym przeskokiem nie ustawionym jako **Internet**. Jeśli to ustawienie jest **urządzeniem wirtualnym** lub **bramą Virtual Network**, należy upewnić się, że urządzenie wirtualne lub urządzenie lokalne może prawidłowo skierować pakiet z powrotem do internetowego miejsca docelowego bez modyfikowania pakietu.

    d.  W przeciwnym razie zmień następny przeskok na **Internet**, wybierz pozycję **Zapisz**, a następnie Sprawdź kondycję zaplecza.

1.  Trasa domyślna anonsowana przez połączenie ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP:

    a.  Jeśli istnieje połączenie ExpressRoute/VPN z siecią wirtualną za pośrednictwem protokołu BGP, a w przypadku anonsowania trasy domyślnej, należy się upewnić, że pakiet jest kierowany z powrotem do lokalizacji docelowej Internetu bez modyfikowania. Za pomocą opcji **Rozwiązywanie problemów z połączeniem** można sprawdzić w portalu Application Gateway.

    b.  Wybierz lokalizację docelową ręcznie jako dowolny adres IP z obsługą Internetu, taki jak 1.1.1.1. Ustaw port docelowy jako wszystkie i sprawdź łączność.

    c.  Jeśli następnym przeskokiem jest Brama sieci wirtualnej, może istnieć trasa domyślna anonsowana za pośrednictwem ExpressRoute lub sieci VPN.

1.  Jeśli w sieci wirtualnej jest skonfigurowany niestandardowy serwer DNS, sprawdź, czy serwer (lub serwery) mogą rozpoznać domeny publiczne. W scenariuszach, w których Application Gateway musi nawiązać połączenie z domenami zewnętrznymi, takimi jak serwery OCSP lub sprawdzić stan odwołania certyfikatu, może być wymagane rozpoznawanie nazw domen publicznych.

1.  Aby sprawdzić, czy Application Gateway jest w dobrej kondycji, przejdź do opcji **Resource Health** w portalu i sprawdź, czy stan jest w **dobrej kondycji**. Jeśli widzisz stan **złej kondycji** lub **obniżonej wydajności** , [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Następne kroki
----------

Dowiedz się więcej na temat [diagnostyki i rejestrowania Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
