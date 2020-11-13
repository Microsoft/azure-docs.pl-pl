---
title: Napraw przerwane konto usługi Azure automanage
description: Dowiedz się, jak naprawić przerwane konto autozarządzania
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557866"
---
# <a name="repair-a-broken-automanage-account"></a>Naprawianie uszkodzonego konta Autozarządzanie
[Konto autozarządzaj](./automanage-virtual-machines.md#automanage-account) jest kontekstem zabezpieczeń lub tożsamością, w której występują operacje zautomatyzowane. Jeśli ostatnio przeniesiono subskrypcję zawierającą konto Autozarządzanie do nowej dzierżawy, konieczne będzie ponowne skonfigurowanie konta autozarządzania. Aby zmienić konfigurację konta Autozarządzanie, należy zresetować typ tożsamości i przypisać odpowiednie role dla konta.

## <a name="step-1-reset-automanage-account-identity-type"></a>Krok 1: Resetowanie typu tożsamości Autozarządzanie kontem
Zresetuj typ tożsamości autozarządzaj kontem przy użyciu szablonu Azure Resource Manager (ARM) poniżej. Zapisz plik lokalnie lub w `armdeploy.json` podobny sposób. Zanotuj nazwę konta i lokalizację autozarządzania, ponieważ są to wymagane parametry w szablonie ARM.

1. Utwórz nowe wdrożenie ARM przy użyciu szablonu poniżej i Użyj `identityType = None`
    * Możesz to zrobić za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu polecenia `az deployment sub create` . Więcej informacji na temat polecenia znajduje się w `az deployment sub` [tym miejscu](https://docs.microsoft.com/cli/azure/deployment/sub).
    * Można to również zrobić przy użyciu programu PowerShell, korzystając z `New-AzDeployment` modułu. Więcej informacji na temat `New AzDeployment` modułu [znajdziesz tutaj](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Uruchom ponownie ten sam szablon ARM przy użyciu `identityType = SystemAssigned`

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
Konto autozarządzaj wymaga ról współautor i zasady współautora zasad zasobów w subskrypcji zawierającej maszyny wirtualne zarządzane przez Autozarządzanie. Role te można przypisywać przy użyciu Azure Portal, szablonów ARM lub interfejsu wiersza polecenia platformy Azure.

Jeśli używasz szablonu ARM lub interfejsu wiersza polecenia platformy Azure, będziesz potrzebować identyfikatora podmiotu zabezpieczeń (znanego również jako identyfikator obiektu) konta Autozarządzanie (nie jest to konieczne, jeśli używasz Azure Portal). Identyfikator podmiotu zabezpieczeń (identyfikator obiektu) można znaleźć, korzystając z następujących metod:

- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ad/sp): Użyj polecenie `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Przejdź do **Azure Active Directory** i Wyszukaj konto Autozarządzanie według nazwy. W obszarze **aplikacje dla przedsiębiorstw** wybierz nazwę konta autozarządzaj wyświetlaną podczas wyświetlania.

### <a name="azure-portal"></a>Azure Portal
1. W obszarze **subskrypcje** przejdź do subskrypcji zawierającej zarządzane przez siebie maszyny wirtualne.
1. Przejdź do **kontroli dostępu (IAM)**.
1. Kliknij pozycję **Dodaj przypisania ról**.
1. Wybierz rolę **współautor** i wpisz nazwę konta do autozarządzania.
1. Naciśnij pozycję **Zapisz**.
1. Powtórz kroki 3-5, tym razem z rolą **współautor zasad zasobów** .

### <a name="arm-template"></a>Szablon ARM
Uruchom następujący szablon ARM. Wymagany jest Identyfikator podmiotu zabezpieczeń konta autozarządzania — kroki umożliwiające uzyskanie identyfikatora podmiotu zabezpieczeń są wyższe. Wprowadź ją po wyświetleniu monitu.

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
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Następne kroki
Więcej informacji o usłudze Azure automanage [znajdziesz tutaj](./automanage-virtual-machines.md).
