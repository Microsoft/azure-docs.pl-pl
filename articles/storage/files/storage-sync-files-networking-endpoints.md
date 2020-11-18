---
title: Konfigurowanie punktów końcowych sieci Azure File Sync | Microsoft Docs
description: Dowiedz się, jak skonfigurować Azure File Sync punkty końcowe sieci.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 5/11/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 02d9e65f5422b7b12900d051f01c1d6f55e8685b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844680"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Konfigurowanie punktów końcowych sieci usługi Azure File Sync
Azure Files i Azure File Sync zapewniają dwa główne typy punktów końcowych do uzyskiwania dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w ramach sieci wirtualnej i mają prywatny adres IP w przestrzeni adresowej tej sieci wirtualnej.

Zarówno dla Azure Files i Azure File Sync, obiektów zarządzania platformy Azure, konta magazynu i usługi synchronizacji magazynu, należy kontrolować zarówno publiczny, jak i prywatny punkt końcowy. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Usługa synchronizacji magazynu to konstrukcja zarządzania reprezentująca zarejestrowane serwery, które są serwerami plików systemu Windows z ustanowioną relacją zaufania z Azure File Sync i grupami synchronizacji, które definiują topologię relacji synchronizacji. 

W tym artykule omówiono sposób konfigurowania punktów końcowych sieci dla Azure Files i Azure File Sync. Aby dowiedzieć się więcej na temat sposobu konfigurowania punktów końcowych sieci do bezpośredniego dostępu do udziałów plików platformy Azure, a nie do lokalnego buforowania przy użyciu Azure File Sync, zobacz [konfigurowanie Azure Files punktów końcowych sieci](storage-files-networking-endpoints.md).

Zalecamy zapoznanie się z [zagadnieniami dotyczącymi sieci Azure File Sync](storage-sync-files-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne 
W tym artykule przyjęto założenie, że:
- Masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Udział plików platformy Azure został już utworzony na koncie magazynu, z którym chcesz nawiązać połączenie z poziomu lokalnego. Aby dowiedzieć się, jak utworzyć udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).
- Została już utworzona usługa synchronizacji magazynu i zarejestrowano w niej serwer plików systemu Windows. Aby dowiedzieć się, jak wdrożyć Azure File Sync, zobacz [wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md).

