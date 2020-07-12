---
title: Skalowanie w górę typu węzła Service Fabric platformy Azure
description: Dowiedz się, jak skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a42e33fa87b6cf7966368481ef6d3920511919e3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260457"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skalowanie w górę węzła klastra usługi Service Fabric podstawowego typu
W tym artykule opisano sposób skalowania w górę typu węzła podstawowego klastra Service Fabric przez zwiększenie zasobów maszyny wirtualnej. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Po utworzeniu klastra Service Fabric można skalować typ węzła klastra w pionie (zmienić zasoby węzłów) lub uaktualnić system operacyjny maszyn wirtualnych typu węzła.  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

> [!WARNING]
> Nie należy podejmować próby przeprowadzenia skalowania w górę podstawowego typu węzła, jeśli stan klastra jest niepoprawny, co spowoduje dalsze niestabilne działanie klastra.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Proces uaktualniania rozmiaru i systemu operacyjnego dla maszyn wirtualnych typu węzła podstawowego
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

## <a name="set-up-the-test-cluster"></a>Konfigurowanie klastra testowego

Zacznij od pobrania dwóch zestawów plików, które będą potrzebne w tym samouczku, przed [szablonem]() i [parametrami]() oraz [szablonem]() i [parametrami]().

Następnie zaloguj się do konta platformy Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Ten samouczek przeprowadzi Cię przez scenariusz tworzenia certyfikatu z podpisem własnym. Aby użyć istniejącego certyfikatu z Azure Key Vault, Pomiń ten krok i w zamian wykonaj duplikaty kroków z [używania istniejącego certyfikatu w celu wdrożenia klastra](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Wygeneruj certyfikat z podpisem własnym i Wdróż klaster

Najpierw Przypisz zmienne, które będą potrzebne do wdrożenia klastra Service Fabric. Dostosuj wartości dla `resourceGroupName` ,, `certSubjectName` `parameterFilePath` i `templateFilePath` dla określonego konta i środowiska:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Upewnij się, że `certOutputFolder` Lokalizacja istnieje na komputerze lokalnym przed uruchomieniem polecenia, aby wdrożyć nowy klaster Service Fabric.

Następnie otwórz *Deploy-2NodeTypes-2ScaleSets.parameters.jsw* pliku i Dostosuj wartości dla i, `clusterName` `dnsName` aby odpowiadały wartościom dynamicznym ustawionym w programie PowerShell, i Zapisz zmiany.

Następnie wdróż klaster testowy Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Po zakończeniu wdrażania zlokalizuj plik *PFX* ( `$certPfx` ) na komputerze lokalnym i zaimportuj go do magazynu certyfikatów:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Operacja zwróci odcisk palca certyfikatu, który zostanie użyty do nawiązania połączenia z nowym klastrem i sprawdzenia jego stanu kondycji.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Nawiąż połączenie z nowym klastrem i sprawdź stan kondycji

Połącz się z klastrem i upewnij się, że wszystkie jego węzły są w dobrej kondycji (zastępując `clusterName` `thumb` zmienne i dla klastra):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Jesteśmy gotowi do rozpoczęcia procedury uaktualniania.

## <a name="upgrade-the-primary-node-type-vms"></a>Uaktualnij podstawowe maszyny wirtualne typu Node

Po podjęciu decyzji o uaktualnieniu podstawowego typu węzła maszyny wirtualne Dodaj nowy zestaw skalowania do typu węzła podstawowego, tak że typ węzła podstawowego ma teraz dwa zestawy skalowania. Podano przykładowy plik [szablonu](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) i [parametrów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) , aby wyświetlić niezbędne zmiany. Nowe maszyny wirtualne zestawu skalowania są w standardowym rozmiarze D4_V2 i uruchamiane są systemy Windows Server 2016 Datacenter z kontenerami. Nowy zestaw skalowania jest również dodawany do nowego modułu równoważenia obciążenia i publicznego adresu IP. 

Aby znaleźć nowy zestaw skalowania w szablonie, wyszukaj zasób "Microsoft. COMPUTE/virtualMachineScaleSets" o nazwie odpowiadającej parametrowi vmNodeType2Name. Nowy zestaw skalowania jest dodawany do typu węzła podstawowego przy użyciu właściwości->virtualMachineProfile->extensionProfile->Extensions->Properties->Settings->nodeTypeRef.

### <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon

Dostosuj `parameterFilePath` i `templateFilePath` w razie konieczności, a następnie uruchom następujące polecenie:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Po zakończeniu wdrożenia Sprawdź kondycję klastra ponownie i upewnij się, że wszystkie węzły (w oryginalnym i w nowym zestawie skalowania) są w dobrej kondycji.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrowanie węzłów do nowego zestawu skalowania

Teraz można rozpocząć wyłączanie węzłów oryginalnego zestawu skalowania. Po wyłączeniu tych węzłów usługi systemowe i węzły inicjatora są migrowane do maszyn wirtualnych nowego zestawu skalowania, ponieważ są również oznaczone jako podstawowy typ węzła.

W przypadku skalowania w górę typów węzłów innych niż podstawowe w tym kroku należy zmodyfikować ograniczenie umieszczania usługi w celu uwzględnienia nowego typu węzła i zestawu skalowania maszyn wirtualnych, a następnie zmniejszyć liczbę wystąpień starego zestawu skalowania maszyn wirtualnych do zera, jeden węzeł w danym momencie (w celu zapewnienia, że usunięcie węzła nie ma wpływu na niezawodność klastra).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Użyj Service Fabric Explorer do monitorowania migracji węzłów inicjatora do nowego zestawu skalowania, a postęp węzłów w pierwotnym zestawie skalowania można *wyłączyć* , aby *wyłączyć stan wyłączony* .

> [!NOTE]
> Ukończenie operacji wyłączania dla wszystkich węzłów oryginalnego zestawu skalowania może zająć trochę czasu. W celu zagwarantowania spójności danych tylko jeden węzeł inicjatora może ulec zmianie jednocześnie. Każda zmiana węzła inicjatora wymaga aktualizacji klastra; w ten sposób wymiana węzła inicjatora wymaga dwóch uaktualnień klastra (jednego z nich do dodania i usunięcia węzła). Uaktualnienie pięciu węzłów inicjatora w tym przykładowym scenariuszu spowoduje przeprowadzenie dziesięciu uaktualnień klastra.

## <a name="remove-the-original-scale-set"></a>Usuń oryginalny zestaw skalowania

Po zakończeniu operacji wyłączania Usuń zestaw skalowania.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

W Service Fabric Explorer usunięte węzły (i w rezultacie *stan kondycji klastra*) będą teraz wyświetlane w stanie *błędu* .

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Usuwanie starego modułu równoważenia obciążenia i aktualizowanie ustawień DNS

Teraz możemy usunąć zasoby związane z starym typem węzła podstawowego, rozpoczynając od modułu równoważenia obciążenia i starego publicznego adresu IP. 

```powershell
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
```

Następnie zaktualizujemy ustawienia DNS nowego publicznego adresu IP, aby dublować ustawienia ze starego publicznego adresu IP typu węzła podstawowego.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Jeszcze raz Sprawdź kondycję klastra

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Na koniec Usuń stan węzła dla każdego z węzłów pokrewnych. Jeśli poziom trwałości starego zestawu skalowania to Silver lub Gold, spowoduje to nastąpić automatycznie.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Typ węzła podstawowego klastra został teraz uaktualniony. Sprawdź, czy wdrożone aplikacje działają prawidłowo i kondycja klastra jest prawidłowa.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać typ węzła do klastra](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).
