---
title: Konfigurowanie kompleksowego protokołu TLS przy użyciu usługi Azure Application Gateway
description: W tym artykule opisano sposób konfigurowania kompleksowego protokołu TLS za pomocą usługi Azure Application Gateway przy użyciu programu PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397811"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Konfigurowanie kompleksowego szyfrowania TLS przy użyciu usługi Application Gateway w programie PowerShell

## <a name="overview"></a>Omówienie

Usługa Azure Application Gateway obsługuje kompleksowe szyfrowanie ruchu sieciowego. Application Gateway przerywa połączenie TLS/SSL w bramie aplikacji. Następnie Brama stosuje reguły routingu do ruchu, ponownie szyfruje pakiet i przekazuje pakiet do odpowiedniego serwera zaplecza na podstawie zdefiniowanych reguł routingu. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego.

Application Gateway obsługuje definiowania niestandardowych opcji protokołu TLS. Obsługuje ona również wyłączanie następujących wersji protokołów: **TLSv 1.0**, **TLSv 1.1** i **TLSv 1.2**, a także Definiowanie mechanizmów szyfrowania, które mają być używane, oraz kolejności preferencji. Aby dowiedzieć się więcej na temat konfigurowalnych opcji protokołu TLS, zobacz [Omówienie zasad protokołu TLS](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Protokoły SSL 2,0 i SSL 3,0 są domyślnie wyłączone i nie można ich włączyć. Są one uznawane za niezabezpieczone i nie mogą być używane z Application Gateway.

![obraz scenariusza][scenario]

## <a name="scenario"></a>Scenariusz

W tym scenariuszu dowiesz się, jak utworzyć bramę aplikacji przy użyciu kompleksowej protokołu TLS z programem PowerShell.

W tym scenariuszu zostaną:

* Utwórz grupę zasobów o nazwie **appgw-RG**.
* Utwórz sieć wirtualną o nazwie **appgwvnet** z przestrzenią adresową **10.0.0.0/16**.
* Utwórz dwie podsieci o nazwie **appgwsubnet** i **appsubnet**.
* Utwórz małą bramę aplikacji obsługującą kompleksowe szyfrowanie TLS, które ograniczają wersje protokołu TLS i mechanizmy szyfrowania.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby skonfigurować kompleksową transkonfigurację TLS przy użyciu bramy aplikacji, wymagany jest certyfikat dla bramy, a certyfikaty są wymagane dla serwerów zaplecza. Certyfikat bramy służy do wyprowadzania klucza symetrycznego zgodnie ze specyfikacją protokołu TLS. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat bramy musi znajdować się w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia wyeksportowanie klucza prywatnego wymaganego przez bramę aplikacji w celu przeprowadzenia szyfrowania i odszyfrowywania ruchu.

Aby kompleksowe szyfrowanie TLS było możliwe, zaplecze musi być jawnie dozwolone przez bramę aplikacji. Przekaż publiczny certyfikat serwerów zaplecza do bramy aplikacji. Dodanie certyfikatu zapewnia, że Brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Pozwala to na zapewnienie kompleksowej komunikacji.

Proces konfiguracji został opisany w poniższych sekcjach.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

W tej sekcji omówiono tworzenie grupy zasobów zawierającej bramę aplikacji.

1. Zaloguj się do swojego konta platformy Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Wybierz subskrypcję, która ma być używana w tym scenariuszu.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Utwórz grupę zasobów. (Pomiń ten krok, jeśli używasz istniejącej grupy zasobów).

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

Poniższy przykład tworzy sieć wirtualną i dwie podsieci. Jedna podsieć jest używana do przechowywania bramy aplikacji. Druga podsieć jest używana na potrzeby zaplecza, który hostuje aplikację sieci Web.

1. Przypisz zakres adresów dla podsieci, który ma być używany przez bramę aplikacji.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsieci skonfigurowane dla bramy aplikacji powinny mieć prawidłowy rozmiar. Bramę aplikacji można skonfigurować dla maksymalnie 10 wystąpień. Każde wystąpienie przyjmuje jeden adres IP z podsieci. Zbyt mała część podsieci może niekorzystnie wpłynąć na skalowanie bramy aplikacji.
   >

2. Przypisz zakres adresów, który ma być używany dla puli adresów zaplecza.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Utwórz sieć wirtualną z podsieciami zdefiniowanymi w powyższych krokach.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Pobierz zasób sieci wirtualnej i zasoby podsieci, które będą używane w kolejnych krokach.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP, który ma być używany przez bramę aplikacji. Ten publiczny adres IP jest używany w jednym z następujących kroków.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway nie obsługuje korzystania z publicznego adresu IP utworzonego za pomocą zdefiniowanej etykiety domeny. Obsługiwany jest tylko publiczny adres IP z dynamicznie utworzoną etykietą domeny. Jeśli potrzebujesz przyjaznej nazwy DNS dla bramy aplikacji, zalecamy użycie rekordu CNAME jako aliasu.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji są ustawione przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

1. Utwórz konfigurację adresu IP bramy aplikacji. To ustawienie określa, które podsieci są wykorzystywane przez bramę aplikacji. Po uruchomieniu usługi Application Gateway Pobiera adres IP ze skonfigurowanej podsieci i kieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Utwórz konfigurację adresu IP frontonu. To ustawienie mapuje prywatny lub publiczny adres IP na fronton bramy aplikacji. Poniższy krok kojarzy publiczny adres IP w poprzednim kroku z konfiguracją adresów IP frontonu.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Skonfiguruj pulę adresów IP zaplecza przy użyciu adresów IP serwerów zaplecza sieci Web. Będą to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Zastąp adresy IP w przykładzie własnymi punktami końcowymi adresów IP aplikacji.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > W pełni kwalifikowana nazwa domeny (FQDN) jest również prawidłową wartością używaną zamiast adresu IP serwerów zaplecza. Można ją włączyć za pomocą przełącznika **-BackendFqdns** . 

4. Skonfiguruj port frontonu IP dla punktu końcowego publicznego adresu IP. Port ten jest portem, z którym łączą się użytkownicy końcowi.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Skonfiguruj certyfikat dla bramy aplikacji. Ten certyfikat jest używany do odszyfrowywania i ponownego szyfrowania ruchu w bramie aplikacji.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Ten przykład umożliwia skonfigurowanie certyfikatu używanego na potrzeby połączenia TLS. Certyfikat musi być w formacie PFX, a hasło może zawierać od 4 do 12 znaków.

6. Utwórz odbiornik HTTP dla bramy aplikacji. Przypisz konfigurację IP frontonu, port i certyfikat TLS/SSL do użycia.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Przekaż certyfikat, który będzie używany w zasobach puli zaplecza z włączoną obsługą protokołu TLS.

   > [!NOTE]
   > Domyślna sonda Pobiera klucz publiczny z *domyślnego* powiązania TLS na adres IP zaplecza i porównuje wartość klucza publicznego, którą otrzymuje do wartości klucza publicznego, którą podano w tym miejscu. 
   > 
   > Jeśli używasz nagłówków hosta i Oznaczanie nazwy serwera (SNI) na zapleczu, pobrany klucz publiczny może nie być zamierzoną lokacją, do której przepływy ruchu. Jeśli masz wątpliwości, odwiedź stronę https://127.0.0.1/ na serwerach zaplecza, aby potwierdzić, który certyfikat jest używany dla *domyślnego* powiązania protokołu TLS. Użyj klucza publicznego z tego żądania w tej sekcji. Jeśli korzystasz z nagłówków hosta i SNI na powiązaniach HTTPS i nie otrzymasz odpowiedzi ani certyfikatu z ręcznego żądania przeglądarki do https://127.0.0.1/ serwerów zaplecza, musisz skonfigurować domyślne powiązanie protokołu TLS. W przeciwnym razie sondy zakończą się niepowodzeniem, a zaplecze jest niedozwolone.
   
   Aby uzyskać więcej informacji na temat SNI w Application Gateway, zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](ssl-overview.md).

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certyfikat podany w poprzednim kroku powinien być kluczem publicznym certyfikatu PFX znajdującego się na zapleczu. Wyeksportuj certyfikat (nie certyfikat główny) zainstalowany na serwerze zaplecza w formacie Claim, dowód i powód (CER) i użyj go w tym kroku. Ten krok pozwala zapleczu z bramą aplikacji.

   Jeśli używasz jednostki SKU Application Gateway v2, Utwórz zaufany certyfikat główny zamiast certyfikatu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Omówienie kompleksowej usługi TLS z Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Skonfiguruj ustawienia protokołu HTTP dla zaplecza usługi Application Gateway. Przypisz certyfikat przekazany w poprzednim kroku do ustawień protokołu HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   W przypadku jednostki SKU Application Gateway v2 Użyj następującego polecenia:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Utwórz regułę routingu usługi równoważenia obciążenia, która konfiguruje zachowanie modułu równoważenia obciążenia. W tym przykładzie zostanie utworzona Podstawowa reguła działania okrężnego.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Skonfiguruj rozmiar wystąpienia bramy aplikacji. Dostępne rozmiary to **standardowe \_ małe**, **standardowe i \_** **\_ duże**.  W celu uzyskania pojemności dostępne są wartości od **1** do **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Liczbę wystąpień 1 można wybrać do celów testowych. Ważne jest, aby wiedzieć, że jakakolwiek liczba wystąpień w ramach dwóch wystąpień nie jest objęta umową SLA i dlatego nie jest zalecana. Małe bramy są używane do testowania deweloperskiego, a nie do celów produkcyjnych.