Dodatkowo:
- Jeśli zamierzasz używać Azure PowerShell, [Zainstaluj najnowszą wersję](/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-the-private-endpoints"></a>Tworzenie prywatnych punktów końcowych
Podczas tworzenia prywatnego punktu końcowego dla zasobu platformy Azure wdrożone są następujące zasoby:

- **Prywatny punkt końcowy**: zasób platformy Azure reprezentujący prywatny punkt końcowy dla konta magazynu lub usługi synchronizacji magazynu. Można to traktować jako zasób, który nawiązuje połączenie z zasobem platformy Azure i interfejsem sieciowym.
- **Interfejs sieciowy (nic)**: interfejs sieciowy, który utrzymuje prywatny adres IP w określonej podsieci/sieci wirtualnej. Jest to dokładnie ten sam zasób, który jest wdrażany podczas wdrażania maszyny wirtualnej, ale nie jest przypisywany do maszyny wirtualnej, jest własnością prywatnego punktu końcowego.
- **Prywatna strefa DNS**: Jeśli nigdy nie wdrożono prywatnego punktu końcowego dla tej sieci wirtualnej, zostanie wdrożona nowa prywatna strefa DNS dla sieci wirtualnej. Zostanie również utworzony rekord DNS A dla zasobu platformy Azure w tej strefie DNS. Jeśli prywatny punkt końcowy został już wdrożony w tej sieci wirtualnej, nowy rekord zasobu platformy Azure zostanie dodany do istniejącej strefy DNS. Wdrożenie strefy DNS jest opcjonalne, jednak zdecydowanie zalecane jest uproszczenie zarządzania usługą DNS.

> [!Note]  
> W tym artykule są stosowane sufiksy DNS dla publicznych regionów platformy Azure, `core.windows.net` dla kont magazynu i `afs.azure.net` usług synchronizacji magazynu. Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura dla instytucji rządowych Azure USA — po prostu zastępuje odpowiednie sufiksy dla danego środowiska.

### <a name="create-the-storage-account-private-endpoint"></a>Tworzenie prywatnego punktu końcowego konta magazynu
# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Jeśli masz maszynę wirtualną w sieci wirtualnej lub usługa przekazywania DNS została skonfigurowana zgodnie z opisem w temacie [Konfigurowanie przekazywania DNS dla Azure Files](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany, uruchamiając następujące polecenia z programu PowerShell, wiersza polecenia lub terminalu (działa dla systemu Windows, Linux lub macOS). Należy zamienić na `<storage-account-name>` odpowiednią nazwę konta magazynu:

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

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Jeśli masz maszynę wirtualną w sieci wirtualnej lub usługa przekazywania DNS została skonfigurowana zgodnie z opisem w temacie [Konfigurowanie przekazywania DNS dla Azure Files](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany przy użyciu następujących poleceń:

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
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Jeśli masz maszynę wirtualną w sieci wirtualnej lub usługa przekazywania DNS została skonfigurowana zgodnie z opisem w temacie [Konfigurowanie przekazywania DNS dla Azure Files](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany przy użyciu następujących poleceń:

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

### <a name="create-the-storage-sync-private-endpoint"></a>Tworzenie prywatnego punktu końcowego synchronizacji magazynu
> [!Important]  
> Aby można było używać prywatnych punktów końcowych w zasobie usługi synchronizacji magazynu, należy użyć agenta Azure File Sync w wersji 10,1 lub nowszej. Wersje agenta wcześniejsze niż 10,1 nie obsługują prywatnych punktów końcowych w usłudze synchronizacji magazynu. Wszystkie wcześniejsze wersje agenta obsługują prywatne punkty końcowe w ramach zasobu konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do **prywatnego centrum linków** , wpisując *prywatny link* na pasku wyszukiwania w górnej części Azure Portal. W spisie treści dla prywatnego centrum łączy wybierz **prywatne punkty końcowe**, a następnie **+ Dodaj** , aby utworzyć nowy prywatny punkt końcowy.

[![Zrzut ekranu przedstawiający prywatne centrum linków](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Wynikiem pracy kreatora jest wiele stron.

W bloku **podstawowe** wybierz żądaną grupę zasobów, nazwę i region dla prywatnego punktu końcowego. Mogą one być odpowiednie, ale nie muszą być zgodne z usługą synchronizacji magazynu w jakikolwiek sposób, chociaż należy utworzyć prywatny punkt końcowy w tym samym regionie, w którym znajduje się sieć wirtualna, w której ma zostać utworzony prywatny punkt końcowy.

![Zrzut ekranu przedstawiający sekcję podstawowe sekcji Tworzenie prywatnego punktu końcowego](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

W bloku **zasób** wybierz przycisk radiowy, aby **nawiązać połączenie z zasobem platformy Azure w moim katalogu**. W obszarze **Typ zasobu** wybierz pozycję **Microsoft. StorageSync/storageSyncServices** dla typu zasobu. 

Blok **Konfiguracja** umożliwia wybranie określonej sieci wirtualnej i podsieci, do której chcesz dodać prywatny punkt końcowy. Wybierz tę samą sieć wirtualną, która została użyta w powyższym koncie magazynu. Blok konfiguracja zawiera również informacje dotyczące tworzenia/aktualizowania prywatnej strefy DNS.

Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć prywatny punkt końcowy.

Możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany, uruchamiając następujące polecenia w programie PowerShell. 

```powershell
$privateEndpointResourceGroupName = "<your-private-endpoint-resource-group>"
$privateEndpointName = "<your-private-endpoint-name>"

Get-AzPrivateEndpoint `
        -ResourceGroupName $privateEndpointResourceGroupName `
        -Name $privateEndpointName `
        -ErrorAction Stop | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
    Select-Object -ExpandProperty Fqdns | `
    ForEach-Object { Resolve-DnsName -Name $_ } | `
    Format-List
```

Jeśli wszystko działało prawidłowo, należy zobaczyć następujące dane wyjściowe, gdzie `192.168.1.4` ,, `192.168.1.5` `192.168.1.6` i `192.168.1.7` są prywatnymi adresami IP przypisanymi do prywatnego punktu końcowego:

```Output
Name     : mysssmanagement.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmanagement.westus2.privatelink.afs.azure.net


Name       : mysssmanagement.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.4

Name     : myssssyncp.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncp.westus2.privatelink.afs.azure.net


Name       : myssssyncp.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.5

Name     : myssssyncs.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncs.westus2.privatelink.afs.azure.net


Name       : myssssyncs.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.6

Name     : mysssmonitoring.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmonitoring.westus2.privatelink.afs.azure.net


Name       : mysssmonitoring.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.7

```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć prywatny punkt końcowy dla usługi synchronizacji magazynu, najpierw musisz uzyskać odwołanie do usługi synchronizacji magazynu. Pamiętaj, aby zamienić `<storage-sync-service-resource-group>` i `<storage-sync-service>` z prawidłowymi wartościami dla danego środowiska. W poniższych poleceniach programu PowerShell założono, że używasz już wypełniono informacje o sieci wirtualnej z powyższych. 

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzStorageSyncService `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name $storageSyncServiceName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageSyncService) {
    $errorMessage = "Storage Sync Service $storageSyncServiceName not found "
    $errorMessage += "in resource group $storageSyncServiceResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}
```

Aby utworzyć prywatny punkt końcowy, należy utworzyć połączenie usługi link prywatny z usługą synchronizacji magazynu. Połączenie prywatne to dane wejściowe do tworzenia prywatnego punktu końcowego.

```PowerShell 
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork = $virtualNetwork | `
    Set-AzVirtualNetwork -ErrorAction Stop

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageSyncServiceName-Connection" `
        -PrivateLinkServiceId $storageSyncService.ResourceId `
        -GroupId "Afs" `
        -ErrorAction Stop

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name "$storageSyncServiceName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Utworzenie prywatnej strefy DNS platformy Azure umożliwia korzystanie z nazw hostów usługi synchronizacji magazynu, takich jak `mysssmanagement.westus2.afs.azure.net` , do rozpoznawania prawidłowych prywatnych adresów IP dla usługi synchronizacji magazynu w sieci wirtualnej. Chociaż jest to opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest on jawnie wymagany dla agenta Azure File Sync, aby uzyskać dostęp do usługi synchronizacji magazynu. 

```powershell
# Get the desired Storage Sync Service suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$azureEnvironment = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

switch($azureEnvironment) {
    "AzureCloud" {
        $storageSyncSuffix = "afs.azure.net"
    }

    "AzureUSGovernment" {
        $storageSyncSuffix = "afs.azure.us"
    }
    
    default {
        Write-Error 
                -Message "The Azure environment $_ is not currently supported by Azure File Sync." `
                -ErrorAction Stop
    }
}

# For public cloud, this will generate the following DNS suffix:
# privatelink.afs.azure.net
$dnsZoneName = "privatelink.$storageSyncSuffix"

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
Teraz, gdy masz odwołanie do prywatnej strefy DNS, musisz utworzyć rekordy A dla usługi synchronizacji magazynu.

```PowerShell 
$privateEndpointIpFqdnMappings = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    ForEach-Object { 
        $privateIpAddress = $_.PrivateIpAddress; 
        $_ | `
            Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
            Select-Object -ExpandProperty Fqdns | `
            Select-Object `
                @{ 
                    Name = "PrivateIpAddress"; 
                    Expression = { $privateIpAddress } 
                }, `
                @{ 
                    Name = "FQDN"; 
                    Expression = { $_ } 
                } 
    }

foreach($ipFqdn in $privateEndpointIpFqdnMappings) {
    $privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $ipFqdn.PrivateIpAddress
    
    $dnsEntry = $ipFqdn.FQDN.Substring(0, 
        $ipFqdn.FQDN.IndexOf(".", $ipFqdn.FQDN.IndexOf(".") + 1))

    New-AzPrivateDnsRecordSet `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsEntry `
            -RecordType A `
            -ZoneName $dnsZoneName `
            -Ttl 600 `
            -PrivateDnsRecords $privateDnsRecordConfig `
            -ErrorAction Stop | `
        Out-Null
}
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć prywatny punkt końcowy dla usługi synchronizacji magazynu, najpierw musisz uzyskać odwołanie do usługi synchronizacji magazynu. Pamiętaj, aby zamienić `<storage-sync-service-resource-group>` i `<storage-sync-service>` z prawidłowymi wartościami dla danego środowiska. Przy użyciu poniższych poleceń interfejsu wiersza polecenia przyjęto założenie, że używasz już wypełniono informacje o sieci wirtualnej z powyższych. 

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

storageSyncService=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "id" | \
    tr -d '"')

storageSyncServiceRegion=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "location" | \
    tr -d '"')
```

Aby utworzyć prywatny punkt końcowy, należy najpierw upewnić się, że zasady sieci prywatnego punktu końcowego podsieci są ustawione na wartość wyłączone. Następnie można utworzyć prywatny punkt końcowy przy użyciu `az network private-endpoint create` polecenia.

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
        --resource-group $storageSyncServiceResourceGroupName \
        --name "$storageSyncServiceName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageSyncService \
        --group-id "Afs" \
        --connection-name "$storageSyncServiceName-Connection" \
        --query "id" | \
    tr -d '"')
```

Utworzenie prywatnej strefy DNS platformy Azure umożliwia korzystanie z nazw hostów usługi synchronizacji magazynu, takich jak `mysssmanagement.westus2.afs.azure.net` , do rozpoznawania prawidłowych prywatnych adresów IP dla usługi synchronizacji magazynu w sieci wirtualnej. Chociaż jest to opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest on jawnie wymagany dla agenta Azure File Sync, aby uzyskać dostęp do usługi synchronizacji magazynu. 

```bash
# Get the desired storage account suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
azureEnvironment=$(az cloud show \
        --query "name" |
    tr -d '"')

storageSyncSuffix=""
if [ $azureEnvironment == "AzureCloud" ]
then
    storageSyncSuffix="afs.azure.net"
elif [ $azureEnvironment == "AzureUSGovernment" ]
then
    storageSyncSuffix="afs.azure.us"
else
    echo "Unsupported Azure environment $azureEnvironment."
fi

# For public cloud, this will generate the following DNS suffix:
# privatelinke.afs.azure.net.
dnsZoneName="privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=""
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

dnsZone=""
possibleDnsZone=""
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
        echo "1" > /dev/null
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
            --resource-group $virtualNetworkResourceGroupName \
            --zone-name $dnsZoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
    
    dnsZoneResourceGroup=$virtualNetworkResourceGroupName
fi
```

Teraz, gdy masz odwołanie do prywatnej strefy DNS, musisz utworzyć rekordy A dla usługi synchronizacji magazynu.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateIpAddresses=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateIpAddress" \
        --output tsv) 

hostNames=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateLinkConnectionProperties.fqdns[]" \
        --output tsv)

i=0
for privateIpAddress in $privateIpAddresses
do
    j=0
    targetHostName=""
    for hostName in $hostNames
    do
        if [ $i == $j ]
        then
            targetHostName=$hostName
            break
        fi

        j=$(expr $j + 1)
    done

    endpointName=$(echo $targetHostName | \
        cut -c1-$(expr $(expr index $targetHostName ".") - 1))

    az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name "$endpointName.$storageSyncServiceRegion" \
        --output none
    
    az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name "$endpointName.$storageSyncServiceRegion" \
        --ipv4-address $privateIpAddress \
        --output none

    i=$(expr $i + 1)
done
```
---

## <a name="restrict-access-to-the-public-endpoints"></a>Ograniczanie dostępu do publicznych punktów końcowych
Można ograniczyć dostęp do publicznych punktów końcowych zarówno dla konta magazynu, jak i usług synchronizacji magazynu. Ograniczanie dostępu do publicznego punktu końcowego zapewnia dodatkowe zabezpieczenia przez zapewnienie, że pakiety sieciowe są akceptowane tylko z zatwierdzonych lokalizacji. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>Ograniczanie dostępu do publicznego punktu końcowego konta magazynu
Ograniczenie dostępu do publicznego punktu końcowego odbywa się przy użyciu ustawień zapory konta magazynu. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej. Istnieją dwa podejścia do ograniczania dostępu do konta magazynu w sieci wirtualnej:

- [Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu](#create-the-storage-account-private-endpoint) i wyłącz dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z żądanych sieci wirtualnych może uzyskiwać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. W przypadku ograniczenia ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskuje się dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>Wyłącz dostęp do publicznego punktu końcowego konta magazynu
Gdy dostęp do publicznego punktu końcowego jest wyłączony, nadal można uzyskać dostęp do konta magazynu za pomocą jego prywatnych punktów końcowych. W przeciwnym razie prawidłowe żądania dotyczące publicznego punktu końcowego konta magazynu zostaną odrzucone. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>Ograniczanie dostępu do publicznego punktu końcowego konta magazynu do określonych sieci wirtualnych
W przypadku ograniczenia konta magazynu do określonych sieci wirtualnych można zezwalać na żądania kierowane do publicznego punktu końcowego z określonych sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. Można go używać z prywatnymi punktami końcowymi lub bez nich.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>Wyłącz dostęp do publicznego punktu końcowego usługi synchronizacji magazynu
Azure File Sync umożliwia ograniczenie dostępu do określonych sieci wirtualnych wyłącznie za poorednictwem prywatnych punktów końcowych. Azure File Sync nie obsługuje punktów końcowych usługi do ograniczania dostępu do publicznego punktu końcowego do określonych sieci wirtualnych. Oznacza to, że dwa stany dla publicznego punktu końcowego usługi synchronizacji magazynu są włączone i wyłączone.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Nie jest to możliwe za pomocą Azure Portal. Wybierz instrukcje na karcie Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby uzyskać instrukcje dotyczące sposobu wyłączania publicznego punktu końcowego usługi synchronizacji magazynu. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby wyłączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu, `incomingTrafficPolicy` Właściwość usługi synchronizacji magazynu zostanie ustawiona na wartość `AllowVirtualNetworksOnly` . Jeśli chcesz włączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu, ustaw `incomingTrafficPolicy` na `AllowAllTraffic` zamian. Pamiętaj, aby zastąpić `<storage-sync-service-resource-group>` i `<storage-sync-service>` .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzResource `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -ResourceName $storageSyncServiceName `
        -ResourceType "Microsoft.StorageSync/storageSyncServices"

$storageSyncService.Properties.incomingTrafficPolicy = "AllowVirtualNetworksOnly"
$storageSyncService = $storageSyncService | Set-AzResource -Confirm:$false -Force
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby wyłączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu, `incomingTrafficPolicy` Właściwość usługi synchronizacji magazynu zostanie ustawiona na wartość `AllowVirtualNetworksOnly` . Jeśli chcesz włączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu, ustaw `incomingTrafficPolicy` na `AllowAllTraffic` zamian. Pamiętaj, aby zastąpić `<storage-sync-service-resource-group>` i `<storage-sync-service>` .

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

az resource update \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --set "properties.incomingTrafficPolicy=AllowVirtualNetworksOnly" \
        --output none
```
---

## <a name="see-also"></a>Zobacz także
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie funkcji Azure File Sync](storage-sync-files-deployment-guide.md)
