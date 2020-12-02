---
title: Dedykowana Pula SQL (dawniej SQL DW) — często zadawane pytania
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics od klientów i deweloperów.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460521"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Dedykowana Pula SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics — często zadawane pytania

## <a name="general"></a>Ogólne

PYTANIE: Co to jest usługa Azure Synapse?

A. Azure Synapse to Usługa analityczna, która łączy magazyn danych i analizy danych Big Data. Usługa Azure Synapse udostępnia te dwa światy wraz z ujednoliconym doświadczeniem w zakresie pozyskiwania, przygotowywania i zarządzania danymi oraz do potrzeb związanych z analizą biznesowej i uczeniem maszynowym. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Co się stało z Azure SQL Data Warehouse?

A. Usługa Azure Synapse jest rozwijająca się Azure SQL Data Warehouse. Ten sam wiodący w branży magazyn danych jest poświęcony na cały nowy poziom wydajności i możliwości. Możesz nadal uruchamiać istniejące obciążenia magazynu danych w środowisku produkcyjnym za pomocą dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Co to jest dedykowana Pula SQL (wcześniej SQL DW) w usłudze Azure Synapse Analytics?

A. Dedykowana Pula SQL (wcześniej SQL DW) odnosi się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Jak mogę rozpocząć pracę z usługą Azure Synapse?

A. Aby uzyskać więcej informacji, możesz zacząć korzystać z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/sql-data-warehouse/) lub [skontaktować się z działem sprzedaży](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

PYTANIE: Co to jest usługa Azure Synapse w celu zapewnienia bezpieczeństwa danych?

A. Usługa Azure Synapse oferuje kilka rozwiązań do ochrony danych, takich jak TDE i inspekcja. Aby uzyskać więcej informacji, zobacz [zabezpieczenia](sql-data-warehouse-overview-manage-security.md).

PYTANIE: Gdzie można dowiedzieć się, jakie standardy prawne i biznesowe są zgodne z usługą Azure Synapse?

A. Odwiedź stronę [zgodności firmy Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) , aby uzyskać różne oferty zgodności według produktów, takich jak SOC i ISO. Najpierw wybierz pozycję według tytułu zgodności. Następnie rozwiń pozycję Azure w sekcji usługi Cloud Services w zasięgu firmy Microsoft znajdującej się po prawej stronie, aby zobaczyć, jakie usługi są zgodne z platformą Azure Synapse.

PYTANIE: Czy mogę połączyć Power BI?

A. Tak! Mimo że Power BI obsługuje bezpośrednie zapytania za pomocą usługi Azure Synapse, nie jest ona przeznaczona dla dużej liczby użytkowników ani danych w czasie rzeczywistym. Aby zwiększyć wydajność Power BI, należy rozważyć użycie Power BI na platformie Azure Analysis Services lub Analysis Service IaaS.

PYTANIE: Co to są dedykowane limity pojemności puli SQL (dawniej SQL DW)?

A. Zobacz nasze bieżące [limity pojemności](sql-data-warehouse-service-capacity-limits.md) strony.

PYTANIE: Dlaczego moje skalowanie/wstrzymywanie/wznawianie trwa tak długo?

A. Kilka czynników może mieć wpływ na czas operacji zarządzania obliczeniami. Typowym przypadkiem długotrwałych operacji jest wycofywanie transakcyjne. Po zainicjowaniu operacji skalowania lub wstrzymania wszystkie sesje przychodzące są blokowane, a kwerendy są opróżniane. Aby pozostawić system w stanie stabilnym, należy ponownie wykonać transakcje przed rozpoczęciem operacji. Im większa liczba i większy rozmiar dziennika transakcji, tym dłużej operacja zostanie wstrzymana, przywracając system do stanu stabilnego.

## <a name="user-support"></a>Obsługa użytkowników

PYTANIE: Mam wniosek o funkcję, gdzie go przesłać?

A. Jeśli masz żądanie dotyczące funkcji, prześlij je na naszej stronie usługi [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

PYTANIE: Jak mogę zrobić x?

A. Aby uzyskać pomoc dotyczącą tworzenia aplikacji za pomocą usługi Azure Synapse, możesz zadawać pytania na naszej stronie [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) .

PYTANIE: Jak mogę przesłać bilet pomocy technicznej?

A. [Bilety pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) mogą być zgłaszane przez Azure Portal.

## <a name="sql-languagefeature-support"></a>Obsługa języka/funkcji SQL

PYTANIE: Jakie typy danych są obsługiwane?

A. Zobacz  [typy danych](sql-data-warehouse-tables-data-types.md).

PYTANIE: Jakie funkcje tabel są obsługiwane?

A. Obsługiwane są wiele funkcji. Funkcje, które nie są obsługiwane, można znaleźć w [nieobsługiwanych funkcjach tabel](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Narzędzia i administracja

PYTANIE: Czy dedykowana Pula SQL (wcześniej SQL DW) obsługuje interfejsy API REST?

A. Tak. Większość funkcji REST, które mogą być używane z SQL Database jest również dostępna z dedykowaną pulą SQL (dawniej SQL DW). Informacje o interfejsie API można znaleźć w ramach stron lub [baz danych](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)Rest.

## <a name="loading"></a>Ładowanie

PYTANIE: Jakie sterowniki klientów są obsługiwane?

A. Obsługę sterowników dedykowanej puli SQL (dawniej SQL DW) można znaleźć na stronie [Parametry połączenia](sql-data-warehouse-connection-strings.md) .

P: Jakie formaty plików są obsługiwane przez bazę wielobazową?

Odp.: Orc, RC, Parquet i prosty rozdzielony tekst

P: jakie źródła danych można połączyć z użyciem bazy

Odp.: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) i [obiekty blob usługi Azure Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: Czy możliwe jest przekazywanie obliczeń podczas łączenia z obiektami BLOB usługi Azure Storage lub ADLS?

Odp.: nie, baza nie działa tylko z składnikami magazynu.

P: Czy można nawiązać połączenie z usługą HDI?

Odp.: HDI może użyć ADLS lub WASB jako warstwy systemu plików HDFS. Jeśli masz albo jako warstwę systemu plików HDFS, możesz załadować te dane do dedykowanej puli SQL (dawniej SQL DW). Nie można jednak generować obliczeń przekazywania do wystąpienia HDI.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse, zobacz naszą stronę z [omówieniem](sql-data-warehouse-overview-what-is.md) .
