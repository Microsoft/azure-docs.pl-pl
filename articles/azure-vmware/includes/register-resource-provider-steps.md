---
title: Rejestrowanie dostawcy zasobów rozwiązań VMware platformy Azure
description: Procedura rejestrowania dostawcy zasobów rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750481"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Aby korzystać z rozwiązania Azure VMware, należy najpierw zarejestrować dostawcę zasobów w ramach subskrypcji. Aby uzyskać więcej informacji na temat dostawców zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure Portal
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.

1. W polu **wszystkie usługi** wprowadź **subskrypcję**, a następnie wybierz pozycję **subskrypcje**.

1. Wybierz subskrypcję z listy subskrypcji do wyświetlenia.

1. Wybierz pozycję **dostawcy zasobów** i wprowadź w polu wyszukiwania **firmę Microsoft. Automatyczna synchronizacja** . 
 
1. Jeśli dostawca zasobów nie jest zarejestrowany, wybierz pozycję **zarejestruj**.