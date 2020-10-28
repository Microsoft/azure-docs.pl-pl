---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736861"
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
