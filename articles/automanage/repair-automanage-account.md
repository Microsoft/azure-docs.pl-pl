---
title: Naprawianie uszkodzonego Azure Automanage konta
description: Jeśli niedawno przeniesiono subskrypcję zawierającą konto automatycznego blokowania do nowej dzierżawy, należy ją ponownie skonfigurować. W tym artykule dowiesz się, jak to zrobić.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834196"
---
# <a name="repair-an-automanage-account"></a>Naprawianie konta automatycznego
Twoje [Azure Automanage to](./automanage-virtual-machines.md#automanage-account) kontekst zabezpieczeń lub tożsamość, w ramach której są wykonywane zautomatyzowane operacje. Jeśli niedawno przeniesiono subskrypcję zawierającą konto automatycznego blokowania do nowej dzierżawy, należy ponownie skonfigurować konto. Aby skonfigurować ją ponownie, należy zresetować typ tożsamości i przypisać odpowiednie role dla konta.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Krok 1. Resetowanie typu tożsamości Konto automatycznego zarządzania
Zresetuj typ tożsamości Automatycznie za pomocą konta, korzystając z następującego Azure Resource Manager (ARM). Zapisz plik lokalnie jako armdeploy.jslub pod podobną nazwą. Zanotuj nazwę i lokalizację konta automatycznego, ponieważ są one wymaganymi parametrami w szablonie usługi ARM.

1. Utwórz wdrożenie Resource Manager przy użyciu następującego szablonu. Użyj polecenia `identityType = None`.
    * Wdrożenie można utworzyć w interfejsie wiersza polecenia platformy Azure przy użyciu polecenia `az deployment sub create` . Aby uzyskać więcej informacji, zobacz [az deployment sub](/cli/azure/deployment/sub).
    * Wdrożenie można utworzyć w programie PowerShell przy użyciu `New-AzDeployment` modułu . Aby uzyskać więcej informacji, [zobacz New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Uruchom ponownie ten sam szablon usługi ARM za `identityType = SystemAssigned` pomocą .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Krok 2. Przypisywanie odpowiednich ról dla konta automatycznego
Konto automatycznego zarządzania wymaga ról Współautor i Współautor zasad zasobów w subskrypcji, która zawiera maszyny wirtualne, które są zarządzane przez funkcję automatycznego zarządzania. Te role można przypisywać przy użyciu Azure Portal, szablonów usługi ARM lub interfejsu wiersza polecenia platformy Azure.

Jeśli używasz szablonu usługi ARM lub interfejsu wiersza polecenia platformy Azure, będziesz potrzebować identyfikatora podmiotu zabezpieczeń (nazywanego również identyfikatorem obiektu) konta automatycznego zarządzania. (Identyfikator nie jest potrzebny, jeśli używasz Azure Portal). Ten identyfikator można znaleźć przy użyciu tych metod:

- [Interfejs wiersza polecenia platformy Azure:](/cli/azure/ad/sp)użyj polecenia `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: przejdź do **Azure Active Directory** i wyszukaj konto automatyczne według nazwy. W **obszarze Aplikacje dla** przedsiębiorstw wybierz nazwę konta automatycznego zarządzania, gdy zostanie wyświetlona.

### <a name="azure-portal"></a>Azure Portal
1. W **obszarze Subskrypcje** przejdź do subskrypcji zawierającej maszyny wirtualne, które są automatycznie.
1. Przejdź do **kontroli dostępu (IAM).**
1. Wybierz **pozycję Dodaj przypisania ról.**
1. Wybierz rolę **Współautor** i wprowadź nazwę konta automatycznego.
1. Wybierz pozycję **Zapisz**.
1. Powtórz kroki od 3 do 5, tym razem z rolą **Współautor zasad** zasobów.

### <a name="arm-template"></a>Szablon ARM
Uruchom następujący szablon usługi ARM. Będziesz potrzebować identyfikatora podmiotu zabezpieczeń konta automatycznego. Kroki, które należy wykonać, znajdują się na początku tej sekcji. Po wyświetleniu monitu wprowadź identyfikator.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Uruchom następujące polecenia:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o Azure Automanage](./automanage-virtual-machines.md)
