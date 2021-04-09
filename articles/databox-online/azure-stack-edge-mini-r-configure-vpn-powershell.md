---
title: Konfigurowanie sieci VPN na urządzeniu Azure Stack Edge mini R przy użyciu Azure PowerShell
description: Opisuje sposób konfigurowania sieci VPN na urządzeniu Azure Stack Edge mini R przy użyciu skryptu Azure PowerShell do tworzenia zasobów platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630966"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Konfigurowanie sieci VPN na urządzeniu z systemem Azure Stack Edge mini R za pośrednictwem Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

Opcja sieci VPN zapewnia drugą warstwę szyfrowania dla danych w ruchu przez *protokół TLS* z Azure Stack Edge mini R lub Azure Stack EDGE Pro na platformie Azure. Sieć VPN można skonfigurować na urządzeniu z systemem Azure Stack Edge mini R za pośrednictwem Azure Portal lub za pośrednictwem Azure PowerShell. 

W tym artykule opisano kroki wymagane do skonfigurowania sieci VPN typu punkt-lokacja (P2S) na urządzeniu z systemem Azure Stack Edge mini R przy użyciu skryptu Azure PowerShell w celu utworzenia konfiguracji w chmurze. Konfiguracja na urządzeniu Azure Stack Edge odbywa się za pośrednictwem lokalnego interfejsu użytkownika.

## <a name="about-vpn-setup"></a>Informacje o konfiguracji sieci VPN

Połączenie bramy sieci VPN P2S umożliwia utworzenie bezpiecznego połączenia z siecią wirtualną z poziomu pojedynczego komputera klienckiego lub urządzenia Azure Stack Edge mini R. Należy uruchomić połączenie P2S z komputera klienckiego lub urządzenia. W tym przypadku połączenie P2S korzysta z sieci VPN protokołu IKEv2, opartej na standardach rozwiązanie sieci VPN IPsec.

Typowy przepływ pracy obejmuje następujące kroki:

1. Konfigurowanie wymagań wstępnych.
2. Konfigurowanie niezbędnych zasobów na platformie Azure.
    1. Utwórz i skonfiguruj sieć wirtualną oraz wymagane podsieci. 
    2. Utwórz i skonfiguruj usługę Azure VPN Gateway (Brama sieci wirtualnej).
    3. Konfigurowanie zapory platformy Azure i dodawanie reguł sieci i aplikacji.
    4. Tworzenie tabel routingu platformy Azure i Dodawanie tras.
    5. Włącz opcję punkt-lokacja w bramie sieci VPN.
        1. Dodaj pulę adresów klienta.
        2. Skonfiguruj typ tunelu.
        3. Skonfiguruj typ uwierzytelniania.
        4. Utwórz certyfikat.
        5. Przekaż certyfikat.
    6. Pobierz książkę telefoniczną.
3. Skonfiguruj sieć VPN w lokalnym interfejsie użytkownika sieci Web urządzenia. 
    1. Udostępnianie książki telefonicznej.
    2. Podaj plik tagów usługi (JSON).


Szczegółowe kroki przedstawiono w poniższych sekcjach.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych

- Należy mieć dostęp do urządzenia z systemem Azure Stack Edge mini R, które jest zainstalowane zgodnie z instrukcjami w temacie [instalowanie Azure Stack Edge mini r urządzenia](azure-stack-edge-mini-r-deploy-install.md). To urządzenie nawiąże połączenie P2S z platformą Azure. 

- Należy mieć dostęp do prawidłowej subskrypcji platformy Azure, która jest włączona dla usługi Azure Stack Edge na platformie Azure. Użyj tej subskrypcji, aby utworzyć odpowiedni zasób na platformie Azure do zarządzania urządzeniem w usłudze Azure Stack Edge mini R.  

