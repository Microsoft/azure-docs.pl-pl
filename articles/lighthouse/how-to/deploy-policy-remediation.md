---
title: Wdrażanie zasad, które można skorygować
description: Aby wdrożyć zasady korzystające z zadania korygowania za pośrednictwem usługi Azure Lighthouse, należy utworzyć tożsamość zarządzaną w dzierżawie klienta.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: eb473fe2f589cf719e3944c887d46e75e9e7fdbf
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670495"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Wdróż zasady, które można skorygować w ramach delegowanej subskrypcji

Usługa [Azure Lighthouse](../overview.md) umożliwia dostawcom usług tworzenie i edytowanie definicji zasad w ramach delegowanej subskrypcji. Aby jednak wdrażać zasady korzystające z [zadania korygowania](../../governance/policy/how-to/remediate-resources.md) (czyli zasad z efektem [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) lub [Modyfikacja](../../governance/policy/concepts/effects.md#modify) ), należy utworzyć [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md) w dzierżawie klienta. Ta tożsamość zarządzana może być używana przez Azure Policy do wdrożenia szablonu w ramach zasad. Aby włączyć ten scenariusz, należy wykonać kroki w przypadku dołączenia klienta do zarządzania zasobami delegowanymi przez platformę Azure i po wdrożeniu samej zasady.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z tych samych procesów.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Tworzenie użytkownika, który może przypisywać role do tożsamości zarządzanej w dzierżawie klienta

Po dołączeniu klienta do usługi Azure Lighthouse należy użyć [szablonu Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) wraz z plikiem parametrów, który definiuje użytkowników, grupy użytkowników i jednostki usługi w dzierżawie zarządzającej, które będą mogły uzyskiwać dostęp do delegowanych zasobów w dzierżawie klienta. W pliku parametrów każdy z tych użytkowników (**principalId**) ma przypisaną [wbudowaną rolę](../../role-based-access-control/built-in-roles.md) (**zduplikowanych**), która definiuje poziom dostępu.

Aby umożliwić usłudze **principalId** utworzenie tożsamości zarządzanej w dzierżawie klienta, należy ustawić jej **zduplikowanych** na **administratora dostępu użytkowników**. Chociaż ta rola nie jest ogólnie obsługiwana, może być używana w tym konkretnym scenariuszu, co umożliwia użytkownikom z uprawnieniami do przypisywania do tożsamości zarządzanych co najmniej jednej konkretnej wbudowanej roli. Role te są zdefiniowane we właściwości **delegatedRoleDefinitionIds** . W tym miejscu możesz dołączyć dowolną wbudowaną rolę z wyjątkiem administratora dostępu użytkownika lub właściciela.

Po dołączeniu klienta **principalId** utworzony w ramach tej autoryzacji będzie mógł przypisywać te wbudowane role do zarządzanych tożsamości w dzierżawie klienta. Nie będą jednak mieć żadnych innych uprawnień zwykle skojarzonych z rolą administratora dostępu użytkownika.

W poniższym przykładzie przedstawiono **principalId** , którzy będą mieć rolę administratora dostępu użytkownika. Ten użytkownik będzie mógł przypisać dwie wbudowane role do zarządzanych tożsamości w dzierżawie klienta: współautor i Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Wdrażanie zasad, które można skorygować

Po utworzeniu użytkownika z wymaganymi uprawnieniami, zgodnie z powyższym opisem, ten użytkownik może wdrażać zasady, które używają zadań korygowania w ramach delegowanych subskrypcji klientów.

Załóżmy na przykład, że chcesz włączyć diagnostykę zasobów Azure Key Vault w dzierżawie klienta, jak pokazano w tym [przykładzie](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Użytkownik w dzierżawie zarządzającej z odpowiednimi uprawnieniami (zgodnie z powyższym opisem) wdroży [szablon Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) , aby włączyć ten scenariusz.

Należy pamiętać, że tworzenie przypisania zasad, które ma być używane z delegowaną subskrypcją, musi być obecnie wykonywane za pomocą interfejsów API, a nie w Azure Portal. W takim przypadku **apiVersion** musi mieć ustawioną wartość **2019-04-01-Preview**, która obejmuje nową właściwość **delegatedManagedIdentityResourceId** . Ta właściwość umożliwia dołączenie tożsamości zarządzanej, która znajduje się w dzierżawie klienta (w ramach subskrypcji lub grupy zasobów, która została dołączona do usługi Azure Lighthouse).

Poniższy przykład przedstawia przypisanie roli z **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [Podobny przykład](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) jest dostępny w celu zademonstrowania sposobu wdrażania zasad, które dodają lub usuwają tag (przy użyciu efektu Modyfikuj) do delegowanej subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Policy](../../governance/policy/index.yml).
- Poznaj [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).
