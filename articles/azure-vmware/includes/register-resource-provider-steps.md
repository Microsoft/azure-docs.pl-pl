---
title: Rejestrowanie dostawcy zasobów rozwiązań VMware platformy Azure
description: Procedura rejestrowania dostawcy zasobów rozwiązania VMware platformy Azure.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512378"
---
Aby korzystać z rozwiązania Azure VMware, należy najpierw zarejestrować dostawcę zasobów w ramach subskrypcji.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md).