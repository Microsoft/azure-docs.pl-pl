---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 579ea74c4767ba1afb6e0128c9f12ff83f7f3998
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826593"
---
## <a name="enable-the-event-grid-resource-provider"></a>Włączanie dostawcy zasobów Event Grid

Jeśli w ramach subskrypcji platformy Azure nie użyto wcześniej Event Grid, może być konieczne zarejestrowanie Event Grid dostawcy zasobów. Uruchom następujące polecenie, aby zarejestrować dostawcę:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Ukończenie rejestracji może chwilę potrwać. Aby sprawdzić stan, uruchom polecenie:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Gdy właściwość `registrationState` uzyska wartość `Registered`, można kontynuować.
