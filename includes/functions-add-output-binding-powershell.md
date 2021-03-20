---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: 753a3d16792e0dbaffe318cd188ea3f0d40bd5cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78191066"
---
Dodaj kod, który używa `Push-OutputBinding` polecenia cmdlet do zapisywania tekstu w kolejce przy użyciu `msg` powiązania danych wyjściowych. Dodaj ten kod przed ustawieniem stanu OK w `if` instrukcji.

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

W tym momencie funkcja powinna wyglądać w następujący sposób:

:::code language="powershell" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::
