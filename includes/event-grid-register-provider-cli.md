---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645420"
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
