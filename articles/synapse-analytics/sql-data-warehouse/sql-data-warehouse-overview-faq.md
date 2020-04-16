---
title: Usługa Azure Synapse Analytics (dawniej SQL DW) Często zadawane pytania
description: W tym artykule wymieniono często zadawane pytania dotyczące usługi Azure Synapse Analytics (dawniej SQL DW) od klientów i deweloperów
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 260086d186f7e2b2d6f76c437057f5fbc8af39b5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416072"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Usługa Azure Synapse Analytics (dawniej SQL DW) Często zadawane pytania

## <a name="general"></a>Ogólne

PYTANIE: Co to jest usługa Azure Synapse?

A. Azure Synapse to nieograniczona usługa analityczna, która łączy magazynowanie danych i analizę dużych zbiorów danych. Daje swobodę do wyszukiwania danych na warunkach, przy użyciu zasobów bez użycia serwera na żądanie lub aprowizacji — na dużą skalę. Usługa Azure Synapse łączy te dwa światy z ujednoliconym doświadczeniem w zakresie pozyskiwania, przygotowywania, zarządzania i obsługi danych na potrzeby natychmiastowej analizy biznesowej i uczenia maszynowego. Aby uzyskać więcej informacji, [zobacz: Co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Co się stało z usługą Azure SQL Data Warehouse?

A. Usługa Azure Synapse jest ewoluowała w usłudze Azure SQL Data Warehouse (SQL DW). Przeszliśmy ten sam wiodący w branży magazyn danych na zupełnie nowy poziom wydajności i możliwości. Możesz kontynuować uruchamianie istniejących obciążeń magazynu danych w produkcji za pomocą platformy Azure Synapse i automatycznie korzystać z nowych możliwości, które są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Co to jest pula SQL Synapse?

A. Pula SQL synapse odnosi się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse. Aby uzyskać więcej informacji, [zobacz: Co to jest usługa Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

PYTANIE: Jak rozpocząć korzystanie z usługi Azure Synapse?

A. Możesz rozpocząć pracę z [bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/sql-data-warehouse/) lub [skontaktować się ze sprzedażą, aby uzyskać więcej informacji.](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)

PYTANIE: Co oferuje usługa Azure Synapse w zakresie bezpieczeństwa danych?

A. Usługa Azure Synapse oferuje kilka rozwiązań do ochrony danych, takich jak TDE i inspekcji. Aby uzyskać więcej informacji, zobacz [Bezpieczeństwo](sql-data-warehouse-overview-manage-security.md).

PYTANIE: Gdzie mogę dowiedzieć się, z jakimi standardami prawnymi lub biznesowymi usługa Azure Synapse jest zgodna z normą?

A. Odwiedź stronę [Zgodności firmy Microsoft, aby](https://www.microsoft.com/trustcenter/compliance/complianceofferings) uzyskać różne oferty zgodności według produktów, takich jak SOC i ISO.
Najpierw wybierz według tytułu zgodności. Następnie rozwiń platformę Azure w sekcji usługi w chmurze firmy Microsoft po prawej stronie strony, aby zobaczyć, jakie usługi są zgodne z usługą Azure Synapse.

PYTANIE: Czy mogę podłączyć program Power BI?

A. Tak! Chociaż usługa Power BI obsługuje bezpośrednie zapytanie za pomocą usługi Azure Synapse, nie jest przeznaczona dla dużej liczby użytkowników ani danych w czasie rzeczywistym. Aby jeszcze bardziej zoptymalizować wydajność usługi Power BI, należy rozważyć użycie usługi Power BI w usłudze Azure Analysis Services lub Analysis Service IaaS.

PYTANIE: Co to są limity pojemności puli synapse SQL?

A. Zobacz naszą aktualną stronę [limitów pojemności.](sql-data-warehouse-service-capacity-limits.md)

PYTANIE: Dlaczego moje skalowanie/pauza/wznawianie trwa tak długo?

A. Kilka czynników może mieć wpływ na czas operacji zarządzania obliczeń. Typowym przypadkiem dla długotrwałych operacji jest wycofywanie transakcyjne. Po zainicjowaniu operacji skalowania lub wstrzymania wszystkie sesje przychodzące są blokowane, a kwerendy są opróżniane. Aby pozostawić system w stanie stabilnym, transakcje muszą zostać wycofane przed rozpoczęciem operacji. Im większa liczba i większy rozmiar dziennika transakcji, tym dłużej operacja zostanie wstrzymana przywracając system do stanu stabilnego.

## <a name="user-support"></a>Obsługa użytkowników

PYTANIE: Mam prośbę o funkcję, gdzie mogę ją przesłać?

A. Jeśli masz prośbę o funkcję, prześlij ją na naszą stronę [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

PYTANIE: Jak mogę zrobić x?

A. Aby uzyskać pomoc dotyczącą tworzenia za pomocą usługi Azure Synapse, możesz zadawać pytania na naszej stronie [Przepełnienie stosu.](https://stackoverflow.com/questions/tagged/azure-sqldw)

PYTANIE: Jak przesłać zgłoszenie do pomocy technicznej?

A. [Bilety pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) można składać za pośrednictwem witryny Azure portal.

## <a name="sql-languagefeature-support"></a>Obsługa języka/funkcji SQL

PYTANIE: Jakie typy danych są obsługiwane?

A. Zobacz [typy danych](sql-data-warehouse-tables-data-types.md).

PYTANIE: Jakie funkcje tabeli obsługujesz?

A. Wiele funkcji są obsługiwane. Funkcje, które nie są obsługiwane, można znaleźć w [nieobsługiconych funkcjach tabeli](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Oprzyrządowanie i administracja

PYTANIE: Czy puli Synapse SQL obsługuje interfejsy API REST?

A. Tak. Większość funkcji REST, które mogą być używane z bazy danych SQL jest również dostępna z puli Synapse SQL. Informacje o interfejsie API można znaleźć na stronach dokumentacji REST lub [bazach danych](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="loading"></a>Ładowania

PYTANIE: Jakie sterowniki klienta obsługujesz?

A. Obsługę sterowników puli Synapse SQL można znaleźć na stronie [Parametry połączenia](../sql/connection-strings.md)

Pyt.: Jakie formaty plików są obsługiwane przez PolyBase?

O: Ork, RC, Parkiet i tekst z płaskim rozdzielanym

Pyt.: Z jakimi źródłami danych można się połączyć przy użyciu Bazy PolyBase?

Odp.: [Usługa Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) i obiekty [blob usługi Azure Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Pyt.: Czy możliwe jest przesunięcia w dół obliczeń podczas łączenia się z obiektami blob usługi Azure Storage lub ADLS?

Odp.: Nie, PolyBase współdziała tylko ze składnikami magazynu.

P: Czy mogę połączyć się z HDI?

O: HDI może używać ADLS lub WASB jako warstwy HDFS. Jeśli masz jedną z warstw HDFS, możesz załadować te dane do puli SQL Synapse. Nie można jednak wygenerować obliczeń wypychania w dół do wystąpienia HDI.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Synapse jako całości, zobacz naszą stronę [przegląd.](sql-data-warehouse-overview-faq.md)
