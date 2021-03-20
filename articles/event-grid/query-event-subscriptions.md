---
title: Zazapytania Azure Event Grid subskrypcje
description: W tym artykule opisano sposób wyświetlania listy Event Grid subskrypcji w ramach subskrypcji platformy Azure. Podajesz różne parametry w zależności od typu subskrypcji.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86120453"
---
# <a name="query-event-grid-subscriptions"></a>Zazapytania Event Grid subskrypcje 

W tym artykule opisano sposób wyświetlania listy subskrypcji Event Grid w ramach subskrypcji platformy Azure. Podczas wykonywania zapytania dotyczącego istniejących subskrypcji Event Grid ważne jest zrozumienie różnych typów subskrypcji. Podajesz różne parametry w zależności od typu subskrypcji, którą chcesz uzyskać.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Grupy zasobów i subskrypcje platformy Azure

Subskrypcje platformy Azure i grupy zasobów nie są zasobami platformy Azure. W związku z tym subskrypcje usługi Event Grid do grup zasobów lub subskrypcji platformy Azure nie mają takich samych właściwości, jak subskrypcje usługi Event Grid do zasobów platformy Azure. Subskrypcje usługi Event Grid do grup zasobów lub subskrypcji platformy Azure są uznawane za globalne.

Aby uzyskać subskrypcje usługi Event Grid dla subskrypcji platformy Azure i jej grup zasobów, nie musisz podawać żadnych parametrów. Upewnij się, że wybrano subskrypcję platformy Azure, którą chcesz zbadać. Poniższe przykłady nie pobierają subskrypcji usługi Event Grid dla tematów niestandardowych ani zasobów platformy Azure.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Aby uzyskać subskrypcje usługi Event Grid dla subskrypcji platformy Azure, podaj typ tematu **Microsoft. resources. subscriptions**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Aby uzyskać subskrypcje usługi Event Grid dla wszystkich grup zasobów w ramach subskrypcji platformy Azure, podaj typ tematu **Microsoft. resources. ResourceGroups**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Aby uzyskać subskrypcje usługi Event Grid dla określonej grupy zasobów, podaj nazwę grupy zasobów jako parametr.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Tematy niestandardowe i zasoby platformy Azure

Tematy niestandardowe usługi Event Grid to zasoby platformy Azure. W związku z tym należy wykonać zapytania dotyczące subskrypcji usługi Event Grid dla tematów niestandardowych i innych zasobów, takich jak konto usługi BLOB Storage, w ten sam sposób. Aby uzyskać subskrypcje usługi Event Grid dla tematów niestandardowych, należy podać parametry, które identyfikują zasób lub identyfikują lokalizację zasobu. Nie jest możliwe szerokie badanie subskrypcji usługi Event Grid dla zasobów w ramach subskrypcji platformy Azure.

Aby uzyskać subskrypcje usługi Event Grid dla tematów niestandardowych i innych zasobów w lokalizacji, podaj nazwę lokalizacji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Aby uzyskać subskrypcje niestandardowych tematów dla lokalizacji, podaj lokalizację i typ tematu **Microsoft. EventGrid. temats**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Aby uzyskać subskrypcje dotyczące kont magazynu dla lokalizacji, podaj lokalizację i typ tematu **Microsoft. Storage. StorageAccounts**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Aby uzyskać subskrypcje usługi Event Grid dla tematu niestandardowego, podaj nazwę tematu niestandardowego i nazwę jego grupy zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Aby uzyskać subskrypcje usługi Event Grid dla określonego zasobu, podaj identyfikator zasobu.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
