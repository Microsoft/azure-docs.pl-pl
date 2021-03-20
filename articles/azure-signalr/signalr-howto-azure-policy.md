---
title: Zgodność przy użyciu Azure Policy
description: Przypisz wbudowane zasady w Azure Policy, aby prześledzić zgodność zasobów usługi Azure Signal Service.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 018033d3a6123948191a7261f5a1ee2ae526e25a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89295027"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Inspekcja zgodności zasobów usługi Azure sygnalizacyjnej przy użyciu Azure Policy

[Azure Policy](../governance/policy/overview.md) to usługa platformy Azure, za pomocą której można tworzyć i przypisywać zasady oraz zarządzać nimi. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług.

W tym artykule wprowadzono wbudowane zasady (wersja zapoznawcza) usługi Azure Signal Service. Te zasady służą do inspekcji nowych i istniejących zasobów sygnalizujących w celu zapewnienia zgodności.

Za korzystanie z Azure Policy nie są naliczane opłaty.

## <a name="built-in-policy-definitions"></a>Wbudowane definicje zasad

Następujące wbudowane definicje zasad są specyficzne dla usługi Azure Signal:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Przypisywanie definicji zasad

* Przypisywanie definicji zasad przy użyciu [Azure Portal](../governance/policy/assign-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](../governance/policy/assign-policy-azurecli.md), [szablonu Menedżer zasobów](../governance/policy/assign-policy-template.md)lub zestawów SDK Azure Policy.
* Określanie zakresu przydziału zasad do grupy zasobów, subskrypcji lub [grupy zarządzania platformy Azure](../governance/management-groups/overview.md). Przypisania zasad sygnalizujących mają zastosowanie do istniejących i nowych zasobów sygnalizujących w zakresie.
* Włącz lub Wyłącz [wymuszanie zasad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) w dowolnym momencie.

> [!NOTE]
> Po przypisaniu lub zaktualizowaniu zasad trwa jakiś czas, aby przypisanie zostało zastosowane do zasobów w zdefiniowanym zakresie. Zobacz informacje na temat [wyzwalaczy oceny zasad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Przegląd zgodności zasad

Uzyskaj dostęp do informacji o zgodności wygenerowanych przez przypisania zasad przy użyciu Azure Portal, narzędzi wiersza polecenia platformy Azure lub zestawów SDK Azure Policy. Aby uzyskać szczegółowe informacje, zobacz [pobieranie danych zgodności zasobów platformy Azure](../governance/policy/how-to/get-compliance-data.md).

Jeśli zasób nie jest zgodny, istnieje wiele możliwych przyczyn. Aby ustalić przyczynę lub aby znaleźć zmianę, zobacz [Określanie braku zgodności](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Zgodność zasad w portalu:

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj **zasady**.
1. Wybierz pozycję **zgodność**.
1. Użyj filtrów, aby ograniczyć stany zgodności lub wyszukać zasady
   
    [![Zgodność zasad w portalu ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Wybierz zasady, aby przejrzeć zagregowane szczegóły zgodności i zdarzenia. W razie potrzeby wybierz konkretny sygnał dla opcji zgodność zasobów.

### <a name="policy-compliance-in-the-azure-cli"></a>Zgodność zasad w interfejsie wiersza polecenia platformy Azure

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby uzyskać dane zgodności. Na przykład za pomocą polecenia [AZ Policy list przypisano](/cli/azure/policy/assignment#az-policy-assignment-list) w interfejsie CLI można pobrać identyfikatory zasad dotyczących zasad usługi Azure sygnalizujących, które są stosowane:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Przykładowe dane wyjściowe:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Następnie uruchom [AZ Policy State list](/cli/azure/policy/state#az-policy-state-list) , aby zwracał stan zgodności sformatowany w formacie JSON dla wszystkich zasobów w ramach określonej grupy zasobów:

```azurecli
az policy state list --g <resourceGroup>
```

Lub uruchom [AZ Policy State list](/cli/azure/policy/state#az-policy-state-list) w celu zwrócenia stanu zgodności w formacie JSON określonego zasobu sygnalizującego:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o Azure Policy [definicjach](../governance/policy/concepts/definition-structure.md) i [efektach](../governance/policy/concepts/effects.md)

* Utwórz [niestandardową definicję zasad](../governance/policy/tutorials/create-custom-policy-definition.md)

* Dowiedz się więcej o [możliwościach ładu](../governance/index.yml) na platformie Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/