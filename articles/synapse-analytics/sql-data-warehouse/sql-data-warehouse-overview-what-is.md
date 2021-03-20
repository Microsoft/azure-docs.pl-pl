---
title: Co to jest dedykowana Pula SQL (wcześniej SQL DW)?
description: Dedykowana Pula SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics to funkcja magazynowania danych przedsiębiorstwa w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9077ce35065b1bf45646496cc4c43d6def82d958
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685229"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Co to jest dedykowana Pula SQL (wcześniej SQL DW) w usłudze Azure Synapse Analytics?

Usługa Azure Synapse Analytics to usługa analizy, która łączy magazynowanie danych przedsiębiorstwa z analizą danych big data. Dedykowana Pula SQL (wcześniej SQL DW) odnosi się do funkcji magazynowania danych przedsiębiorstwa, które są dostępne w usłudze Azure Synapse Analytics.



![Dedykowana Pula SQL (wcześniej SQL DW) w odniesieniu do usługi Azure Synapse](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Dedykowana Pula SQL (wcześniej SQL DW) reprezentuje kolekcję zasobów analitycznych, które są obsługiwane podczas korzystania z Synapse SQL. Rozmiar dedykowanej puli SQL (dawniej SQL DW) jest określany przez jednostki magazynowania danych (jednostek dwu).

Po utworzeniu dedykowanej puli SQL można zaimportować dane Big Data za [pomocą prostych zapytań](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL, a następnie użyć możliwości aparatu zapytań rozproszonych w celu uruchomienia analizy o wysokiej wydajności. Po zintegrowaniu i przeanalizowaniu danych dedykowana Pula SQL (dawniej SQL DW) stanie się jedną wersją prawdy, na którą firma może liczyć, aby szybciej i bardziej niezawodnie analizować szczegółowe informacje.

> [!NOTE]
>Zapoznaj się z [dokumentacją usługi Azure Synapse Analytics](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Kluczowy składnik rozwiązania danych Big Data

Magazynowanie danych to kluczowy składnik oparty na chmurze, kompleksowe rozwiązanie do obsługi danych Big Data.

![Rozwiązanie magazynu danych](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z wielu różnych źródeł. Kiedy już znajdą się w magazynie danych big data, algorytmy platform Hadoop, Spark i uczenia maszynowego przygotowują i trenują dane. Gdy dane są gotowe do złożonej analizy, dedykowana Pula SQL używa bazy danych do wykonywania zapytań dotyczących magazynów Big Data. Baza kodu używa standardowych zapytań T-SQL do przenoszenia danych do tabel dedykowanej puli SQL (dawniej SQL DW).

Dedykowana Pula SQL (wcześniej SQL DW) przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane znajdują się w magazynie, można wykonywać analizy na wielką skalę. W porównaniu do tradycyjnych systemów baz danych zapytania analizy wykonywane są w ciągu kilku sekund (zamiast kilku minut) lub kilku godzin (zamiast kilku dni).

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę usługi Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [Tworzenie dedykowanej puli SQL](create-data-warehouse-portal.md)
- [Ładowanie przykładowych danych](./load-data-from-azure-blob-storage-using-copy.md).
- Eksploruj [wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Lub zapoznaj się z innymi zasobami usługi Azure Synapse.

- Wyszukaj [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Prześlij [żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
- Wyszukiwanie [w witrynie Microsoft Q&stronie pytania](/answers/topics/azure-synapse-analytics.html)
- [Forum wyszukiwania Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)