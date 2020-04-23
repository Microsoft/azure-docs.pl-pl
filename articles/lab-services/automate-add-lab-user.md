---
title: Automatyzacja dodawania użytkownika laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak zautomatyzować dodawanie użytkownika do laboratorium w laboratoriach DevTest Azure przy użyciu szablonów usługi Azure Resource Manager, Programu PowerShell i interfejsu wiersza polecenia.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023626"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatyzacja dodawania użytkownika laboratorium do laboratorium w laboratorium usługi Azure DevTest Labs
Laboratorium devtest platformy Azure umożliwia szybkie tworzenie samoobsługowych środowisk deweloperskich i testowych przy użyciu witryny Azure portal. Jeśli jednak masz kilka zespołów i kilka wystąpień DevTest Labs, automatyzacja procesu tworzenia może zaoszczędzić czas. [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) umożliwiają tworzenie laboratoriów, maszyn wirtualnych w laboratorium, obrazów niestandardowych, formuł i dodawanie użytkowników w sposób zautomatyzowany. Ten artykuł koncentruje się w szczególności na dodawanie użytkowników do DevTest Labs wystąpienia.

Aby dodać użytkownika do laboratorium, należy dodać użytkownika do **devtest labs roli użytkownika** w laboratorium. W tym artykule pokazano, jak zautomatyzować dodawanie użytkownika do laboratorium przy użyciu jednego z następujących sposobów:

- Szablony usługi Azure Resource Manager
- Polecenia cmdlet programu Azure PowerShell 
- Interfejsu wiersza polecenia platformy Azure.

