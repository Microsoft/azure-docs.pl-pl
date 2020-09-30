---
title: Rejestrowanie dostawcy zasobów rozwiązań VMware platformy Azure
description: Procedura rejestrowania dostawcy zasobów rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575745"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Aby korzystać z rozwiązania Azure VMware, należy najpierw zarejestrować dostawcę zasobów w ramach subskrypcji.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Alternatywnie można użyć graficznego interfejsu użytkownika do zarejestrowania dostawcy zasobów **Microsoft. Automatyczna synchronizacja** .  Aby uzyskać więcej informacji, zobacz artykuł [Rejestrowanie dostawcy zasobów i typów](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) .  
