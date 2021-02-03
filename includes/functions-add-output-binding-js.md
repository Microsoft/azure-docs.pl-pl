---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 5e99c6e795729426964ec1f89f5f8c904e330227
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493399"
---
Dodaj kod, który używa `msg` obiektu powiązania danych wyjściowych w `context.bindings` celu utworzenia komunikatu w kolejce. Dodaj ten kod przed `context.res` instrukcją.

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

W tym momencie funkcja powinna wyglądać w następujący sposób:

:::code language="javascript" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::