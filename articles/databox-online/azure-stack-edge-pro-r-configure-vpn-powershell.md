---
title: Konfigurowanie sieci VPN na urządzeniu Azure Stack EDGE Pro przy użyciu Azure PowerShell
description: Opisuje sposób konfigurowania sieci VPN na urządzeniu z systemem Azure Stack EDGE Pro przy użyciu skryptu Azure PowerShell do tworzenia zasobów platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634179"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Skonfiguruj sieć VPN na urządzeniu Azure Stack EDGE Pro urządzenie R za pośrednictwem Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

Opcja sieci VPN zapewnia drugą warstwę szyfrowania dla danych w ruchu over *TLS* z urządzenia Azure Stack EDGE Pro na platformie Azure. Sieć VPN można skonfigurować na urządzeniu Azure Stack EDGE Pro za pośrednictwem Azure Portal lub za pośrednictwem Azure PowerShell.

W tym artykule opisano kroki wymagane do skonfigurowania sieci VPN na urządzeniu z systemem Azure Stack EDGE Pro przy użyciu Azure PowerShell w celu utworzenia konfiguracji w chmurze.

## <a name="about-vpn-setup"></a>Informacje o konfiguracji sieci VPN

Połączenie sieci VPN między lokalizacjami składa się z bramy sieci VPN platformy Azure, lokalnego urządzenia sieci VPN oraz tunelu VPN S2S IPsec łączącego dwa. Typowy przepływ pracy obejmuje następujące kroki:

- Konfigurowanie wymagań wstępnych.
- Konfigurowanie niezbędnych zasobów na platformie Azure.
    - Utwórz i skonfiguruj usługę Azure VPN Gateway (Brama sieci wirtualnej).
    - Utwórz i skonfiguruj bramę sieci lokalnej platformy Azure, która reprezentuje lokalną sieć i urządzenie sieci VPN.
    - Utwórz i skonfiguruj połączenie sieci VPN platformy Azure między bramą sieci VPN platformy Azure i bramą sieć lokalną.
    - Konfigurowanie zapory platformy Azure i dodawanie reguł sieci i aplikacji.
    - Tworzenie tabeli routingu platformy Azure i Dodawanie tras.
- Skonfiguruj sieć VPN w lokalnym interfejsie użytkownika sieci Web urządzenia. Można skonfigurować lokalne urządzenie sieci VPN reprezentowane przez bramę sieci lokalnej w celu ustalenia rzeczywistego tunelu sieci VPN S2S przy użyciu bramy sieci VPN platformy Azure.

Szczegółowe kroki przedstawiono w poniższych sekcjach.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych

1. Należy mieć dostęp do urządzenia z Azure Stack EDGE Pro R, które zostało zainstalowane zgodnie z instrukcjami podanymi w temacie [Install the Azure Stack EDGE Pro r Device](azure-stack-edge-pro-r-deploy-install.md). To urządzenie będzie działać jako lokalne urządzenie sieci VPN w celu utworzenia połączenia sieci VPN z platformą Azure. 

2. Urządzenie sieci VPN powinno mieć statyczny publiczny adres IP (zewnętrzny). Ten adres nie powinien być translatorem adresów sieciowych.

3. Należy mieć dostęp do prawidłowej subskrypcji platformy Azure, która jest włączona dla usługi Azure Stack Edge na platformie Azure. Użyj tej subskrypcji, aby utworzyć odpowiedni zasób na platformie Azure do zarządzania urządzeniem z systemem Azure Stack EDGE Pro R.  

