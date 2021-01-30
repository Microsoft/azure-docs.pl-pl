---
title: Uaktualnianie węzłów klastra do korzystania z usługi Azure Managed disks
description: Oto jak uaktualnić istniejący klaster Service Fabric, aby używać usługi Azure Managed disks z niewielkim lub żadnym przestojem klastra.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: c374c4536309a13abcf8c882b041a9c5357878e5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090658"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Uaktualnianie węzłów klastra do korzystania z usługi Azure Managed disks

[Azure Managed disks](../virtual-machines/managed-disks-overview.md) to zalecana oferta magazynu dyskowego do użycia z maszynami wirtualnymi platformy Azure na potrzeby trwałego magazynowania danych. Odporność obciążeń Service Fabric można zwiększyć, uaktualniając zestawy skalowania maszyn wirtualnych, które podstawą typy węzłów w celu używania dysków zarządzanych. Oto jak uaktualnić istniejący klaster Service Fabric, aby używać usługi Azure Managed disks z niewielkim lub żadnym przestojem klastra.

Ogólna strategia uaktualniania Service Fabric węzła klastra do korzystania z usługi Managed disks to:

1. Wdróż duplikat zestawu skalowania maszyn wirtualnych w innym przypadku tego typu węzła, ale z obiektem [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) dodanym do `osDisk` sekcji szablonu wdrożenia zestawu skalowania maszyn wirtualnych. Nowy zestaw skalowania powinien być powiązany z tym samym modułem równoważenia obciążenia/IP jako oryginalny, dzięki czemu klienci nie napotykają przerwy w działaniu usługi podczas migracji.

2. Po uruchomieniu zarówno oryginalnego, jak i uaktualnionego zestawu skalowania obok siebie należy wyłączyć oryginalne wystąpienia węzłów pojedynczo, tak aby usługi systemowe (lub repliki usług stanowych) były migrowane do nowego zestawu skalowania.

3. Sprawdź, czy klaster i nowe węzły są w dobrej kondycji, a następnie usuń oryginalny zestaw skalowania i stan węzła dla usuniętych węzłów.

W tym artykule opisano kroki uaktualniania podstawowego typu węzła przykładowego klastra do korzystania z dysków zarządzanych, unikając czasu przestoju klastra (patrz Uwaga poniżej). Początkowy stan przykładowego klastra testowego składa się z jednego typu węzła o [trwałości Silver](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), który jest objęty jednym zestawem skalowania z pięcioma węzłami.

> [!NOTE]
> Ograniczenia podstawowego modułu równoważenia obciążenia jednostki SKU uniemożliwiają dodanie dodatkowego zestawu skalowania. Zalecamy użycie w zamian standardowego modułu równoważenia obciążenia jednostki SKU. Aby uzyskać więcej informacji, zobacz [porównanie dwóch jednostek SKU](../load-balancer/skus.md).

> [!CAUTION]
> W tej procedurze wystąpi awaria tylko wtedy, gdy istnieją zależności w systemie DNS klastra (na przykład podczas uzyskiwania dostępu do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). [Najlepszym rozwiązaniem w zakresie architektury dla usług frontonu](/azure/architecture/microservices/design/gateway) jest posiadanie pewnego rodzaju [modułu równoważenia obciążenia](/azure/architecture/guide/technology-choices/load-balancing-overview) przed typami węzłów w celu zapewnienia możliwości wymiany węzłów bez przestoju.

