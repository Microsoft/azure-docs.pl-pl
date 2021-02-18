---
title: Rejestrowanie dostawcy zasobów rozwiązań VMware platformy Azure
description: Procedura rejestrowania dostawcy zasobów rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653175"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Aby korzystać z rozwiązania Azure VMware, należy najpierw zarejestrować dostawcę zasobów w ramach subskrypcji. Aby uzyskać więcej informacji na temat dostawców zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](/azure/azure-resource-manager/management/resource-providers-and-types).

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
