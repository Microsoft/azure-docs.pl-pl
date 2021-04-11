---
title: 'Eksportowanie danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Moduł eksportowanie danych w programie Azure Machine Learning Designer umożliwia zapisywanie wyników i danych pośrednich poza programem Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 90755aef66fa51084d83d036722187b61449a6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656910"
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

1. W obszarze **Magazyn** danych wybierz istniejący magazyn danych z listy rozwijanej. Możesz również utworzyć nowy magazyn danych. Sprawdź, jak odwiedzać [dane dostępu w usługach Azure Storage](../how-to-access-data.md).

    > [!NOTE]
    > Eksportowanie danych określonego typu danych do kolumny bazy danych SQL określonej jako inny typ danych nie jest obsługiwane. Tabela docelowa nie musi najpierw istnieć.

1. Pole wyboru, **Wygeneruj ponownie dane wyjściowe**, decyduje o tym, czy uruchomić moduł w celu ponownego wygenerowania danych wyjściowych w czasie wykonywania. 

    Jest to domyślnie niezaznaczone, co oznacza, że moduł został wykonany z tymi samymi parametrami, system użyje danych wyjściowych z ostatniego uruchomienia, aby skrócić czas wykonywania. 

    W przypadku wybrania tej operacji system ponownie uruchomi moduł w celu ponownego wygenerowania danych wyjściowych.

1. Zdefiniuj ścieżkę w magazynie danych, w której są dane. Ścieżka jest ścieżką względną. Zrób to na `data/testoutput` przykład, co oznacza, że dane wejściowe **eksportu** są eksportowane do magazynu danych `data/testoutput` ustawionego w **ustawieniach danych wyjściowych** modułu.

    > [!NOTE]
    > Puste ścieżki lub **ścieżki URL** są niedozwolone.


1. W polu **Format pliku** wybierz format, w którym mają być przechowywane dane.
 
1. Prześlij potok.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
