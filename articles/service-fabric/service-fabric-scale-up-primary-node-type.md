---
title: Skalowanie w górę podstawowego typu węzła Service Fabric platformy Azure
description: Skalowanie klastra Service Fabric w pionie przez dodanie nowego typu węzła i usunięcie poprzedniego.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251185"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skalowanie w górę węzła klastra usługi Service Fabric podstawowego typu

W tym artykule opisano sposób skalowania w górę typu węzła podstawowego klastra Service Fabric przy minimalnym przestoju. Ogólna strategia uaktualniania Service Fabric węzła klastra ma:

1. Dodaj nowy typ węzła do klastra Service Fabric, którego kopia zapasowa została wykonana przez uaktualnioną (lub zmodyfikowaną) jednostkę SKU i konfigurację zestawu skalowania maszyn wirtualnych. Ten krok obejmuje również skonfigurowanie nowego modułu równoważenia obciążenia, podsieci i publicznego adresu IP dla zestawu skalowania.

1. Po uruchomieniu zarówno oryginalnego, jak i uaktualnionego zestawu skalowania obok siebie należy wyłączyć oryginalne wystąpienia węzłów pojedynczo, tak aby usługi systemowe (lub repliki usług stanowych) były migrowane do nowego zestawu skalowania.

1. Sprawdź, czy klaster i nowe węzły są w dobrej kondycji, a następnie usuń oryginalny zestaw skalowania (i powiązane zasoby) oraz stan węzła dla usuniętych węzłów.

Poniższe instrukcje przeprowadzą Cię przez proces aktualizowania rozmiaru maszyny wirtualnej i systemu operacyjnego dla maszyn wirtualnych typu węzła podstawowego w przykładowym klastrze z [trwałością Silver](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), a następnie za pomocą jednego zestawu skalowania z pięcioma węzłami. Będziemy uaktualniać typ węzła podstawowego:

- Od rozmiaru maszyny wirtualnej *Standard_D2_V2* do *D4_V2 standardowej*, a
- Z systemu operacyjnego maszyny wirtualnej *systemu Windows server 2016 Datacenter z kontenerami* do *systemu Windows Server 2019 Datacenter z kontenerami*.

> [!WARNING]
> Przed podjęciem próby wykonania tej procedury w klastrze produkcyjnym zalecamy zbadanie przykładowych szablonów i zweryfikowanie procesu względem klastra testowego. Klaster może być również niedostępny przez krótki czas.
>
> Nie należy podejmować próby przeprowadzenia skalowania w górę podstawowego typu węzła, jeśli stan klastra jest niepoprawny, co spowoduje dalsze niestabilne działanie klastra.

Oto przykładowe szablony wdrożenia platformy Azure, których będziemy używać do ukończenia tego przykładowego scenariusza uaktualniania: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Konfigurowanie klastra testowego

Skonfigurujmy wstępny klaster testowy Service Fabric. Najpierw [pobierz](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) Azure Resource Manager szablony przykładowe, których będziemy używać do realizacji tego scenariusza.

Następnie zaloguj się do konta platformy Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Następnie otwórz [*parameters.jsw*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) pliku i zaktualizuj wartość `clusterName` do wartości unikatowe (na platformie Azure).

