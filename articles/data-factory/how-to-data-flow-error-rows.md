---
title: Obsługa wierszy błędów z mapowaniem przepływów danych w Azure Data Factory
description: Dowiedz się, jak obsłużyć błędy obcinania SQL w Azure Data Factory przy użyciu mapowania przepływów danych.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: a7a03ff1a58f50f16ebefce48b9e2772a16a011a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386343"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Obsługa wierszy błędów obcięcia SQL w Data Factory mapowania przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Typowym scenariuszem w Data Factory podczas korzystania z mapowania przepływów danych jest zapisanie przekształconych danych do bazy danych w Azure SQL Database. W tym scenariuszu typowym warunkiem błędu, które należy zablokować, jest możliwość obcięcia kolumny.

Istnieją dwie podstawowe metody, które umożliwiają płynne obsłudze błędów podczas zapisywania danych do ujścia bazy danych w przepływach danych ADF:

* Ustaw [obsługę wiersza błędu](./connector-azure-sql-database.md#error-row-handling) ujścia na "Kontynuuj przy błędzie" podczas przetwarzania danych bazy danych. To jest zautomatyzowana Metoda catch-all, która nie wymaga logiki niestandardowej w przepływie danych.
* Alternatywnie wykonaj poniższe kroki, aby podać rejestrowanie kolumn, które nie mieszczą się w docelowej kolumnie ciągu, co pozwoli na kontynuowanie przepływu danych.

> [!NOTE]
> Podczas włączania automatycznej obsługi wierszy błędów, w przeciwieństwie do metody poniżej pisania własnej logiki obsługi błędów, nastąpi Mała wydajność i dodatkowy krok wykonany przez funkcję ADF w celu przeprowadzenia wielofazowej operacji w przypadku błędów pułapek.

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

5. Jeśli wybierzesz opcję obsługi wierszy błędów w transformację ujścia i ustawisz "dane wyjściowe wierszy błędów", funkcja ADF automatycznie wygeneruje dane wyjściowe pliku CSV z danymi wierszy wraz z komunikatami o błędach zgłoszonymi przez sterowniki. Nie trzeba ręcznie dodawać tej logiki do przepływu danych przy użyciu tej opcji alternatywnej. W przypadku tej opcji nastąpi mała spadek wydajności, dzięki czemu ADF może zaimplementować 2-fazowyą metodologię do błędów pułapek i rejestrować je.

    ![Ukończ przepływ danych z wierszami błędów](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.