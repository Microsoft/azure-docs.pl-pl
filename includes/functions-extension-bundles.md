---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010473"
---
Najprostszym sposobem instalacji rozszerzeń powiązań jest włączenie [pakietów rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Po włączeniu pakietów zostanie automatycznie zainstalowana wstępnie zdefiniowany zestaw pakietów rozszerzeń.

Aby włączyć zbiory rozszerzeń, Otwórz host.jspliku i zaktualizuj jego zawartość w celu dopasowania do następującego kodu:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```