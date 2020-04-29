---
title: Najlepsze rozwiązania dotyczące usług SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas pracy z usługą SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429072"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące usług SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule znajdziesz zbiór najlepszych rozwiązań dotyczących korzystania z programu SQL na żądanie (wersja zapoznawcza). SQL na żądanie jest dodatkowym zasobem w usłudze Azure Synapse Analytics.

## <a name="general-considerations"></a>Zagadnienia ogólne

Usługa SQL na żądanie umożliwia wykonywanie zapytań dotyczących plików na kontach usługi Azure Storage. Nie ma ona lokalnego magazynu ani możliwości pozyskiwania. W związku z tym wszystkie pliki, których celem jest zapytanie, są zewnętrzne na żądanie SQL. Wszystkie elementy związane z odczytem plików z magazynu mogą mieć wpływ na wydajność zapytań.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Lokalizowanie konta usługi Azure Storage i SQL na żądanie

Aby zminimalizować opóźnienie, umieść konto usługi Azure Storage i punkt końcowy na żądanie SQL. Konta magazynu i punkty końcowe inicjowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie.

Aby uzyskać optymalną wydajność, Jeśli uzyskujesz dostęp do innych kont magazynu za pomocą programu SQL na żądanie, upewnij się, że znajdują się one w tym samym regionie. Jeśli nie znajdują się w tym samym regionie, nastąpi zwiększone opóźnienie transferu sieciowego danych między regionami zdalnymi i punktami końcowymi.

## <a name="azure-storage-throttling"></a>Ograniczanie usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Ograniczanie magazynu odbywa się, gdy połączone operacje we/wy są generowane przez aplikacje, usługi i obciążenie na żądanie SQL, przekraczają limity konta magazynu. W efekcie wystąpi znaczący negatywny wpływ na wydajność zapytań.

Po wykryciu ograniczenia przepustowości SQL na żądanie ma wbudowaną obsługę tego scenariusza. Program SQL na żądanie będzie przesyłał żądania do magazynu w wolniejszym tempie, dopóki ograniczanie zostanie rozwiązane.

> [!TIP]
> W celu zapewnienia optymalnego wykonywania zapytań nie należy naciskać konta magazynu z innymi obciążeniami podczas wykonywania zapytania.

## <a name="prepare-files-for-querying"></a>Przygotuj pliki do zapytania

Jeśli to możliwe, można przygotować pliki w celu uzyskania lepszej wydajności:

- Convert CSV do Parquet-Parquet ma format kolumnowy. Ponieważ jest skompresowany, jego rozmiary plików są mniejsze niż pliki CSV z tymi samymi danymi. Do odczytu na żądanie SQL wymagane są mniej czasu i żądania pamięci masowej.
- Jeśli zapytanie odwołuje się do pojedynczego dużego pliku, można je podzielić na kilka mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV poniżej 10 GB.
- Lepiej jest mieć pliki o równym rozmiarze dla jednej ścieżki OPENROWSET lub lokalizacji tabeli zewnętrznej.
- Podziel dane przez przechowywanie partycji w różnych folderach lub nazwach plików — zaznacz [opcję Użyj funkcji filename i FilePath, aby określić docelowe partycje](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Używanie funkcji FileInfo i FilePath do określonych partycji

Dane często są zorganizowane w partycjach. Można wydać instrukcję SQL na żądanie, aby wykonywać zapytania dotyczące określonych folderów i plików. Ta funkcja zmniejszy liczbę plików i ilość danych, które zapytanie musi odczytać i przetworzyć. Dodatkową premią jest osiągnięcie lepszej wydajności.

Aby uzyskać więcej informacji, zapoznaj się z funkcjami [filename](develop-storage-files-overview.md#filename-function) i [FilePath](develop-storage-files-overview.md#filepath-function) i przykładami dotyczącymi [zapytań określonych plików](query-specific-files.md).

Jeśli przechowywane dane nie są podzielone na partycje, należy rozważyć ich partycjonowanie, aby można było używać tych funkcji do optymalizowania zapytań przeznaczonych dla tych plików. Podczas [wykonywania zapytania dotyczącego partycjonowanych tabel platformy Spark](develop-storage-files-spark-tables.md) z poziomu bazy danych SQL na żądanie zapytanie będzie automatycznie kierować tylko te pliki.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Korzystanie z CETAS w celu zwiększenia wydajności zapytań i sprzężeń

[CETAS](develop-tables-cetas.md) to jedna z najważniejszych funkcji dostępnych w programie SQL na żądanie. CETAS to równoległa operacja, która tworzy metadane tabeli zewnętrznej i eksportuje wyniki zapytania SELECT do zestawu plików na koncie magazynu.

Można użyć CETAS do przechowywania często używanych części zapytań, takich jak sprzężone tabele odwołań, do nowego zestawu plików. Następnie można przyłączyć się do tej pojedynczej tabeli zewnętrznej zamiast powtarzających się wspólnych sprzężeń w wielu zapytaniach.

Ponieważ CETAS generuje pliki Parquet, statystyki zostaną automatycznie utworzone, gdy pierwsze zapytanie odwołuje się do tej tabeli zewnętrznej, co spowodowało zwiększenie wydajności.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [rozwiązywania problemów](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby uzyskać typowe problemy i rozwiązania. Jeśli pracujesz z pulą SQL, a nie SQL na żądanie, zobacz [najlepsze rozwiązania dotyczące puli SQL](best-practices-sql-pool.md) , aby uzyskać szczegółowe wskazówki.
