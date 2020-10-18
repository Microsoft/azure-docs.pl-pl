---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 906413d0a6702e6146779f79d628b5cebf383af1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165773"
---
| | |
|--|--|
|**`<DESTINATION>`**| Miejsce docelowe, do którego są wysyłane dzienniki. Prawidłowe wartości to `AppInsights` i `Blob` .<br/>Gdy używasz `AppInsights` , upewnij się, że [w aplikacji funkcji jest włączona Application Insights](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Gdy ustawisz miejsce docelowe na `Blob` , dzienniki są tworzone w kontenerze obiektów BLOB o nazwie `azure-functions-scale-controller` w domyślnym koncie magazynu ustawionym w `AzureWebJobsStorage` ustawieniu aplikacji. |
|**`<VERBOSITY>`** | Określa poziom rejestrowania. Obsługiwane wartości to `None` , `Warning` , i `Verbose` .<br/>Po ustawieniu na `Verbose` kontroler skalowania rejestruje przyczynę każdej zmiany liczby procesów roboczych, a także informacje o wyzwalaczach, które są w tych decyzjach. Pełne dzienniki obejmują ostrzeżenia wyzwalacza i skróty używane przez wyzwalacze przed uruchomieniem kontrolera skali i po nim. |

> [!TIP]
> Należy pamiętać, że w przypadku pozostawienia włączonego rejestrowania kontrolera skalowania ma wpływ na [potencjalne koszty monitorowania aplikacji funkcji](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Należy rozważyć włączenie rejestrowania do momentu zebrania wystarczającej ilości danych, aby zrozumieć, jak działa kontroler skalowania, a następnie jego wyłączenie.