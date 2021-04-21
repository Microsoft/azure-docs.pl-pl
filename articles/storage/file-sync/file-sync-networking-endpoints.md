---
title: Konfigurowanie Azure File Sync sieciowych punktów końcowych | Microsoft Docs
description: Dowiedz się, jak skonfigurować Azure File Sync końcowe sieci.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b89e0f8209c0165b71eef3d143ea1a84ea8bf64e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796857"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Konfigurowanie punktów końcowych sieci usługi Azure File Sync
Azure Files i Azure File Sync dwa główne typy punktów końcowych do uzyskiwania dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w sieci wirtualnej i mają prywatny adres IP z przestrzeni adresowej tej sieci wirtualnej.

W przypadku Azure Files i Azure File Sync, obiekty zarządzania platformy Azure, konto magazynu i usługa synchronizacji magazynu, kontrolują zarówno publiczne, jak i prywatne punkty końcowe. Konto magazynu jest konstrukcją zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob lub kolejki. Usługa synchronizacji magazynu to konstrukcja zarządzania reprezentująca zarejestrowane serwery, które są serwerami plików systemu Windows z ustanowioną relacją zaufania z usługą Azure File Sync i grupami synchronizacji, które definiują topologię relacji synchronizacji. 

W tym artykule opisano sposób konfigurowania punktów końcowych sieci dla Azure Files i Azure File Sync. Aby dowiedzieć się więcej na temat konfigurowania punktów końcowych sieci w celu uzyskiwania bezpośredniego dostępu do udziałów plików platformy Azure zamiast buforowania lokalnego za pomocą usługi Azure File Sync, zobacz Konfigurowanie punktów końcowych Azure Files [sieci.](../files/storage-files-networking-endpoints.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

Zalecamy [przeczytanie Azure File Sync zagadnienia dotyczące sieci](file-sync-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne 
W tym artykule przyjęto założenie, że:
- Masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Udział plików platformy Azure został już utworzony na koncie magazynu, z którym chcesz nawiązać połączenie ze środowisk lokalnych. Aby dowiedzieć się, jak utworzyć udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- Utworzono już usługę synchronizacji magazynu i zarejestrowano w nim serwer plików systemu Windows. Aby dowiedzieć się, jak wdrożyć Azure File Sync, [zobacz Wdrażanie Azure File Sync](file-sync-deployment-guide.md).

Dodatkowo:
- Jeśli zamierzasz używać programu Azure PowerShell, [zainstaluj najnowszą wersję programu](/powershell/azure/install-az-ps).
- Jeśli zamierzasz używać interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję .](/cli/azure/install-azure-cli)

## <a name="create-the-private-endpoints"></a>Tworzenie prywatnych punktów końcowych
Podczas tworzenia prywatnego punktu końcowego dla zasobu platformy Azure wdrażane są następujące zasoby:

- **Prywatny punkt końcowy:** zasób platformy Azure reprezentujący prywatny punkt końcowy dla konta magazynu lub usługę synchronizacji magazynu. Można to myśleć jak o zasobie, który łączy zasób platformy Azure i interfejs sieciowy.
- **Interfejs sieciowy ( NIC):** interfejs sieciowy, który przechowuje prywatny adres IP w określonej sieci wirtualnej/podsieci. Jest to dokładnie ten sam zasób, który jest wdrażany podczas wdrażania maszyny wirtualnej, jednak zamiast przypisywać ją do maszyny wirtualnej, jest ona własnością prywatnego punktu końcowego.
- **Prywatna strefa DNS:** jeśli wcześniej nie wdrożono prywatnego punktu końcowego dla tej sieci wirtualnej, dla sieci wirtualnej zostanie wdrożona nowa prywatna strefa DNS. Rekord DNS A zostanie również utworzony dla zasobu platformy Azure w tej strefie DNS. Jeśli w tej sieci wirtualnej wdrożono już prywatny punkt końcowy, do istniejącej strefy DNS zostanie dodany nowy rekord A dla zasobu platformy Azure. Wdrożenie strefy DNS jest opcjonalne, ale zdecydowanie zaleca się uproszczenie wymaganego zarządzania systemem DNS.

> [!Note]  
> W tym artykule używane są sufiksy DNS dla regionów publicznych platformy Azure, kont `core.windows.net` magazynu i usług synchronizacji `afs.azure.net` magazynu. Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura azure us government — wystarczy zastąpić odpowiednie sufiksy dla twojego środowiska.

### <a name="create-the-storage-account-private-endpoint"></a>Tworzenie prywatnego punktu końcowego konta magazynu
# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie DNS zgodnie z opisem w temacie Konfigurowanie przekazywania DNS dla usługi [Azure Files,](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany, uruchamiając następujące polecenia w programie PowerShell, wierszu polecenia lub terminalu (działa to w systemach Windows, Linux lub macOS). Należy zastąpić `<storage-account-name>` odpowiednią nazwą konta magazynu:

```console
nslookup <storage-account-name>.file.core.windows.net
```

Jeśli wszystko działa prawidłowo, powinny zostać wyświetlone następujące dane wyjściowe, gdzie to prywatny adres IP prywatnego punktu końcowego w sieci wirtualnej (dane wyjściowe widoczne dla `192.168.0.5` systemu Windows):

```output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie DNS zgodnie z opisem w temacie Konfigurowanie przekazywania DNS dla usługi [Azure Files,](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany za pomocą następujących poleceń:

```powershell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Jeśli wszystko zadziałało pomyślnie, powinny zostać wyświetlony następujące dane wyjściowe, gdzie to prywatny adres IP prywatnego punktu końcowego `192.168.0.5` w sieci wirtualnej:

```output
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

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie DNS zgodnie z opisem w temacie Konfigurowanie przekazywania DNS dla usługi [Azure Files,](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany za pomocą następujących poleceń:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Jeśli wszystko zadziałało pomyślnie, powinny zostać wyświetlony następujące dane wyjściowe, gdzie to prywatny adres IP prywatnego punktu końcowego `192.168.0.5` w sieci wirtualnej:

```output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-service-private-endpoint"></a>Tworzenie prywatnego punktu końcowego usługi synchronizacji magazynu
> [!Important]  
> Aby można było używać prywatnych punktów końcowych w zasobie usługi synchronizacji magazynu, należy użyć agenta Azure File Sync w wersji 10.1 lub większej. Wersje agenta wcześniejsze niż 10.1 nie obsługują prywatnych punktów końcowych w usłudze synchronizacji magazynu. Wszystkie wcześniejsze wersje agenta obsługują prywatne punkty końcowe w zasobie konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do **Centrum usługi Private Link,** wpisując *Private Link* na pasku wyszukiwania w górnej części Azure Portal. W spisie treści aplikacji wybierz Centrum usługi Private Link prywatne punkty **końcowe,** a następnie pozycję **+ Dodaj,** aby utworzyć nowy prywatny punkt końcowy.

[![Zrzut ekranu przedstawiający centrum łącza prywatnego](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Wynikowy kreator ma wiele stron do ukończenia.

W bloku **Podstawowe wybierz** żądaną grupę zasobów, nazwę i region prywatnego punktu końcowego. Może to być dowolny sposób. Nie muszą one w żaden sposób odpowiadać usłudze synchronizacji magazynu, chociaż należy utworzyć prywatny punkt końcowy w tym samym regionie, w którym znajduje się sieć wirtualna, w której chcesz utworzyć prywatny punkt końcowy.

![Zrzut ekranu przedstawiający sekcję Podstawy sekcji tworzenie prywatnego punktu końcowego](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

W bloku **Zasób** wybierz przycisk radiowy Połącz z **zasobem platformy Azure w katalogu**. W obszarze **Typ zasobu** wybierz **pozycję Microsoft.StorageSync/storageSyncServices** jako typ zasobu. 

Blok **Konfiguracja** umożliwia wybranie określonej sieci wirtualnej i podsieci, do których chcesz dodać prywatny punkt końcowy. Wybierz tę samą sieć wirtualną, która jest używana dla konta magazynu powyżej. Blok Konfiguracja zawiera również informacje dotyczące tworzenia/aktualizowania prywatnej strefy DNS.

Kliknij **pozycję Przejrzyj i utwórz,** aby utworzyć prywatny punkt końcowy.

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

Jeśli wszystko działa prawidłowo, powinny zostać wyświetlony następujące dane wyjściowe, w których , , i są prywatnymi adresami IP przypisanymi `192.168.1.4` `192.168.1.5` do `192.168.1.6` `192.168.1.7` prywatnego punktu końcowego:

```output
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
Aby utworzyć prywatny punkt końcowy dla usługi synchronizacji magazynu, najpierw musisz uzyskać odwołanie do usługi synchronizacji magazynu. Pamiętaj, aby zastąpić wartości i `<storage-sync-service-resource-group>` `<storage-sync-service>` prawidłowymi wartościami dla swojego środowiska. Następujące polecenia programu PowerShell zakładają, że używasz już wypełnionych informacji o sieci wirtualnej z powyższych. 

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

Aby utworzyć prywatny punkt końcowy, należy utworzyć połączenie usługi łącza prywatnego z usługą synchronizacji magazynu. Połączenie z połączeniem prywatnym jest elementem wejściowym tworzenia prywatnego punktu końcowego.

```powershell 
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

Utworzenie prywatnej strefy DNS platformy Azure umożliwia rozpoznawanie nazw hostów dla usługi synchronizacji magazynu, takich jak , jako prawidłowych prywatnych adresów IP dla usługi synchronizacji magazynu w sieci `mysssmanagement.westus2.afs.azure.net` wirtualnej. Chociaż jest to opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest jawnie wymagane, aby agent Azure File Sync uzyskać dostęp do usługi synchronizacji magazynu. 

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

```powershell 
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
Aby utworzyć prywatny punkt końcowy dla usługi synchronizacji magazynu, najpierw musisz uzyskać odwołanie do usługi synchronizacji magazynu. Pamiętaj, aby zastąpić wartości i `<storage-sync-service-resource-group>` `<storage-sync-service>` prawidłowymi wartościami dla swojego środowiska. W poniższych poleceniach interfejsu wiersza polecenia założono, że używasz już wypełnionych informacji o sieci wirtualnej z powyższych danych. 

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

Aby utworzyć prywatny punkt końcowy, należy najpierw upewnić się, że zasady sieci prywatnego punktu końcowego podsieci są wyłączone. Następnie możesz utworzyć prywatny punkt końcowy za pomocą `az network private-endpoint create` polecenia .

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

Utworzenie prywatnej strefy DNS platformy Azure umożliwia rozpoznawanie nazw hostów dla usługi synchronizacji magazynu, takich jak , do poprawnych prywatnych adresów IP dla usługi synchronizacji magazynu w sieci `mysssmanagement.westus2.afs.azure.net` wirtualnej. Chociaż jest to opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest jawnie wymagane, aby agent Azure File Sync uzyskać dostęp do usługi synchronizacji magazynu. 

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
Możesz ograniczyć dostęp do publicznych punktów końcowych zarówno konta magazynu, jak i usług synchronizacji magazynu. Ograniczenie dostępu do publicznego punktu końcowego zapewnia dodatkowe zabezpieczenia, zapewniając, że pakiety sieciowe są akceptowane tylko z zatwierdzonych lokalizacji. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>Ograniczanie dostępu do publicznego punktu końcowego konta magazynu
Ograniczenie dostępu do publicznego punktu końcowego jest wykonywane przy użyciu ustawień zapory konta magazynu. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej. Istnieją dwa podejścia do ograniczania dostępu do konta magazynu do sieci wirtualnej:

- [Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu i](#create-the-storage-account-private-endpoint) wyłącz dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z odpowiednich sieci wirtualnych może uzyskać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej. Działa to przy użyciu możliwości sieci wirtualnej nazywanej *punktami końcowymi usługi*. Po ograniczeniu ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskujesz dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>Wyłączanie dostępu do publicznego punktu końcowego konta magazynu
Gdy dostęp do publicznego punktu końcowego jest wyłączony, dostęp do konta magazynu jest nadal możliwy za pośrednictwem jego prywatnych punktów końcowych. W przeciwnym razie prawidłowe żądania do publicznego punktu końcowego konta magazynu zostaną odrzucone. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>Ograniczanie dostępu do publicznego punktu końcowego konta magazynu do określonych sieci wirtualnych
Po ograniczeniu konta magazynu do określonych sieci wirtualnych można zezwalać na żądania do publicznego punktu końcowego z określonych sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej nazywanej *punktami końcowymi usługi*. Można jej używać z prywatnymi punktami końcowymi lub bez niego.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>Wyłączanie dostępu do publicznego punktu końcowego usługi synchronizacji magazynu
Azure File Sync umożliwia ograniczenie dostępu do określonych sieci wirtualnych tylko za pośrednictwem prywatnych punktów końcowych; Azure File Sync nie obsługuje punktów końcowych usługi w celu ograniczenia dostępu do publicznego punktu końcowego do określonych sieci wirtualnych. Oznacza to, że dwa stany publicznego punktu końcowego usługi synchronizacji magazynu są włączone i wyłączone.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Nie jest to możliwe za pośrednictwem Azure Portal. Wybierz kartę Azure PowerShell, aby uzyskać instrukcje dotyczące wyłączania publicznego punktu końcowego usługi synchronizacji magazynu. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby wyłączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu, ustawimy właściwość w usłudze `incomingTrafficPolicy` synchronizacji magazynu na wartość `AllowVirtualNetworksOnly` . Jeśli chcesz włączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu, zamiast tego ustaw `incomingTrafficPolicy` wartość `AllowAllTraffic` na . Pamiętaj, aby zastąpić `<storage-sync-service-resource-group>` i `<storage-sync-service>` .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

Set-AzStorageSyncService `
    -ResourceGroupName $storageSyncServiceResourceGroupName `
    -Name $storageSyncServiceName `
    -IncomingTrafficPolicy AllowVirtualNetworksOnly
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Interfejs wiersza polecenia platformy Azure nie obsługuje ustawiania `incomingTrafficPolicy` właściwości w usłudze synchronizacji magazynu. Wybierz kartę Azure PowerShell, aby uzyskać instrukcje dotyczące wyłączania publicznego punktu końcowego usługi synchronizacji magazynu.

---

## <a name="azure-policy"></a>Azure Policy
Azure Policy wymuszanie standardów organizacji i ocenianie zgodności z tymi standardami na dużą skalę. Azure Files i Azure File Sync uwidocznić kilka przydatnych zasad sieciowych inspekcji i korygowania, które ułatwiają monitorowanie i automatyzowanie wdrożenia.

Zasady przejmują inspekcję środowiska i ostrzegają, jeśli konta magazynu lub usługi synchronizacji magazynu różnią się od zdefiniowanego zachowania. Na przykład jeśli publiczny punkt końcowy jest włączony, gdy zasady zostały ustawione tak, aby publiczne punkty końcowe były wyłączone. Modyfikowanie/wdrażanie zasad to kolejny krok i aktywne modyfikowanie zasobu (takiego jak usługa synchronizacji magazynu) lub wdrażanie zasobów (takich jak prywatne punkty końcowe) w celu dostosowania do zasad.

Następujące wstępnie zdefiniowane zasady są dostępne dla Azure Files i Azure File Sync:

| Akcja | Usługa | Warunek | Nazwa zasady |
|-|-|-|-|
| Inspekcja | Azure Files | Publiczny punkt końcowy konta magazynu jest włączony. Aby uzyskać więcej informacji, zobacz Wyłączanie [dostępu do publicznego punktu końcowego](#disable-access-to-the-storage-account-public-endpoint) konta magazynu. | Konta magazynu powinny ograniczać dostęp sieciowy |
| Inspekcja | Azure File Sync | Publiczny punkt końcowy usługi synchronizacji magazynu jest włączony. Aby [uzyskać więcej informacji, zobacz Wyłączanie dostępu do publicznego](#disable-access-to-the-storage-sync-service-public-endpoint) punktu końcowego usługi synchronizacji magazynu. | Dostęp do sieci publicznej powinien być wyłączony dla Azure File Sync |
| Inspekcja | Azure Files | Konto magazynu wymaga co najmniej jednego prywatnego punktu końcowego. Aby uzyskać więcej informacji, zobacz Tworzenie [prywatnego punktu końcowego](#create-the-storage-account-private-endpoint) konta magazynu. | Konto magazynu powinno używać połączenia z łączem prywatnym |
| Inspekcja | Azure File Sync | Usługa synchronizacji magazynu wymaga co najmniej jednego prywatnego punktu końcowego. Aby [uzyskać więcej informacji, zobacz Tworzenie prywatnego punktu końcowego usługi synchronizacji](#create-the-storage-sync-service-private-endpoint) magazynu. | Azure File Sync powinien używać linku prywatnego |
| Modyfikowanie | Azure File Sync | Wyłącz publiczny punkt końcowy usługi synchronizacji magazynu. | Modyfikowanie — konfigurowanie Azure File Sync w celu wyłączenia dostępu do sieci publicznej |
| Wdróż | Azure File Sync | Wdrażanie prywatnego punktu końcowego dla usługi synchronizacji magazynu. | Konfigurowanie Azure File Sync z prywatnymi punktami końcowymi |
| Wdróż | Azure File Sync | Wdrażanie rekordu A w privatelink.afs.azure.net dns. | Konfigurowanie Azure File Sync do używania prywatnych stref DNS |

### <a name="set-up-a-private-endpoint-deployment-policy"></a>Konfigurowanie zasad wdrażania prywatnego punktu końcowego
Aby skonfigurować zasady wdrażania prywatnego punktu końcowego, przejdź do Azure Portal [i](https://portal.azure.com/)wyszukaj **zasady**. Centrum Azure Policy powinno być najlepszym wynikiem. Przejdź do **definicji**  >  **tworzenia** w spisie treści Centrum zasad. Wynikowe okienko **Definicje** zawiera wstępnie zdefiniowane zasady dla wszystkich usług platformy Azure. Aby znaleźć określone zasady,  wybierz kategorię Magazyn w filtrze kategorii lub wyszukaj pozycję **Azure File Sync z prywatnymi punktami końcowymi.** Wybierz **pozycję ...** i przypisz, aby utworzyć nowe zasady z definicji. 

Blok **Podstawy** Kreatora  przypisywania zasad umożliwia ustawienie zakresu, zasobu lub listy wykluczeń grupy zasobów oraz nadaj zasadom przyjazną nazwę, która pomoże je odróżnić. Nie musisz ich modyfikować, aby zasady działały, ale możesz, jeśli chcesz wprowadzić modyfikacje. Wybierz **przycisk Dalej,** aby przejść do **strony** Parametry. 

W  bloku Parametry wybierz pozycję **...** obok listy rozwijanej **privateEndpointSubnetId,** aby wybrać sieć wirtualną i podsieć, w której powinny zostać wdrożone prywatne punkty końcowe dla zasobów usługi synchronizacji magazynu. Wynikowy kreator może potrwać kilka sekund, aby załadować dostępne sieci wirtualne w ramach subskrypcji. Wybierz odpowiednią sieć wirtualną/podsieć dla swojego środowiska, a następnie kliknij pozycję **Wybierz**. Wybierz **przycisk Dalej,** aby przejść do **bloku Korygowanie.**

Aby prywatny punkt końcowy został wdrożony w przypadku zidentyfikowania usługi synchronizacji magazynu  bez prywatnego punktu końcowego, należy wybrać zadanie Tworzenia korygowania na **stronie Korygowanie.** Na koniec wybierz **pozycję Przeglądanie + tworzenie,** aby przejrzeć przypisanie zasad, i **pozycję Utwórz,** aby je utworzyć.

Wynikowe przypisanie zasad będzie wykonywane okresowo i nie będzie uruchamiane natychmiast po utworzeniu.

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
- [Wdrażanie funkcji Azure File Sync](file-sync-deployment-guide.md)
