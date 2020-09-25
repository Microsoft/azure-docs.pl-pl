---
title: Rejestrowanie dostawcy zasobów rozwiązań VMware platformy Azure
description: Procedura rejestrowania dostawcy zasobów rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 380cb513ec389293db757e667ed4681778e29348
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254645"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Aby korzystać z rozwiązania Azure VMware, należy najpierw zarejestrować dostawcę zasobów w ramach subskrypcji.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Alternatywnie można użyć graficznego interfejsu użytkownika do zarejestrowania dostawcy zasobów **Microsoft. Automatyczna synchronizacja** .  Aby uzyskać więcej informacji, zobacz artykuł [Rejestrowanie dostawcy zasobów i typów](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) .  
