---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673362"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie parametrów połączenia usługi Azure Storage

Wcześniej utworzono konto usługi Azure Storage do użycia przez aplikację funkcji. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając ustawienia do *local.settings.jsw* pliku, można użyć zapisu tego połączenia do kolejki magazynu w ramach tego samego konta podczas lokalnego uruchamiania funkcji. 

1. Z poziomu katalogu głównego projektu uruchom następujące polecenie, zastępując je `<app_name>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki Start. To polecenie spowoduje zastąpienie wszelkich istniejących wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otwórz *local.settings.jsna* i Znajdź wartość o nazwie `AzureWebJobsStorage` , która jest ciągiem połączenia konta magazynu. Używasz nazwy `AzureWebJobsStorage` i parametrów połączenia w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *local.settings.json* zawiera wpisy tajne pobrane z platformy Azure, należy zawsze wykluczyć ten plik z kontroli źródła. Plik *. gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.