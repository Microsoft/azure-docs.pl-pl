---
title: Tworzenie maszyny wirtualnej w DevTest Labs przy użyciu Azure PowerShell
description: Dowiedz się, jak używać Azure DevTest Labs do tworzenia maszyn wirtualnych i zarządzania nimi przy użyciu Azure PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499258"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Tworzenie maszyny wirtualnej za pomocą DevTest Labs przy użyciu Azure PowerShell
W tym artykule pokazano, jak utworzyć maszynę wirtualną w Azure DevTest Labs przy użyciu Azure PowerShell. Za pomocą skryptów programu PowerShell można zautomatyzować tworzenie maszyn wirtualnych w laboratorium w Azure DevTest Labs. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

- [Utwórz laboratorium](devtest-lab-create-lab.md) , jeśli nie chcesz używać istniejącego laboratorium do testowania skryptu lub poleceń w tym artykule. 
- [Zainstaluj Azure PowerShell](/powershell/azure/install-az-ps) lub użyj Azure Cloud Shell zintegrowanych w Azure Portal. 

## <a name="powershell-script"></a>Skrypt programu PowerShell
Przykładowy skrypt w tej sekcji używa polecenia cmdlet [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) .  To polecenie cmdlet pobiera identyfikator zasobu laboratorium, nazwę akcji do wykonania ( `createEnvironment` ), a parametry niezbędne do wykonania tej akcji. Parametry znajdują się w tabeli skrótów, która zawiera wszystkie właściwości opisu maszyny wirtualnej. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Właściwości maszyny wirtualnej w powyższym skrypcie pozwalają nam utworzyć maszynę wirtualną z systemem Windows Server 2016 DataCenter jako systemem operacyjnym. Dla każdego typu maszyny wirtualnej te właściwości będą nieco inne. Sekcja [Zdefiniuj maszynę wirtualną](#define-virtual-machine) pokazuje, jak określić właściwości, które mają być używane w tym skrypcie.

Następujące polecenie zawiera przykład uruchamiania skryptu zapisanego w nazwie pliku: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Zdefiniuj maszynę wirtualną
W tej sekcji pokazano, jak uzyskać właściwości specyficzne dla typu maszyny wirtualnej, którą chcesz utworzyć. 

### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia maszyny wirtualnej w Azure Portal można wygenerować szablon Azure Resource Manager. Nie musisz ukończyć procesu tworzenia maszyny wirtualnej. Kroki należy wykonać dopiero po wyświetleniu szablonu. Jest to najlepszy sposób, aby uzyskać niezbędny opis JSON, jeśli nie masz jeszcze utworzonej maszyny wirtualnej laboratorium. 

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie.
3. Wyszukaj i wybierz pozycję **DevTest Labs** z listy usług. 
4. Na stronie **DevTest Labs** wybierz swoje laboratorium na liście laboratoriów.
5. Na stronie głównej laboratorium wybierz pozycję **+ Dodaj** na pasku narzędzi. 
6. Wybierz **obraz podstawowy** dla maszyny wirtualnej. 
7. Wybierz **Opcje automatyzacji** w dolnej części strony powyżej przycisku **Prześlij** . 
8. Zobaczysz **szablon Azure Resource Manager** na potrzeby tworzenia maszyny wirtualnej. 
9. Segment JSON w sekcji **resources** zawiera definicję wybranego wcześniej typu obrazu. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

W tym przykładzie zobaczysz, jak uzyskać definicję obrazu miejsca na rynku platformy Azure. W ten sam sposób można uzyskać definicję niestandardowego obrazu, formuły lub środowiska. Dodaj wszelkie artefakty potrzebne dla maszyny wirtualnej i ustaw wymagane ustawienia zaawansowane. Po podania wartości dla wymaganych pól i wszelkich opcjonalnych pól, przed wybraniem przycisku **Opcje automatyzacji** .

### <a name="use-azure-rest-api"></a>Korzystanie z interfejsu API REST platformy Azure
Poniższa procedura zawiera instrukcje dotyczące pobierania właściwości obrazu przy użyciu interfejsu API REST: te kroki działają tylko dla istniejącej maszyny wirtualnej w laboratorium. 

1. Przejdź do strony [listy Virtual Machines](/rest/api/dtl/virtualmachines/list) wybierz pozycję **Wypróbuj** . 
2. Wybierz swoją **subskrypcję platformy Azure**.
3. Wprowadź **grupę zasobów dla laboratorium**.
4. Wprowadź **nazwę laboratorium**. 
5. Wybierz pozycję **Uruchom**.
6. Zobaczysz **Właściwości obrazu,** na podstawie którego została utworzona maszyna wirtualna. 

## <a name="set-expiration-date"></a>Ustaw datę wygaśnięcia
W scenariuszach takich jak szkolenia, pokazy i wersje próbne można tworzyć maszyny wirtualne i usuwać je automatycznie po upływie ustalonego czasu, aby nie ponosić niepotrzebnych kosztów. Można ustawić datę wygaśnięcia dla maszyny wirtualnej podczas jej tworzenia przy użyciu programu PowerShell, jak pokazano w przykładowej sekcji [skryptu programu PowerShell](#powershell-script) .

Oto przykładowy skrypt programu PowerShell, który ustawia datę wygaśnięcia dla wszystkich istniejących maszyn wirtualnych w laboratorium:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą zawartością: [dokumentacja Azure PowerShell dla Azure DevTest Labs](/powershell/module/az.devtestlabs/)
