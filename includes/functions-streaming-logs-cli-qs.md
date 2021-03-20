---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93425078"
---
Uruchom następujące polecenie, aby wyświetlić [dzienniki przesyłania strumieniowego](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)niemal w czasie rzeczywistym:

```console
func azure functionapp logstream <APP_NAME> 
```

W osobnym oknie terminalu lub w przeglądarce wywołaj funkcję zdalną ponownie. W terminalu zostanie wyświetlony pełny dziennik wykonywania funkcji na platformie Azure. 