---
title: Szablony Menedżer zasobów dla Azure Cosmos DB interfejsu API dla MongoDB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować interfejs API Azure Cosmos DB dla MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 7664d48bad153b34e7557e9faaf4c8aa0d4215ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340631"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu szablonów Azure Resource Manager
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule dowiesz się, jak używać szablonów Azure Resource Manager, aby ułatwić wdrażanie i zarządzanie kontami Azure Cosmos DB dla MongoDB interfejsu API, baz danych i kolekcji.

W tym artykule przedstawiono przykłady Azure Cosmos DB API for MongoDB, aby znaleźć przykłady dla innych kont typu interfejsu API, zobacz: Używanie szablonów Azure Resource Manager z interfejsem API Azure Cosmos DB dla [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [SQL](templates-samples-sql.md), artykułów [tabeli](templates-samples-table.md) .

> [!IMPORTANT]
>
> * Nazwy kont są ograniczone do 44 znaków, wszystkie małe litery.
> * Aby zmienić wartości przepływności, wdróż ponownie szablon z zaktualizowanymi jednostkami RU/s.
> * Po dodaniu lub usunięciu lokalizacji na koncie usługi Azure Cosmos nie można jednocześnie modyfikować innych właściwości. Te operacje należy wykonać oddzielnie.

Aby utworzyć dowolny z poniższych zasobów Azure Cosmos DB, Skopiuj poniższy przykładowy szablon do nowego pliku JSON. Opcjonalnie można utworzyć plik JSON parametrów do użycia podczas wdrażania wielu wystąpień tego samego zasobu z różnymi nazwami i wartościami. Istnieje wiele sposobów wdrażania szablonów Azure Resource Manager, w tym [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) i [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Konto usługi Azure Cosmos dla MongoDB z obsługą funkcji automatycznego skalowania

Ten szablon spowoduje utworzenie konta usługi Azure Cosmos dla interfejsu API MongoDB (3,2 lub 3,6) z dwiema kolekcjami, które udostępniają przepływność skalowania automatycznego na poziomie bazy danych. Ten szablon jest również dostępny dla jednego kliknięcia przycisku Wdróż z galerii szablonów szybkiego startu platformy Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Konto usługi Azure Cosmos dla MongoDB przy użyciu standardowej, alokowanej przepływności

Ten szablon spowoduje utworzenie konta usługi Azure Cosmos dla interfejsu API MongoDB (3,2 lub 3,6) z dwiema kolekcjami, które współ400 użytkują standardową przepływność (ręcznie) na poziomie bazy danych. Ten szablon jest również dostępny dla jednego kliknięcia przycisku Wdróż z galerii szablonów szybkiego startu platformy Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

* [Dokumentacja usługi Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schemat dostawcy zasobów Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Szablony szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Rozwiązywanie typowych błędów wdrażania Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)