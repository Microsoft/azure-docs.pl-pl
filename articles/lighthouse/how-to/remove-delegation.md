---
title: Usuwanie dostępu do delegacji
description: Dowiedz się, jak usunąć dostęp do zasobów, które zostały delegowane do dostawcy usług dla usługi Azure Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: c53b678ba6e37ece1bcaf2860abceb9eea980532
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555785"
---
# <a name="remove-access-to-a-delegation"></a>Usuwanie dostępu do delegacji

Po oddelegowaniu subskrypcji lub grupy zasobów klienta do dostawcy usług w [usłudze Azure Lighthouse](../overview.md)delegowanie można usunąć w razie potrzeby. Po usunięciu delegowania dostęp do [zarządzania zasobami delegowanych przez platformę Azure](../concepts/azure-delegated-resource-management.md) , który został wcześniej udzielony użytkownikom w dzierżawie dostawcy usług, nie będzie już stosowany.

Usunięcie delegowania może odbywać się przez użytkownika w dzierżawie klienta lub w dzierżawie dostawcy usług, o ile użytkownik ma odpowiednie uprawnienia.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z tych samych procesów.

## <a name="customers"></a>Klienci

Użytkownicy w dzierżawie klienta, którzy posiadają rolę z `Microsoft.Authorization/roleAssignments/write` uprawnieniem, taką jak [właściciel](../../role-based-access-control/built-in-roles.md#owner), mogą usunąć dostęp dostawcy usług do tej subskrypcji (lub grup zasobów w ramach tej subskrypcji). W tym celu użytkownik może przejść do [strony dostawcy usług](view-manage-service-providers.md#add-or-remove-service-provider-offers) w Azure Portal, znaleźć ofertę na ekranie oferta **dostawcy usług** , a następnie wybrać ikonę kosza w wierszu dla tej oferty.

Po potwierdzeniu usunięcia nie wszyscy użytkownicy w dzierżawie dostawcy usług będą mogli uzyskać dostęp do zasobów, które zostały wcześniej delegowane.

## <a name="service-providers"></a>Dostawcy usług

Użytkownicy w dzierżawie zarządzającej mogą usunąć dostęp do delegowanych zasobów, jeśli udzielono [roli usuwania przypisania rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) dla zasobów klienta. Jeśli ta rola nie została przypisana do żadnych użytkowników dostawcy usług, delegowanie może zostać usunięte tylko przez użytkownika w dzierżawie klienta.

W poniższym przykładzie przedstawiono przypisanie przyznanie **roli usuwania przypisania rejestracji usług zarządzanych** , która może być uwzględniona w pliku parametrów podczas [procesu](onboard-customer.md)dołączania:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Tę rolę można również wybrać w **autoryzacji** podczas [tworzenia oferty usługi zarządzanej](../../marketplace/plan-managed-service-offer.md) do opublikowania w portalu Azure Marketplace.

Użytkownik mający to uprawnienie może usunąć delegowanie w jeden z następujących sposobów.

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do [strony moi klienci](view-manage-customers.md).
2. Wybierz pozycję **delegacje**.
3. Znajdź delegowanie, które chcesz usunąć, a następnie wybierz ikonę kosza, która pojawia się w jego wierszu.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](../concepts/azure-delegated-resource-management.md).
- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
- Dowiedz się, jak [zaktualizować poprzednie delegowanie](update-delegation.md).
