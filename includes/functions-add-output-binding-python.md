---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78191065"
---
Zaktualizuj *HttpExample \\ \_ \_ init \_ \_ . PR* , aby pasował do poniższego kodu, dodając `msg` parametr do definicji funkcji i `msg.set(name)` w ramach `if name:` instrukcji.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg`Parametr jest wystąpieniem [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . Jego `set` Metoda zapisuje komunikat ciągu do kolejki, w tym przypadku nazwa została przeniesiona do funkcji w ciągu zapytania adresu URL.
