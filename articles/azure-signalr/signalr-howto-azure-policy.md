---
title: Zgodność przy użyciu Azure Policy
description: Przypisz wbudowane zasady w Azure Policy do inspekcji zgodności Azure SignalR Service zasobów.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784577"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Inspekcja zgodności Azure SignalR Service zasobów przy użyciu Azure Policy

[Azure Policy](../governance/policy/overview.md) to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług.

W tym artykule wprowadzono wbudowane zasady (wersja zapoznawcza) dla Azure SignalR Service. Użyj tych zasad, aby inspekcji nowych i istniejących zasobów SignalR w celu zapewnienia zgodności.

Korzystanie z usługi nie jest Azure Policy.

## <a name="built-in-policy-definitions"></a>Wbudowane definicje zasad

Następujące wbudowane definicje zasad są specyficzne dla Azure SignalR Service:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Przypisywanie definicji zasad

* Przypisz definicje zasad przy [użyciu Azure Portal](../governance/policy/assign-policy-portal.md), interfejsu wiersza polecenia platformy [Azure,](../governance/policy/assign-policy-azurecli.md) [Resource Manager szablonu](../governance/policy/assign-policy-template.md)lub Azure Policy SDK.
* Zakres przypisania zasad do grupy zasobów, subskrypcji lub grupy zarządzania [platformy Azure.](../governance/management-groups/overview.md) Przypisania zasad SignalR dotyczą istniejących i nowych zasobów SignalR w zakresie.
* Włącz lub wyłącz [wymuszanie zasad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) w dowolnym momencie.

> [!NOTE]
> Po przypisaniu lub zaktualizowaniu zasad zastosowanie przypisania do zasobów w zdefiniowanym zakresie zajmuje trochę czasu. Zobacz informacje o [wyzwalaczach oceny zasad.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Przeglądanie zgodności zasad

Uzyskaj dostęp do informacji o zgodności generowanych przez przypisania zasad przy użyciu Azure Portal, narzędzi wiersza polecenia platformy Azure lub zestawów AZURE POLICY SDK. Aby uzyskać szczegółowe informacje, [zobacz Get compliance data of Azure resources (Uzyskiwanie danych zgodności zasobów platformy Azure).](../governance/policy/how-to/get-compliance-data.md)

Gdy zasób jest niezgodny, istnieje wiele możliwych przyczyn. Aby określić przyczynę lub znaleźć odpowiedzialną zmianę, zobacz [Określanie niezgodności](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Zgodność z zasadami w portalu:

1. Wybierz **pozycję Wszystkie usługi** i wyszukaj pozycję **Zasady.**
1. Wybierz **pozycję Zgodność.**
1. Używanie filtrów do ograniczania stanów zgodności lub wyszukiwania zasad
   
    [![Zgodność z zasadami w portalu ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Wybierz zasady, aby przejrzeć zagregowane szczegóły zgodności i zdarzenia. W razie potrzeby wybierz określoną signalr na potrzeby zgodności zasobów.

### <a name="policy-compliance-in-the-azure-cli"></a>Zgodność z zasadami w interfejsie wiersza polecenia platformy Azure

Dane dotyczące zgodności można również uzyskać za pomocą interfejsu wiersza polecenia platformy Azure. Na przykład użyj polecenia [az policy assignment list w](/cli/azure/policy/assignment#az_policy_assignment_list) interfejsie wiersza polecenia, aby uzyskać identyfikatory zasad Azure SignalR Service zasad, które są stosowane:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Przykładowe dane wyjściowe:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Następnie uruchom [pozycję az policy state list,](/cli/azure/policy/state#az_policy_state_list) aby zwrócić stan zgodności w formacie JSON dla wszystkich zasobów w określonej grupie zasobów:

```azurecli
az policy state list --g <resourceGroup>
```

Lub uruchom [az policy state list,](/cli/azure/policy/state#az_policy_state_list) aby zwrócić stan zgodności określonego zasobu SignalR w formacie JSON:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Azure Policy [i](../governance/policy/concepts/definition-structure.md) [efektów](../governance/policy/concepts/effects.md)

* Tworzenie [niestandardowej definicji zasad](../governance/policy/tutorials/create-custom-policy-definition.md)

* Dowiedz się więcej o [możliwościach ładu na](../governance/index.yml) platformie Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/