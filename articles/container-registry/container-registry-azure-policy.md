---
title: Zgodność przy użyciu Azure Policy
description: Przypisywanie wbudowanych zasad w Azure Policy do inspekcji zgodności rejestrów kontenerów platformy Azure
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 0fed0c4132043e1eaed7e634e1f45b27f7c6e933
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014302"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu Azure Policy

[Azure Policy](../governance/policy/overview.md) to usługa platformy Azure, za pomocą której można tworzyć i przypisywać zasady oraz zarządzać nimi. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług.

W tym artykule wprowadzono wbudowane zasady Azure Container Registry. Te zasady służą do inspekcji nowych i istniejących rejestrów pod kątem zgodności.

Za korzystanie z Azure Policy nie są naliczane opłaty.

## <a name="built-in-policy-definitions"></a>Wbudowane definicje zasad

Następujące wbudowane definicje zasad są specyficzne dla Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Przypisywanie zasad

* Przypisywanie zasad za pomocą [Azure Portal](../governance/policy/assign-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](../governance/policy/assign-policy-azurecli.md), [szablonu Menedżer zasobów](../governance/policy/assign-policy-template.md)lub zestawów SDK Azure Policy.
* Określanie zakresu przydziału zasad do grupy zasobów, subskrypcji lub [grupy zarządzania platformy Azure](../governance/management-groups/overview.md). Przypisania zasad rejestru kontenera mają zastosowanie do istniejących i nowych rejestrów kontenerów w ramach zakresu.
* Włącz lub Wyłącz [wymuszanie zasad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) w dowolnym momencie.

> [!NOTE]
> Po przypisaniu lub zaktualizowaniu zasad trwa jakiś czas, aby przypisanie zostało zastosowane do zasobów w zdefiniowanym zakresie. Zobacz informacje na temat [wyzwalaczy oceny zasad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Przegląd zgodności zasad

Uzyskaj dostęp do informacji o zgodności wygenerowanych przez przypisania zasad przy użyciu Azure Portal, narzędzi wiersza polecenia platformy Azure lub zestawów SDK Azure Policy. Aby uzyskać szczegółowe informacje, zobacz [pobieranie danych zgodności zasobów platformy Azure](../governance/policy/how-to/get-compliance-data.md).

Jeśli zasób nie jest zgodny, istnieje wiele możliwych przyczyn. Aby ustalić przyczynę lub aby znaleźć zmianę, zobacz [Określanie braku zgodności](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Zgodność zasad w portalu:

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj **zasady**.
1. Wybierz pozycję **zgodność**.
1. Użyj filtrów, aby ograniczyć stany zgodności lub wyszukać zasady.

    ![Zgodność zasad w portalu](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Wybierz zasady, aby przejrzeć zagregowane szczegóły zgodności i zdarzenia. W razie potrzeby wybierz konkretny rejestr w celu zapewnienia zgodności zasobów.

### <a name="policy-compliance-in-the-azure-cli"></a>Zgodność zasad w interfejsie wiersza polecenia platformy Azure

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby uzyskać dane zgodności. Na przykład użyj polecenia [AZ Policy list przypisano](/cli/azure/policy/assignment#az-policy-assignment-list) w interfejsie CLI, aby uzyskać identyfikatory zasad stosowanych Azure Container Registry zasad:

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

Następnie uruchom [AZ Policy State list](/cli/azure/policy/state#az-policy-state-list) , aby przywrócić stan zgodności sformatowany w formacie JSON dla wszystkich zasobów w ramach określonego identyfikatora zasad:

```azurecli
az policy state list \
  --resource <policyID>
```

Lub uruchom [AZ Policy State list](/cli/azure/policy/state#az-policy-state-list) w celu zwrócenia stanu zgodności w formacie JSON określonego zasobu rejestru, takiego jak mój *Rejestr*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Azure Policy [definicji](../governance/policy/concepts/definition-structure.md) i [efektów](../governance/policy/concepts/effects.md).

* Utwórz [niestandardową definicję zasad](../governance/policy/tutorials/create-custom-policy-definition.md).

* Dowiedz się więcej o [możliwościach ładu](../governance/index.yml) na platformie Azure.
