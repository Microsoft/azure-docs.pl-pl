---
title: Co to jest usługa Azure Synapse Analytics (dawniej SQL DW)?
description: Usługa Azure Synapse Analytics (dawniej SQL DW) to nieograniczona usługa analityczna, która łączy w sobie hurtownię danych w przedsiębiorstwie i analizę dużych zbiorów danych.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a9506d45350a567e3643b6edd6afc7668662f6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416029"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co to jest usługa Azure Synapse Analytics (dawniej SQL DW)?

Azure Synapse to usługa analityczna, która łączy w sobie hurtownię danych w przedsiębiorstwie i analizę dużych zbiorów danych. Zapewnia swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z bezserwerowych zasobów na żądanie lub aprowizowanych zasobów — w dużej skali. Usługa Azure Synapse łączy te dwa światy z ujednoliconym doświadczeniem w zakresie pozyskiwania, przygotowywania, zarządzania i obsługi danych na potrzeby natychmiastowej analizy biznesowej i uczenia maszynowego.

Usługa Azure Synapse ma cztery składniki:

- Synapse SQL: Kompletna analiza oparta na T-SQL — ogólnie dostępna
  - Pula SQL (płatność za dwu aprowizowana)
  - SQL na żądanie (płatność za TB przetwarzane) - (Wersja zapoznawcza)
- Iskra: Głęboko zintegrowana platforma Apache Spark (wersja zapoznawcza)
- Potoki Synapse: hybrydowa integracja danych (wersja zapoznawcza)
- Studio: Ujednolicone środowisko użytkownika.  (Wersja zapoznawcza)

> [!NOTE]
> Aby uzyskać dostęp do funkcji w wersji zapoznawczej platformy Azure Synapse, poproś o dostęp [tutaj](https://aka.ms/synapsepreview). Firma Microsoft będzie klasyfikować wszystkie żądania i odpowiadać tak szybko, jak to możliwe.
>
> Wyświetl [dokumentację podglądu usługi Azure Synapse](../overview-what-is.md).

## <a name="synapse-sql-pool-in-azure-synapse"></a>Pula SQL synapse w usłudze Azure Synapse

Pula SQL synapse odnosi się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse.

Pula SQL reprezentuje zbiór zasobów analitycznych, które są aprowizacji podczas korzystania z Synapse SQL. Rozmiar puli SQL jest określany przez jednostki magazynowania danych (DWU).

Importuj duże zbiory danych za pomocą prostych zapytań [T-SQL PolyBase,](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a następnie korzystaj z mocy MPP do uruchamiania analizy o wysokiej wydajności. Podczas integrowania i analizowania pula SQL Synapse stanie się pojedynczą wersją prawdy, na której twoja firma może liczyć na szybsze i bardziej niezawodne spostrzeżenia.  

## <a name="key-component-of-a-big-data-solution"></a>Kluczowy składnik rozwiązania do dużych zbiorów danych

Hurtownia danych jest kluczowym składnikiem opartego na chmurze, kompleksowego rozwiązania do przetwarzania dużych zbiorów danych.

![Rozwiązanie magazynu danych](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z różnych źródeł. Platformy Hadoop, Spark i algorytmy uczenia maszynowego przygotowują i przystosowują dane, gdy te już znajdą się w magazynie danych big data. Gdy dane są gotowe do złożonej analizy, puli Synapse SQL używa PolyBase do kwerendy magazynów dużych zbiorów danych. PolyBase używa standardowych zapytań T-SQL, aby przenieść dane do tabel puli Synapse SQL.

Pula Synapse SQL przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane znajdują się w magazynie, można wykonywać analizy na wielką skalę. W porównaniu do tradycyjnych systemów bazy danych, zapytania analizy kończą się w ciągu sekund zamiast minut lub godzin zamiast dni.

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę Usługi Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [tworzenie puli SQL](create-data-warehouse-portal.md)
- [Załaduj przykładowe dane](load-data-from-azure-blob-storage-using-polybase.md).
- Przeglądaj [filmy](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Lub spójrz na niektóre z tych innych zasobów usługi Azure Synapse.

- Szukaj [blogów](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Przesyłanie [żądań funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
- Szukaj [na forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- Szukaj [na forum Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-sqldw)