4. Masz dostęp do klienta systemu Windows, który będzie używany do uzyskiwania dostępu do urządzenia z usługą Azure Stack EDGE Pro R. Ten klient będzie używany do programowego tworzenia konfiguracji w chmurze.

    1. Aby zainstalować wymaganą wersję programu PowerShell na kliencie systemu Windows, uruchom następujące polecenia:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Aby nawiązać połączenie z kontem i subskrypcją platformy Azure, uruchom następujące polecenia:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Podaj nazwę subskrypcji platformy Azure używaną na urządzeniu z Azure Stack EDGE Pro R, aby skonfigurować sieć VPN.

    3. [Pobierz skrypt](https://aka.ms/ase-vpn-deployment) wymagany do utworzenia konfiguracji w chmurze. Skrypt:
        
        - Utwórz sieć wirtualną platformy Azure oraz następujące podsieci: *GatewaySubnet* i *AzureFirewallSubnet*.
        - Utwórz i skonfiguruj bramę sieci VPN platformy Azure.
        - Utwórz i skonfiguruj bramę sieci lokalnej platformy Azure.
        - Utwórz i skonfiguruj połączenie sieci VPN platformy Azure między bramą sieci VPN platformy Azure i bramą sieć lokalną.
        - Utwórz zaporę platformy Azure i Dodaj reguły sieci, reguły aplikacji.
        - Utwórz tabelę routingu platformy Azure i Dodaj do niej trasy.


## <a name="use-the-script"></a>Używanie skryptu

Najpierw należy zmodyfikować `parameters.json` plik, aby wprowadzić parametry. Następnie uruchom skrypt przy użyciu zmodyfikowanego pliku JSON.

Każdy z tych kroków został omówiony w poniższych sekcjach.

### <a name="download-service-tags-file"></a>Pobierz plik tagów usługi

Być może masz już `ServiceTags.json` plik w folderze, do którego został pobrany skrypt. W przeciwnym razie można pobrać plik tagów usługi.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Modyfikuj plik parametrów

Pierwszym krokiem jest zmodyfikowanie `parameters.json` pliku i zapisanie zmian. 


W przypadku utworzonych zasobów platformy Azure podaj następujące nazwy:

|Nazwa parametru  |Opis  |
|---------|---------|
|virtualNetworks_vnet_name    | Nazwa Virtual Network platformy Azure        |
|azureFirewalls_firewall_name     | Nazwa zapory platformy Azure        |
|routeTables_routetable_name     | Nazwa tabeli tras platformy Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Nazwa publicznego adresu IP dla bramy sieci wirtualnej       |
|virtualNetworkGateways_VNGW_name    | Nazwa usługi Azure VPN Gateway (Brama sieci wirtualnej)        |
|publicIPAddresses_firewall_public_ip_name     | Nazwa publicznego adresu IP dla zapory platformy Azure         |
|localNetworkGateways_LNGW_name    |Nazwa bramy sieci lokalnej platformy Azure          |
|connections_vngw_lngw_name    | Nazwa połączenia sieci VPN platformy Azure. Jest to połączenie między bramą sieci wirtualnej i bramą sieci lokalnej.       |
|location     |Jest to region, w którym chcesz utworzyć sieć wirtualną. Wybierz ten sam region, który jest skojarzony z Twoim urządzeniem.         |

Następujące adresy IP i przestrzenie adresowe odnoszą się do zasobów platformy Azure, które są tworzone, w tym sieci wirtualnej i skojarzonych podsieci (domyślnie: Zapora, GatewaySubnet).

|Nazwa parametru  |Opis  |
|---------|---------|
|VnetIPv4AddressSpace    | Jest to przestrzeń adresowa skojarzona z siecią wirtualną.       |
|DefaultSubnetIPv4AddressSpace    |Jest to przestrzeń adresowa skojarzona z podsiecią `Default` dla sieci wirtualnej.         |
|FirewallSubnetIPv4AddressSpace    |Jest to przestrzeń adresowa skojarzona z podsiecią `Firewall` dla sieci wirtualnej.          |
|GatewaySubnetIPv4AddressSpace    |Jest to przestrzeń adresowa skojarzona z `GatewaySubnet` siecią wirtualną.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Jest to adres IP zarezerwowany dla komunikacji BGP i jest oparty na przestrzeni adresowej skojarzonej z `GatewaySubnet` dla sieci wirtualnej.          |

Następujące adresy IP i przestrzenie adresowe odnoszą się do sieci lokalnej (w której wdrożono urządzenie Azure Stack EDGE Pro R).

|Nazwa parametru  |Opis  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Jest to przestrzeń adresowa dla prywatnego adresu IP.       |
|CustomerPublicNetworkAddressSpace    |  Jest to przestrzeń adresowa dla publicznego adresu IP.       |
|DbeIOTNetworkAddressSpace    |Ten adres IP jest zarezerwowany przez usługę IoT. Nie zmieniaj tego parametru.        |
|AzureVPNsharedKey    |Ten klucz współużytkowany jest używany podczas tworzenia zasobu połączenia sieci VPN platformy Azure. Ten klucz jest również dostarczany jako wspólny klucz tajny sieci VPN podczas konfiguracji sieci VPN lokalnego interfejsu użytkownika sieci Web.         |
|DBE-Gateway-IPAddress   | Publiczny adres IP dla urządzenia z Azure Stack EDGE Pro. Może to nie być znane i można uruchomić skrypt z zastępczym adresem IP. Edytuj bramę sieci lokalnej później za pomocą rzeczywistego adresu IP.     |

#### <a name="caveats-to-keep-in-mind"></a>Ostrzeżenia, które należy wziąć pod uwagę:

- Nie masz adresu IP urządzenia Azure Stack EDGE Pro R. Możesz użyć zastępczego adresu IP, aby utworzyć zasób i później zmodyfikować bramę sieci lokalnej platformy Azure, aby przypisać rzeczywisty adres IP urządzenia i przestrzeń adresową sieci lokalnej dla urządzenia.
- Na podstawie kierunku od IETF w organizacji IANA (Internet Assigned Numbers Authority) Użyj dowolnej podsieci z 172.16. x. y do 172.24. z.a. Zastrzegamy zakresy adresów IPv4 172,24 dla sieci platformy Azure.

### <a name="run-the-script"></a>Uruchamianie skryptu

Wykonaj następujące kroki, aby użyć zmodyfikowanego `parameters.json` i uruchomić skrypt w celu utworzenia zasobów platformy Azure.

1. Uruchom program PowerShell jako administrator.

2. Przejdź do katalogu, w którym znajduje się skrypt.

3. Uruchom skrypt.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Poniżej pokazano przykładowe dane wyjściowe.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    Wykonanie skryptu trwa około 90 minut. Po zakończeniu wykonywania skryptu dziennik wdrożenia jest generowany w tym samym folderze, w którym znajduje się skrypt.


## <a name="verify-the-azure-resources"></a>Weryfikowanie zasobów platformy Azure

Po pomyślnym uruchomieniu skryptu Sprawdź, czy wszystkie zasoby zostały utworzone na platformie Azure.

Następnie skonfigurujesz sieć VPN w lokalnym interfejsie użytkownika sieci Web urządzenia.


## <a name="vpn-configuration-on-the-device"></a>Konfiguracja sieci VPN na urządzeniu

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Weryfikowanie sieci VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Weryfikowanie transferu danych za pośrednictwem sieci VPN

Aby upewnić się, że sieć VPN działa, skopiuj dane do udziału SMB. Wykonaj kroki opisane w temacie [Dodawanie udziału](azure-stack-edge-gpu-manage-shares.md#add-a-share) na urządzeniu Azure Stack EDGE Pro R. 

1. Skopiuj plik, na przykład \data\pictures\waterfall.jpg do udziału SMB, który został zainstalowany w systemie klienta. 
2. Sprawdź, czy ten plik jest wyświetlany na koncie magazynu w chmurze.

Aby sprawdzić, czy dane przechodzą przez sieć VPN:

1. Otwórz zasób połączenia znajdujący się w grupie zasobów. 

2. Sprawdź dane w danych i danych.
 
3. Otwórz bramę Virtual Network w grupie zasobów. Wyświetl wykresy dla **łącznego** ruchu przychodzącego tunelu i **całkowitej liczby wychodzących tuneli**.
 
## <a name="debug-issues"></a>Problemy z debugowaniem

Aby debugować wszelkie problemy, użyj następujących poleceń:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Przykładowe dane wyjściowe przedstawiono poniżej:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie sieci VPN za pomocą lokalnego interfejsu użytkownika na urządzeniu Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
