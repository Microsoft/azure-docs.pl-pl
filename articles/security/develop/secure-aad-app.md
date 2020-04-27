---
title: Tworzenie bezpiecznej aplikacji sieci Web usługi Azure AD | Microsoft Docs
description: Ta prosta Przykładowa aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które ulepszają aplikację i stan bezpieczeństwa organizacji podczas opracowywania na platformie Azure.
keywords: nie dotyczy
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 58deae64bdde27580b85b00a1c87ba115e24478a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159902"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Opracowywanie bezpiecznej aplikacji dla aplikacji usługi Azure AD
## <a name="overview"></a>Omówienie

Ten przykład to prosta Azure Active Directory z aplikacją internetową, która łączy się z zasobami zabezpieczeń na potrzeby tworzenia aplikacji na platformie Azure. Aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które mogą pomóc w ulepszaniu aplikacji i stan bezpieczeństwa organizacji podczas opracowywania aplikacji na platformie Azure.

Skrypty wdrażania skonfigurują infrastrukturę. Po uruchomieniu skryptów wdrażania należy wykonać ręczną konfigurację w Azure Portal, aby połączyć składniki i usługi ze sobą. Ten przykład jest przeznaczony dla doświadczonych deweloperów na platformie Azure, którzy pracują w branży detalicznej i chcą stworzyć zabezpieczony Azure Active Directory za pomocą bezpiecznej infrastruktury platformy Azure. 


W opracowywaniu i wdrażaniu tej aplikacji dowiesz się, jak 
- Utwórz wystąpienie Azure Key Vault, Zapisz i Pobierz wpisy tajne.
- Wdróż aplikację internetową platformy Azure, która jest dedykowana izolowana od dostępu do zapory frontonu. 
- Utwórz i skonfiguruj wystąpienie usługi Azure Application Gateway przy użyciu zapory korzystającej z OWASP 10 najważniejszych zestawów reguł. 
- Włącz szyfrowanie danych podczas przesyłania i w spoczynku przy użyciu usług platformy Azure. 
- Skonfiguruj usługę Azure Policy i Security Center, aby oszacować compliancies. 

Po utworzeniu i wdrożeniu tej aplikacji skonfigurujesz następującą przykładową aplikację internetową wraz z opisanymi przez siebie środkami konfiguracji i zabezpieczeń.

## <a name="architecture"></a>Architektura
Aplikacja jest typową aplikacją n-warstwową z trzema warstwami. Warstwy frontonu, zaplecza i warstwa bazy danych ze zintegrowanymi składnikami monitorowania i zarządzania kluczami tajnymi przedstawiono tutaj:

![Architektura aplikacji](./media/secure-aad-app/architecture.png)

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji architektura wdrażania. 

Architektura obejmuje te składniki

- [Application Gateway platformy Azure](../../application-gateway/index.yml). Zapewnia bramę i zaporę dla naszej architektury aplikacji.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Zapewnia rozszerzalną usługę zarządzania wydajnością aplikacji (APM) na wielu platformach.
- [Azure Key Vault](../../key-vault/index.yml). Przechowuje i szyfruje wpisy tajne aplikacji i zarządza tworzeniem zasad dostępu.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Zapewnia usługi zarządzania tożsamościami i dostępem w chmurze, logowania i uzyskiwania dostępu do zasobów.
- [System nazw domen platformy Azure](../../dns/dns-overview.md). Podaj usługę do hostowania domeny.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Umożliwia skalowanie aplikacji i tworzenie wysokiej dostępności dla usług.
- [Aplikacja internetowa platformy Azure](../../app-service/overview.md).  Zapewnia usługę opartą na protokole HTTP do hostowania aplikacji sieci Web.
- [Azure Security Center](../../security-center/index.yml). zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze.
- [Azure Policy](../../governance/policy/overview.md). Zapewnia ocenę zasobów pod kątem braku zgodności z przypisanymi zasadami.

