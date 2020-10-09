---
title: Obsługa wierszy błędów z mapowaniem przepływów danych w Azure Data Factory
description: Dowiedz się, jak obsłużyć błędy obcinania SQL w Azure Data Factory przy użyciu mapowania przepływów danych.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 3f8ac2d1434019548b01d8468015a543d89d0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254416"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Obsługa wierszy błędów obcięcia SQL w Data Factory mapowania przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Typowym scenariuszem w Data Factory podczas korzystania z mapowania przepływów danych jest zapisanie przekształconych danych do bazy danych w Azure SQL Database. W tym scenariuszu typowym warunkiem błędu, które należy zablokować, jest możliwość obcięcia kolumny. Wykonaj następujące kroki, aby zapewnić rejestrowanie kolumn, które nie mieszczą się w docelowej kolumnie ciągu, co pozwoli na kontynuowanie przepływu danych w tych scenariuszach.

## <a name="scenario"></a>Scenariusz

1. Istnieje docelowa tabela bazy danych z ```nvarchar(5)``` kolumną o nazwie "name".

2. W ramach przepływu danych chcemy zmapować tytuły filmów z ujścia do tej docelowej kolumny "name".

    ![Przepływ danych filmu 1](media/data-flow/error4.png)
    
3. Problem polega na tym, że tytuł filmu nie pasuje do kolumny ujścia, która może zawierać tylko 5 znaków. Po wykonaniu tego przepływu danych zostanie wyświetlony następujący błąd: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Ten film wideo analizuje przykład logiki obsługi wierszy błędów konfiguracji w przepływie danych:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Jak projektować dookoła tego stanu

1. W tym scenariuszu Maksymalna długość kolumny "name" wynosi pięć znaków. Dodajmy przekształcenie podziału warunkowego, które umożliwi nam rejestrowanie wierszy z "tytułami", które są dłuższe niż pięć znaków, a także Zezwalanie na pozostałą część wierszy, które mogą się zmieścić w tym miejscu w celu zapisu w bazie danych.

    ![podział warunkowy](media/data-flow/error1.png)

2. Ta transformacja podziału warunkowego określa maksymalną długość tytułu równą pięć. Każdy wiersz, który jest mniejszy niż lub równy pięć, będzie przeszedł do ```GoodRows``` strumienia. Wszystkie wiersze, które są większe niż pięć, zostaną umieszczone w ```BadRows``` strumieniu.

3. Teraz musimy zarejestrować wiersze, które nie powiodły się. Dodaj transformację ujścia do ```BadRows``` strumienia w celu zarejestrowania. W tym miejscu będziemy "mapować teraz wszystkie pola, aby rejestrować kompletne rekordy transakcji. Jest to rozdzielany tekstem plik CSV danych wyjściowych do pojedynczego pliku w Blob Storage. Wywołajemy plik dziennika "badrows.csv".

    ![Złe wiersze](media/data-flow/error3.png)
    
4. Ukończony przepływ danych jest przedstawiony poniżej. Teraz można rozdzielić wiersze błędów, aby uniknąć błędów obcinania SQL i umieścić te wpisy w pliku dziennika. W tym czasie udane wiersze mogą nadal zapisywać w naszej docelowej bazie danych.

    ![Ukończ przepływ danych](media/data-flow/error2.png)

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.
