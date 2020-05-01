---
title: Konfigurowanie punktów końcowych sieci Azure Files | Microsoft Docs
description: Omówienie opcji sieciowych dla Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8ee9ddbd8a2d0ecbe8e2f13e6421cec177c7ce69
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594206"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurowanie punktów końcowych sieci Azure Files
Azure Files udostępnia dwa główne typy punktów końcowych do uzyskiwania dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w ramach sieci wirtualnej i mają prywatny adres IP w przestrzeni adresowej tej sieci wirtualnej.

Publiczne i prywatne punkty końcowe istnieją na koncie usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki.

W tym artykule omówiono sposób konfigurowania punktów końcowych konta magazynu do bezpośredniego dostępu do udziału plików platformy Azure. Większość szczegółów podanych w tym dokumencie dotyczy również sposobu, w jaki Azure File Sync współdziałają z publicznymi i prywatnymi punktami końcowymi dla konta magazynu, jednak aby uzyskać więcej informacji dotyczących zagadnień sieciowych dotyczących wdrożenia Azure File Sync, zobacz [konfigurowanie Azure File Sync proxy i ustawień zapory](storage-sync-files-firewall-and-proxy.md).

Zalecamy zapoznanie się z [zagadnieniami dotyczącymi sieci Azure Files](storage-files-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne
- W tym artykule przyjęto założenie, że utworzono już subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- W tym artykule przyjęto założenie, że masz już utworzony udział plików platformy Azure na koncie magazynu, z którym chcesz nawiązać połączenie ze środowiska lokalnego. Aby dowiedzieć się, jak utworzyć udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).
- Jeśli zamierzasz używać Azure PowerShell, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
Utworzenie prywatnego punktu końcowego dla konta magazynu spowoduje wdrożenie następujących zasobów platformy Azure:

- **Prywatny punkt końcowy**: zasób platformy Azure reprezentujący prywatny punkt końcowy konta magazynu. Można to traktować jako zasób, który nawiązuje połączenie z kontem magazynu i interfejsem sieciowym.
- **Interfejs sieciowy (nic)**: interfejs sieciowy, który utrzymuje prywatny adres IP w określonej podsieci/sieci wirtualnej. Jest to dokładnie ten sam zasób, który jest wdrażany podczas wdrażania maszyny wirtualnej, ale nie jest przypisywany do maszyny wirtualnej, jest własnością prywatnego punktu końcowego.
- **Prywatna strefa DNS**: Jeśli nigdy nie wdrożono prywatnego punktu końcowego dla tej sieci wirtualnej, zostanie wdrożona nowa prywatna strefa DNS dla sieci wirtualnej. Zostanie również utworzony rekord DNS A dla konta magazynu w tej strefie DNS. Jeśli w tej sieci wirtualnej wdrożono już prywatny punkt końcowy, nowy rekord konta magazynu zostanie dodany do istniejącej strefy DNS. Wdrażanie strefy DNS jest opcjonalne, ale zdecydowanie zalecane i wymagane, Jeśli instalujesz udziały plików platformy Azure za pomocą jednostki usługi AD lub przy użyciu interfejsu API FileREST.

> [!Note]  
> W tym artykule jest stosowany sufiks DNS konta magazynu dla publicznych regionów platformy Azure `core.windows.net`. Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura dla instytucji rządowych w Stanach Zjednoczonych i Azure (Chiny) — po prostu zastępuje odpowiednie sufiksy dla danego środowiska. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do konta magazynu, dla którego chcesz utworzyć prywatny punkt końcowy. W spisie treści konta magazynu wybierz pozycję **połączenia prywatne punktu końcowego**, a następnie **+ prywatny punkt końcowy** , aby utworzyć nowy prywatny punkt końcowy. 

