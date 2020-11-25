---
title: Tworzenie klastra usługi Azure Service Fabric
description: Dowiedz się, jak skonfigurować bezpieczny klaster Service Fabric na platformie Azure przy użyciu Azure Resource Manager.  Klaster można utworzyć przy użyciu szablonu domyślnego lub przy użyciu własnego szablonu klastra.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: fd2c472d24f305e42f1706e5fc49168ccde2a580
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008671"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Tworzenie klastra Service Fabric przy użyciu Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Witryna Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Klaster Service Fabric platformy Azure](service-fabric-deploy-anywhere.md) to połączony z siecią zestaw maszyn wirtualnych, w których są wdrażane i zarządzane mikrousługi.  Klaster Service Fabric uruchomiony na platformie Azure to zasób platformy Azure, który jest wdrażany przy użyciu Azure Resource Manager. W tym artykule opisano sposób wdrażania bezpiecznego klastra Service Fabric na platformie Azure przy użyciu Menedżer zasobów. Możesz użyć domyślnego szablonu klastra lub szablonu niestandardowego.  Jeśli nie masz jeszcze szablonu niestandardowego, możesz [dowiedzieć się, jak go utworzyć](service-fabric-cluster-creation-create-template.md).

Typ zabezpieczeń wybrany do zabezpieczenia klastra (tj. tożsamość systemu Windows, certyfikat x509 itp.) musi być określony dla początkowego tworzenia klastra i nie można go zmienić później. Przed skonfigurowaniem klastra zapoznaj się z artykułem [scenariusze zabezpieczeń klastra Service Fabric][service-fabric-cluster-security]. Na platformie Azure Service Fabric używa certyfikatu x509 do zabezpieczania klastra i jego punktów końcowych, uwierzytelniania klientów i szyfrowania danych. Azure Active Directory jest również zalecane do zabezpieczenia dostępu do punktów końcowych zarządzania. Aby uzyskać więcej informacji, przeczytaj temat [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

Jeśli tworzysz klaster produkcyjny do uruchamiania obciążeń produkcyjnych, zalecamy zapoznanie się z [listą kontrolną gotowości do produkcji](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
W tym artykule należy wdrożyć klaster przy użyciu modułów Service Fabric Menedżera zasobów programu PowerShell lub interfejsu wiersza polecenia platformy Azure:

* [Azure PowerShell 4,1 i nowsze][azure-powershell]
* [Interfejs wiersza polecenia platformy Azure w wersji 2,0 i nowszej][azure-CLI]

Dokumentację referencyjną Service Fabric modułów można znaleźć tutaj:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [AZ SF CLI module](/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Przed uruchomieniem dowolnego polecenia z tego artykułu, najpierw Zaloguj się do platformy Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Tworzenie nowego klastra przy użyciu wygenerowanego przez system certyfikatu z podpisem własnym

Użyj następujących poleceń, aby utworzyć klaster zabezpieczony przy użyciu wygenerowanego przez system certyfikatu z podpisem własnym. To polecenie konfiguruje podstawowy certyfikat klastra służący do zabezpieczenia klastra i konfigurowania dostępu administratora do wykonywania operacji zarządzania przy użyciu tego certyfikatu.  Certyfikaty z podpisem własnym są przydatne do zabezpieczania klastrów testowych.  Klaster produkcyjny powinien być zabezpieczony przy użyciu certyfikatu z urzędu certyfikacji (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Użyj domyślnego szablonu klastra, który jest dostarczany w module

Użyj następującego polecenia, aby szybko utworzyć klaster, określając minimalne parametry przy użyciu szablonu domyślnego.

Używany szablon jest dostępny w [przykładach szablonów Service Fabric platformy Azure: szablon szablonu systemu Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablon Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Następujące polecenie może utworzyć klastry z systemem Windows lub Linux, aby odpowiednio określić system operacyjny. Polecenie PowerShell/interfejsu wiersza polecenia również wyprowadza certyfikat w określonym *CertificateOutputFolder*; Upewnij się jednak, że folder certyfikatu został już utworzony. Polecenie wykonuje również inne parametry, takie jak jednostka SKU maszyny wirtualnej.

> [!NOTE]
> Następujące polecenie programu PowerShell działa tylko z `Az` modułem Azure PowerShell. Aby sprawdzić bieżącą wersję programu Azure Resource Manager PowerShell w wersji, uruchom następujące polecenie programu PowerShell "Get-module AZ". Skorzystaj z [tego linku](/powershell/azure/install-Az-ps) , aby uaktualnić wersję programu Azure Resource Manager PowerShell. 
>
>

Wdróż klaster przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Wdróż klaster przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Korzystanie z własnego szablonu niestandardowego

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zdecydowanie zalecamy, aby zacząć od jednego z szablonów dostępnych w [przykładach szablonów Service Fabric platformy Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Dowiedz się, jak [dostosować szablon klastra][customize-your-cluster-template].

Jeśli masz już szablon niestandardowy, sprawdź, czy wszystkie trzy parametry powiązane z certyfikatami w szablonie i pliku parametrów są nazwane w następujący sposób, a wartości mają wartość null w następujący sposób:

```json
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

Wdróż klaster przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Wdróż klaster przy użyciu interfejsu wiersza polecenia platformy Azure:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Tworzenie nowego klastra przy użyciu własnego certyfikatu X. 509

Użyj następującego polecenia, aby utworzyć klaster, jeśli masz certyfikat, którego chcesz użyć do zabezpieczenia klastra za pomocą programu.

Jeśli jest to certyfikat podpisany przez urząd certyfikacji, który będzie używany do innych celów, należy również podać odrębną grupę zasobów dla magazynu kluczy. Zalecamy umieszczenie magazynu kluczy w jego własnej grupie zasobów. Ta akcja umożliwia usunięcie grup zasobów obliczeniowych i magazynu, w tym grupy zasobów zawierającej klaster Service Fabric bez utraty kluczy i wpisów tajnych. **Grupa zasobów zawierająca Magazyn kluczy *musi znajdować się w tym samym regionie* co klaster, który go używa.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Użyj domyślnego piątego węzła, jednego szablonu typu węzła, który jest dostarczany w module
Używany szablon jest dostępny w [przykładach na platformie Azure: szablon systemu Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablon Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Wdróż klaster przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Wdróż klaster przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Korzystanie z własnego niestandardowego szablonu klastra
Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zdecydowanie zalecamy, aby zacząć od jednego z szablonów dostępnych w [przykładach szablonów Service Fabric platformy Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Dowiedz się, jak [dostosować szablon klastra][customize-your-cluster-template].

Jeśli masz już szablon niestandardowy, upewnij się, że dokładnie sprawdź, czy wszystkie trzy parametry powiązane z certyfikatami w szablonie i pliku parametrów są nazwane w następujący sposób, a wartości mają wartość null w następujący sposób.

```json
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

Wdróż klaster przy użyciu programu PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Wdróż klaster przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Użyj wskaźnika do wpisu tajnego przekazanego do magazynu kluczy

Aby można było użyć istniejącego magazynu kluczy, Magazyn kluczy musi być [włączony do wdrożenia](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) , aby umożliwić dostawcy zasobów obliczeniowych pobieranie z niego certyfikatów i instalowanie ich w węzłach klastra.

Wdróż klaster przy użyciu programu PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Wdróż klaster przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Następne kroki
W tym momencie masz bezpieczny klaster działający na platformie Azure. Następnie połącz się z [klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak zarządzać wpisami [tajnymi aplikacji](service-fabric-application-secret-management.md).

Aby poznać składnię i właściwości JSON dotyczące korzystania z szablonu, zobacz [Dokumentacja szablonu Microsoft. servicefabric/klastrów](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
