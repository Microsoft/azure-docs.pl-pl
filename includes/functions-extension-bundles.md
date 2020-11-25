---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
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