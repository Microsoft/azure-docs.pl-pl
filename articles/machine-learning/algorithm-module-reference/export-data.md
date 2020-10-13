---
title: 'Eksportowanie danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu eksport danych w Azure Machine Learning, aby zapisywać wyniki, dane pośrednie i dane robocze z potoków do miejsc docelowych magazynu w chmurze poza Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: 5964ed9773d45b809fc79252eabc754b1b04af84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907992"
---
# <a name="export-data-module"></a>Eksportuj moduł danych

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do zapisywania wyników, danych pośrednich i danych roboczych z potoków do miejsc docelowych magazynu w chmurze. 

Ten moduł obsługuje eksportowanie danych do następujących usług danych w chmurze:

- Kontener obiektów blob platformy Azure
- Udział plików platformy Azure
- Usługa Azure Data Lake Storage 1. generacji
- Usługa Azure Data Lake Storage 2. generacji
- Baza danych Azure SQL Database

Przed wyeksportowaniem danych musisz najpierw zarejestrować magazyn danych w obszarze roboczym Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [dostęp do danych w usługach Azure Storage](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Jak skonfigurować dane eksportu

1. Dodaj moduł **eksportu danych** do potoku w projektancie. Ten moduł można znaleźć w kategorii **dane wejściowe i wyjściowe** .

1. Połącz **eksport danych** z modułem zawierającym dane, które chcesz wyeksportować.

1. Wybierz pozycję **Eksportuj dane** , aby otworzyć okienko **Właściwości** .

1. W obszarze **Magazyn**danych wybierz istniejący magazyn danych z listy rozwijanej. Możesz również utworzyć nowy magazyn danych. Sprawdź, jak odwiedzać [dane dostępu w usługach Azure Storage](../how-to-access-data.md).

    > [!NOTE]
    > Eksportowanie danych określonego typu danych do kolumny bazy danych SQL określonej jako inny typ danych nie jest obsługiwane. Tabela docelowa nie musi najpierw istnieć.

1. Pole wyboru, **Wygeneruj ponownie dane wyjściowe**, decyduje o tym, czy uruchomić moduł w celu ponownego wygenerowania danych wyjściowych w czasie wykonywania. 

    Jest to domyślnie niezaznaczone, co oznacza, że moduł został wykonany z tymi samymi parametrami, system użyje danych wyjściowych z ostatniego uruchomienia, aby skrócić czas wykonywania. 

    W przypadku wybrania tej operacji system ponownie uruchomi moduł w celu ponownego wygenerowania danych wyjściowych.

1. Zdefiniuj ścieżkę w magazynie danych, w której są dane. Ścieżka jest ścieżką względną. Puste ścieżki lub ścieżki URL są niedozwolone.


1. W polu **Format pliku**wybierz format, w którym mają być przechowywane dane.
 
1. Prześlij potok.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
