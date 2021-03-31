---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "72329599"
---
Aplikacja funkcji została już utworzona na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do local.settings.jspliku. 

Z poziomu katalogu głównego projektu uruchom następujące polecenie Azure Functions Core Tools, aby pobrać ustawienia do local.settings.jsprzy zastępowaniu `<APP_NAME>` przy użyciu nazwy aplikacji funkcji z poprzedniego artykułu:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Może być konieczne zalogowanie się do konta platformy Azure.

> [!IMPORTANT]  
> To polecenie zastępuje wszystkie istniejące ustawienia wartościami z aplikacji funkcji na platformie Azure.  
>
> Ponieważ zawiera wpisy tajne, local.settings.jsw pliku nigdy nie są publikowane i powinny być wykluczone z kontroli źródła.  
> 

Wymagana `AzureWebJobsStorage` jest wartość, czyli parametry połączenia konta magazynu. To połączenie służy do sprawdzania, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.
