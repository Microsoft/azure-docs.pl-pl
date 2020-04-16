---
title: Najważniejsze wskazówki dotyczące sql na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas pracy z sql na żądanie (wersja zapoznawcza).
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429072"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Najważniejsze wskazówki dotyczące sql na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule znajdziesz zbiór najlepszych rozwiązań dotyczących korzystania z języka SQL na żądanie (wersja zapoznawcza). SQL na żądanie jest dodatkowym zasobem w usłudze Azure Synapse Analytics.

## <a name="general-considerations"></a>Zagadnienia ogólne

SQL na żądanie umożliwia wykonywanie zapytań o pliki na kontach magazynu platformy Azure. Nie ma możliwości lokalnej pamięci masowej lub pozyskiwania. W związku z tym wszystkie pliki, które są obiekty docelowe kwerendy są zewnętrzne do SQL na żądanie. Wszystko związane z odczytywaniem plików z magazynu może mieć wpływ na wydajność kwerendy.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Kolokowanie konta usługi Azure Storage i języka SQL na żądanie

Aby zminimalizować opóźnienia, współlokuj swoje konto magazynu platformy Azure i punkt końcowy SQL na żądanie. Konta magazynu i punkty końcowe aprowizowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie.

Aby uzyskać optymalną wydajność, jeśli uzyskujesz dostęp do innych kont magazynu z SQL na żądanie, upewnij się, że znajdują się w tym samym regionie. Jeśli nie znajdują się w tym samym regionie, zostanie zwiększone opóźnienie transferu sieciowego danych między regionami zdalnego i końcowego punktu końcowego.

## <a name="azure-storage-throttling"></a>Ograniczanie przepustowości usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Ograniczanie alokacji magazynu występuje, gdy połączone we/wy usługi We/Wy lub przepływność generowane przez aplikacje, usługi i obciążenie SQL na żądanie przekraczają limity konta magazynu. W rezultacie wystąpi znaczący negatywny wpływ na wydajność kwerendy.

Po wykryciu ograniczania przepustowości sql na żądanie ma wbudowaną obsługę tego scenariusza. SQL na żądanie będzie żądania do magazynu w wolniejszym tempie, dopóki ograniczanie przepustowości zostanie rozwiązany.

> [!TIP]
> Aby uzyskać optymalne wykonanie kwerendy, nie należy podkreślać konta magazynu z innymi obciążeniami podczas wykonywania kwerendy.

## <a name="prepare-files-for-querying"></a>Przygotowywanie plików do wykonywania zapytań

Jeśli to możliwe, można przygotować pliki do lepszej wydajności:

- Konwertuj CSV na parkiet - Parkiet jest formatem kolumnowym. Ponieważ jest skompresowany, jego rozmiary plików są mniejsze niż pliki CSV z tymi samymi danymi. SQL na żądanie będzie potrzebował mniej czasu i żądań magazynu, aby go odczytać.
- Jeśli kwerenda jest przeznaczona dla pojedynczego dużego pliku, skorzystasz z podzielenia go na wiele mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV poniżej 10 GB.
- Lepiej mieć pliki o jednakowym rozmiarze dla pojedynczej ścieżki OPENROWSET lub zewnętrznej lokalizacji tabeli.
- Partycjonowanie danych przez przechowywanie partycji do różnych folderów lub nazw plików - sprawdź [użyć funkcji nazwy pliku i ścieżki pliku, aby kierować określone partycje](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Użyj funkcji fileinfo i filepath do kierowania określonych partycji

Dane są często zorganizowane w partycje. Można poinstruować sql na żądanie do kwerendy poszczególnych folderów i plików. Ta funkcja zmniejszy liczbę plików i ilość danych, które kwerenda musi odczytać i przetworzyć. Dodatkową zaletą jest to, że osiągniesz lepszą wydajność.

Aby uzyskać więcej informacji, sprawdź funkcje [nazwy pliku](develop-storage-files-overview.md#filename-function) i [ścieżki plików](develop-storage-files-overview.md#filepath-function) oraz przykłady dotyczące [wykonywania zapytań](query-specific-files.md)o określone pliki .

Jeśli przechowywane dane nie są podzielone na partycje, należy wziąć pod uwagę partycjonowanie go, dzięki czemu można użyć tych funkcji do optymalizacji kwerend docelowych tych plików. Podczas [wykonywania zapytań o partycjonowane tabele platformy Spark](develop-storage-files-spark-tables.md) z języka SQL na żądanie, kwerenda będzie automatycznie kierowana tylko na potrzebne pliki.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Stosowanie cetasu w celu zwiększenia wydajności zapytań i sprzężenia

[CETAS](develop-tables-cetas.md) jest jedną z najważniejszych funkcji dostępnych w sql na żądanie. CETAS to operacja równoległa, która tworzy metadane tabeli zewnętrznej i eksportuje wyniki kwerendy SELECT do zestawu plików na koncie magazynu.

Za pomocą programu CETAS można przechowywać często używane części zapytań, takie jak przyłączone tabele odwołań, w nowym zestawie plików. Następnie można dołączyć do tej pojedynczej tabeli zewnętrznej zamiast powtarzać wspólne sprzężenia w wielu kwerendach.

Gdy CETAS generuje pliki parkietu, statystyki będą tworzone automatycznie, gdy pierwsze zapytanie jest przeznaczone dla tej zewnętrznej tabeli, co spowoduje poprawę wydajności.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [rozwiązywania problemów,](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aby uzyskać typowe problemy i rozwiązania. Jeśli pracujesz z puli SQL, a nie SQL na żądanie, zobacz [najlepsze rozwiązania dla puli SQL](best-practices-sql-pool.md) artykuł dla konkretnych wskazówek.