11. Skonfiguruj zasady protokołu TLS, które mają być używane przez bramę aplikacji. Application Gateway obsługuje możliwość ustawienia minimalnej wersji protokołu TLS.

    Poniżej przedstawiono listę wersji protokołu, które można zdefiniować:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    W poniższym przykładzie ustawiono minimalną wersję protokołu do **TLSv1_2** i włącza **Protokół \_ TLS \_ \_ \_ \_ 128 \_ GCM \_ SHA256**, **tls \_ ECDHE \_ ECDSA \_ z \_ algorytmem AES \_ 256 \_ GCM \_ SHA384** i **TLS \_ \_ \_ \_ 128 \_ GCM \_** SHA256.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Korzystając ze wszystkich powyższych kroków, należy utworzyć bramę aplikacji. Tworzenie bramy to proces, który zajmuje dużo czasu na uruchomienie.

W przypadku jednostki SKU V1 Użyj poniższego polecenia
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

W przypadku wersji 2 jednostki SKU Użyj poniższego polecenia
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Zastosuj nowy certyfikat w przypadku wygaśnięcia certyfikatu zaplecza

Użyj tej procedury, aby zastosować nowy certyfikat w przypadku wygaśnięcia certyfikatu zaplecza.

1. Pobierz bramę aplikacji do zaktualizowania.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Dodaj nowy zasób certyfikatu z pliku cer, który zawiera klucz publiczny certyfikatu i może być również tym samym certyfikatem, który został dodany do odbiornika protokołu TLS w usłudze Application Gateway.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Pobierz nowy obiekt certyfikatu uwierzytelniania do zmiennej (TypeName: Microsoft. Azure. Commands. Network. models. PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Przypisz nowy certyfikat do ustawienia **BackendHttp** i odwołaj się do niego przy użyciu zmiennej $AuthCert. (Określ nazwę ustawienia HTTP, które chcesz zmienić).
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Zatwierdź zmianę w bramie aplikacji i przekaż nową konfigurację znajdującą się w zmiennej $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Usuń nieużywany wygasły certyfikat z ustawień protokołu HTTP

Użyj tej procedury, aby usunąć nieużywany wygasły certyfikat z ustawień protokołu HTTP.

1. Pobierz bramę aplikacji do zaktualizowania.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Wyświetl nazwę certyfikatu uwierzytelniania, który chcesz usunąć.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Usuń certyfikat uwierzytelniania z bramy aplikacji.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Zatwierdź zmianę.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Ogranicz wersje protokołu TLS w istniejącej bramie aplikacji

Powyższe kroki przeprowadziły przez proces tworzenia aplikacji z kompleksowym protokołem TLS i wyłączania określonych wersji protokołu TLS. Poniższy przykład powoduje wyłączenie pewnych zasad protokołu TLS w istniejącej bramie aplikacji.

1. Pobierz bramę aplikacji do zaktualizowania.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Zdefiniuj zasady protokołu TLS. W poniższym przykładzie **TLSv 1.0** i **TLSv 1.1** są wyłączone, a mechanizm szyfrowania **TLS \_ \_ \_ \_ \_ 128 \_ \_** GCM SHA256, **TLS \_ ECDHE \_ ECDSA \_ z \_ algorytmem AES \_ 256 \_ GCM \_ SHA384** i **TLS \_ \_ \_ \_ 128 \_ \_** GCM SHA256 to jedyne dozwolone.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Na koniec zaktualizuj bramę. Ten ostatni krok to długotrwałe zadanie. Gdy to zrobisz, na bramie aplikacji zostanie skonfigurowany protokół TLS-to-end.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Pobieranie nazwy DNS bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu do komunikacji. Application Gateway wymaga dynamicznie przypisanej nazwy DNS podczas korzystania z publicznego adresu IP, co jest niezrozumiałe. Aby użytkownicy końcowi mogli korzystać z bramy aplikacji, można użyć rekordu CNAME do wskazywania publicznego punktu końcowego bramy aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Aby skonfigurować alias, Pobierz szczegóły bramy aplikacji i skojarzonej z nią nazwy IP/DNS przy użyciu elementu **PublicIPAddress** dołączonego do bramy aplikacji. Użyj nazwy DNS bramy aplikacji, aby utworzyć rekord CNAME wskazujący dwie aplikacje sieci Web na tę nazwę DNS. Nie zaleca się używania rekordów A, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wzmacniania zabezpieczeń aplikacji sieci Web przy użyciu zapory aplikacji sieci Web za pomocą usługi Application Gateway, zobacz [Omówienie zapory aplikacji sieci Web](../web-application-firewall/ag/ag-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png