---
title: Rejestrowanie dostawcy zasobów rozwiązań VMware platformy Azure
description: Procedura rejestrowania dostawcy zasobów rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555933"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Aby korzystać z rozwiązania Azure VMware, należy najpierw zarejestrować dostawcę zasobów w ramach subskrypcji. Aby uzyskać więcej informacji na temat dostawców zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.

1. W polu **wszystkie usługi** wprowadź **subskrypcję**, a następnie wybierz pozycję **subskrypcje**.

1. Wybierz subskrypcję z listy subskrypcji do wyświetlenia.

1. Wybierz pozycję **dostawcy zasobów** i wprowadź w polu wyszukiwania **firmę Microsoft. Automatyczna synchronizacja** . 
 
1. Jeśli dostawca zasobów nie jest zarejestrowany, wybierz pozycję **zarejestruj**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zaloguj się do subskrypcji platformy Azure, która jest używana na potrzeby wdrożenia rozwiązania Azure VMware za pomocą interfejsu wiersza polecenia platformy Azure. Zarejestruj `Microsoft.AVS` dostawcę zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider#az_provider_register) :

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Możesz użyć polecenia [AZ Provider list](/cli/azure/provider#az_provider_list) , aby wyświetlić wszystkich dostępnych dostawców.

---


 
