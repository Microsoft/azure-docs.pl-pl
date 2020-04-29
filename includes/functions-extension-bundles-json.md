---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878253"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Następujące właściwości są dostępne w `extensionBundle`:

| Właściwość | Opis |
| -------- | ----------- |
| id | Przestrzeń nazw dla pakietów rozszerzenia funkcji Microsoft Azure. |
| Wersja | Wersja pakietu do zainstalowania. Środowisko uruchomieniowe funkcji zawsze wybiera maksymalną dopuszczalną wersję zdefiniowaną przez zakres wersji lub interwał. Powyższa wartość wersji zezwala na wszystkie wersje pakietów z 1.0.0 do 2.0.0. Aby uzyskać więcej informacji, zobacz [notacja interwału określania zakresów wersji](/nuget/reference/package-versioning#version-ranges). |

Wersje pakietu zwiększają się wraz z pakietem w ramach zmiany pakietu. Zmiany wersji głównej są wykonywane, gdy pakiety w pakiecie są zwiększane przez wersję główną. Główne zmiany wersji w zbiorze zwykle pokrywają się ze zmianą wersji głównej środowiska uruchomieniowego funkcji.  

Bieżący zestaw rozszerzeń instalowanych przez pakiet domyślny jest wyliczany w tym [pliku Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