![Zrzut ekranu przedstawiający element połączenia prywatnego punktu końcowego w spisie treści konta magazynu](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Wynikiem pracy kreatora jest wiele stron.

W bloku **podstawowe** wybierz żądaną grupę zasobów, nazwę i region dla prywatnego punktu końcowego. Mogą one być odpowiednie, ale nie muszą być zgodne z kontem magazynu w tym przypadku, chociaż należy utworzyć prywatny punkt końcowy w tym samym regionie, w którym znajduje się sieć wirtualna, w której ma zostać utworzony prywatny punkt końcowy.

![Zrzut ekranu przedstawiający sekcję podstawowe sekcji Tworzenie prywatnego punktu końcowego](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

W bloku **zasób** wybierz przycisk radiowy, aby **nawiązać połączenie z zasobem platformy Azure w moim katalogu**. W obszarze **Typ zasobu**wybierz pozycję **Microsoft. Storage/storageAccounts** dla typu zasobu. Pole **zasobu** to konto magazynu z udziałem plików platformy Azure, z którym chcesz nawiązać połączenie. Docelowy zasób podrzędny to **plik**, ponieważ jest on przeznaczony dla Azure Files.

Blok **Konfiguracja** umożliwia wybranie określonej sieci wirtualnej i podsieci, do której chcesz dodać prywatny punkt końcowy. Wybierz utworzoną powyżej sieć wirtualną. Należy wybrać odrębną podsieć z podsieci, do której dodano punkt końcowy usługi. Blok konfiguracja zawiera również informacje dotyczące tworzenia/aktualizowania prywatnej strefy DNS. Zalecamy korzystanie z strefy domyślnej `privatelink.file.core.windows.net` .

![Zrzut ekranu przedstawiający sekcję konfiguracyjną](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć prywatny punkt końcowy. 

Jeśli masz maszynę wirtualną w sieci wirtualnej lub przekazanie usługi DNS zostało skonfigurowane zgodnie z opisem w [tym miejscu](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany, uruchamiając następujące polecenia z programu PowerShell, wiersza polecenia lub terminalu (działa dla systemu Windows, Linux lub macOS). Należy zamienić `<storage-account-name>` na odpowiednią nazwę konta magazynu:

```
nslookup <storage-account-name>.file.core.windows.net
```

Jeśli wszystko działało prawidłowo, powinny zostać wyświetlone następujące dane wyjściowe, gdzie `192.168.0.5` jest prywatnym adresem IP prywatnego punktu końcowego w sieci wirtualnej (dane wyjściowe wyświetlane dla systemu Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Aby utworzyć prywatny punkt końcowy dla konta magazynu, najpierw musisz uzyskać odwołanie do konta magazynu i podsieci sieci wirtualnej, do której chcesz dodać prywatny punkt końcowy. Zamień `<storage-account-resource-group-name>`, `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, i `<vnet-subnet-name>` poniżej:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Aby utworzyć prywatny punkt końcowy, należy utworzyć połączenie usługi link prywatny z kontem magazynu. Połączenie usługi link prywatny to dane wejściowe do tworzenia prywatnego punktu końcowego. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Utworzenie prywatnej strefy DNS platformy Azure umożliwia pierwotną nazwę konta magazynu, na przykład w `storageaccount.file.core.windows.net` celu rozpoznania prywatnego adresu IP w sieci wirtualnej. Chociaż jest to opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest on jawnie wymagany do zainstalowania udziału plików platformy Azure przy użyciu podmiotu zabezpieczeń usługi AD lub dostępu za pośrednictwem interfejsu API REST.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

Teraz, gdy masz odwołanie do prywatnej strefy DNS, musisz utworzyć rekord A dla konta magazynu.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Jeśli masz maszynę wirtualną w sieci wirtualnej lub przekazanie usługi DNS zostało skonfigurowane zgodnie z opisem w [tym miejscu](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany przy użyciu następujących poleceń:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Jeśli wszystko działało prawidłowo, należy zobaczyć następujące dane wyjściowe, gdzie `192.168.0.5` jest prywatnym adresem IP prywatnego punktu końcowego w sieci wirtualnej:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć prywatny punkt końcowy dla konta magazynu, najpierw musisz uzyskać odwołanie do konta magazynu i podsieci sieci wirtualnej, do której chcesz dodać prywatny punkt końcowy. Zamień `<storage-account-resource-group-name>`, `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, i `<vnet-subnet-name>` poniżej:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Aby utworzyć prywatny punkt końcowy, należy najpierw upewnić się, że zasady sieci prywatnego punktu końcowego podsieci są ustawione na wartość wyłączone. Następnie można utworzyć prywatny punkt końcowy przy użyciu `az network private-endpoint create` polecenia

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Utworzenie prywatnej strefy DNS platformy Azure umożliwia pierwotną nazwę konta magazynu, na przykład w `storageaccount.file.core.windows.net` celu rozpoznania prywatnego adresu IP w sieci wirtualnej. Chociaż jest to opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest on jawnie wymagany do zainstalowania udziału plików platformy Azure przy użyciu podmiotu zabezpieczeń usługi AD lub dostępu za pośrednictwem interfejsu API REST.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Teraz, gdy masz odwołanie do prywatnej strefy DNS, musisz utworzyć rekord A dla konta magazynu.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Jeśli masz maszynę wirtualną w sieci wirtualnej lub przekazanie usługi DNS zostało skonfigurowane zgodnie z opisem w [tym miejscu](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany przy użyciu następujących poleceń:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Jeśli wszystko działało prawidłowo, należy zobaczyć następujące dane wyjściowe, gdzie `192.168.0.5` jest prywatnym adresem IP prywatnego punktu końcowego w sieci wirtualnej:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Ograniczanie dostępu do publicznego punktu końcowego
Dostęp do publicznego punktu końcowego można ograniczyć przy użyciu ustawień zapory konta magazynu. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej. Istnieją dwa podejścia do ograniczania dostępu do konta magazynu w sieci wirtualnej:

- [Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu](#create-a-private-endpoint) i Ogranicz dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z żądanych sieci wirtualnych może uzyskiwać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. W przypadku ograniczenia ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskuje się dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Ogranicz dostęp do publicznego punktu końcowego
Gdy wszystkie prawa dostępu do publicznego punktu końcowego są ograniczone, do konta magazynu nadal można uzyskać dostęp za pomocą prywatnych punktów końcowych. W przeciwnym razie prawidłowe żądania dotyczące publicznego punktu końcowego konta magazynu zostaną odrzucone. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do konta magazynu, dla którego chcesz ograniczyć dostęp do publicznego punktu końcowego. W spisie treści dla konta magazynu wybierz pozycję **zapory i sieci wirtualne**.

W górnej części strony wybierz przycisk radiowy **wybrane sieci** . Spowoduje to ukrycie szeregu ustawień służących do kontrolowania ograniczenia publicznego punktu końcowego. Zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta usługi** , aby umożliwić zaufanym usługom firmy Microsoft, takim jak Azure File Sync, uzyskanie dostępu do konta magazynu.

![Zrzut ekranu przedstawiający blok zapory i sieci wirtualne z odpowiednimi ograniczeniami](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Następujące polecenie programu PowerShell będzie odrzucać cały ruch do publicznego punktu końcowego konta magazynu. Należy pamiętać, że to polecenie `-Bypass` ma parametr ustawiony `AzureServices`na. Pozwoli to na dostęp do konta magazynu przez publiczny punkt końcowy, takich jak Azure File Sync.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Następujące polecenie interfejsu wiersza polecenia będzie odrzucać cały ruch do publicznego punktu końcowego konta magazynu. Należy pamiętać, że to polecenie `-bypass` ma parametr ustawiony `AzureServices`na. Pozwoli to na dostęp do konta magazynu przez publiczny punkt końcowy, takich jak Azure File Sync.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Ograniczanie dostępu do publicznego punktu końcowego do określonych sieci wirtualnych
W przypadku ograniczenia konta magazynu do określonych sieci wirtualnych można zezwalać na żądania kierowane do publicznego punktu końcowego z określonych sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. Można go używać z prywatnymi punktami końcowymi lub bez nich.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do konta magazynu, dla którego chcesz ograniczyć publiczny punkt końcowy do określonych sieci wirtualnych. W spisie treści dla konta magazynu wybierz pozycję **zapory i sieci wirtualne**. 

W górnej części strony wybierz przycisk radiowy **wybrane sieci** . Spowoduje to ukrycie szeregu ustawień służących do kontrolowania ograniczenia publicznego punktu końcowego. Kliknij pozycję **+ Dodaj istniejącą sieć wirtualną** , aby wybrać konkretną sieć wirtualną, która powinna mieć zezwolenie na dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego. Będzie to wymagało wybrania sieci wirtualnej i podsieci dla tej sieci wirtualnej. 

Zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta usługi** , aby umożliwić zaufanym usługom firmy Microsoft, takim jak Azure File Sync, uzyskanie dostępu do konta magazynu.

![Zrzut ekranu przedstawiający blok zapory i sieci wirtualne z określoną siecią wirtualną, która może uzyskiwać dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Aby ograniczyć dostęp do publicznego punktu końcowego konta magazynu do określonych sieci wirtualnych za pomocą punktów końcowych usługi, najpierw musimy zebrać informacje o koncie magazynu i sieci wirtualnej. Wypełnij pola `<storage-account-resource-group>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>`i `<subnet-name>` , aby zebrać te informacje.

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

Aby ruch z sieci wirtualnej był dozwolony przez sieć szkieletową platformy Azure, aby uzyskać dostęp do publicznego punktu końcowego konta magazynu, podsieć sieci wirtualnej musi mieć uwidoczniony punkt `Microsoft.Storage` końcowy usługi. Następujące polecenia programu PowerShell spowodują dodanie punktu końcowego `Microsoft.Storage` usługi do podsieci, jeśli jeszcze jej nie ma.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

Ostatnim krokiem ograniczającym ruch do konta magazynu jest utworzenie reguły sieci i dodanie jej do zestawu reguł sieci konta magazynu.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby ograniczyć dostęp do publicznego punktu końcowego konta magazynu do określonych sieci wirtualnych za pomocą punktów końcowych usługi, najpierw musimy zebrać informacje o koncie magazynu i sieci wirtualnej. Wypełnij pola `<storage-account-resource-group>`, `<storage-account-name>`, `<vnet-resource-group-name>`, `<vnet-name>`i `<subnet-name>` , aby zebrać te informacje.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Aby ruch z sieci wirtualnej był dozwolony przez sieć szkieletową platformy Azure, aby uzyskać dostęp do publicznego punktu końcowego konta magazynu, podsieć sieci wirtualnej musi mieć uwidoczniony punkt `Microsoft.Storage` końcowy usługi. Następujące polecenie interfejsu wiersza polecenia spowoduje dodanie punktu `Microsoft.Storage` końcowego usługi do podsieci, jeśli jeszcze nie istnieje.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

Ostatnim krokiem ograniczającym ruch do konta magazynu jest utworzenie reguły sieci i dodanie jej do zestawu reguł sieci konta magazynu.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Zobacz także
- [Zagadnienia dotyczące sieci Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie przekazywania DNS dla usługi Azure Files](storage-files-networking-dns.md)
- [Konfigurowanie sieci VPN S2S dla Azure Files](storage-files-configure-s2s-vpn.md)