Poniższe polecenia przeprowadzą Cię przez proces generowania nowego certyfikatu z podpisem własnym i wdrożenia klastra testowego. Jeśli masz już certyfikat, którego chcesz użyć, Pomiń, aby [użyć istniejącego certyfikatu do wdrożenia klastra](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Wygeneruj certyfikat z podpisem własnym i Wdróż klaster

Najpierw Przypisz zmienne, które będą potrzebne do wdrożenia klastra Service Fabric. Dostosuj wartości dla `resourceGroupName` ,,  `certSubjectName` `parameterFilePath` i `templateFilePath` dla określonego konta i środowiska:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> `certOutputFolder`Przed uruchomieniem polecenia w celu wdrożenia nowego klastra Service Fabric upewnij się, że lokalizacja istnieje na komputerze lokalnym.

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

Operacja zwróci odcisk palca certyfikatu, którego teraz można użyć do [nawiązania połączenia z nowym klastrem](#connect-to-the-new-cluster-and-check-health-status) i sprawdzenia stanu kondycji. (Pomiń poniższą sekcję, która stanowi alternatywne podejście do wdrożenia klastra).

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Używanie istniejącego certyfikatu do wdrożenia klastra

Alternatywnie można użyć istniejącego certyfikatu Azure Key Vault do wdrożenia klastra testowego. Aby to zrobić, musisz [uzyskać odwołania do Key Vault](#obtain-your-key-vault-references) i odcisku palca certyfikatu.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Następnie należy określić nazwę grupy zasobów dla klastra i ustawić `templateFilePath` `parameterFilePath` lokalizacje i:

> [!NOTE]
> Wskazana Grupa zasobów musi już istnieć i znajdować się w tym samym regionie co Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Na koniec Uruchom następujące polecenie, aby wdrożyć początkowy klaster testowy:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Nawiąż połączenie z nowym klastrem i sprawdź stan kondycji

Połącz się z klastrem i upewnij się, że wszystkie pięć jego węzłów są w dobrej kondycji (Zamień `clusterName` zmienne i na `thumb` własne wartości):

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

Dzięki temu wszystko jest gotowe do rozpoczęcia procedury uaktualniania.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Wdróż nowy typ węzła podstawowego z uaktualnionym zestawem skalowania

Aby przeprowadzić uaktualnienie (skalowanie w pionie) typu węzła, najpierw musimy wdrożyć nowy typ węzła z obsługą nowego zestawu skalowania i obsłużyć zasoby. Nowy zestaw skalowania zostanie oznaczony jako podstawowy ( `isPrimary: true` ), podobnie jak oryginalny zestaw skalowania (chyba że wykonujesz uaktualnienie typu węzła innego niż podstawowy). Zasoby utworzone w poniższej sekcji staną się ostatecznie nowym typem węzła podstawowego w klastrze, a oryginalne zasoby typu węzła podstawowego zostaną usunięte.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aktualizowanie szablonu klastra za pomocą uaktualnionego zestawu skalowania

Poniżej przedstawiono modyfikacje sekcji dotyczące oryginalnego szablonu wdrożenia klastra w celu dodania nowego typu węzła podstawowego i obsługi zasobów.

Wymagane zmiany w tym kroku zostały już wykonane w [*Step1-AddPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) w pliku szablonu, a poniższe informacje pozwolą szczegółowo opisać te zmiany. Jeśli wolisz, możesz pominąć wyjaśnienie i nadal [uzyskać informacje dotyczące Key Vault](#obtain-your-key-vault-references) i [wdrożyć zaktualizowany szablon](#deploy-the-updated-template) , który dodaje nowy typ węzła podstawowego do klastra.

> [!Note]
> Upewnij się, że używasz nazw unikatowych z oryginalnego typu węzła, zestawu skalowania, modułu równoważenia obciążenia, publicznego adresu IP i podsieci oryginalnego typu węzła podstawowego, ponieważ te zasoby zostaną usunięte w późniejszym kroku procesu.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Utwórz nową podsieć w istniejącej sieci wirtualnej

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Utwórz nowy publiczny adres IP z unikatowym wartość domainnamelabel

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Utwórz nowy moduł równoważenia obciążenia dla publicznego adresu IP

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Utwórz nowy zestaw skalowania maszyn wirtualnych (z uaktualnionymi jednostkami SKU maszyny wirtualnej i systemem operacyjnym)

Typ węzła ref

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Jednostka SKU maszyny wirtualnej

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

JEDNOSTKA SKU SYSTEMU OPERACYJNEGO

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Upewnij się również, że zawarto dodatkowe rozszerzenia, które są wymagane dla obciążenia.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Dodawanie nowego typu węzła podstawowego do klastra

Teraz, gdy nowy typ węzła (vmNodeType1Name) ma własną nazwę, podsieć, adres IP, moduł równoważenia obciążenia i zestaw skalowania, może ponownie użyć wszystkich innych zmiennych z oryginalnego typu węzła (takich jak `nt0applicationEndPort` , `nt0applicationStartPort` i `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Po zaimplementowaniu wszystkich zmian w plikach szablonu i parametrów przejdź do następnej sekcji, aby uzyskać informacje dotyczące Key Vault i wdrożyć aktualizacje w klastrze.

### <a name="obtain-your-key-vault-references"></a>Uzyskaj informacje o Key Vault

Do wdrożenia zaktualizowanej konfiguracji potrzebne są kilka odwołań do certyfikatu klastra przechowywanego w Key Vault. Najprostszym sposobem znalezienia tych wartości jest użycie Azure Portal. Potrzebne będą następujące elementy:

* **Adres URL Key Vault certyfikatu klastra.** Na Key Vault w Azure Portal wybierz pozycję **Certyfikaty**  >  *żądany*  >  **Identyfikator tajny** certyfikatu:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Odcisk palca certyfikatu klastra.** (Prawdopodobnie jest już to konieczne, jeśli [nawiązano połączenie z początkowym klastrem](#connect-to-the-new-cluster-and-check-health-status) w celu sprawdzenia jego stanu kondycji). W tym samym bloku certyfikatu (**Certyfikaty**  >  *żądanego certyfikatu*) w Azure Portal Skopiuj **odcisk palca SHA-1 programu X. 509 (szesnastkowo)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Identyfikator zasobu Key Vault.** Na Key Vault w Azure Portal wybierz pozycję **Właściwości**  >  **Identyfikator zasobu**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon

Dostosuj w `templateFilePath` razie konieczności i uruchom następujące polecenie:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Po zakończeniu wdrożenia Sprawdź kondycję klastra ponownie i upewnij się, że wszystkie węzły w obu typach węzłów są w dobrej kondycji.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrowanie węzłów inicjatora do nowego typu węzła

Teraz wszystko jest gotowe do zaktualizowania oryginalnego typu węzła jako niepodstawowy i uruchomienia jego węzłów. Po wyłączeniu węzłów usługi systemowe i węzły inicjatora klastra są migrowane do nowego zestawu skalowania.

### <a name="unmark-the-original-node-type-as-primary"></a>Usuń oznaczenie oryginalnego typu węzła jako podstawowego

Najpierw usuń `isPrimary` oznaczenie w szablonie z oryginalnego typu węzła.

```json
{
    "isPrimary": false,
}
```

Następnie wdróż szablon z aktualizacją. Spowoduje to zainicjowanie migracji węzłów inicjatora do nowego zestawu skalowania.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Ukończenie migracji węzłów inicjatora do nowego zestawu skalowania zajmie trochę czasu. W celu zagwarantowania spójności danych tylko jeden węzeł inicjatora może ulec zmianie jednocześnie. Każda zmiana węzła inicjatora wymaga aktualizacji klastra; w ten sposób wymiana węzła inicjatora wymaga dwóch uaktualnień klastra (jednego z nich do dodania i usunięcia węzła). Uaktualnienie pięciu węzłów inicjatora w tym przykładowym scenariuszu spowoduje przeprowadzenie dziesięciu uaktualnień klastra.

Aby monitorować migrację węzłów inicjatora do nowego zestawu skalowania, użyj Service Fabric Explorer. Węzły oryginalnego typu węzła (nt0vm) powinny mieć *wartość false* w kolumnie **is Node** , a te o nowym typie węzła (nt1vm) będą *prawdziwe*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Wyłącz węzły w oryginalnym zestawie skalowania typu węzła

Po przeprowadzeniu migracji wszystkich węzłów inicjatora do nowego zestawu skalowania można wyłączyć węzły oryginalnego zestawu skalowania.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Użyj Service Fabric Explorer, aby monitorować postęp węzłów w pierwotnym zestawie skalowania ze stanu *wyłączenia* na *wyłączony* .

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer wyświetlania stanu wyłączonych węzłów":::

W przypadku trwałości srebra i złota niektóre węzły przechodzą w stan wyłączenia, a inne mogą pozostać w stanie *wyłączenia* . W Service Fabric Explorer, sprawdź kartę **szczegóły** węzłów w obszarze wyłączanie stanu. Jeśli są wyświetlane *oczekujące sprawdzanie bezpieczeństwa* rodzaju *EnsurePartitionQuorem* (zapewnianie kworum dla partycji usługi infrastruktury), można bezpiecznie kontynuować.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Można kontynuować z zatrzymywaniem danych i usuwanie węzłów zablokowanych w stanie &quot;Wyłączanie&quot;, jeśli zawierają one oczekujące sprawdzanie bezpieczeństwa rodzaju &quot;EnsurePartitionQuorum&quot;.":::

Jeśli klaster jest trwałością Bronów, poczekaj, aż wszystkie węzły osiągną stan *wyłączony* .

### <a name="stop-data-on-the-disabled-nodes"></a>Zatrzymaj dane w wyłączonych węzłach

Teraz można zatrzymać dane na wyłączonych węzłach.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Usuń oryginalny typ węzła i oczyść jego zasoby

Wszystko jest gotowe do usunięcia oryginalnego typu węzła i skojarzonych z nim zasobów, aby zakończyć procedurę skalowania w pionie.

### <a name="remove-the-original-scale-set"></a>Usuń oryginalny zestaw skalowania

Najpierw usuń zestaw skalowania zapasowego typu węzła.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Usuwanie oryginalnych zasobów adresów IP i modułów równoważenia obciążenia

Można teraz usunąć oryginalny adres IP i zasoby modułu równoważenia obciążenia. W tym kroku zostanie również zaktualizowana nazwa DNS.

> [!Note]
> Ten krok jest opcjonalny, jeśli używasz już publicznego adresu IP i modułu równoważenia obciążenia *standardowej* jednostki SKU. W takim przypadku można mieć wiele zestawów skalowania/typów węzłów w ramach tego samego modułu równoważenia obciążenia.

Uruchom następujące polecenia, modyfikując `$lbname` wartość w razie konieczności.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Usuń stan węzła z oryginalnego typu węzła

W pierwotnych węzłach typu węzła zostanie wyświetlony *komunikat o błędzie* o **stanie kondycji**. Usuń stan węzła z klastra.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer powinien teraz odzwierciedlać tylko pięć węzłów nowego typu węzła (nt1vm), a wszystkie wartości kondycji są *poprawne*. Stan kondycji klastra nadal będzie wyświetlany *błąd*. Korygujemy to później przez zaktualizowanie szablonu w celu odzwierciedlenia najnowszych zmian i ponownego wdrożenia.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Aktualizowanie szablonu wdrożenia w celu odzwierciedlenia nowo skalowanego typu węzła podstawowego

Wymagane zmiany w tym kroku zostały już wprowadzone w [*Step3-CleanupOriginalPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) pliku szablonu, a poniższe sekcje objaśniają te zmiany szablonu szczegółowo. Jeśli wolisz, możesz pominąć wyjaśnienie i nadal [wdrażać zaktualizowany szablon](#deploy-the-finalized-template) i ukończyć samouczek.

#### <a name="update-the-cluster-management-endpoint"></a>Aktualizowanie punktu końcowego zarządzania klastrem

Zaktualizuj klaster `managementEndpoint` w szablonie wdrożenia, aby odwołać się do nowego adresu IP (przez aktualizację *VmNodeType0Name* z *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Usuń pierwotne odwołanie do typu węzła

Usuń pierwotne odwołanie do typu węzła z zasobu Service Fabric w szablonie wdrożenia:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Konfigurowanie zasad kondycji w celu ignorowania istniejących błędów

W przypadku klastrów Silver i wyższych trwałości należy zaktualizować zasób klastra w szablonie i skonfigurować zasady kondycji w celu zignorowania `fabric:/System` kondycji aplikacji, dodając *applicationDeltaHealthPolicies* w obszarze właściwości zasobów klastra zgodnie z poniższym opisem. Poniższe zasady spowodują zignorowanie istniejących błędów, ale nie pozwalają na nowe błędy kondycji.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Usuń zasoby pomocnicze dla oryginalnego typu węzła

Usuń wszystkie inne zasoby związane z oryginalnym typem węzła z szablonu ARM i pliku parametrów. Usuń następujące elementy:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Wdróż końcowy szablon

Na koniec Wdróż zmodyfikowany szablon Azure Resource Manager.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Ten krok zajmie trochę czasu, zwykle do dwóch godzin.

Uaktualnienie zmieni ustawienia na *InfrastructureService*; w związku z tym wymagane jest ponowne uruchomienie węzła. W tym przypadku *forceRestart* jest ignorowana. Parametr `upgradeReplicaSetCheckTimeout` określa maksymalny czas, który Service Fabric czeka, aż partycja będzie w stanie bezpiecznym, jeśli nie jest jeszcze w stanie bezpiecznym. Gdy sprawdzanie bezpieczeństwa zostanie zakończone dla wszystkich partycji w węźle, Service Fabric kontynuuje uaktualnianie w tym węźle. Wartość parametru `upgradeTimeout` może być zredukowana do 6 godzin, ale w celu zapewnienia maksymalnego poziomu bezpieczeństwa 12 godzin powinno być używane.

Po zakończeniu wdrożenia Sprawdź, czy Azure Portal, czy zasób Service Fabric jest *gotowy*. Sprawdź, czy można uzyskać dostęp do nowego punktu końcowego Service Fabric Explorer, **stanu kondycji klastra** jest *OK*, a wszystkie wdrożone aplikacje działają prawidłowo.

Dzięki temu można skalować w pionie typ węzła podstawowego klastra!

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [dodać typ węzła do klastra](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).
