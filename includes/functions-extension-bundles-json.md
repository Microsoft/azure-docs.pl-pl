---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689567"
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

Następujące właściwości są dostępne w `extensionBundle` :

| Właściwość | Opis |
| -------- | ----------- |
| identyfikator | Przestrzeń nazw dla pakietów rozszerzenia funkcji Microsoft Azure. |
| Wersja | Wersja pakietu do zainstalowania. Środowisko uruchomieniowe funkcji zawsze wybiera maksymalną dopuszczalną wersję zdefiniowaną przez zakres wersji lub interwał. Powyższa wartość wersji zezwala na wszystkie wersje pakietów z 1.0.0 do 2.0.0. Aby uzyskać więcej informacji, zobacz [notacja interwału określania zakresów wersji](/nuget/reference/package-versioning#version-ranges). |