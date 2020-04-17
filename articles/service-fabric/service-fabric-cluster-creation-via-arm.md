---
title: Tworzenie klastra sieci szkieletowej usług Azure
description: Dowiedz się, jak skonfigurować bezpieczny klaster sieci szkieletowej usług na platformie Azure przy użyciu usługi Azure Resource Manager.  Klaster można utworzyć przy użyciu szablonu domyślnego lub przy użyciu własnego szablonu klastra.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 24bc80a7ab3ce61b79466cdd943c60efd367ce01
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458304"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Tworzenie klastra sieci szkieletowej usług przy użyciu usługi Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Klaster sieci szkieletowej usług Azure](service-fabric-deploy-anywhere.md) to połączony z siecią zestaw maszyn wirtualnych, na których mikrousługi są wdrażane i zarządzane.  Klaster sieci szkieletowej usług uruchomiony na platformie Azure jest zasobem platformy Azure i jest wdrażany przy użyciu usługi Azure Resource Manager. W tym artykule opisano sposób wdrażania bezpiecznego klastra sieci szkieletowej usług na platformie Azure przy użyciu Menedżera zasobów. Można użyć domyślnego szablonu klastra lub szablonu niestandardowego.  Jeśli nie masz jeszcze szablonu niestandardowego, możesz [dowiedzieć się, jak go utworzyć](service-fabric-cluster-creation-create-template.md).

Typ zabezpieczeń wybrany do zabezpieczenia klastra (np. tożsamość systemu Windows, X509 itp.) musi być określony dla początkowego utworzenia klastra i nie można go później zmienić. Przed skonfigurowaniem klastra należy zapoznać się ze [scenariuszami zabezpieczeń klastra sieci szkieletowej usług][service-fabric-cluster-security]. Na platformie Azure sieć szkieletowa usług używa certyfikatu x509 do zabezpieczania klastra i jego punktów końcowych, uwierzytelniania klientów i szyfrowania danych. Usługa Azure Active Directory jest również zalecane do bezpiecznego dostępu do punktów końcowych zarządzania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure AD w celu uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

