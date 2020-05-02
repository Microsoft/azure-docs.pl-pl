---
title: Co to jest Azure Synapse Analytics (dawniej SQL DW)?
description: Azure Synapse Analytics (dawniej SQL DW) to nieograniczona Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 27e6c7beb8c88a29b90b30e0c77e35e493f2480a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690908"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co to jest Azure Synapse Analytics (dawniej SQL DW)?

> [!NOTE] 
> Wypróbuj najnowsze funkcje usługi Azure Synapse, takie jak obszary robocze, Spark, SQL na żądanie i zintegrowane środowisko Synapse Studio, [żądając dostępu do usługi Azure Synapse (obszary robocze w wersji zapoznawczej)](https://aka.ms/synapsepreview). 
>
>Zapoznaj się z [dokumentacją platformy Azure Synapse (wersja zapoznawcza)](../overview-what-is.md).

Azure Synapse to Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data. Zapewnia swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z bezserwerowych zasobów na żądanie lub aprowizowanych zasobów — w dużej skali. Usługa Azure Synapse udostępnia te dwa światy wraz z ujednoliconym doświadczeniem do pozyskiwania, przygotowywania, zarządzania i obsługi danych w celu natychmiastowej analizy biznesowej i uczenia maszynowego.

Usługa Azure Synapse ma cztery składniki:

- Synapse SQL: Pełna analiza oparta na języku T-SQL — ogólnie dostępna
  - Pula SQL (płatność za jednostek dwu z obsługą administracyjną)
  - SQL na żądanie (płatność za 1 TB przetworzonych) — (wersja zapoznawcza)
- Spark: głębokie zintegrowane Apache Spark (wersja zapoznawcza)
- Potoki Synapse: integracja danych hybrydowych (wersja zapoznawcza)
- Studio: ujednolicone środowisko użytkownika.  (Wersja zapoznawcza)



## <a name="synapse-sql-pool-in-azure-synapse"></a>Synapse pulę SQL na platformie Azure Synapse

Synapse Pula SQL odwołuje się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse.

Pula SQL reprezentuje kolekcję zasobów analitycznych, które są obsługiwane podczas korzystania z programu Synapse SQL. Rozmiar puli SQL jest określany przez jednostki magazynowania danych (jednostek dwu).

Zaimportuj dane Big Data za [pomocą prostych zapytań](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL, a następnie użyj możliwości MPP, aby uruchomić analizę o wysokiej wydajności. Po zintegrowaniu i przeanalizowaniu Pula SQL Synapse stanie się jedną wersją prawdy, na którą firma będzie mogła na szybszy i bardziej niezawodny wgląd w szczegółowe dane.  

## <a name="key-component-of-a-big-data-solution"></a>Kluczowy składnik rozwiązania danych Big Data

Magazynowanie danych to kluczowy składnik oparty na chmurze, kompleksowe rozwiązanie do obsługi danych Big Data.

![Rozwiązanie magazynu danych](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z różnych źródeł. Platformy Hadoop, Spark i algorytmy uczenia maszynowego przygotowują i przystosowują dane, gdy te już znajdą się w magazynie danych big data. Gdy dane są gotowe do złożonej analizy, Synapse puli SQL używa bazy danych w celu wysyłania zapytań do magazynów z danymi Big Data. Baza kodu używa standardowych zapytań T-SQL do przenoszenia danych do tabel puli SQL Synapse.

Synapse Pool SQL przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane znajdują się w magazynie, można wykonywać analizy na wielką skalę. W porównaniu do tradycyjnych systemów bazy danych, zapytania analizy kończą się w ciągu sekund zamiast minut lub godzin zamiast dni.

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę usługi Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [Tworzenie puli SQL](create-data-warehouse-portal.md)
- [Ładowanie przykładowych danych](load-data-from-azure-blob-storage-using-polybase.md).
- Eksploruj [wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Lub zapoznaj się z innymi zasobami usługi Azure Synapse.

- Wyszukaj [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Prześlij [żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
- Wyszukaj [forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- [Forum wyszukiwania Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
