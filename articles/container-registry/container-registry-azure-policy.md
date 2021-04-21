---
title: Zgodność przy użyciu Azure Policy
description: Przypisywanie wbudowanych zasad w Azure Policy w celu inspekcji zgodności rejestrów kontenerów platformy Azure
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784177"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy

[Azure Policy](../governance/policy/overview.md) to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług.

W tym artykule oprowadzono wbudowane zasady dla Azure Container Registry. Te zasady są służące do inspekcji nowych i istniejących rejestrów w celu zapewnienia zgodności.

Korzystanie z usługi Azure Policy nie jest Azure Policy.

## <a name="built-in-policy-definitions"></a>Wbudowane definicje zasad

Następujące wbudowane definicje zasad są specyficzne dla Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Przypisywanie zasad

* Przypisz zasady przy [użyciu Azure Portal](../governance/policy/assign-policy-portal.md), interfejsu wiersza polecenia platformy [Azure,](../governance/policy/assign-policy-azurecli.md) [Resource Manager](../governance/policy/assign-policy-template.md)szablonu usługi lub Azure Policy SDK.
* Zakres przypisania zasad do grupy zasobów, subskrypcji lub grupy zarządzania [platformy Azure.](../governance/management-groups/overview.md) Przypisania zasad rejestru kontenerów mają zastosowanie do istniejących i nowych rejestrów kontenerów w zakresie.
* Włącz lub wyłącz [wymuszanie zasad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) w dowolnym momencie.

> [!NOTE]
> Po przypisaniu lub zaktualizowaniu zasad zastosowanie przypisania do zasobów w zdefiniowanym zakresie zajmuje trochę czasu. Zobacz informacje o [wyzwalaczach oceny zasad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Przeglądanie zgodności zasad

Uzyskaj dostęp do informacji o zgodności wygenerowanych przez przypisania zasad przy użyciu Azure Portal, narzędzi wiersza polecenia platformy Azure lub zestawów AZURE POLICY SDK. Aby uzyskać szczegółowe informacje, [zobacz Get compliance data of Azure resources (Uzyskiwanie danych zgodności zasobów platformy Azure).](../governance/policy/how-to/get-compliance-data.md)

Jeśli zasób jest niezgodny, istnieje wiele możliwych przyczyn. Aby określić przyczynę lub znaleźć odpowiedzialną zmianę, zobacz [Określanie niezgodności](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Zgodność z zasadami w portalu:

1. Wybierz **pozycję Wszystkie usługi** i wyszukaj pozycję **Zasady.**
1. Wybierz **pozycję Zgodność.**
1. Użyj filtrów, aby ograniczyć stany zgodności lub wyszukać zasady.

    ![Zgodność z zasadami w portalu](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Wybierz zasady, aby przejrzeć zagregowane szczegóły zgodności i zdarzenia. W razie potrzeby wybierz określony rejestr na potrzeby zgodności zasobów.

### <a name="policy-compliance-in-the-azure-cli"></a>Zgodność z zasadami w interfejsie wiersza polecenia platformy Azure

Aby uzyskać dane dotyczące zgodności, możesz również użyć interfejsu wiersza polecenia platformy Azure. Na przykład użyj polecenia [az policy assignment list w](/cli/azure/policy/assignment#az_policy_assignment_list) interfejsie wiersza polecenia, aby uzyskać identyfikatory zasad Azure Container Registry, które są stosowane:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Przykładowe dane wyjściowe:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Następnie uruchom [pozycję az policy state list,](/cli/azure/policy/state#az_policy_state_list) aby zwrócić stan zgodności w formacie JSON dla wszystkich zasobów w ramach określonego identyfikatora zasad:

```azurecli
az policy state list \
  --resource <policyID>
```

Możesz też [uruchomić az policy state list,](/cli/azure/policy/state#az_policy_state_list) aby zwrócić stan zgodności określonego zasobu rejestru w formacie JSON, takiego jak *myregistry:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Azure Policy [i](../governance/policy/concepts/definition-structure.md) [efektów.](../governance/policy/concepts/effects.md)

* Utwórz [niestandardową definicję zasad](../governance/policy/tutorials/create-custom-policy-definition.md).

* Dowiedz się więcej o [możliwościach ładu na](../governance/index.yml) platformie Azure.
