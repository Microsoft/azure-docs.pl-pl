---
title: Skalowanie w górę typu węzła Service Fabric platformy Azure
description: Dowiedz się, jak skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 4dbb9e4fbfeb27c5b8b13f70207888cf37bbb0e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998939"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skalowanie w górę węzła klastra usługi Service Fabric podstawowego typu
W tym artykule opisano sposób skalowania w górę typu węzła podstawowego klastra Service Fabric przez zwiększenie zasobów maszyny wirtualnej. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Po utworzeniu klastra Service Fabric można skalować typ węzła klastra w pionie (zmienić zasoby węzłów) lub uaktualnić system operacyjny maszyn wirtualnych typu węzła.  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

> [!WARNING]
> Nie rozpoczynaj zmiany podstawowej jednostki SKU maszyny wirtualnej NodeType, jeśli kondycja klastra jest zła. Jeśli kondycja klastra jest zła, w przypadku próby zmiany jednostki SKU maszyny wirtualnej będzie można jeszcze bardziej zawęzić ten klaster.
>
> Zalecamy, aby nie zmieniać jednostki SKU maszyny wirtualnej zestawu skalowania/typu węzła, chyba że jest on uruchomiony w wersji [Silver lub nowszej](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Zmiana rozmiaru jednostki SKU maszyny wirtualnej jest operacją infrastruktury służącej do wypróbowania danych. Bez możliwości opóźniania lub monitorowania tej zmiany możliwe jest, że operacja może spowodować utratę danych dla usług stanowych lub spowodować inne nieprzewidziane problemy z działaniem, nawet w przypadku obciążeń bezstanowych. Oznacza to, że typ węzła podstawowego, w którym działa usługi systemowej usługi Service Fabric, lub dowolny typ węzła, który uruchamia aplikację stanową.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uaktualnij rozmiar i system operacyjny z maszyn wirtualnych typu węzła podstawowego
Oto proces aktualizowania rozmiaru maszyny wirtualnej i systemu operacyjnego dla maszyn wirtualnych typu węzła podstawowego.  Po uaktualnieniu maszyny wirtualne typu węzła podstawowego mają rozmiar standardowy D4_V2 i uruchomiono system Windows Server 2016 Datacenter z kontenerami.

> [!WARNING]
> Przed podjęciem próby wykonania tej procedury w klastrze produkcyjnym zalecamy zbadanie przykładowych szablonów i zweryfikowanie procesu względem klastra testowego. Klaster jest również niedostępny przez czas. NIE można wprowadzać zmian w wielu VMSS zadeklarowanych jako te same NodeType równolegle; należy wykonać rozdzielone operacje wdrażania, aby zastosować zmiany do poszczególnych NodeType VMSS indywidualnie.

1. Wdróż początkowy klaster z dwoma typami węzłów i dwoma zestawami skalowania (jeden zestaw skalowania na typ węzła) przy użyciu tych przykładowych plików [szablonów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) i [parametrów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) .  Oba zestawy skalowania mają rozmiar standardowy D2_V2 i uruchomiono system Windows Server 2012 R2 Datacenter.  Poczekaj, aż klaster ukończy uaktualnienie punktu odniesienia.   
2. Opcjonalne — Wdróż stanowy przykład w klastrze.
3. Po podjęciu decyzji o uaktualnieniu podstawowego typu węzła maszyny wirtualne Dodaj nowy zestaw skalowania do typu węzła podstawowego przy użyciu tych przykładowych plików [szablonów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) i [parametrów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) , tak aby typ węzła podstawowego miał teraz dwa zestawy skalowania.  Usługi systemowe i aplikacje użytkownika mogą migrować między maszynami wirtualnymi w dwóch różnych zestawach skalowania.  Nowe maszyny wirtualne zestawu skalowania mają rozmiar standardowy D4_V2 i uruchomiono system Windows Server 2016 Datacenter z kontenerami.  Nowy zestaw skalowania jest również dodawany do nowego modułu równoważenia obciążenia i publicznego adresu IP.  
    Aby znaleźć nowy zestaw skalowania w szablonie, wyszukaj zasób "Microsoft. COMPUTE/virtualMachineScaleSets" o nazwie odpowiadającej parametrowi *vmNodeType2Name* .  Nowy zestaw skalowania jest dodawany do typu węzła podstawowego przy użyciu właściwości->virtualMachineProfile->extensionProfile->Extensions->Properties->Settings->nodeTypeRef.
4. Sprawdź kondycję klastra i sprawdź, czy wszystkie węzły są w dobrej kondycji.
5. Wyłącz węzły w starym zestawie skalowania typu węzła podstawowego z zamiarem usunięcia węzła. Można wyłączyć wszystkie jednocześnie, a operacje są umieszczane w kolejce. Poczekaj, aż wszystkie węzły zostaną wyłączone, co może chwilę potrwać.  Ponieważ starsze węzły w typie węzła są wyłączone, usługi systemowe i węzły inicjatora są migrowane do maszyn wirtualnych nowego zestawu skalowania w podstawowym typie węzła.
6. Usuń starszy zestaw skalowania z typu węzła podstawowego. (Po wyłączeniu węzłów, jak w kroku 5, w bloku zestawu skalowania maszyn wirtualnych w Azure Portal Cofnij alokację węzłów ze starego typu węzła o jeden).
7. Usuń moduł równoważenia obciążenia skojarzony ze starym zestawem skalowania. Klaster jest niedostępny, gdy nowy publiczny adres IP i moduł równoważenia obciążenia są skonfigurowane dla nowego zestawu skalowania.  
8. Zapisz ustawienia DNS publicznego adresu IP skojarzonego z starym zestawem skalowania typu węzła podstawowego w zmiennej i Usuń ten publiczny adres IP.
9. Zastąp ustawienia DNS publicznego adresu IP skojarzonego z nowym zestawem skalowania typu węzła podstawowego za pomocą ustawień DNS usuniętego publicznego adresu IP.  Klaster jest teraz ponownie dostępny.
10. Usuń stan węzła z klastra.  Jeśli poziom trwałości starego zestawu skalowania to Silver lub Gold, ten krok jest wykonywany automatycznie przez system.
11. Jeśli aplikacja stanowa została wdrożona w poprzednim kroku, sprawdź, czy aplikacja działa.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać typ węzła do klastra](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

