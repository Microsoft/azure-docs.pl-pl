---
title: Usuwanie dostępu do delegowania
description: Dowiedz się, jak usunąć dostęp do zasobów, które zostały delegowane do dostawcy usług w celu zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: d0db809eb057f8b4bb48bdf9dd127f4d488f0406
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149452"
---
# <a name="remove-access-to-a-delegation"></a>Usuwanie dostępu do delegowania

Po oddelegowaniu subskrypcji lub grupy zasobów klienta do dostawcy usług w celu [zarządzania zasobami delegowanymi przez platformę Azure](../concepts/azure-delegated-resource-management.md)delegowanie można usunąć w razie potrzeby. Po usunięciu delegowania dostęp uzyskany wcześniej do użytkowników w dzierżawie dostawcy usług nie będzie już stosowany.

Usunięcie delegowania może odbywać się przez użytkownika w dzierżawie klienta lub w dzierżawie dostawcy usług, o ile użytkownik ma odpowiednie uprawnienia.

## <a name="customers"></a>Klienci

Użytkownicy w dzierżawie klienta, którzy mają [wbudowaną rolę właściciela](../../role-based-access-control/built-in-roles.md#owner) subskrypcji, mogą usunąć dostęp dostawcy usług do tej subskrypcji (lub grup zasobów w ramach tej subskrypcji). W tym celu użytkownik w dzierżawie klienta może przejść do [strony dostawcy usług](view-manage-service-providers.md#add-or-remove-service-provider-offers) Azure Portal, znaleźć ofertę na ekranie **oferty dostawcy usług** , a następnie wybrać ikonę kosza w wierszu dla tej oferty.

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

Tę rolę można również wybrać w **autoryzacji** podczas [tworzenia oferty usługi zarządzanej](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) do opublikowania w portalu Azure Marketplace.

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