Poniżej przedstawiono [Szablony i polecenia cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) dla Azure Resource Manager, które zostaną użyte do ukończenia scenariusza uaktualniania. Zmiany w szablonie zostaną omówione w temacie [Wdróż uaktualniony zestaw skalowania dla typu węzła podstawowego](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  poniżej.

## <a name="set-up-the-test-cluster"></a>Konfigurowanie klastra testowego

Skonfigurujmy wstępny klaster testowy Service Fabric. Najpierw [pobierz](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) Azure Resource Manager szablony przykładowe, których będziemy używać do realizacji tego scenariusza.

Następnie zaloguj się do konta platformy Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Poniższe polecenia przeprowadzą Cię przez proces generowania nowego certyfikatu z podpisem własnym i wdrożenia klastra testowego. Jeśli masz już certyfikat, którego chcesz użyć, Pomiń, aby [użyć istniejącego certyfikatu do wdrożenia klastra](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Wygeneruj certyfikat z podpisem własnym i Wdróż klaster

Najpierw Przypisz zmienne, które będą potrzebne do wdrożenia klastra Service Fabric. Dostosuj wartości dla `resourceGroupName` ,,  `certSubjectName` `parameterFilePath` i `templateFilePath` dla określonego konta i środowiska:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Upewnij się, że `certOutputFolder` Lokalizacja istnieje na komputerze lokalnym przed uruchomieniem polecenia, aby wdrożyć nowy klaster Service Fabric.

Następnie otwórz [*Initial-1NodeType-UnmanagedDisks.parameters.jsw*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) pliku i Dostosuj wartości dla i, `clusterName` `dnsName` aby odpowiadały wartościom dynamicznym ustawionym w programie PowerShell, i Zapisz zmiany.

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

Operacja zwróci odcisk palca certyfikatu, który zostanie użyty do [nawiązania połączenia z nowym klastrem](#connect-to-the-new-cluster-and-check-health-status) i sprawdzenia jego stanu kondycji. (Pomiń poniższą sekcję, która stanowi alternatywne podejście do wdrożenia klastra).

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Używanie istniejącego certyfikatu do wdrożenia klastra

Do wdrożenia klastra testowego można także użyć istniejącego certyfikatu Azure Key Vault. Aby to zrobić, musisz [uzyskać odwołania do Key Vault](#obtain-your-key-vault-references) i odcisku palca certyfikatu.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Otwórz [*Initial-1NodeType-UnmanagedDisks.parameters.jsw*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) pliku i zmień wartości dla `clusterName` i `dnsName` na unikatowy.

Na koniec należy wyznaczyć nazwę grupy zasobów dla klastra i ustawić `templateFilePath` `parameterFilePath` lokalizacje plików *początkowego-1NodeType-UnmanagedDisks* :

> [!NOTE]
> Wskazana Grupa zasobów musi już istnieć i znajdować się w tym samym regionie co Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Na koniec Uruchom następujące polecenie, aby wdrożyć początkowy klaster testowy:

```powershell
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

Połącz się z klastrem i upewnij się, że wszystkie pięć jego węzłów są w dobrej kondycji (zastępując `clusterName` `thumb` zmienne i dla klastra):

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Wdróż uaktualniony zestaw skalowania dla typu węzła podstawowego

W celu uaktualnienia lub *skalowania w pionie*, typu węzła, musimy wdrożyć kopię zestawu skalowania maszyn wirtualnych typu węzła, który jest inny niż oryginalny zestaw skalowania (w tym odwołania do tego samego `nodeTypeRef` , `subnet` i `loadBalancerBackendAddressPools` ), z tą różnicą, że zawiera żądane uaktualnienie/zmiany i własną pulę adresów NAT dla ruchu przychodzącego. Ponieważ uaktualniamy typ węzła podstawowego, nowy zestaw skalowania zostanie oznaczony jako podstawowy ( `isPrimary: true` ), podobnie jak oryginalny zestaw skalowania. (W przypadku uaktualnień typu węzła innego niż podstawowe, należy po prostu pominąć to).

Dla wygody wymagane zmiany zostały już wprowadzone [w plikach](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) [szablonu](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *upgrade-1NodeType-2ScaleSets-ManagedDisks* .

W poniższych sekcjach opisano szczegółowo zmiany szablonu. Jeśli wolisz, możesz pominąć wyjaśnienie i przejść do [następnego kroku procedury uaktualniania](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aktualizowanie szablonu klastra za pomocą uaktualnionego zestawu skalowania

Poniżej przedstawiono modyfikacje sekcji dotyczące oryginalnego szablonu wdrożenia klastra w celu dodania uaktualnionego zestawu skalowania dla typu węzła podstawowego.

#### <a name="parameters"></a>Parametry

Dodaj parametry dla nazwy wystąpienia, liczby i rozmiaru nowego zestawu skalowania. Należy pamiętać, że `vmNodeType1Name` jest ona unikatowa dla nowego zestawu skalowania, podczas gdy wartości Count i size są identyczne z oryginalnym zestawem skalowania.

**Plik szablonu**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Plik parametrów**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Zmienne

W sekcji szablon wdrożenia `variables` Dodaj wpis dla puli adresów NAT dla ruchu przychodzącego nowego zestawu skalowania.

**Plik szablonu**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Zasoby

W sekcji *zasoby* szablonu wdrożenia Dodaj nowy zestaw skalowania maszyn wirtualnych, pamiętając o następujących kwestiach:

* Nowy zestaw skalowania odwołuje się do tego samego typu węzła co oryginalny:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Nowy zestaw skalowania odwołuje się do tego samego adresu zaplecza i podsieci usługi równoważenia obciążenia (ale używa innej puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Podobnie jak w przypadku oryginalnego zestawu skalowania, nowy zestaw skalowania jest oznaczony jako podstawowy typ węzła. (Podczas uaktualniania typów węzłów innych niż podstawowe należy pominąć tę zmianę).

    ```json
    "isPrimary": true,
    ```

* W przeciwieństwie do oryginalnego zestawu skalowania, nowy zestaw skalowania jest uaktualniany do korzystania z dysków zarządzanych.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Po zaimplementowaniu wszystkich zmian w plikach szablonu i parametrów przejdź do następnej sekcji, aby uzyskać informacje dotyczące Key Vault i wdrożyć aktualizacje w klastrze.

### <a name="obtain-your-key-vault-references"></a>Uzyskaj informacje o Key Vault

Aby wdrożyć zaktualizowaną konfigurację, należy najpierw uzyskać kilka odwołań do certyfikatu klastra przechowywanego w Key Vault. Najprostszym sposobem znalezienia tych wartości jest użycie Azure Portal. Potrzebne będą następujące elementy:

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

Dostosuj `parameterFilePath` i `templateFilePath` w razie konieczności, a następnie uruchom następujące polecenie:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Po zakończeniu wdrożenia Sprawdź kondycję klastra ponownie i upewnij się, że wszystkie dziesięć węzłów (pięć w oryginalnym i pięciu) jest w dobrej kondycji.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrowanie węzłów inicjatora do nowego zestawu skalowania

Teraz można rozpocząć wyłączanie węzłów oryginalnego zestawu skalowania. Po wyłączeniu tych węzłów usługi systemowe i węzły inicjatora są migrowane do maszyn wirtualnych nowego zestawu skalowania, ponieważ są również oznaczone jako podstawowy typ węzła.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Użyj Service Fabric Explorer do monitorowania migracji węzłów inicjatora do nowego zestawu skalowania, a postęp węzłów w pierwotnym zestawie skalowania można *wyłączyć* , aby *wyłączyć stan wyłączony* .

![Service Fabric Explorer wyświetlania stanu wyłączonych węzłów](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

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

![Service Fabric Explorer pokazywania wyłączonych węzłów w stanie błąd](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Usuń przestarzałe węzły z klastra Service Fabric, aby przywrócić stan kondycji klastra na *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer z węzłami w dół z usuniętym stanem błędu](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Następne kroki

W tym instruktażu pokazano, jak uaktualnić zestawy skalowania maszyn wirtualnych klastra Service Fabric, aby używać dysków zarządzanych przy jednoczesnym uniknięciu przerwy w działaniu usługi w trakcie procesu. Aby uzyskać więcej informacji na temat powiązanych tematów, zapoznaj się z następującymi zasobami.

Instrukcje:

* [Skalowanie w górę węzła klastra usługi Service Fabric podstawowego typu](service-fabric-scale-up-primary-node-type.md)

* [Konwertowanie szablonu zestawu skalowania w celu korzystania z dysków zarządzanych](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Usuwanie Service Fabric typu węzła](service-fabric-how-to-remove-node-type.md)

Zobacz też:

* [Przykład: Uaktualnij węzły klastra, aby korzystać z usługi Azure Managed disks](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade)

* [Zagadnienia dotyczące skalowania w pionie](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)