---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201941"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Podczas uruchamiania Host pobiera i instaluje [rozszerzenie powiązania magazynu](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) oraz inne rozszerzenia powiązań firmy Microsoft. Ta instalacja jest wykonywana, ponieważ rozszerzenia powiązań są domyślnie włączone w *host.js* pliku o następujących właściwościach:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Jeśli występują błędy związane z rozszerzeniami powiązań, sprawdź, czy powyższe właściwości są obecne w *host.js*.
::: zone-end  