- Masz dostęp do klienta systemu Windows, który będzie używany do uzyskiwania dostępu do urządzenia z usługą Azure Stack Edge mini R. Ten klient będzie używany do programowego tworzenia konfiguracji w chmurze.

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
        Podaj nazwę subskrypcji platformy Azure używaną na urządzeniu Azure Stack Edge mini R, aby skonfigurować sieć VPN.

    3. [Pobierz skrypt](https://aka.ms/ase-vpn-deployment) wymagany do utworzenia konfiguracji w chmurze. Skrypt:
        
        - Utwórz sieć wirtualną platformy Azure oraz następujące podsieci: *GatewaySubnet* i *AzureFirewallSubnet*.
        - Utwórz i skonfiguruj bramę sieci VPN platformy Azure.
        - Utwórz i skonfiguruj bramę sieci lokalnej platformy Azure.
        - Utwórz i skonfiguruj połączenie sieci VPN platformy Azure między bramą sieci VPN platformy Azure i bramą sieć lokalną.
        - Utwórz zaporę platformy Azure i Dodaj reguły sieci, reguły aplikacji.
        - Utwórz tabelę routingu platformy Azure i Dodaj do niej trasy.

    4. Utwórz grupę zasobów w Azure Portal, w której chcesz utworzyć zasoby platformy Azure. Przejdź do listy usług w Azure Portal, wybierz pozycję **Grupa zasobów** , a następnie wybierz pozycję **+ Dodaj**. Podaj informacje o subskrypcji i nazwę grupy zasobów, a następnie wybierz pozycję **Utwórz**. Jeśli przejdziesz do tej grupy zasobów, nie powinna ona mieć żadnych zasobów w tej chwili.

        ![Grupa zasobów platformy Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Musisz mieć podstawowy certyfikat zakodowany zgodnie z 64 w `.cer` formacie dla urządzenia Azure Stack Edge mini R. Ten certyfikat należy przekazać do urządzenia Azure Stack Edge jako `pfx` z kluczem prywatnym. Ten certyfikat należy również zainstalować w zaufanym katalogu głównym magazynu na kliencie, który próbuje nawiązać połączenie z usługą P2S.

## <a name="use-the-script"></a>Używanie skryptu

Najpierw należy zmodyfikować `parameters-p2s.json` plik, aby wprowadzić parametry. Następnie uruchom skrypt przy użyciu zmodyfikowanego pliku JSON.

Każdy z tych kroków został omówiony w poniższych sekcjach.

### <a name="download-service-tags-file"></a>Pobierz plik tagów usługi

Być może masz już `ServiceTags.json` plik w folderze, do którego został pobrany skrypt. W przeciwnym razie można pobrać plik tagów usługi.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Modyfikuj plik parametrów

Pierwszym krokiem jest zmodyfikowanie `parameters-p2s.json` pliku i zapisanie zmian. 

W przypadku utworzonych zasobów platformy Azure podaj następujące nazwy:

|Nazwa parametru  |Opis  |
|---------|---------|
|virtualNetworks_vnet_name    | Nazwa Virtual Network platformy Azure        |
|azureFirewalls_firewall_name     | Nazwa zapory platformy Azure        |
|routeTables_routetable_name     | Nazwa tabeli tras platformy Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Nazwa publicznego adresu IP dla bramy sieci wirtualnej       |
|virtualNetworkGateways_VNGW_name    | Nazwa usługi Azure VPN Gateway (Brama sieci wirtualnej)        |
|publicIPAddresses_firewall_public_ip_name     | Nazwa publicznego adresu IP dla zapory platformy Azure         |
|location     |Jest to region, w którym chcesz utworzyć sieć wirtualną. Wybierz ten sam region, który jest skojarzony z Twoim urządzeniem.         |
|RouteTables_routetable_onprem_name| Jest to nazwa dodatkowej tabeli tras, która ułatwia kierowanie przez zaporę pakietów z powrotem do Azure Stack urządzenia brzegowego. Skrypt tworzy dwie dodatkowe trasy i kojarzy *domyślne* i *FirewallSubnet* z tą tabelą tras.|

Podaj następujące adresy IP i przestrzenie adresowe dla utworzonych zasobów platformy Azure, w tym sieci wirtualnej i skojarzonych podsieci (*Domyślnie*: *Zapora*, *GatewaySubnet*).

|Nazwa parametru  |Opis  |
|---------|---------|
|VnetIPv4AddressSpace    | Jest to przestrzeń adresowa skojarzona z siecią wirtualną. Podaj zakres adresów IP sieci wirtualnej jako prywatny zakres adresów IP ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Jest to przestrzeń adresowa skojarzona z podsiecią `Default` dla sieci wirtualnej.         |
|FirewallSubnetIPv4AddressSpace    |Jest to przestrzeń adresowa skojarzona z podsiecią `Firewall` dla sieci wirtualnej.          |
|GatewaySubnetIPv4AddressSpace    |Jest to przestrzeń adresowa skojarzona z `GatewaySubnet` siecią wirtualną.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Jest to adres IP zarezerwowany dla komunikacji BGP i jest oparty na przestrzeni adresowej skojarzonej z `GatewaySubnet` dla sieci wirtualnej.          |
|ClientAddressPool    | Ten adres IP jest używany dla puli adresów w konfiguracji P2S w Azure Portal.         |
|PublicCertData     | Dane certyfikatu publicznego są używane przez VPN Gateway do uwierzytelniania klientów P2S łączących się z nią. Aby uzyskać dane certyfikatu, Zainstaluj certyfikat główny. Upewnij się, że certyfikat jest Base-64 zakodowany przy użyciu rozszerzenia. cer. Otwórz ten certyfikat i skopiuj tekst w certyfikacie między = = BEGIN CERTIFICATE = = i = = END CERTIFICATE = = w jednej linii ciągłej.     |



### <a name="run-the-script"></a>Uruchamianie skryptu

Wykonaj następujące kroki, aby użyć zmodyfikowanego `parameters-p2s.json` i uruchomić skrypt w celu utworzenia zasobów platformy Azure.

1. Uruchom program PowerShell. Przejdź do katalogu, w którym znajduje się skrypt.

3. Uruchom skrypt.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > W tej wersji skrypt działa tylko w lokalizacji Wschodnie stany USA.

    Po uruchomieniu skryptu należy wprowadzić następujące informacje:

    
    |Parametr  |Opis  |
    |---------|---------|
    |Lokalizacja     |Jest to region, w którym należy utworzyć zasoby platformy Azure.         |
    |AzureAppRuleFilePath     | To jest ścieżka do pliku `appRule.json` .       |
    |AzureIPRangesFilePath     |To jest plik JSON znacznika usługi pobrany we wcześniejszym kroku.         |
    |ResourceGroupName     | Jest to nazwa grupy zasobów, w ramach której są tworzone wszystkie zasoby platformy Azure.        |
    |AzureDeploymentName    |To jest nazwa wdrożenia platformy Azure.         |
    |NetworkRuleCollectionName            | Jest to nazwa kolekcji wszystkich reguł sieci, które są tworzone i dodawane do zapory platformy Azure.             |
    |Priorytet            | Jest to priorytet przypisany do wszystkich utworzonych reguł sieci i aplikacji.              |
    |AppRuleCollectionName            |Jest to nazwa kolekcji wszystkich reguł aplikacji, które są tworzone i dodawane do zapory platformy Azure.                |


    Poniżej pokazano przykładowe dane wyjściowe.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - Wykonanie skryptu trwa około 90 minut. Upewnij się, że logujesz się do sieci bezpośrednio przed uruchomieniem skryptu.
    > - Jeśli z jakiegoś powodu Wystąpił błąd sesji z skryptem, upewnij się, że Usunięto grupę zasobów, aby usunąć wszystkie utworzone w niej zasoby.
  
    
    Po zakończeniu wykonywania skryptu dziennik wdrożenia jest generowany w tym samym folderze, w którym znajduje się skrypt.


## <a name="verify-the-azure-resources"></a>Weryfikowanie zasobów platformy Azure

Po pomyślnym uruchomieniu skryptu Sprawdź, czy wszystkie zasoby zostały utworzone na platformie Azure. Przejdź do utworzonej grupy zasobów. Powinny zostać wyświetlone następujące zasoby:

![Zasoby platformy Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Pobierz książkę telefoniczną dla profilu sieci VPN

W tym kroku zostanie pobrany profil sieci VPN dla Twojego urządzenia.

1. W Azure Portal przejdź do grupy zasobów, a następnie wybierz bramę sieci wirtualnej, która została utworzona we wcześniejszym kroku.

    ![Brama sieci wirtualnej platformy Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Przejdź do pozycji **ustawienia > Konfiguracja punktu do lokacji**. Wybierz pozycję **Pobierz klienta sieci VPN**.

    ![Włącz konfigurację P2S 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Zapisz spakowany profil i wyodrębnij go na kliencie systemu Windows.

    ![Włącz konfigurację P2S 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Przejdź do folderu *WindowsAmd64* , a następnie wyodrębnij `.exe` *VpnClientSetupAmd64.exe*.

    ![Włącz konfigurację P2S 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Utwórz ścieżkę tymczasową. Na przykład:

    `C:\NewTemp\vnet\tmp`

4. Uruchom program PowerShell i przejdź do katalogu, w którym znajduje się folder `.exe` . Aby wykonać `.exe` , wpisz:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Ścieżka tymczasowa będzie zawierać nowe pliki. Oto przykładowe dane wyjściowe:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. Plik *. pbk* to książka telefoniczna dla profilu sieci VPN. Będzie ona używana w lokalnym interfejsie użytkownika.


## <a name="vpn-configuration-on-the-device"></a>Konfiguracja sieci VPN na urządzeniu

Wykonaj następujące kroki w lokalnym interfejsie użytkownika urządzenia brzegowego Azure Stack.

1. W lokalnym interfejsie użytkownika przejdź do strony **sieci VPN** . W obszarze stan sieci VPN wybierz pozycję **Konfiguruj**.

    ![Konfigurowanie sieci VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. W bloku **Konfigurowanie sieci VPN** :
    
    1. W pliku książka telefoniczna przekazywania wskaż plik. pbk, który został utworzony w poprzednim kroku.
    2. W pliku konfiguracyjnym przekazywania listy publicznych adresów IP podaj plik JSON zakresu adresów IP usługi Azure Data Center jako dane wejściowe. Ten plik został pobrany we wcześniejszym kroku z: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Wybierz **wschód** jako region i wybierz pozycję **Zastosuj**.

    ![Konfigurowanie sieci VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. W sekcji **zakresy adresów IP, do których można uzyskać dostęp przy użyciu sieci VPN** , wprowadź zakres adresów IPv4 sieci wirtualnej, który został wybrany dla Virtual Network platformy Azure.

    ![Konfigurowanie sieci VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Weryfikuj połączenie klienta

1. W Azure Portal przejdź do bramy sieci VPN.
2. Przejdź do pozycji **ustawienia > Konfiguracja punktu do lokacji**. W obszarze **przydzielono adresy IP** adres IP Azure Stack urządzenia brzegowego powinien być widoczny.

## <a name="validate-data-transfer-through-vpn"></a>Weryfikowanie transferu danych za pośrednictwem sieci VPN

Aby upewnić się, że sieć VPN działa, skopiuj dane do udziału SMB. Wykonaj kroki opisane w temacie [Dodawanie udziału](azure-stack-edge-gpu-manage-shares.md#add-a-share) na urządzeniu Azure Stack Edge. 

1. Skopiuj plik, na przykład \data\pictures\waterfall.jpg do udziału SMB, który został zainstalowany w systemie klienta. 
2. Aby sprawdzić, czy dane przechodzą przez sieć VPN, podczas gdy dane są kopiowane:

    1. Przejdź do bramy sieci VPN w Azure Portal. 

    2. Przejdź do pozycji **monitorowanie > metryki**.

    3. W okienku po prawej stronie wybierz **zakres** jako bramę sieci VPN, **metrykę** jako P2S przepustowość bramy i **agregację** jako średnią.

    4. Podczas kopiowania danych zostanie wyświetlony wzrost wykorzystania przepustowości i po zakończeniu kopiowania danych wykorzystanie przepustowości zostanie porzucane.

        ![Metryki sieci VPN platformy Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Sprawdź, czy ten plik jest wyświetlany na koncie magazynu w chmurze.
 
## <a name="debug-issues"></a>Problemy z debugowaniem

Aby debugować wszelkie problemy, użyj następujących poleceń:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Przykładowe dane wyjściowe przedstawiono poniżej:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[Skonfiguruj sieć VPN za pomocą lokalnego interfejsu użytkownika na urządzeniu Azure Stack Edge](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).