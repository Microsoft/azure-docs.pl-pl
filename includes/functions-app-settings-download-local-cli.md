---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72329599"
---
Aplikacja funkcji została już utworzona na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do pliku Local. Settings. JSON. 

Z poziomu katalogu głównego projektu uruchom następujące polecenie Azure Functions Core Tools, aby pobrać ustawienia do pliku Local. Settings. JSON, zastępując `<APP_NAME>` je nazwą aplikacji funkcji z poprzedniego artykułu:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Może być konieczne zalogowanie się do konta platformy Azure.

> [!IMPORTANT]  
> To polecenie zastępuje wszystkie istniejące ustawienia wartościami z aplikacji funkcji na platformie Azure.  
>
> Ponieważ zawiera wpisy tajne, plik Local. Settings. JSON nigdy nie jest publikowany i powinien być wykluczony z kontroli źródła.  
> 

Wymagana jest wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. To połączenie służy do sprawdzania, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.
