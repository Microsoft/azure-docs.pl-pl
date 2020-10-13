---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169916"
---
| | |
|--|--|
|**`<DESTINATION>`**| Miejsce docelowe, do którego są wysyłane dzienniki. Prawidłowe wartości to `AppInsights` i `Blob` .<br/>Gdy używasz `AppInsights` , upewnij się, że [w aplikacji funkcji jest włączona Application Insights](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Gdy ustawisz miejsce docelowe na `Blob` , dzienniki są tworzone w kontenerze obiektów BLOB o nazwie `azure-functions-scale-controller` w domyślnym koncie magazynu ustawionym w `AzureWebJobsStorage` ustawieniu aplikacji. |
|**`<VERBOSITY>`** | Określa poziom rejestrowania. Obsługiwane wartości to `None` , `Warning` , i `Verbose` .<br/>Po ustawieniu na `Verbose` kontroler skalowania rejestruje przyczynę każdej zmiany liczby procesów roboczych, a także informacje o wyzwalaczach, które są w tych decyzjach. Pełne dzienniki obejmują ostrzeżenia wyzwalacza i skróty używane przez wyzwalacze przed uruchomieniem kontrolera skali i po nim. |

> [!CAUTION]
> Nie zostawiaj włączonego rejestrowania kontrolera skalowania. Włącz rejestrowanie do momentu zebrania wystarczającej ilości danych, aby zrozumieć, jak działa kontroler skalowania, a następnie go wyłącz.