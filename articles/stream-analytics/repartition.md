---
title: Używanie ponownego partycjonowania do optymalizowania Azure Stream Analytics zadań
description: W tym artykule opisano sposób użycia ponownego partycjonowania w celu zoptymalizowania Azure Stream Analytics zadań, które nie mogą być równoległe.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75732386"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Użyj ponownego partycjonowania, aby zoptymalizować przetwarzanie za pomocą Azure Stream Analytics

W tym artykule pokazano, jak za pomocą ponownego partycjonowania skalować zapytanie Azure Stream Analytics w scenariuszach, które nie mogą być w pełni [równoległe](stream-analytics-scale-jobs.md).

Nie można używać [przetwarzanie równoległe](stream-analytics-parallelization.md) , jeśli:

* Nie kontrolujesz klucza partycji dla strumienia wejściowego.
* Twoje Źródło "rozpylania" na wielu partycjach, które później muszą zostać scalone.

Ponowne partycjonowanie lub reshuffling jest wymagane podczas przetwarzania danych w strumieniu, który nie jest podzielonej na fragmenty zgodnie z naturalnym schematem wejściowym, takim jak **PartitionID** dla Event Hubs. Po ponownym partycjonowaniu każdy fragmentu może być przetwarzany niezależnie, co pozwala na liniowe skalowanie potoku przesyłania strumieniowego.

## <a name="how-to-repartition"></a>Jak ponownie podzielić na partycje

Aby ponownie podzielić na partycje, użyj słowa kluczowego **do** po instrukcji **Partition by** w zapytaniu. Poniższy przykład dzieli dane według elementu **DeviceID** na liczbę partycji 10. Mieszanie elementu **DeviceID** służy do określenia, która partycja akceptuje Podstrumień. Dane są opróżniane niezależnie dla każdego strumienia partycjonowanego, przy założeniu, że dane wyjściowe obsługują operacje zapisu partycjonowane i mają 10 partycji.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Poniższe przykładowe zapytanie łączy dwa strumienie danych z podziałem na partycje. W przypadku sprzęgania dwóch strumieni danych z podziałem na partycje, strumienie muszą mieć ten sam klucz partycji i liczbę. Wynik jest strumieniem, który ma ten sam schemat partycji.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Schemat danych wyjściowych powinien być zgodny z kluczem schematu strumienia i liczbą, tak aby każdy Podstrumień mógł zostać opróżniony niezależnie. Strumień można także scalić i ponownie podzielić na partycje przez inny schemat przed użyciem operacji opróżniania, ale należy unikać tej metody, ponieważ dodaje do ogólnego opóźnienia przetwarzania i zwiększa wykorzystanie zasobów.

## <a name="streaming-units-for-repartitions"></a>Jednostki przesyłania strumieniowego na potrzeby ponownego partycjonowania

Eksperymentuj i Obserwuj użycie zasobów zadania, aby określić dokładną liczbę potrzebnych partycji. Liczba [jednostek przesyłania strumieniowego (Su)](stream-analytics-streaming-unit-consumption.md) musi zostać skorygowana zgodnie z zasobami fizycznymi wymaganymi dla każdej partycji. Ogólnie rzecz biorąc, dla każdej partycji są dostępne sześć usług SUs. Jeśli do zadania są przypisane niewystarczające zasoby, system zastosuje repartycję tylko wtedy, gdy będzie ona czerpać korzyści z zadania.

## <a name="repartitions-for-sql-output"></a>Podział na partycje dla danych wyjściowych SQL

Gdy zadanie korzysta z usługi SQL Database na potrzeby danych wyjściowych, należy użyć jawnego ponownego partycjonowania, aby dopasować optymalną liczbę partycji. Ze względu na to, że program SQL działa najlepiej z ośmioma składnikami zapisywania, należy ponownie podzielić przepływ na osiem przed użyciem operacji opróżniania lub w innym miejscu, w którym można skorzystać z wydajności zadań. 

Jeśli istnieje więcej niż 8 partycji wejściowych, dziedziczenie schematu partycjonowania danych wejściowych może nie być odpowiednią opcją. Rozważ użycie polecenia [into](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) w zapytaniu, aby jawnie określić liczbę składników zapisywania danych wyjściowych. 

Poniższy przykład odczytuje dane wejściowe, bez względu na to, że są w naturalny sposób partycjonowane i ponownie dzieli dziesięciokrotny strumienia zgodnie z wymiarem DeviceID i opróżnia dane do danych wyjściowych. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics danych wyjściowych Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Korzystanie z przetwarzanie równoległe zapytań w Azure Stream Analytics](stream-analytics-parallelization.md)