## <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager
Poniższy przykładowy szablon Menedżera zasobów określa użytkownika, który ma zostać dodany do roli **użytkownika DevTest Labs** w laboratorium. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Jeśli przypisujesz rolę w tym samym szablonie, który tworzy laboratorium, pamiętaj, aby dodać zależność między zasobem przypisania roli a laboratorium. Aby uzyskać więcej informacji, zobacz [Definiowanie zależności w artykule Szablony usługi Azure Resource Manager.](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>Informacje o zasobie przydziału ról
Zasób przydziału roli musi określić typ i nazwę.

Pierwszą rzeczą, na którą należy zwrócić uwagę, jest to, że typ zasobu nie `Microsoft.Authorization/roleAssignments` jest taki, jak w przypadku grupy zasobów.  Zamiast tego typ zasobu `{provider-namespace}/{resource-type}/providers/roleAssignments`jest zgodny ze wzorcem . W takim przypadku typem `Microsoft.DevTestLab/labs/providers/roleAssignments`zasobu będzie .

Sama nazwa przypisania roli musi być unikatowa globalnie.  Nazwa przypisania używa wzorca `{labName}/Microsoft.Authorization/{newGuid}`. Jest `newGuid` to wartość parametru dla szablonu. Zapewnia, że nazwa przypisania roli jest unikatowa. Ponieważ nie ma żadnych funkcji szablonu do tworzenia identyfikatorów GUID, musisz samodzielnie wygenerować identyfikator GUID przy użyciu dowolnego narzędzia generatora identyfikatorów GUID.  

W szablonie nazwa przypisania roli jest `fullDevTestLabUserRoleName` definiowana przez zmienną. Dokładna linia z szablonu to:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Właściwości zasobu przydziału ról
Samo przypisanie roli definiuje trzy właściwości. Potrzebuje `roleDefinitionId`, `principalId`, `scope`i .

### <a name="role-definition"></a>Definicja roli
Identyfikator definicji roli jest identyfikatorem ciągu dla istniejącej definicji roli. Identyfikator roli jest w `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`formularzu . 

Identyfikator subskrypcji jest uzyskiwany przy użyciu `subscription().subscriptionId` funkcji szablonu.  

Musisz uzyskać definicję roli `DevTest Labs User` dla wbudowanej roli. Aby uzyskać identyfikator GUID dla roli [użytkownika devtest labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) można użyć [interfejsu API REST przydziałów ról](/rest/api/authorization/roleassignments) lub polecenia cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Identyfikator roli jest zdefiniowany w sekcji `devTestLabUserRoleId`zmiennych i nazwany . W szablonie identyfikator roli jest ustawiony na: 111111111-0000-0000-111111111111111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Identyfikator główny
Identyfikator główny to identyfikator obiektu użytkownika, grupy lub jednostki usługi Active Directory, który chcesz dodać jako użytkownika laboratorium do laboratorium. Szablon używa `ObjectId` jako parametru.

ObjectId można uzyskać przy użyciu polecenia cmdlet [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup lub [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell. Te polecenia cmdlet zwracają pojedyncze lub listy obiektów usługi Active Directory, które mają właściwość identyfikatora, która jest identyfikatorem obiektu, którego potrzebujesz. W poniższym przykładzie pokazano, jak uzyskać identyfikator obiektu pojedynczego użytkownika w firmie.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Można również użyć poleceń cmdlet programu Azure Active Directory PowerShell, które obejmują [Polecenia Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)i [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Zakres
Zakres określa zasób lub grupę zasobów, do których powinno być stosowane przypisanie roli. W przypadku zasobów zakres jest `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`w formie: . Szablon `subscription().subscriptionId` używa funkcji do wypełnienia `subscription-id` części i `resourceGroup().name` funkcji szablonu, `resource-group-name` aby wypełnić część. Korzystanie z tych funkcji oznacza, że laboratorium, do którego przypisujesz rolę, musi istnieć w bieżącej subskrypcji i tej samej grupy zasobów, do której jest dokonywane wdrożenie szablonu. Ostatnia część, `resource-name`, to nazwa laboratorium. Ta wartość jest odbierana za pośrednictwem parametru szablonu w tym przykładzie. 

Zakres roli w szablonie: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Wdrażanie szablonu
Najpierw utwórz plik parametrów (na przykład: azuredeploy.parameters.json), który przekazuje wartości parametrów w szablonie Menedżera zasobów. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Następnie użyj polecenia cmdlet programu PowerShell [new-AzureRmResourceGroupDeployment,](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) aby wdrożyć szablon Menedżera zasobów. Poniższe przykładowe polecenie przypisuje osobę, grupę lub jednostkę usługi do roli użytkownika DevTest Labs dla laboratorium.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Należy pamiętać, że nazwa wdrożenia grupy i identyfikator GUID przypisania roli muszą być unikatowe. Jeśli spróbujesz wdrożyć przydział zasobów z identyfikatorem GUID nieużytym, zostanie wyświetlony `RoleAssignmentUpdateNotPermitted` błąd.

Jeśli planujesz użyć szablonu kilka razy, aby dodać kilka obiektów usługi Active Directory do roli użytkownika DevTest Labs dla laboratorium, należy rozważyć użycie obiektów dynamicznych w poleceniu programu PowerShell. W poniższym przykładzie użyto polecenia cmdlet [New-Guid,](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) aby dynamicznie określić nazwę wdrożenia grupy zasobów i identyfikator GUID przypisania roli.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Jak wspomniano we wstępie, należy utworzyć nowe przypisanie roli platformy Azure, aby dodać użytkownika do roli **użytkownika DevTest Labs** dla laboratorium. W programie PowerShell należy to zrobić przy użyciu polecenia cmdlet [new-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) To polecenie cmdlet ma wiele parametrów opcjonalnych, aby zapewnić elastyczność. , `ObjectId` `SigninName`lub `ServicePrincipalName` można określić jako obiekt, któremu udzielono uprawnień.  

Oto przykładowe polecenie programu Azure PowerShell, które dodaje użytkownika do roli użytkownika DevTest Labs w określonym laboratorium.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Aby określić zasób, do którego uprawnienia są przyznawane, `ResourceGroup` można `scope` określić za pomocą kombinacji `ResourceName` `ResourceType`, lub parametru. Niezależnie od kombinacji parametrów, należy podać wystarczającą ilość informacji do polecenia cmdlet, aby jednoznacznie zidentyfikować obiekt usługi Active Directory (użytkownik, grupę lub jednostkę usługi), zakres (grupa zasobów lub zasób) i definicję roli.

## <a name="use-azure-command-line-interface-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure (CLI)
W interfejsie wiersza polecenia platformy Azure dodawanie użytkownika `az role assignment create` laboratorium odbywa się za pomocą polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure.](../role-based-access-control/role-assignments-cli.md)

Obiekt, do który jest przyznawany dostęp, można określić za `objectId`pomocą parametrów , `signInName` `spn` . Laboratorium, do którego obiekt jest przyznawany dostęp można `scope` zidentyfikować za pomocą `resource-name` `resource-type`adresu `resource-group` URL lub kombinacji , i parametrów.

W poniższym przykładzie interfejsu wiersza polecenia platformy Azure pokazano, jak dodać osobę do roli użytkownika devtest labs dla określonego laboratorium.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Tworzenie maszyn wirtualnych i zarządzanie nimi za pomocą laboratorium DevTest labs przy użyciu interfejsu wiersza polecenia platformy Azure](devtest-lab-vmcli.md)
- [Tworzenie maszyny wirtualnej w laboratoriach DevTest przy użyciu programu Azure PowerShell](devtest-lab-vm-powershell.md)
- [Uruchamianie i zatrzymywania maszyn wirtualnych w laboratoriach DevTest Labs za pomocą narzędzi wiersza polecenia](use-command-line-start-stop-virtual-machines.md)

