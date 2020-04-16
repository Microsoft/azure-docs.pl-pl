---
title: Obsługa wierszy błędów z przepływami danych mapowania w usłudze Azure Data Factory
description: Dowiedz się, jak radzić sobie z błędami obcinania SQL w usłudze Azure Data Factory przy użyciu przepływów danych mapowania.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 4f65421a6457d4bf4d438ce9d035d46476829da2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414353"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Obsługa wierszy błędów obcinania SQL w przepływach danych mapowania usługi Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Typowym scenariuszem w fabryce danych podczas korzystania z przepływów danych mapowania jest zapisanie przekształconych danych w bazie danych SQL platformy Azure. W tym scenariuszu typowym warunkiem błędu, który należy zapobiec jest możliwe obcinania kolumny. Wykonaj następujące kroki, aby zapewnić rejestrowanie kolumn, które nie będą pasować do kolumny docelowego ciągu, umożliwiając przepływ danych, aby kontynuować w tych scenariuszach.

## <a name="scenario"></a>Scenariusz

1. Mamy docelową tabelę bazy danych ```nvarchar(5)``` SQL platformy Azure, która ma kolumnę o nazwie "nazwa".

2. Wewnątrz naszego przepływu danych chcemy mapować tytuły filmów z naszego zlewu do tej docelowej kolumny "nazwa".

    ![Przepływ danych filmowych 1](media/data-flow/error4.png)
    
3. Problem polega na tym, że tytuł filmu nie zmieści się w kolumnie zlewu, która może pomieścić tylko 5 znaków. Podczas wykonywania tego przepływu danych zostanie wyświetlony błąd, taki jak ten:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Jak zaprojektować wokół tego warunku

1. W tym scenariuszu maksymalna długość kolumny "nazwa" wynosi pięć znaków. Dodajmy więc transformację podziału warunkowego, która pozwoli nam rejestrować wiersze z "tytułami", które są dłuższe niż pięć znaków, a także pozwalać pozostałym wierszom, które mogą zmieścić się w tym miejscu, aby zapisać w bazie danych.

    ![podział warunkowy](media/data-flow/error1.png)

2. Ta transformacja podziału warunkowego definiuje maksymalną długość "title" na pięć. Każdy wiersz, który jest mniejszy lub równy pięciu trafi do strumienia. ```GoodRows``` Każdy wiersz, który jest większy ```BadRows``` niż pięć, przejdzie do strumienia.

3. Teraz musimy rejestrować wiersze, które nie powiodły się. Dodaj transformację ujścia do strumienia ```BadRows``` do rejestrowania. Tutaj "automatycznie mapujemy" wszystkie pola, abyśmy rejestrowali pełny rekord transakcji. Jest to plik CSV rozdzielany tekstem do pojedynczego pliku w magazynie obiektów Blob. Będziemy nazywać plik dziennika "badrows.csv".

    ![Złe wiersze](media/data-flow/error3.png)
    
4. Wypełniony przepływ danych jest pokazany poniżej. Jesteśmy teraz w stanie podzielić wiersze błędów, aby uniknąć błędów obcinania SQL i umieścić te wpisy w pliku dziennika. Tymczasem wiersze pomyślne można nadal zapisywać w naszej docelowej bazie danych.

    ![pełny przepływ danych](media/data-flow/error2.png)

## <a name="next-steps"></a>Następne kroki

* Skompiluj pozostałą część logiki przepływu danych przy użyciu [przekształceń przepływów](concepts-data-flow-overview.md)danych mapowania .
