---
title: Szablony Menedżer zasobów dla Azure Cosmos DB interfejs API tabel
description: Utwórz i skonfiguruj Azure Cosmos DB interfejs API tabel za pomocą szablonów Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 41f3a4fe5afad1f993d91af83c7da27ac28e3759
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101074"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejs API tabel Azure Cosmos DB przy użyciu szablonów Azure Resource Manager
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

W tym artykule dowiesz się, jak używać szablonów Azure Resource Manager, aby ułatwić wdrażanie kont Azure Cosmos DB, baz danych i kontenerów oraz zarządzanie nimi.

W tym artykule przedstawiono przykłady tylko dla kont interfejs API tabel, aby znaleźć przykłady dla innych kont typu interfejsu API, zobacz: Używanie szablonów Azure Resource Manager z interfejsem API Azure Cosmos DB dla [Cassandra](./templates-samples-cassandra.md), [Gremlin](./templates-samples-gremlin.md), [MongoDB](./templates-samples-mongodb.md)i [SQL](./manage-with-templates.md) .

> [!IMPORTANT]
>
> * Nazwy kont są ograniczone do 44 znaków, wszystkie małe litery.
> * Aby zmienić wartości przepływności, wdróż ponownie szablon z zaktualizowanymi jednostkami RU/s.
> * Po dodaniu lub usunięciu lokalizacji na koncie usługi Azure Cosmos nie można jednocześnie modyfikować innych właściwości. Te operacje należy wykonać oddzielnie.

Aby utworzyć dowolny z poniższych zasobów Azure Cosmos DB, Skopiuj poniższy przykładowy szablon do nowego pliku JSON. Opcjonalnie można utworzyć plik JSON parametrów do użycia podczas wdrażania wielu wystąpień tego samego zasobu z różnymi nazwami i wartościami. Istnieje wiele sposobów wdrażania szablonów Azure Resource Manager, w tym [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) i [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Aby włączyć współdzieloną przepływność przy korzystaniu z interfejs API tabel, należy włączyć przepływność na poziomie konta w Azure Portal.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Konto usługi Azure Cosmos dla tabeli o przepływności automatycznego skalowania

Ten szablon utworzy konto usługi Azure Cosmos dla interfejs API tabel z jedną tabelą o przepływności skalowania automatycznego. Ten szablon jest również dostępny dla jednego kliknięcia przycisku Wdróż z galerii szablonów szybkiego startu platformy Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Konto usługi Azure Cosmos dla tabeli z zainicjowaną przepustowością standardowa

Ten szablon utworzy konto usługi Azure Cosmos dla interfejs API tabel z jedną tabelą ze standardową przepływność. Ten szablon jest również dostępny dla jednego kliknięcia przycisku Wdróż z galerii szablonów szybkiego startu platformy Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

* [Dokumentacja usługi Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)