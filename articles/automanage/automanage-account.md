---
title: Azure Automanage konto
description: Dowiedz się, jak działa automatyczne konto i jak je utworzyć.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368698"
---
# <a name="automanage-accounts"></a>Automatyczne odpowiadanie kontom

Konto Automatycznego zarządzania to tożsamość, która jest używana przez usługę Automatycznego zarządzania do wykonywania jej zautomatyzowanych operacji.

W środowisku Azure Portal, gdy włączasz automatyczne zarządzania na maszynach wirtualnych, w bloku Włączanie najlepszych rozwiązań dla maszyn wirtualnych platformy **Azure** znajduje się lista rozwijana Zaawansowane, która umożliwia przypisanie lub ręczne utworzenie konta automatycznego zarządzania.

Do konta automatycznego zarządzania zostaną przyznane  role **Współautor** i Współautor zasad zasobów w subskrypcjach zawierających maszyny, które zostały dołączane do funkcji Automatycznego zarządzania. Możesz użyć tego samego konta automatycznego zarządzania na maszynach w wielu subskrypcjach, co spowoduje przyznanie uprawnień Współautor konta automatycznego i Współautor **zasad** zasobów we wszystkich subskrypcjach. 

Jeśli maszyna wirtualna jest połączona z obszarem roboczym usługi Log Analytics  w  innej subskrypcji, konto automatycznego zarządzania zostanie przyznane współautorowi i współautorowi zasad zasobów również w tej innej subskrypcji.

W przypadku włączania funkcji Automanage przy użyciu nowego konta automatycznegomanage potrzebne  są następujące uprawnienia w ramach subskrypcji: **Rola** właściciela lub Współautor wraz z rolami **administratora dostępu** użytkowników.

W przypadku włączania funkcji Automatycznego zarządzania przy użyciu istniejącego konta automatycznego zarządzania musisz mieć rolę **Współautor** w grupie zasobów zawierającej maszyny wirtualne.

> [!NOTE]
> Po wyłączeniu opcji Najlepsze rozwiązania dotyczące automatycznego postępowania uprawnienia konta automatycznego do skojarzonych subskrypcji pozostaną niezmienione. Ręcznie usuń uprawnienia, przechodząc do strony IAM subskrypcji lub usuń konto automatycznego konta. Nie można usunąć konta automatycznego zarządzania, jeśli nadal zarządza ono dowolnymi maszynami.

## <a name="create-an-automanage-account"></a>Tworzenie konta automatycznego
Możesz utworzyć konto automatycznego konta za pomocą portalu lub szablonu usługi ARM.

### <a name="portal"></a>Portal
1. Przejdź do **bloku Automatycznemanage** w portalu
1. Kliknij **pozycję Włącz na istniejącej maszynie**
1. W **obszarze Zaawansowane** kliknij pozycję "Utwórz nowe konto"
1. Wypełnij wymagane pola i kliknij pozycję **Utwórz**

### <a name="arm-template"></a>Szablon ARM
Tworzenie konta automatycznego za pomocą szablonu usługi ARM wymaga wykonania 2 kroków:
1. Tworzenie konta automatycznego
1. Przyznaj kontu wystarczające uprawnienia, aby umożliwić mu wykonywanie operacji za Ciebie
    1. Będziesz potrzebować identyfikatora obiektu konta utworzonego na potrzeby tego kroku.
        1. Kroki służące do znalezienia szczegółów jednostki usługi konta (w tym identyfikatora obiektu) są [dostępne tutaj.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)
    1. Po odnalezioniu jednostki usługi skopiuj identyfikator **obiektu**. Zapisz ten plik, ponieważ będzie on potrzebny do delegowania uprawnień poniżej.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Tworzenie konta automatycznego (nie powoduje przyznania do niego uprawnień)
Aby utworzyć konto automatycznego zarządzania, zapisz następujący szablon usługi ARM jako i uruchom poniższe polecenie interfejsu `azuredeploy.json` wiersza polecenia platformy Azure. Gdy wszystko będzie gotowe, przejdź do drugiego szablonu poniżej, aby delegować wystarczające uprawnienia do konta.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Udzielanie uprawnień do konta automatycznego
Aby udzielić wystarczających uprawnień do konta automatycznego, należy wykonać następujące czynności:
1. Zapisz następujący szablon usługi ARM jako `azuredeploy2.json` i uruchom poniższe polecenie interfejsu wiersza polecenia platformy Azure.
1. Po wyświetleniu monitu wprowadź identyfikator obiektu utworzonego i zapisanego konta automatycznego.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usługach automanage dla [systemów Linux i](./automanage-linux.md) [Windows](./automanage-windows-server.md)