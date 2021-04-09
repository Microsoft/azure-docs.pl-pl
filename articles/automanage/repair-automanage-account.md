---
title: Napraw przerwane konto usługi Azure automanage
description: Jeśli ostatnio przeniesiono subskrypcję zawierającą konto Autozarządzanie do nowej dzierżawy, należy ją ponownie skonfigurować. W tym artykule dowiesz się, jak.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183706"
---
# <a name="repair-an-automanage-account"></a>Naprawa konta Autozarządzanie
[Konto usługi Azure automanage](./automanage-virtual-machines.md#automanage-account) to kontekst zabezpieczeń lub tożsamość, w ramach której występują operacje zautomatyzowane. Jeśli ostatnio przeniesiono subskrypcję zawierającą konto Autozarządzanie do nowej dzierżawy, należy zmienić konfigurację konta. Aby zmienić jego konfigurację, należy zresetować typ tożsamości i przypisać odpowiednie role dla konta.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Krok 1: Resetowanie typu tożsamości Autozarządzanie konta
Zresetuj typ tożsamości konta Autozarządzanie przy użyciu następującego szablonu Azure Resource Manager (ARM). Zapisz plik lokalnie jako armdeploy.jslub podobną nazwę. Zanotuj nazwę konta i lokalizację autozarządzania, ponieważ są one wymaganymi parametrami w szablonie ARM.

1. Utwórz wdrożenie Menedżer zasobów przy użyciu poniższego szablonu. Użyj polecenia `identityType = None`.
    * Wdrożenie można utworzyć w interfejsie wiersza polecenia platformy Azure przy użyciu polecenia `az deployment sub create` . Aby uzyskać więcej informacji, zobacz [AZ Deployment sub](/cli/azure/deployment/sub).
    * Wdrożenie w programie PowerShell można utworzyć przy użyciu `New-AzDeployment` modułu. Aby uzyskać więcej informacji, zobacz polecenie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Uruchom ponownie ten sam szablon ARM przy użyciu programu `identityType = SystemAssigned` .

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

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Krok 2. przypisanie odpowiednich ról dla konta autozarządzaj
Konto Autozarządzanie wymaga ról współautor i współautora zasad zasobów w subskrypcji zawierającej maszyny wirtualne zarządzane przez Autozarządzanie. Role te można przypisywać przy użyciu Azure Portal, szablonów ARM lub interfejsu wiersza polecenia platformy Azure.

Jeśli używasz szablonu ARM lub interfejsu wiersza polecenia platformy Azure, będziesz potrzebować identyfikatora podmiotu zabezpieczeń (znanego również jako identyfikator obiektu) konta autozarządzania. (Jeśli używasz Azure Portal, nie potrzebujesz tego identyfikatora.) Ten identyfikator można znaleźć, korzystając z następujących metod:

- [Interfejs wiersza polecenia platformy Azure](/cli/azure/ad/sp): Użyj polecenie `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Przejdź do **Azure Active Directory** i Wyszukaj konto Autozarządzanie według nazwy. W obszarze **aplikacje dla przedsiębiorstw** wybierz nazwę konta autozarządzaj, gdy zostanie wyświetlona.

### <a name="azure-portal"></a>Azure Portal
1. W obszarze **subskrypcje** przejdź do subskrypcji zawierającej zarządzane przez siebie maszyny wirtualne.
1. Przejdź do **kontroli dostępu (IAM)**.
1. Wybierz pozycję **Dodaj przypisania ról**.
1. Wybierz rolę **współautor** i wprowadź nazwę konta do autozarządzania.
1. Wybierz pozycję **Zapisz**.
1. Powtórz kroki od 3 do 5, tym razem z rolą **współautor zasad zasobów** .

### <a name="arm-template"></a>Szablon ARM
Uruchom następujący szablon ARM. Wymagany jest Identyfikator podmiotu zabezpieczeń konta automanage. Kroki, które należy pobrać, znajdują się na początku tej sekcji. Wprowadź identyfikator po wyświetleniu monitu.

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
[Dowiedz się więcej o usłudze Azure automanage](./automanage-virtual-machines.md)