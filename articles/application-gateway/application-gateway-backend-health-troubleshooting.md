---
title: Rozwiązywanie problemów z kondycją zaplecza w Azure Application Gateway
description: Opisuje sposób rozwiązywania problemów z kondycją zaplecza dla Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 8664f9327af37345c7104c65b2521212669ae806
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786330"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Problemy z kondycją zaplecza w usłudze Application Gateway
==================================================

<a name="overview"></a>Omówienie
--------

Domyślnie program Azure Application Gateway serwery zaplecza, aby sprawdzić ich stan kondycji i sprawdzić, czy są one gotowe do obsługi żądań. Użytkownicy mogą również tworzyć niestandardowe sondy, aby wspomnieć o nazwie hosta, ścieżce do sondowania i kodach stanu do zaakceptowania jako W dobrej kondycji. W każdym przypadku, jeśli serwer zaplecza nie odpowie pomyślnie, program Application Gateway oznacza serwer jako w złej kondycji i zatrzymuje przekazywanie żądań do serwera. Gdy serwer zacznie odpowiadać pomyślnie, Application Gateway wznowi przekazywanie żądań.

### <a name="how-to-check-backend-health"></a>Jak sprawdzić kondycję zaplecza

Aby sprawdzić kondycję puli zaplecza, możesz użyć strony **Kondycja** zaplecza na stronie Azure Portal. Możesz też użyć interfejsu [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), [interfejsu wiersza](/cli/azure/network/application-gateway#az_network_application_gateway_show_backend_health)polecenia lub [interfejsu API REST.](/rest/api/application-gateway/applicationgateways/backendhealth)

Stanem pobieranym przez dowolną z tych metod może być dowolna z następujących czynności:

- Dobra kondycja

- Nieprawidłowy

- Nieznane

Jeśli kondycja zaplecza serwera jest w dobrej kondycji, oznacza to, że Application Gateway będzie przesyłać żądania do tego serwera. Jeśli jednak kondycja zaplecza dla wszystkich serwerów w puli zaplecza jest w złej kondycji lub nieznana, podczas próby uzyskania dostępu do aplikacji mogą wystąpić problemy. W tym artykule opisano objawy, przyczynę i rozwiązanie każdego z wyświetlanych błędów.

<a name="backend-health-status-unhealthy"></a>Stan kondycji zaplecza: W złej kondycji
-------------------------------

Jeśli kondycja zaplecza ma stan W złej kondycji, widok portalu będzie podobny do następującego zrzutu ekranu:

![Application Gateway kondycji zaplecza — w złej kondycji](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Jeśli używasz zapytania interfejsu Azure PowerShell, interfejsu wiersza polecenia lub interfejsu API REST platformy Azure, otrzymasz odpowiedź, która będzie podobna do następującej:
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
Po otrzymaniu stanu serwera zaplecza w złej kondycji dla wszystkich serwerów w puli zaplecza żądania nie są przekazywane do serwerów, a program Application Gateway zwraca błąd "502 Zła brama" do klienta żądającego. Aby rozwiązać ten problem, sprawdź **kolumnę Szczegóły** na **karcie Kondycja zaplecza.**

Komunikat wyświetlany w kolumnie **Szczegóły** zawiera bardziej szczegółowe informacje o problemie i na ich podstawie możesz rozpocząć rozwiązywanie problemu.

> [!NOTE]
> Domyślne żądanie sondy jest wysyłane w formacie \<protocol\> ://127.0.0.1: \<port\> /. Na przykład http://127.0.0.1:80 sonda HTTP na porcie 80. Tylko kody stanu HTTP od 200 do 399 są uznawane za w dobrej kondycji. Protokół i port docelowy są dziedziczone z ustawień protokołu HTTP. Jeśli chcesz, Application Gateway sondować na innym protokole, nazwie hosta lub ścieżce i rozpoznać inny kod stanu jako W dobrej kondycji, skonfiguruj niestandardową sondę i skojarz ją z ustawieniami PROTOKOŁU HTTP.

<a name="error-messages"></a>Komunikaty o błędach
------------------------
#### <a name="backend-server-timeout"></a>Limit czasu serwera zaplecza

**Komunikat:** Czas odpowiedzi zaplecza na sondę kondycji bramy aplikacji jest większy niż próg limitu czasu \' w ustawieniu sondy.

**Przyczyna:** Gdy Application Gateway żądanie sondy HTTP(S) do serwera zaplecza, czeka na odpowiedź z serwera zaplecza przez skonfigurowany okres. Jeśli serwer zaplecza nie odpowiada w skonfigurowanym okresie (wartość limitu czasu), jest oznaczany jako W złej kondycji, dopóki nie zacznie odpowiadać ponownie w skonfigurowanym okresie limitu czasu.

**Rozwiązanie:** Sprawdź, dlaczego serwer lub aplikacja zaplecza nie odpowiada w skonfigurowanym okresie limitu czasu, a także sprawdź zależności aplikacji. Na przykład sprawdź, czy w bazie danych nie ma problemów, które mogą spowodować opóźnienie odpowiedzi. Jeśli masz świadomość zachowania aplikacji i powinna ona reagować dopiero po przeoczaniu wartości limitu czasu, zwiększ wartość limitu czasu z niestandardowych ustawień sondy. Aby zmienić wartość limitu czasu, musisz mieć niestandardową sondę. Aby uzyskać informacje na temat konfigurowania sondy niestandardowej, [zobacz stronę dokumentacji](./application-gateway-create-probe-portal.md).

Aby zwiększyć wartość limitu czasu, wykonaj następujące kroki:

1.  Uzyskaj bezpośredni dostęp do serwera zaplecza i sprawdź czas odpowiedzi serwera na tej stronie. Możesz użyć dowolnego narzędzia do uzyskiwania dostępu do serwera zaplecza, w tym przeglądarki przy użyciu narzędzi dewelopera.

1.  Po skojarzeniu czasu odpowiedzi aplikacji wybierz kartę  Sondy kondycji, a następnie wybierz sondę skojarzoną z ustawieniami protokołu HTTP.

1.  Wprowadź dowolną wartość limitu czasu większą niż czas odpowiedzi aplikacji w sekundach.

1.  Zapisz niestandardowe ustawienia sondy i sprawdź, czy kondycja zaplecza jest teraz w dobrej kondycji.

#### <a name="dns-resolution-error"></a>Błąd rozpoznawania nazw DNS

**Komunikat:** Application Gateway nie można utworzyć sondy dla tego zaplecza. Dzieje się tak zazwyczaj wtedy, gdy nazwa FQDN zaplecza nie została wprowadzona prawidłowo. 

**Przyczyna:** Jeśli pula zaplecza jest typu adres IP/nazwa FQDN lub App Service, adres Application Gateway jest rozpoznany jako adres IP nazwy FQDN wprowadzonej za pośrednictwem systemu nazw domen (DNS) (wartość domyślna niestandardowa lub domyślna dla platformy Azure) i próbuje nawiązać połączenie z serwerem na porcie TCP wymienionym w ustawieniach PROTOKOŁU HTTP. Jednak jeśli ten komunikat jest wyświetlany, sugeruje to, Application Gateway nie może pomyślnie rozpoznać adresu IP wprowadzonej w sieci FQDN.

**Rozwiązanie:**

1.  Sprawdź, czy nazwa FQDN wprowadzona w puli zaplecza jest poprawna i czy jest to domena publiczna, a następnie spróbuj rozwiązać problem z komputera lokalnego.

1.  Jeśli możesz rozpoznać adres IP, może wystąpić problem z konfiguracją DNS w sieci wirtualnej.

1.  Sprawdź, czy sieć wirtualna jest skonfigurowana przy użyciu niestandardowego serwera DNS. Jeśli tak, sprawdź serwer DNS, dlaczego nie może rozpoznać adresu IP określonej nazwy FQDN.

1.  Jeśli używasz domyślnego systemu DNS platformy Azure, skontaktuj się z rejestratorem nazw domen, aby sprawdzić, czy zostało ukończone właściwe mapowanie rekordów A lub CNAME.

1.  Jeśli domena jest prywatna lub wewnętrzna, spróbuj rozwiązać problem z poziomu maszyny wirtualnej w tej samej sieci wirtualnej. Jeśli możesz rozwiązać ten problem, uruchom ponownie Application Gateway i sprawdź ponownie. Aby ponownie Application Gateway, należy zatrzymać [](/powershell/module/azurerm.network/start-azurermapplicationgateway) i rozpocząć korzystanie z poleceń programu PowerShell opisanych w połączonych zasobach. [](/powershell/module/azurerm.network/stop-azurermapplicationgateway)

#### <a name="tcp-connect-error"></a>Błąd połączenia TCP

**Komunikat:** Application Gateway nie można nawiązać połączenia z zaplecza.
Sprawdź, czy zaplecza odpowiada na porcie używanym do sondowania.
Sprawdź również, czy jakakolwiek sieciowa sieciowa żadna sieciowa żadna z sieciowych zasad sieciowych,UDR/zapora nie blokuje dostępu do adresu IP i portu tego zaplecza

**Przyczyna:** Po fazie rozpoznawania nazw DNS program Application Gateway nawiązać połączenie z serwerem zaplecza na porcie TCP skonfigurowanym w ustawieniach protokołu HTTP. Jeśli Application Gateway nie można ustanowić sesji TCP na określonym porcie, sonda zostanie oznaczona jako w złej kondycji za pomocą tego komunikatu.

**Rozwiązanie:** Jeśli wystąpi ten błąd, wykonaj następujące kroki:

1.  Sprawdź, czy możesz nawiązać połączenie z serwerem zaplecza na porcie wymienionym w ustawieniach protokołu HTTP przy użyciu przeglądarki lub programu PowerShell. Na przykład uruchom następujące polecenie: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Jeśli wymieniony port nie jest żądanym portem, wprowadź prawidłowy numer portu, Application Gateway nawiązać połączenie z serwerem zaplecza

1.  Jeśli nie możesz również nawiązać połączenia na porcie z komputera lokalnego, możesz:

    a.  Sprawdź ustawienia sieciowej grupy zabezpieczeń karty sieciowej i podsieci serwera zaplecza oraz czy połączenia przychodzące do skonfigurowanego portu są dozwolone. Jeśli nie, utwórz nową regułę, aby zezwolić na połączenia. Aby dowiedzieć się, jak tworzyć reguły sieciowej grupy bezpieczeństwa, [zobacz stronę dokumentacji](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Sprawdź, czy ustawienia sieciowej grupy zabezpieczeń podsieci Application Gateway zezwalają na wychodzący ruch publiczny i prywatny, aby można było nawiązane połączenie. Sprawdź stronę dokumentu podaną w kroku 3a, aby dowiedzieć się więcej na temat sposobu tworzenia reguł sieciowej grupy bezpieczeństwa.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Sprawdź ustawienia tras zdefiniowanych przez użytkownika (UDR, user-defined routes) Application Gateway podsieci serwera zaplecza pod temat wszelkich anomalii routingu. Upewnij się, że sieć UDR nie kieruje ruchu z podsieci zaplecza. Na przykład sprawdź trasy do wirtualnych urządzeń sieciowych lub trasy domyślne anonsowane do podsieci Application Gateway za pośrednictwem sieci Azure ExpressRoute i/lub sieci VPN.

    d.  Aby sprawdzić obowiązujące trasy i reguły dla karty sieciowej, można użyć następujących poleceń programu PowerShell:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Jeśli nie znajdziesz żadnych problemów z sieciową siecią i sieciową siecią,sprawdź, czy na serwerze zaplecza nie ma problemów związanych z aplikacją, które uniemożliwiają klientom ustanawianie sesji TCP na skonfigurowanych portach. Kilka rzeczy do sprawdzenia:

    a.  Otwórz wiersz polecenia (Win+R — \> cmd), wprowadź `netstat` polecenie i wybierz klawisz Enter.

    b.  Sprawdź, czy serwer nasłuchuje na skonfigurowanym porcie. Na przykład:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Jeśli nie nasłuchuje na skonfigurowanym porcie, sprawdź ustawienia serwera internetowego. Na przykład: powiązania witryn w usługach IIS, blok serwera na serwerze NGINX i host wirtualny na serwerze Apache.

    d.  Sprawdź ustawienia zapory systemu operacyjnego, aby upewnić się, że ruch przychodzący do portu jest dozwolony.

#### <a name="http-status-code-mismatch"></a>Niezgodność kodu stanu HTTP

**Komunikat:** Kod stanu odpowiedzi HTTP zaplecza \' nie jest zgodne z ustawieniem sondowania. Oczekiwano: {HTTPStatusCode0} Odebrano: {HTTPStatusCode1}.

**Przyczyna:** Po nawiązaniu połączenia TCP i zakończeniu połączenia TLS (jeśli protokół TLS jest włączony) program Application Gateway wyśle sondę jako żądanie HTTP GET do serwera zaplecza. Jak opisano wcześniej, domyślna sonda będzie mieć wartość \<protocol\> ://127.0.0.1: /, a kody stanu odpowiedzi w ramach \<port\> rage od 200 do 399 są w dobrej kondycji. Jeśli serwer zwróci jakikolwiek inny kod stanu, ten komunikat zostanie oznaczony jako W złej kondycji.

**Rozwiązanie:** W zależności od kodu odpowiedzi serwera zaplecza można wykonać następujące kroki. Poniżej wymieniono kilka typowych kodów stanu:

| **Błąd** | **Akcje** |
| --- | --- |
| Niezgodność kodu stanu sondy: odebrano błąd 401 | Sprawdź, czy serwer zaplecza wymaga uwierzytelniania. Application Gateway nie mogą przekazywać poświadczeń do uwierzytelniania. Zezwalaj na kod HTTP 401 w kodzie stanu sondy lub sonduj ścieżkę, w której \" \" serwer nie wymaga uwierzytelniania. |
| Niezgodność kodu stanu sondy: odebrano błąd 403 | Dostęp zabroniony. Sprawdź, czy dostęp do ścieżki jest dozwolony na serwerze zaplecza. |
| Niezgodność kodu stanu sondy: odebrano błąd 404 | Nie znaleziono strony. Sprawdź, czy ścieżka nazwy hosta jest dostępna na serwerze zaplecza. Zmień parametr nazwy hosta lub ścieżki na dostępną wartość. |
| Niezgodność kodu stanu sondy: odebrano błąd 405 | Żądania sondy dla Application Gateway używają metody GET protokołu HTTP. Sprawdź, czy serwer zezwala na tę metodę. |
| Niezgodność kodu stanu sondy: odebrano 500 | Wewnętrzny błąd serwera. Sprawdź kondycję serwera zaplecza i sprawdź, czy usługi są uruchomione. |
| Niezgodność kodu stanu sondy: odebrano błąd 503 | Usługa niedostępna. Sprawdź kondycję serwera zaplecza i sprawdź, czy usługi są uruchomione. |

Jeśli uważasz, że odpowiedź jest uzasadniona i chcesz, Application Gateway akceptować inne kody stanu jako W dobrej kondycji, możesz utworzyć sondę niestandardową. Takie podejście przydaje się w sytuacjach, w których witryna internetowa zaplecza wymaga uwierzytelniania. Ponieważ żądania sondy nie przenoszą żadnych poświadczeń użytkownika, nie powiodą się, a serwer zaplecza zwróci kod stanu HTTP 401.

Aby utworzyć sondę niestandardową, wykonaj [następujące kroki.](./application-gateway-create-probe-portal.md)

#### <a name="http-response-body-mismatch"></a>Niezgodność treści odpowiedzi HTTP

**Komunikat:** Treść odpowiedzi \' HTTP zaplecza nie jest dopasowana do ustawienia sondy. Odebrana treść odpowiedzi nie zawiera ciągu {string}.

**Przyczyna:** Podczas tworzenia niestandardowej sondy można oznaczyć serwer zaplecza jako w dobrej kondycji, dopasowując ciąg z treści odpowiedzi. Można na przykład skonfigurować usługę Application Gateway tak, aby akceptowała ciąg "nieautoryzowany" jako ciąg do dopasowania. Jeśli odpowiedź serwera zaplecza dla żądania sondy zawiera ciąg nieautoryzowany, zostanie on oznaczony jako W dobrej kondycji. W przeciwnym razie ten komunikat zostanie oznaczony jako W złej kondycji.

**Rozwiązanie:** Aby rozwiązać ten problem, wykonaj następujące kroki:

1.  Uzyskaj dostęp do serwera zaplecza lokalnie lub z komputera klienckiego na ścieżce sondowania i sprawdź treść odpowiedzi.

1.  Sprawdź, czy treść odpowiedzi w Application Gateway niestandardowej sondy odpowiada skonfigurowanej konfiguracji.

1.  Jeśli nie są zgodne, zmień konfigurację sondy tak, aby zawierała poprawną wartość ciągu do zaakceptowania.

Dowiedz się więcej o [dopasowywaniu Application Gateway sondy.](./application-gateway-probe-overview.md#probe-matching)

>[!NOTE]
> Aby dowiedzieć się więcej o zachowaniu funkcji SNI i różnicach między wersjami 1 i 2 dla wszystkich komunikatów o błędach związanych z TLS, zapoznaj się ze stroną [przeglądu usługi TLS.](ssl-overview.md)


#### <a name="backend-server-certificate-invalid-ca"></a>Nieprawidłowy urząd certyfikacji certyfikatu serwera zaplecza

**Komunikat:** Certyfikat serwera używany przez zaplecza nie jest podpisany przez dobrze znany urząd certyfikacji. Zezwładuj na Application Gateway przez przekazanie certyfikatu głównego certyfikatu serwera używanego przez zaplecza.

**Przyczyna:** Protokół SSL typu end-to-end z Application Gateway w wersji 2 wymaga zweryfikowania certyfikatu serwera zaplecza, aby można było go w dobrej kondycji.
Aby certyfikat TLS/SSL był zaufany, ten certyfikat serwera zaplecza musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji (na przykład jeśli został użyty certyfikat z podpisem własnym), użytkownicy powinni przekazać certyfikat wystawcy do usługi Application Gateway.

**Rozwiązanie:** Wykonaj następujące kroki, aby wyeksportować i przekazać zaufany certyfikat główny do Application Gateway. (Te kroki są dostępne dla klientów z systemem Windows).

1.  Zaloguj się do maszyny, na której jest hostowana aplikacja.

1.  Wybierz pozycję Win+R lub kliknij prawym przyciskiem myszy **przycisk Start,** a następnie wybierz polecenie **Uruchom**.

1.  Wprowadź `certmgr.msc` i wybierz klawisz Enter. Możesz również wyszukać Menedżera certyfikatów w menu **Start.**

1.  Znajdź certyfikat , zazwyczaj w lokalizacji `\Certificates - Current User\\Personal\\Certificates\` , i otwórz go.

1.  Wybierz certyfikat główny, a następnie wybierz **pozycję Wyświetl certyfikat.**

1.  We właściwościach certyfikatu wybierz **kartę** Szczegóły.

1.  Na karcie **Szczegóły** wybierz  opcję Kopiuj do pliku i zapisz plik w formacie X.509 zakodowanym w formacie Base-64 (. CER).

1.  Otwórz Application Gateway ustawienia HTTP **w** witrynie Azure Portal.

1. Otwórz ustawienia PROTOKOŁU HTTP, wybierz **pozycję Dodaj certyfikat** i znajdź zapisany plik certyfikatu.

1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia PROTOKOŁU HTTP.

Certyfikat główny można też wyeksportować z komputera klienckiego, bezpośrednio korzystając z serwera (pomijając Application Gateway) za pośrednictwem przeglądarki i eksportując certyfikat główny z przeglądarki.

Aby uzyskać więcej informacji na temat wyodrębniania i przekazywania zaufanych certyfikatów głównych w programie Application Gateway, zobacz Eksportowanie zaufanego certyfikatu głównego [(dla wersji 2 SKU).](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)

#### <a name="trusted-root-certificate-mismatch"></a>Niezgodność zaufanego certyfikatu głównego

**Komunikat:** Certyfikat główny certyfikatu serwera używanego przez zaplecza nie jest zgodne z zaufanym certyfikatem głównym dodanym do bramy aplikacji. Upewnij się, że dodasz prawidłowy certyfikat główny, aby zezwolić na listę zaplecza.

**Przyczyna:** Protokół SSL typu end-to-end z Application Gateway w wersji 2 wymaga zweryfikowania certyfikatu serwera zaplecza w celu ustawienia serwera w dobrej kondycji.
Aby certyfikat TLS/SSL był zaufany, certyfikat serwera zaplecza musi być wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji (na przykład został użyty certyfikat z podpisem własnym), użytkownicy powinni przekazać certyfikat wystawcy do Application Gateway.

Certyfikat przekazany do usługi Application Gateway ustawienia http musi być zgodne z certyfikatem głównym certyfikatu serwera zaplecza.

**Rozwiązanie:** Jeśli zostanie wyświetlony ten komunikat o błędzie, występuje niezgodność między certyfikatem przekazanym do usługi Application Gateway i certyfikatem przekazanym na serwer zaplecza.

Wykonaj kroki od 1 do 11 w poprzedniej metodzie, aby przekazać prawidłowy zaufany certyfikat główny do Application Gateway.

Aby uzyskać więcej informacji na temat wyodrębniania i przekazywania zaufanych certyfikatów głównych w programie Application Gateway, zobacz Eksportowanie zaufanego certyfikatu głównego [(dla wersji 2 SKU).](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)
> [!NOTE]
> Ten błąd może również wystąpić, jeśli serwer zaplecza nie wymienia pełnego łańcucha certyfikatu, w tym głównego serwera > pośredniego (jeśli ma zastosowanie) > Leaf podczas ugody TLS. Aby to sprawdzić, można użyć poleceń OpenSSL z dowolnego klienta i połączyć się z serwerem zaplecza przy użyciu ustawień skonfigurowanych w Application Gateway danych.

Na przykład:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Jeśli dane wyjściowe nie pokazują pełnego łańcucha zwracanego certyfikatu, wyeksportuj certyfikat ponownie z kompletnym łańcuchem, łącznie z certyfikatem głównym. Skonfiguruj ten certyfikat na serwerze zaplecza. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nieprawidłowa nazwa pospolita certyfikatu zaplecza

**Komunikat:** Nazwa pospolita (CN) certyfikatu zaplecza nie jest dopasowana do nagłówka hosta sondy.

**Przyczyna:** Application Gateway sprawdza, czy nazwa hosta określona w ustawieniach HTTP zaplecza jest taka sama jak nazwa określonej nazwy określonej przez certyfikat TLS/SSL serwera zaplecza. Jest to Standard_v2 i WAF_v2 (V2). Standardowa i WAF SKU (v1) Oznaczanie nazwy serwera (SNI) jest ustawiona jako WQDN w adresie puli zaplecza. Aby uzyskać więcej informacji na temat zachowania funkcji SNI i różnic między wersjami 1 i 2, zobacz Overview of TLS termination and end to end TLS with Application Gateway (Omówienie zakończenia TLS i [end-to-end TLS z](ssl-overview.md)Application Gateway .

W wersji 2 SKU, jeśli istnieje sonda domyślna (nie skonfigurowano ani nie skojarzono niestandardowej sondy), zostanie ustawiona nazwa SNI na podstawie nazwy hosta wymienionej w ustawieniach protokołu HTTP. Jeśli natomiast w ustawieniach PROTOKOŁU HTTP zostanie wspomniano o poleceniu "Wybierz nazwę hosta z adresu zaplecza", gdzie pula adresów zaplecza zawiera prawidłową nazwę FQDN, to ustawienie zostanie zastosowane.

Jeśli z ustawieniami PROTOKOŁU HTTP jest skojarzona sonda niestandardowa, zostanie ustawiona nazwa hosta wymieniona w niestandardowej konfiguracji sondy. Jeśli w niestandardowej **sondze** wybrano opcję Wybierz nazwę hosta z ustawień HTTP zaplecza, zostanie ustawiona nazwa hosta wymieniona w ustawieniach PROTOKOŁU HTTP.

Jeśli **ustawienie Wybierz nazwę hosta z adresu zaplecza** jest ustawione w ustawieniach PROTOKOŁU HTTP, pula adresów zaplecza musi zawierać prawidłową nazwę FQDN.

Jeśli zostanie wyświetlony ten komunikat o błędzie, nazwa cn certyfikatu zaplecza nie jest taka sama jak nazwa hosta skonfigurowana w niestandardowej sondie lub ustawieniach PROTOKOŁU HTTP (jeśli wybrano opcję Wybierz nazwę hosta z ustawień **HTTP** zaplecza). Jeśli używasz sondy domyślnej, nazwa hosta zostanie ustawiona na **127.0.0.1.** Jeśli nie jest to żądana wartość, należy utworzyć sondę niestandardową i skojarzyć ją z ustawieniami protokołu HTTP.

**Rozwiązanie:**

Aby rozwiązać ten problem, wykonaj poniższe czynności.

W przypadku systemu Windows:

1.  Zaloguj się do maszyny, na której jest hostowana aplikacja.

1.  Wybierz pozycję Win+R lub kliknij prawym przyciskiem myszy **przycisk Start,** a następnie wybierz polecenie **Uruchom.**

1.  Wprowadź **certmgr.msc i** wybierz klawisz Enter. Możesz również wyszukać Menedżera certyfikatów w menu **Start.**

1.  Znajdź certyfikat (zazwyczaj w `\Certificates - Current User\\Personal\\Certificates` programie ), a następnie otwórz certyfikat.

1.  Na karcie **Szczegóły** sprawdź temat **certyfikatu**.

1.  Sprawdź nazwę CN certyfikatu z poziomu szczegółów i wprowadź tę samą nazwę w polu nazwy hosta sondy niestandardowej lub w ustawieniach PROTOKOŁU HTTP (jeśli wybrano opcję Wybierz nazwę hosta z ustawień **HTTP** zaplecza). Jeśli nie jest to żądana nazwa hosta dla witryny internetowej, musisz uzyskać certyfikat dla tej domeny lub wprowadzić poprawną nazwę hosta w konfiguracji niestandardowej sondy lub ustawienia PROTOKOŁU HTTP.

W przypadku systemu Linux używającego programu OpenSSL:

1.  Uruchom to polecenie w programie OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  W wyświetlonych właściwościach znajdź nazwę CN certyfikatu i wprowadź tę samą nazwę w polu nazwy hosta w ustawieniach protokołu HTTP. Jeśli nie jest to żądana nazwa hosta dla witryny internetowej, musisz uzyskać certyfikat dla tej domeny lub wprowadzić poprawną nazwę hosta w konfiguracji niestandardowej sondy lub ustawienia PROTOKOŁU HTTP.

#### <a name="backend-certificate-is-invalid"></a>Certyfikat zaplecza jest nieprawidłowy

**Komunikat:** Certyfikat zaplecza jest nieprawidłowy. Bieżąca data nie znajduje się w zakresie od valid from i \" \" Valid do daty \" \" certyfikatu.

**Przyczyna:** Każdy certyfikat ma zakres ważności, a połączenie HTTPS nie będzie bezpieczne, chyba że certyfikat TLS/SSL serwera jest prawidłowy. Bieżące dane muszą znajdować się w **zakresie od i** valid **do** zakresu. Jeśli tak nie jest, certyfikat jest uznawany za nieprawidłowy i spowoduje to utworzenie problemu z zabezpieczeniami, w którym program Application Gateway oznacza serwer zaplecza jako w złej kondycji.

**Rozwiązanie:** Jeśli certyfikat TLS/SSL wygasł, odnów certyfikat u dostawcy i zaktualizuj ustawienia serwera przy użyciu nowego certyfikatu. Jeśli jest to certyfikat z podpisem własnym, należy wygenerować prawidłowy certyfikat i przekazać certyfikat główny do Application Gateway http. W tym celu wykonaj następujące kroki:

1.  Otwórz ustawienia Application Gateway HTTP w portalu.

1.  Wybierz ustawienie, które ma wygasły certyfikat, wybierz **pozycję Dodaj certyfikat** i otwórz nowy plik certyfikatu.

1.  Usuń stary certyfikat przy użyciu **ikony** Usuń obok certyfikatu, a następnie wybierz pozycję **Zapisz.**

#### <a name="certificate-verification-failed"></a>Weryfikacja certyfikatu nie powiodła się

**Komunikat:** Nie można zweryfikować ważności certyfikatu zaplecza. Aby dowiedzieć się, jaka jest przyczyna, sprawdź diagnostykę OpenSSL dla komunikatu skojarzonego z kodem błędu {errorCode}

**Przyczyna:** Ten błąd występuje, Application Gateway nie może zweryfikować ważności certyfikatu.

**Rozwiązanie:** Aby rozwiązać ten problem, sprawdź, czy certyfikat na serwerze został utworzony prawidłowo. Na przykład możesz użyć protokołu [OpenSSL,](https://www.openssl.org/docs/man1.0.2/man1/verify.html) aby zweryfikować certyfikat i jego właściwości, a następnie spróbować ponownie załadować certyfikat do ustawień protokołu HTTP Application Gateway.

<a name="backend-health-status-unknown"></a>Stan kondycji zaplecza: nieznany
-------------------------------
Jeśli kondycja zaplecza jest wyświetlana jako Nieznana, widok portalu będzie wyglądać podobnie jak na poniższym zrzucie ekranu:

![Application Gateway kondycji zaplecza — nieznana](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Takie zachowanie może mieć co najmniej jedną z następujących przyczyn:

1.  Sieciowa grupa danych w podsieci Application Gateway blokuje dostęp przychodzący do portów 65503-65534 (wersja 1 SKU) lub 65200–65535 (wersja 2 SKU) z "Internetu".
1.  Trasa UDR w podsieci Application Gateway jest ustawiona na trasę domyślną (0.0.0.0/0), a następny przeskok nie jest określony jako "Internet".
1.  Trasa domyślna jest anonsowana przez połączenie usługi ExpressRoute/sieci VPN z siecią wirtualną za pośrednictwem protokołu BGP.
1.  Niestandardowy serwer DNS jest skonfigurowany w sieci wirtualnej, która nie może rozpoznać nazw domen publicznych.
1.  Usługa Application Gateway znajduje się w złej kondycji.

**Rozwiązanie:**

1.  Sprawdź, czy sieciowa żadna z sieciowych sieci nie blokuje dostępu do portów 65503-65534 (wersja 1 SKU) czy 65200-65535 (wersja 2 SKU) z **Internetu:**

    a.  Na karcie Application Gateway **Przegląd** wybierz link **Virtual Network/Podsieć.**

    b.  Na karcie Podsieci sieci wirtualnej wybierz **podsieć,** w której Application Gateway została wdrożona.

    c.  Sprawdź, czy skonfigurowano jakikolwiek sieciowy g.

    d.  Jeśli skonfigurowano sieciowa grupę danych, wyszukaj ten zasób sieciowej grupy danych na **karcie** Wyszukiwanie lub w **obszarze Wszystkie zasoby.**

    e.  W  sekcji Reguły ruchu przychodzącego dodaj regułę ruchu przychodzącego, aby zezwolić na zakres portów docelowych 65503–65534 dla wersji 1 SKU lub 65200-65535 v2 z ustawieniem Źródło ustawione na Dowolny lub **Internet.**  

    f.  Wybierz **pozycję Zapisz** i sprawdź, czy możesz wyświetlić zaplecza jako W dobrej kondycji. Alternatywnie możesz to zrobić za pomocą programu [PowerShell/interfejsu wiersza polecenia](../virtual-network/manage-network-security-group.md).

1.  Sprawdź, czy trasa UDR ma trasę domyślną (0.0.0.0/0) z następnym przeskoku, który nie jest ustawiony jako **Internet:**
    
    a.  Wykonaj kroki 1a i 1b, aby określić podsieć.

    b.  Sprawdź, czy skonfigurowano dowolną konfigurację UDR. Jeśli tak, wyszukaj zasób na pasku wyszukiwania lub w obszarze **Wszystkie zasoby.**

    c.  Sprawdź, czy istnieją trasy domyślne (0.0.0.0/0) z następnym przeskoku, które nie są ustawione jako **Internet.** Jeśli ustawienie to  urządzenie wirtualne lub brama usługi **Virtual Network** Gateway, upewnij się, że urządzenie wirtualne lub urządzenie lokalne może prawidłowo przekierowyować pakiet z powrotem do miejsca docelowego Internetu bez modyfikowania pakietu.

    d.  W przeciwnym razie zmień następny przeskok na **Internet,** **wybierz pozycję Zapisz** i sprawdź kondycję zaplecza.

1.  Trasa domyślna anonsowana przez połączenie usługi ExpressRoute/sieci VPN z siecią wirtualną za pośrednictwem protokołu BGP:

    a.  Jeśli masz połączenie usługi ExpressRoute/sieci VPN z siecią wirtualną za pośrednictwem protokołu BGP, a w przypadku anrygowania trasy domyślnej musisz upewnić się, że pakiet jest przekierowyny z powrotem do miejsca docelowego Internetu bez modyfikowania go. Możesz to sprawdzić za pomocą opcji **Rozwiązywanie problemów z połączeniami** w Application Gateway portal.

    b.  Ręcznie wybierz miejsce docelowe jako dowolny adres IP z routem internetowym, taki jak 1.1.1.1. Ustaw port docelowy jako wszystko i sprawdź łączność.

    c.  Jeśli następnym przeskoku jest brama sieci wirtualnej, może to być trasa domyślna anonsowana za pośrednictwem usługi ExpressRoute lub sieci VPN.

1.  Jeśli w sieci wirtualnej skonfigurowano niestandardowy serwer DNS, sprawdź, czy serwer (lub serwery) może rozpoznać domeny publiczne. Rozpoznawania nazw domen publicznych może być wymagane w scenariuszach, Application Gateway muszą się z domenami zewnętrznymi, takich jak serwery OCSP lub sprawdzić stan odwołania certyfikatu.

1.  Aby sprawdzić, Application Gateway jest w dobrej kondycji i działa, przejdź do Resource Health w portalu i sprawdź, czy stan jest w dobrej **kondycji.**  Jeśli widzisz stan **złej kondycji** lub **obniżonej wydajności,** skontaktuj [się z pomocą techniczną](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Następne kroki
----------

Dowiedz się więcej [o Application Gateway diagnostyki i rejestrowania.](./application-gateway-diagnostics.md)
