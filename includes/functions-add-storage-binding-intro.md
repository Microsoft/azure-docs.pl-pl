---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "72329612"
---
Azure Functions umożliwia łączenie usług platformy Azure i innych zasobów z funkcjami bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są podawane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](../articles/azure-functions/functions-triggers-bindings.md).