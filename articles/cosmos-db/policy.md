---
title: Używanie Azure Policy do implementowania ładu i kontroli dla zasobów Azure Cosmos DB
description: Dowiedz się, jak używać Azure Policy do implementowania ładu i kontroli dla zasobów Azure Cosmos DB.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 2249dbdebecc52a8f5d6decccb83d3b1fc0777f7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747372"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Używanie Azure Policy do implementowania ładu i kontroli dla zasobów Azure Cosmos DB

[Azure Policy](../governance/policy/overview.md) pomaga wymuszać standardy ładu organizacyjnego, oceniać zgodność zasobów i implementować automatyczne korygowanie. Typowe przypadki użycia obejmują zabezpieczenia, zarządzanie kosztami i spójność konfiguracji.

Azure Policy udostępnia wbudowane definicje zasad. Można utworzyć niestandardowe definicje zasad dla scenariuszy, które nie są rozwiązywane przez wbudowane definicje zasad. Więcej informacji można znaleźć w [dokumentacji Azure Policy](../governance/policy/overview.md) .

## <a name="assign-a-built-in-policy-definition"></a>Przypisywanie wbudowanej definicji zasad

Definicje zasad opisują warunki zgodności zasobów i wpływ, jaki należy wykonać w przypadku spełnienia warunku. _Przypisania_ zasad są tworzone na podstawie _definicji_zasad. Dla zasobów Azure Cosmos DB można używać wbudowanych lub niestandardowych definicji zasad. Przypisania zasad są ograniczone do grupy zarządzania platformy Azure, subskrypcji platformy Azure lub grupy zasobów i są stosowane do zasobów w ramach wybranego zakresu. Opcjonalnie możesz wykluczyć określone zasoby z zakresu.

Przypisania zasad można tworzyć za pomocą szablonu [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [interfejsu wiersza polecenia platformy Azure lub usługi](../governance/policy/assign-policy-azurecli.md) [ARM](../governance/policy/assign-policy-template.md).

Aby utworzyć przypisanie zasad z wbudowanej definicji zasad dla Azure Cosmos DB, wykonaj kroki opisane w sekcji [Tworzenie przypisania zasad za pomocą Azure Portal](../governance/policy/assign-policy-portal.md) artykułu.

W kroku, aby wybrać definicję zasad, wprowadź `Cosmos DB` w polu wyszukiwania, aby odfiltrować listę dostępnych wbudowanych definicji zasad. Wybierz jedną z dostępnych wbudowanych definicji zasad, a następnie wybierz **pozycję Wybierz** , aby kontynuować tworzenie przypisania zasad.

> [!TIP]
> Do tworzenia przypisań zasad można również użyć wbudowanych nazw definicji zasad widocznych w okienku **dostępne definicje** z Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonów ARM.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Wyszukaj Azure Cosmos DB wbudowane definicje zasad":::

## <a name="create-a-custom-policy-definition"></a>Tworzenie niestandardowej definicji zasad

W przypadku określonych scenariuszy, które nie są rozwiązywane przez zasady wbudowane, można utworzyć [niestandardową definicję zasad](../governance/policy/tutorials/create-custom-policy-definition.md). Później można utworzyć _przypisanie_ zasad z _definicji_zasad niestandardowych.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Typy właściwości i aliasy właściwości w regułach zasad

Aby zidentyfikować właściwości zasobów i aliasy właściwości, które są wymagane do tworzenia reguł zasad, należy użyć [niestandardowych kroków definicji zasad](../governance/policy/tutorials/create-custom-policy-definition.md) .

Aby zidentyfikować Azure Cosmos DB specyficzne aliasy właściwości, użyj przestrzeni nazw `Microsoft.DocumentDB` z jedną z metod przedstawionych w artykule dotyczącej definicji zasad niestandardowych.

#### <a name="use-the-azure-cli"></a>Użyj interfejsu wiersza polecenia platformy Azure:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Użyj Azure PowerShell:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Te polecenia wyprowadzają listę nazw aliasów właściwości dla właściwości Azure Cosmos DB. Poniżej znajduje się fragment danych wyjściowych:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Można użyć dowolnej z tych nazw aliasów właściwości w [regułach definicji zasad niestandardowych](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Poniżej przedstawiono przykładową definicję zasad, która sprawdza, czy Azure Cosmos DB przepływność administracyjna bazy danych SQL jest większa niż maksymalny dozwolony limit wynoszący 400 RU/s. Niestandardowa definicja zasad obejmuje dwie reguły: jeden do sprawdzenia określonego typu aliasu właściwości, a drugi dla konkretnej właściwości typu. Obie reguły używają nazw aliasów.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings"
      },
      {
      "field": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/default.resource.throughput",
      "greater": 400
      }
    ]
  }
}
```

Niestandardowe definicje zasad mogą służyć do tworzenia przypisań zasad, podobnie jak wbudowane definicje zasad są używane.

## <a name="policy-compliance"></a>Zgodność z zasadami

Po utworzeniu przypisań zasad Azure Policy szacuje zasoby w zakresie przypisania. Oceniane są _zgodność_ wszystkich zasobów z zasadami. _Efekt_ określony w zasadach zostanie następnie zastosowany do niezgodnych zasobów.

Możesz przejrzeć wyniki zgodności i szczegóły korygowania w [Azure Portal](../governance/policy/how-to/get-compliance-data.md#portal) lub za pośrednictwem [interfejsu wiersza polecenia platformy Azure](../governance/policy/how-to/get-compliance-data.md#command-line) lub [dzienników Azure monitor](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

Poniższy zrzut ekranu przedstawia dwa przykładowe przypisania zasad. Jedno przypisanie jest oparte na wbudowanej definicji zasad, która sprawdza, czy zasoby Azure Cosmos DB są wdrażane tylko dla dozwolonych regionów świadczenia usługi Azure. Inne przypisanie jest oparte na definicji zasad niestandardowych. To przypisanie sprawdza, czy zainicjowana przepływność dla Azure Cosmos DB zasobów nie przekracza określonego maksymalnego limitu.

Po wdrożeniu przypisań zasad pulpit nawigacyjny zgodności pokazuje wyniki oceny. Należy pamiętać, że po wdrożeniu przypisania zasad może to potrwać do 30 minut.

Zrzut ekranu przedstawia następujące wyniki oceny zgodności:

- Zero z jednego z kont Azure Cosmos DB w określonym zakresie są zgodne z przypisaniem zasad, aby sprawdzić, czy zasoby zostały wdrożone w dozwolonych regionach.
- Jeden z dwóch Azure Cosmos DB bazy danych lub zasobów kolekcji w określonym zakresie jest zgodny z przypisaniem zasad, aby sprawdzić, czy zainicjowana przepływność przekracza określony limit maksymalny.

:::image type="content" source="./media/policy/compliance.png" alt-text="Wyszukaj Azure Cosmos DB wbudowane definicje zasad":::

Aby skorygować niezgodne zasoby, zobacz artykuł [skorygowany Azure Policy](../governance/policy/how-to/remediate-resources.md) .

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z przykładowymi definicjami zasad niestandardowych dla Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)
- [Utwórz przypisanie zasad w Azure Portal](../governance/policy/assign-policy-portal.md)
- [Przejrzyj Azure Policy wbudowane definicje zasad dla Azure Cosmos DB](./policy-samples.md)
