---
title: Włącz szyfrowanie dysków dla klastrów systemu Windows
description: W tym artykule opisano sposób włączania szyfrowania dysków dla węzłów klastra Service Fabric platformy Azure przy użyciu Azure Key Vault w Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78251815"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Włączanie szyfrowania dysków dla węzłów klastra usługi Azure Service Fabric w systemie Windows 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla systemu Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

W tym samouczku dowiesz się, jak włączyć szyfrowanie dysków na Service Fabric węzłach klastra w systemie Windows. Należy wykonać następujące kroki dla każdego typu węzła i zestawów skalowania maszyn wirtualnych. W przypadku szyfrowania węzłów użyjemy Azure Disk Encryption możliwości w ramach zestawów skalowania maszyn wirtualnych.

Przewodnik obejmuje następujące tematy:

* Kluczowe koncepcje, o których należy pamiętać w przypadku włączania szyfrowania dysków na Service Fabric klastrach skalowania maszyn wirtualnych w systemie Windows.
* Kroki, które należy wykonać przed włączeniem szyfrowania dysków na Service Fabric węzłach klastra w systemie Windows.
* Kroki, które należy wykonać, aby włączyć szyfrowanie dysków na Service Fabric węzłach klastra w systemie Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

**Rejestracja samoobsługowa** 

Wersja zapoznawcza szyfrowania dysków dla zestawu skalowania maszyn wirtualnych wymaga samorejestracji. Wykonaj następujące kroki: 

1. Najpierw uruchom następujące polecenie:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Odczekaj około 10 minut do momentu *zarejestrowania*stanu. Stan można sprawdzić, uruchamiając następujące polecenie: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Usługa Azure Key Vault** 

1. Utwórz magazyn kluczy w tej samej subskrypcji i regionie co zestaw skalowania, a następnie wybierz zasady dostępu **EnabledForDiskEncryption** w magazynie kluczy przy użyciu polecenia cmdlet programu PowerShell. Zasady można również ustawić przy użyciu Key Vault interfejsu użytkownika w Azure Portal za pomocą następującego polecenia:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), który zawiera nowe polecenia szyfrowania.
3. Zainstaluj najnowszą wersję [zestawu Azure SDK z wersji Azure PowerShell](https://github.com/Azure/azure-powershell/releases) . Poniżej znajduje się zestaw skalowania maszyn wirtualnych Azure Disk Encryption polecenia cmdlet umożliwiające włączenie ([ustawienie](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) szyfrowania, pobranie ([pobranie](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stanu szyfrowania i usunięcie ([wyłączenie](/powershell/module/az.compute/disable-azvmssdiskencryption)) szyfrowania w wystąpieniu zestawu skalowania.

| Polecenie | Wersja |  Źródło  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 lub nowszy | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 lub nowszy | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 lub nowszy | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 lub nowszy | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 lub nowszy | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 lub nowszy | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Obsługiwane scenariusze dotyczące szyfrowania dysków
* Szyfrowanie dla zestawów skalowania maszyn wirtualnych jest obsługiwane tylko dla zestawów skalowania utworzonych przy użyciu usługi Managed Disks. Nie jest obsługiwane w przypadku zestawów skalowania dysków natywnych (lub niezarządzanych).
* Szyfrowanie jest obsługiwane dla woluminów systemu operacyjnego i danych w zestawach skalowania maszyn wirtualnych w systemie Windows. Funkcja wyłącz szyfrowanie jest również obsługiwana dla woluminów systemu operacyjnego i danych dla zestawów skalowania maszyn wirtualnych w systemie Windows.
* Operacje odwzorowania i uaktualniania maszyn wirtualnych dla zestawów skalowania maszyn wirtualnych nie są obsługiwane w bieżącej wersji zapoznawczej.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Tworzenie nowego klastra i Włączanie szyfrowania dysków

Użyj następujących poleceń, aby utworzyć klaster i włączyć szyfrowanie dysków przy użyciu szablonu Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się przy użyciu następujących poleceń:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, który już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zdecydowanie zalecamy, aby zacząć od jednego z szablonów dostępnych na stronie [przykładów szablonu tworzenia klastra Service Fabric platformy Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . Aby [dostosować sekcję szablonu klastra][customize-your-cluster-template] , zapoznaj się z poniższymi wskazówkami.

Jeśli masz już szablon niestandardowy, sprawdź, czy wszystkie trzy parametry związane z certyfikatami w szablonie i pliku parametrów są nazwane w następujący sposób i czy wartości mają wartość null w następujący sposób:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Wdrażanie aplikacji w klastrze Service Fabric w systemie Windows
Aby wdrożyć aplikację w klastrze, postępuj zgodnie z instrukcjami i wskazówkami dotyczącymi [wdrażania i usuwania aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Włącz szyfrowanie dysków dla utworzonych wcześniej zestawów skalowania maszyn wirtualnych

Aby włączyć szyfrowanie dysków dla zestawów skalowania maszyn wirtualnych utworzonych w poprzednich krokach, uruchom następujące polecenia:
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Sprawdź, czy włączono szyfrowanie dysków dla zestawu skalowania maszyn wirtualnych w systemie Windows
Pobierz stan całego zestawu skalowania maszyn wirtualnych lub dowolnego wystąpienia w zestawie skalowania, uruchamiając następujące polecenia.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Ponadto możesz zalogować się do zestawu skalowania maszyn wirtualnych i upewnić się, że dyski są zaszyfrowane.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Wyłączanie szyfrowania dysków dla zestawu skalowania maszyn wirtualnych w klastrze Service Fabric 
Wyłącz szyfrowanie dysków dla zestawu skalowania maszyn wirtualnych, uruchamiając następujące polecenia. Należy pamiętać, że wyłączenie szyfrowania dysków dotyczy całego zestawu skalowania maszyn wirtualnych, a nie poszczególnych wystąpień.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Następne kroki
W tym momencie należy mieć bezpieczny klaster i wiedzieć, jak włączyć i wyłączyć szyfrowanie dysków dla Service Fabric węzłów klastra i zestawów skalowania maszyn wirtualnych. Aby poznać podobne wskazówki dotyczące Service Fabric węzłów klastra w systemie Linux, zobacz [szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