Jeśli tworzysz klaster produkcyjny do uruchamiania obciążeń produkcyjnych, zalecamy najpierw zapoznanie się z [listą kontrolną gotowości produkcyjnej.](service-fabric-production-readiness-checklist.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
W tym artykule użyj modułów powershell rm sieci szkieletowej sieci szkieletowej sieci szkieletowej usług lub platformy Azure, aby wdrożyć klaster:

* [Program Azure PowerShell 4.1 lub powyżej][azure-powershell]
* [Interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub wyższej][azure-CLI]

Dokumentację referencyjną dla modułów sieci szkieletowej usług można znaleźć tutaj:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [Moduł interfejsu wiersza polecenia AZ SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Przed uruchomieniem któregokolwiek z poleceń w tym artykule, najpierw zaloguj się na platformie Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Tworzenie nowego klastra przy użyciu certyfikatu wygenerowanego przez system z podpisem własnym

Poniższe polecenia można użyć do utworzenia klastra zabezpieczonego certyfikatem wygenerowanym przez system z podpisem własnym. To polecenie konfiguruje podstawowy certyfikat klastra, który jest używany do zabezpieczeń klastra i konfigurowania dostępu administratora do wykonywania operacji zarządzania przy użyciu tego certyfikatu.  Certyfikaty z podpisem własnym są przydatne do zabezpieczania klastrów testowych.  Klastry produkcyjne powinny być zabezpieczone certyfikatem urzędu certyfikacji(CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Użyj domyślnego szablonu klastra dostarczanego w module

Użyj następującego polecenia, aby szybko utworzyć klaster, określając minimalne parametry przy użyciu szablonu domyślnego.

Szablon, który jest używany jest dostępny w [przykładach szablonów sieci szkieletowej usługi Azure: szablon systemu Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablon Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Następujące polecenie może utworzyć klastry systemu Windows lub Linux, należy odpowiednio określić system operacyjny. Polecenia programu PowerShell/CLI również wyprowadzają certyfikat w określonym *certificateoutputfolder;* Upewnij się jednak, że folder certyfikatu został już utworzony. Polecenie przyjmuje również inne parametry, takie jak jednostka SKU maszyny Wirtualnej.

> [!NOTE]
> Następujące polecenie programu PowerShell działa tylko `Az` z modułem programu Azure PowerShell. Aby sprawdzić bieżącą wersję wersji programu Azure Resource Manager programu PowerShell, uruchom następujące polecenie programu PowerShell "Get-Module Az". Kliknij [to łącze,](/powershell/azure/install-Az-ps) aby uaktualnić wersję programu PowerShell usługi Azure Resource Manager. 
>
>

Wdrażanie klastra przy użyciu programu PowerShell:

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

Wdrażanie klastra przy użyciu interfejsu wiersza polecenia platformy Azure:

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

### <a name="use-your-own-custom-template"></a>Używanie własnego szablonu niestandardowego

Jeśli chcesz zasilić szablon niestandardowy zgodnie z potrzebami, zdecydowanie zaleca się rozpoczęcie od jednego z szablonów dostępnych w [przykładach szablonów usługi Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Dowiedz się, jak [dostosować szablon klastra][customize-your-cluster-template].

Jeśli masz już szablon niestandardowy, sprawdź, czy wszystkie trzy parametry związane z certyfikatem w szablonie i pliku parametrów są nazwane w następujący sposób, a wartości mają wartość null w następujący sposób:

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

Wdrażanie klastra przy użyciu programu PowerShell:

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

Wdrażanie klastra przy użyciu interfejsu wiersza polecenia platformy Azure:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Tworzenie nowego klastra przy użyciu własnego certyfikatu X.509

Użyj następującego polecenia, aby utworzyć klaster, jeśli masz certyfikat, za pomocą którego chcesz zabezpieczyć klaster.

Jeśli jest to certyfikat podpisany przez urząd certyfikacji, który będzie używany również do innych celów, zaleca się podanie odrębnej grupy zasobów specjalnie dla magazynu kluczy. Zaleca się umieszczenie magazynu kluczy w własnej grupie zasobów. Ta akcja umożliwia usunięcie grup zasobów obliczeniowych i magazynowania, w tym grupy zasobów, która zawiera klaster sieci szkieletowej usług, bez utraty kluczy i wpisów tajnych. **Grupa zasobów zawierająca magazyn kluczy *musi znajdować się w tym samym regionie co* klaster, który go używa.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Użyj domyślnego szablonu pięciu węzłów, jednego węzła, który jest dostarczany w module
Szablon, który jest używany jest dostępny w [przykładach platformy Azure: Szablon systemu Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) i [szablon Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Wdrażanie klastra przy użyciu programu PowerShell:

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

Wdrażanie klastra przy użyciu interfejsu wiersza polecenia platformy Azure:

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

### <a name="use-your-own-custom-cluster-template"></a>Używanie własnego niestandardowego szablonu klastra
Jeśli chcesz zasilić szablon niestandardowy zgodnie z potrzebami, zdecydowanie zaleca się rozpoczęcie od jednego z szablonów dostępnych w [przykładach szablonów usługi Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Dowiedz się, jak [dostosować szablon klastra][customize-your-cluster-template].

Jeśli masz już szablon niestandardowy, upewnij się, że dokładnie sprawdź, czy wszystkie trzy parametry związane z certyfikatem w szablonie i pliku parametrów są nazwane w następujący sposób, a wartości mają wartość null w następujący sposób.

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

Wdrażanie klastra przy użyciu programu PowerShell:

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

Wdrażanie klastra przy użyciu interfejsu wiersza polecenia platformy Azure:

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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Używanie wskaźnika do klucza tajnego przekazanego do magazynu kluczy

Aby użyć istniejącego magazynu kluczy, magazyn kluczy musi być [włączony do wdrożenia,](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) aby umożliwić dostawcy zasobów obliczeniowych uzyskanie certyfikatów z niego i zainstalowanie go w węzłach klastra.

Wdrażanie klastra przy użyciu programu PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Wdrażanie klastra przy użyciu interfejsu wiersza polecenia platformy Azure:

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
W tym momencie masz bezpieczny klaster uruchomiony na platformie Azure. Następnie [połącz się z klastrem](service-fabric-connect-to-secure-cluster.md) i dowiedz się, jak [zarządzać wpisami tajnymi aplikacji.](service-fabric-application-secret-management.md)

Aby uzyskać składnię json i właściwości do używania szablonu, zobacz [Microsoft.ServiceFabric/clusters template reference](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
