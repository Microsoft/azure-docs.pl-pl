---
title: plik dołączany
description: plik dołączany
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183847"
---
## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli usługa Event Grid nie była wcześniej używana w subskrypcji platformy Azure, może być konieczne zarejestrowanie dostawcy zasobów usługi Event Grid. Uruchom następujące polecenie:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Ukończenie rejestracji może chwilę potrwać. Aby sprawdzić stan, uruchom polecenie:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Gdy właściwość `RegistrationStatus` uzyska wartość `Registered`, można kontynuować.