## <a name="threat-model"></a>Model zagrożeń
Modelowanie zagrożeń polega na zidentyfikowaniu potencjalnych zagrożeń bezpieczeństwa dla Twojej firmy i aplikacji, a następnie zapewnieniu prawidłowego planu zaradczego.

W tym przykładzie użyto [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) do zaimplementowania modelowania zagrożeń dla bezpiecznej przykładowej aplikacji. Dzięki utworzeniu diagramu składników i przepływów danych można wcześnie identyfikować problemy i zagrożenia w procesie tworzenia oprogramowania. Czas i pieniądze zostaną zapisane później za pomocą tego polecenia.

Oto model zagrożeń dla przykładowej aplikacji

![Model zagrożeń](./media/secure-aad-app/threat-model.png)

Niektóre przykładowe zagrożenia i potencjalne luki w zabezpieczeniach generowane przez narzędzie do modelowania zagrożeń przedstawiono na poniższym zrzucie ekranu. Model zagrożeń zawiera przegląd narażonych obszarów ataków i umożliwia deweloperom zaprezentowanie informacji o sposobach łagodzenia problemów.

![Dane wyjściowe modelu zagrożeń](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Wymagania wstępne
Aby można było uruchomić aplikację, należy zainstalować następujące narzędzia:

- Edytor kodu służący do modyfikowania i wyświetlania kodu aplikacji. [Visual Studio Code](https://code.visualstudio.com/) to opcja "open source".
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) na komputerze deweloperskim.
- System [git](https://git-scm.com/) w systemie. Git służy do lokalnego klonowania kodu źródłowego.
- [JQ](https://stedolan.github.io/jq/), narzędzie systemu UNIX do wykonywania zapytań w formacie JSON w sposób przyjazny dla użytkownika.

Do wdrożenia zasobów przykładowej aplikacji potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/) w celu przetestowania przykładowej aplikacji.

Po zainstalowaniu tych narzędzi możesz przystąpić do wdrażania aplikacji na platformie Azure.

### <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji
Skrypt wdrażania to jeden skrypt, który można podzielić na cztery etapy. Każda faza wdraża i konfiguruje zasób platformy Azure, który znajduje się na [diagramie architektury](#architecture).

Cztery etapy to

- Wdróż Azure Key Vault.
- Wdróż Web Apps platformy Azure.
- Wdróż Application Gateway za pomocą zapory aplikacji sieci Web.
- Skonfiguruj usługę Azure AD za pomocą wdrożonej aplikacji.

Każda faza kompiluje się w poprzednim z nich przy użyciu konfiguracji z wcześniej wdrożonych zasobów.

Aby wykonać kroki implementacji, upewnij się, że zainstalowano narzędzia wymienione w sekcji [wymagania wstępne](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Wdróż Azure Key Vault
W tej sekcji utworzysz i wdrażasz wystąpienie Azure Key Vault, które jest używane do przechowywania wpisów tajnych i certyfikatów.

Po zakończeniu wdrażania masz Azure Key Vault wystąpienie wdrożone na platformie Azure.

Aby wdrożyć Azure Key Vault przy użyciu programu PowerShell
 
1. Zadeklaruj zmienne dla Azure Key Vault.
2. Zarejestruj dostawcę Azure Key Vault.
3. Utwórz grupę zasobów dla tego wystąpienia.
4. Utwórz wystąpienie Azure Key Vault w grupie zasobów utworzonej w kroku 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Poniższy użytkownik usługi Azure AD będzie miał uprawnienia administratora do Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Rejestracja AZ Providers
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Tworzenie wystąpienia Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Dodaj zasady administratora do Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Aby utworzyć zasady dostępu pozwalające użytkownikowi na pobieranie kluczy kryptograficznych, certyfikatów i wpisów tajnych, jeśli znasz główną nazwę użytkownika:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Najlepszym rozwiązaniem jest użycie zarządzanych tożsamości dla zasobów platformy Azure w aplikacjach korzystających z Key Vault w celu uzyskania dostępu do zasobów. Stan zabezpieczeń zwiększa się, gdy klucze dostępu do Key Vault nie są przechowywane w kodzie ani w konfiguracji.

Certyfikat główny znajduje się w kontenerze. Kroki podejmowane w celu uzyskania certyfikatu to

1. Pobierz plik certyfikatu z [urzędu certyfikacji](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Dekodowanie pliku certyfikatu:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Ten skrypt tworzy przypisaną tożsamość dla wystąpienia App Service, które może być używane z programem MSI do współpracy z Azure Key Vault bez wprowadzania stałych kodów tajnych w kodzie lub konfiguracji.

Przejdź do wystąpienia Azure Key Vault w portalu, aby autoryzować przypisaną tożsamość na karcie zasady dostępu. Wybierz pozycję **Dodaj nowe zasady dostępu**. W obszarze **Wybierz podmiot zabezpieczeń**Wyszukaj nazwę aplikacji podobną do nazwy utworzonego wystąpienia App Service.
Nazwa główna usługi dołączona do aplikacji powinna być widoczna. Wybierz ją i Zapisz zasady dostępu, jak pokazano na poniższym zrzucie ekranu.

Ponieważ aplikacja wymaga tylko pobrania kluczy, wybierz uprawnienie **Get** w opcjach wpisów tajnych, co umożliwia dostęp przy jednoczesnym zmniejszeniu przyznanych uprawnień.

![Zasady dostępu Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Tworzenie zasad dostępu Key Vault*

Zapisz zasady dostępu, a następnie Zapisz nową zmianę na karcie **zasady dostępu** , aby zaktualizować zasady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Wdróż Application Gateway z włączoną zaporą aplikacji sieci Web
W usłudze Web Apps nie zaleca się ujawniania usług bezpośrednio w świecie zewnętrznym w Internecie.
Równoważenie obciążenia i reguły zapory zapewniają większe bezpieczeństwo i kontrolę nad ruchem przychodzącym oraz ułatwia zarządzanie nim.

Aby wdrożyć wystąpienie Application Gateway

1. Utwórz grupę zasobów, w której znajduje się Brama aplikacji.
2. Zainicjuj obsługę sieci wirtualnej w celu dołączenia do bramy.
3. Utwórz podsieć dla bramy w sieci wirtualnej.
4. Zapewnij publiczny adres IP.
5. Zainicjuj obsługę administracyjną bramy aplikacji.
6. Włącz zaporę aplikacji sieci Web na bramie.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Wdrażanie Web Apps platformy Azure
Azure App Service umożliwia tworzenie i hostowanie aplikacji sieci Web przy użyciu języków, takich jak Python, Ruby, C# i Java. Platforma Azure obsługuje również kontenery niestandardowe, co pozwala na uruchamianie praktycznie wszystkich języków programowania na platformie Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Utwórz plan App Service w warstwie Bezpłatna
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Przed kontynuowaniem przejdź do interfejsu użytkownika konfiguracji systemu nazw domen platformy Azure dla domeny niestandardowej i postępuj zgodnie z https://aka.ms/appservicecustomdns instrukcjami w obszarze w celu skonfigurowania rekordu CNAME dla nazwy hosta "www" i wskaż nazwę domyślnej domeny aplikacji sieci Web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Uaktualnij plan App Service do warstwy udostępnionej (minimum wymagane przez domeny niestandardowe)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Dodaj niestandardową nazwę domeny do aplikacji sieci Web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="network"></a>Sieć
Po zakończeniu wdrażania masz bramę aplikacji z włączoną zaporą aplikacji sieci Web.

Wystąpienie bramy uwidacznia port 443 dla protokołu HTTPS. Ta konfiguracja gwarantuje, że nasza aplikacja jest dostępna tylko na porcie 443 za pośrednictwem protokołu HTTPS.

Najlepszym rozwiązaniem w zakresie zabezpieczeń jest blokowanie nieużywanych portów i ograniczanie ekspozycji narażonych na ataki.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Dodawanie sieciowych grup zabezpieczeń do wystąpienia App Service

Wystąpienia App Service mogą być zintegrowane z sieciami wirtualnymi. Ta Integracja umożliwia skonfigurowanie zasad grupy zabezpieczeń sieci, które zarządzają ruchem przychodzącym i wychodzącym aplikacji.

1. Aby włączyć tę funkcję, w bloku wystąpienie usługi Azure App Service w obszarze **Ustawienia**wybierz pozycję **Sieć**. W okienku po prawej stronie Skonfiguruj pozycję **integracja**z siecią wirtualną.

   ![Nowa integracja sieci wirtualnej](./media/secure-web-app/app-vnet-menu.png)

    *Nowa integracja sieci wirtualnej dla App Service*
1. Na następnej stronie wybierz pozycję **Dodaj sieć wirtualną (wersja zapoznawcza)**.

1. W następnym menu wybierz sieć wirtualną utworzoną w ramach wdrożenia, które zaczyna się od `aad-vnet`. Można utworzyć nową podsieć lub wybrać istniejącą.
   W takim przypadku należy utworzyć nową podsieć. Ustaw **zakres adresów** na **10.0.3.0/24** i nadaj nazwę podsieci **aplikacji**podsieć.

   ![App Service konfigurację sieci wirtualnej](./media/secure-web-app/app-vnet-config.png)

    *Konfiguracja sieci wirtualnej dla App Service*

Po włączeniu integracji sieci wirtualnej możesz dodać sieciowe grupy zabezpieczeń do naszej aplikacji.

1. Użyj pola wyszukiwania, aby wyszukać **sieciowe grupy zabezpieczeń**. W wynikach wybierz pozycję **sieciowe grupy zabezpieczeń** .

    ![Wyszukaj sieciowe grupy zabezpieczeń](./media/secure-web-app/nsg-search-menu.png)

    *Wyszukaj sieciowe grupy zabezpieczeń*

2. W następnym menu wybierz pozycję **Dodaj**. Wprowadź **nazwę** sieciowej grupy zabezpieczeń i **grupę zasobów** , w której ma się znajdować. Ta sieciowej grupy zabezpieczeń zostanie zastosowana do podsieci bramy aplikacji.

    ![Utwórz sieciowej grupy zabezpieczeń](./media/secure-web-app/nsg-create-new.png)

    *Utwórz sieciowej grupy zabezpieczeń*

3. Po utworzeniu sieciowej grupy zabezpieczeń wybierz ją. W jego bloku w obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**. Skonfiguruj te ustawienia, aby zezwalać na połączenia przychodzące do bramy aplikacji przez port 443.

   ![Konfigurowanie sieciowej grupy zabezpieczeń](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurowanie sieciowej grupy zabezpieczeń*

4. W regułach ruchu wychodzącego dla bramy sieciowej grupy zabezpieczeń Dodaj regułę, która zezwala na połączenia wychodzące z wystąpieniem App Service, tworząc regułę, która odwołuje się do tagu usługi.`AppService`

   ![Dodaj reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Dodaj reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń*

    Dodaj kolejną regułę ruchu wychodzącego, aby umożliwić bramie wysyłanie reguł ruchu wychodzącego do sieci wirtualnej.

   ![Dodaj kolejną regułę ruchu wychodzącego](./media/secure-web-app/nsg-outbound-vnet.png)

    *Dodaj kolejną regułę ruchu wychodzącego*

5. W bloku podsieci sieciowej grupy zabezpieczeń wybierz opcję **Skojarz**, wybierz sieć wirtualną utworzoną we wdrożeniu i wybierz podsieć bramy o nazwie **GW-Subnet**. SIECIOWEJ grupy zabezpieczeń jest stosowana do podsieci.

6. Utwórz kolejną sieciowej grupy zabezpieczeń jak w poprzednim kroku, tym razem z wystąpieniem App Service. Nadaj mu nazwę. Dodaj regułę ruchu przychodzącego dla portu 443, jak w przypadku usługi Application Gateway sieciowej grupy zabezpieczeń.

   Jeśli masz wystąpienie App Service wdrożone w wystąpieniu App Service Environment, które nie jest używane w przypadku tej aplikacji, możesz dodać reguły ruchu przychodzącego, aby umożliwić Azure Service Healthe sondy przez otwarcie portów 454-455 w przychodzących grupach zabezpieczeń App Service sieciowej grupy zabezpieczeń. Oto konfiguracja:

   ![Dodaj reguły dla sond Azure Service Health](./media/secure-web-app/nsg-create-healthprobes.png)

    *Dodaj reguły dla sond Azure Service Health (tylko App Service Environment)*

Aby ograniczyć obszar narażony na ataki, zmodyfikuj ustawienia sieci App Service tak, aby zezwalały na dostęp do aplikacji tylko bramie aplikacji.
Aby zastosować ustawienia, przejdź do karty sieć App Service, wybierz kartę **Ograniczenia adresów IP** i Utwórz regułę zezwalania, która zezwala na bezpośredni dostęp do usługi za pomocą adresu IP bramy aplikacji. Adres IP bramy można pobrać ze strony przeglądowej. Na karcie **adres IP CIDR** wprowadź adres IP w tym formacie: `<GATEWAY_IP_ADDRESS>/32`.

![Zezwalaj tylko na bramę](./media/secure-web-app/app-allow-gw-only.png)

*Zezwalanie na dostęp do App Service tylko za pomocą adresu IP bramy*

### <a name="azure-domain-name-system"></a>System nazw domen platformy Azure 
System nazw domen platformy Azure lub system nazw domen Azure jest odpowiedzialny za tłumaczenie (lub rozwiązanie) nazwy witryny sieci Web lub usługi na adres IP. System nazw domen platformy Azurehttps://docs.microsoft.com/azure/dns/dns-overview) (to usługa hostingu domen systemu nazw domen, która zapewnia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure pozwala użytkownikom na Zarządzanie rekordami systemu nazw domen przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. System nazw domen platformy Azure obsługuje również prywatne domeny systemu nazw domen.

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
Azure Disk Encryption korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Poniższe technologie zapewniają możliwości zarządzania dostępem do danych posiadaczy kart w środowisku platformy Azure
- Azure Active Directory to oparta na chmurze usługa zarządzania tożsamościami i tożsamość firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do usługi Azure WebApp.
- Kontrola dostępu oparta na rolach na platformie Azure umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych posiadaczy tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- Azure Active Directory Privileged Identity Management umożliwia klientom zminimalizowanie liczby użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane posiadaczy kart. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- Azure Active Directory Identity Protection wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.
### <a name="secrets-management"></a>Zarządzanie wpisami tajnymi
Rozwiązanie używa Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić takie dane i uzyskiwać do nich dostęp
   - Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
   - Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
   - Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
   - Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza to sprzętowy moduł zabezpieczeń (HSM) chroniony 2048-bitowy klucz RSA.
   - Za pomocą Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych). Pliki i hasła PFX) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). 
   - Użyj Access Control opartej na rolach (RBAC) do przypisywania uprawnień użytkownikom, grupom i aplikacjom w określonym zakresie.     
   - Użyj Key Vault do zarządzania certyfikatami TLS przy użyciu autoodnawiania. 
   - Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
   - Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.
### <a name="azure-security-center"></a>Azure Security Center
Dzięki Azure Security Center klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto 
   - Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.
   - Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw wstępnie zdefiniowanych alertów zabezpieczeń, które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. Niestandardowe reguły alertów w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.
   - Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. Raport analizy zagrożeń jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway platformy Azure z skonfigurowaną zaporą aplikacji sieci Web, a zestaw reguł OWASP jest włączony. Dodatkowe możliwości obejmują
   - Kompleksowa protokół TLS.
   - Wyłącz protokół TLS w wersji 1.0 i 1.1.
   - Włącz TLSv 1.2.
   - Zapora aplikacji sieci Web (tryb zapobiegania).
   - Tryb zapobiegania z zestawem reguł OWASP 3,0.
   - Włącz rejestrowanie diagnostyczne.
   - Niestandardowe sondy kondycji.
   - Azure Security Center i Azure Advisor zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.
### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji
Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
   - Dzienniki aktywności: [dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
   - Dzienniki diagnostyczne: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.
### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
   Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są zorganizowane w oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

   Następujące rozwiązania do [monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) platformy Azure są dołączone jako część tej architektury

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): rozwiązanie Active Directory Health Check ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
   - [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, i liczbę agentów, które przesyłają dane operacyjne.
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna usługa zarządzania wydajnością aplikacji dla deweloperów sieci Web na wielu platformach. Application Insights wykrywa anomalie wydajności i klienci mogą używać jej do monitorowania działającej aplikacji sieci Web. Zawiera ona zaawansowane narzędzia analityczne, które ułatwiają klientom diagnozowanie problemów i zrozumienie, jakie użytkownicy faktycznie robią z ich aplikacjami. Zaprojektowano, aby pomóc klientom w ciągłym ulepszaniu wydajności i użyteczności.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault
   Utwórz magazyn dla organizacji, w której mają być przechowywane klucze, i obsługuj odpowiedzialności za zadania operacyjne, takie jak poniżej

   - Dane przechowywane w Key Vault obejmują   
   - Klucz usługi Application Insight
   - Klucz dostępu do magazynu danych
   - Parametry połączenia
   - Nazwa tabeli danych
   - Poświadczenia użytkownika
   - Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb
   - Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych
   - Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia
   - Wszystkie klucze w Key Vault są chronione przez sprzętowy moduł zabezpieczeń (HSM) [typ klucza = sprzętowy moduł zabezpieczeń (HSM) chroniony       
     2048-bitowy klucz RSA]
   - Wszyscy użytkownicy/tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu Access Control opartego na rolach (RBAC)
   - Aplikacje nie udostępniają Key Vault, chyba że ufają sobie nawzajem i potrzebują dostępu do tych samych wpisów tajnych w czasie wykonywania
   - Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
   - Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych wymaganych

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
   Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) wymagany do skonfigurowania przez bezpieczne ustanowienie połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

   Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. Tryb tunelowania IPsec jest używany w tej opcji jako mechanizm szyfrowania.

   Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

   [Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Zaimplementuj Azure Active Directory OIDC

1. Aby sklonować repozytorium kodu źródłowego, użyj tego polecenia git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Aktualizowanie adresów URL przekierowania
1.  Przejdź z powrotem do Azure Portal. W okienku nawigacji po lewej stronie wybierz usługę Azure Active Directory a następnie wybierz pozycję Rejestracje aplikacji.
2.  Na ekranie wynikowym wybierz aplikację WebApp-OpenIDConnect-DotNet-Code-v2.
3.  Na karcie uwierzytelnianie w sekcji identyfikatory URI przekierowania wybierz pozycję Sieć Web w polu kombi i Dodaj następujące identyfikatory URI przekierowania.
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc`
    o w sekcji Ustawienia zaawansowane Ustaw adres URL wylogowywania na`https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc`
4.  Na karcie znakowanie o aktualizacji adresu URL strony głównej na adres usługi App Service, na przykład `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`.
        o Zapisz konfigurację.
5.  Jeśli aplikacja wywołuje internetowy interfejs API, upewnij się, że zastosowano niezbędne zmiany w projekcie appSettings. JSON, więc wywoła opublikowany adres URL API zamiast localhost.
Publikowanie przykładu
    1.  Na karcie Omówienie App Service Pobierz profil publikowania, klikając link Pobierz profil publikowania i Zapisz go. Można również użyć innych mechanizmów wdrażania, takich jak z kontroli źródła.
    2.  Przełącz się do programu Visual Studio i przejdź do projektu WebApp-OpenIDConnect-DotNet-Code-v2. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie Publikuj. Kliknij przycisk Importuj profil na dolnym pasku i zaimportuj wcześniej pobrany profil publikacji.
    3.  Kliknij pozycję Konfiguruj i na karcie połączenie zaktualizuj docelowy adres URL, aby był to adres https na stronie sieci Web na przykład `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net`. Kliknij przycisk Dalej.
    4.  Na karcie Ustawienia upewnij się, że nie wybrano opcję Włącz uwierzytelnianie organizacyjne. Kliknij pozycję Zapisz. Kliknij pozycję Publikuj na ekranie głównym.
    5.  Program Visual Studio opublikuje projekt i automatycznie otworzy w przeglądarce adres URL projektu. Jeśli zostanie wyświetlona domyślna strona sieci Web projektu, publikacja zakończyła się pomyślnie.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Zaimplementuj Multi-Factor Authentication dla Azure Active Directory
   Administratorzy muszą upewnić się, że konta subskrypcji w portalu są chronione. Subskrypcja jest narażona na ataki, ponieważ zarządza utworzonymi przez siebie zasobami. Aby chronić subskrypcję, Włącz Multi-Factor Authentication na karcie **Azure Active Directory** subskrypcji.

   Usługa Azure AD działa na podstawie zasad, które są stosowane do użytkowników lub grup użytkowników spełniających określone kryteria.
Platforma Azure tworzy domyślne zasady określające, że administratorzy muszą mieć uwierzytelnianie dwuskładnikowe, aby zalogować się do portalu.
Po włączeniu tych zasad może zostać wyświetlony monit o wylogowanie się i ponowne zalogowanie do Azure Portal.

Aby włączyć usługę MFA dla logowania administratora

   1. Przejdź do karty **Azure Active Directory** w Azure Portal
   2. W kategorii zabezpieczenia wybierz pozycję dostęp warunkowy. Zobaczysz ten ekran

       ![Dostęp warunkowy — zasady](./media/secure-aad-app/ad-mfa-conditional-add.png)

Jeśli nie możesz utworzyć nowych zasad

   1. Przejdź do karty **MFA** .
   2. Wybierz link Azure AD — wersja Premium **bezpłatnych wersji próbnych** , aby subskrybować bezpłatną wersję próbną.

   ![Bezpłatna wersja próbna Azure AD — wersja Premium](./media/secure-aad-app/ad-trial-premium.png)

Wróć do ekranu dostęp warunkowy.

   1. Wybierz kartę nowe zasady.
   2. Wprowadź nazwę zasad.
   3. Wybierz użytkowników lub grupy, dla których chcesz włączyć usługę MFA.
   4. W obszarze **kontroli dostępu**wybierz kartę **Grant (Udziel** ), a następnie wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego** (i inne ustawienia, jeśli chcesz).

   ![Wymaganie usługi MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Zasady można włączyć, zaznaczając pole wyboru w górnej części ekranu lub na karcie **dostęp warunkowy** . Gdy zasady są włączone, użytkownicy potrzebują uwierzytelniania wieloskładnikowego w celu zalogowania się do portalu.

   Istnieją zasady podstawowe, które wymagają uwierzytelniania wieloskładnikowego dla wszystkich administratorów platformy Azure. Możesz ją natychmiast włączyć w portalu. Włączenie tych zasad może unieważnić bieżącą sesję i wymusić ponowne zalogowanie.

   Jeśli zasady bazowe nie są włączone
   1.   Wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego dla administratorów**.
   2.   Wybierz pozycję **Użyj zasad natychmiast**.

   ![Wybierz pozycję Użyj zasad natychmiast](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Monitorowanie aplikacji i zasobów przy użyciu wskaźnikowego platformy Azure

   W miarę zwiększania się aplikacji trudno jest agregować wszystkie sygnały zabezpieczeń i metryki otrzymane z zasobów i zwiększyć ich użyteczność w sposób zorientowany na działania.

   Wskaźnik na platformie Azure jest przeznaczony do zbierania danych, wykrywania możliwych typów zagrożeń i zapewnienia wglądu w zdarzenia związane z bezpieczeństwem.
Podczas oczekiwania na ręczną interwencję Wskaźnik produkcyjny platformy Azure może polegać na przedpisanych elementy playbookie, aby uruchomić alerty i procesy zarządzania zdarzeniami.

   Przykładowa aplikacja składa się z kilku zasobów, które mogą być monitorowane przez platformę Azure.
Aby skonfigurować platformę Azure, musisz najpierw utworzyć obszar roboczy Log Analytics, w którym są przechowywane wszystkie dane zebrane z różnych zasobów.

Aby utworzyć ten obszar roboczy

   1. W polu wyszukiwania w Azure Portal Wyszukaj ciąg **log Analytics**. Wybierz **log Analytics obszary robocze**.

   ![Wyszukaj Log Analytics obszary robocze](./media/secure-aad-app/sentinel-log-analytics.png)

   *Wyszukaj Log Analytics obszary robocze*

   2. Na następnej stronie wybierz pozycję **Dodaj** , a następnie podaj nazwę, grupę zasobów i lokalizację dla obszaru roboczego.
   ![Tworzenie obszaru roboczego usługi Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Tworzenie obszaru roboczego usługi Log Analytics*

   3. Użyj pola wyszukiwania, aby wyszukać **platformę Azure**.

   ![Wyszukiwanie ciągu „Azure Sentinel”](./media/secure-aad-app/sentinel-add.png)

   *Wyszukiwanie ciągu „Azure Sentinel”*

   4. Wybierz pozycję **Dodaj** , a następnie wybierz utworzony wcześniej obszar roboczy log Analytics.

   ![Dodawanie Log Analyticsego obszaru roboczego](./media/secure-aad-app/sentinel-workspace-add.png)

   *Dodawanie Log Analyticsego obszaru roboczego*

   5. Na stronie **Łączniki danych platformy Azure —** w obszarze **Konfiguracja**wybierz pozycję **Łączniki danych**. Zostanie wyświetlona tablica usług platformy Azure, którą można połączyć z wystąpieniem magazynu Log Analytics, aby przeprowadzić analizę na platformie Azure.

   ![Łączniki danych Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Dodawanie łącznika danych do usługi Azure wskaźnikowego*

   Aby na przykład połączyć się z bramą aplikacji, wykonaj następujące czynności:

   1. Otwórz blok wystąpienia usługi Azure Application Gateway.
   2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
   3. Wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   ![Dodawanie Application Gateway diagnostyki](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Dodawanie Application Gateway diagnostyki*

   4. Na stronie **Ustawienia diagnostyczne** wybierz utworzony przez siebie obszar roboczy log Analytics a następnie wybierz wszystkie metryki, które mają być zbierane i wysyłane do funkcji wskaźnikowej platformy Azure. Wybierz pozycję **Zapisz**.

   ![Ustawienia łącznika wskaźnikowego platformy Azure](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Kwestie związane z kosztami
   Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć bezpłatny. Przejdź do [strony bezpłatnego konta](https://azure.microsoft.com/free/) , aby rozpocząć pracę, zobacz co możesz zrobić za pomocą bezpłatnego konta platformy Azure i Dowiedz się, które produkty są bezpłatne przez 12 miesięcy.

   Aby wdrożyć zasoby w przykładowej aplikacji z funkcjami zabezpieczeń, musisz uregulować pewne funkcje w warstwie Premium. Gdy aplikacja jest skalowana i warstwy bezpłatne i wersje próbne oferowane przez platformę Azure muszą zostać uaktualnione w celu spełnienia wymagań aplikacji, koszty mogą ulec zwiększeniu. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure, aby oszacować swoje koszty.

## <a name="next-steps"></a>Następne kroki
   Poniższe artykuły mogą pomóc w projektowaniu, projektowaniu i wdrażaniu bezpiecznych aplikacji.

- [Projektowanie](secure-design.md)
- [Programowanie](secure-develop.md)
- [Wdróż](secure-deploy.md)
