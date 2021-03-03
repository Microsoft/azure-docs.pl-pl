---
title: Widoki języka T-SQL korzystające z pul SQL
description: Porady dotyczące korzystania z widoków T-SQL i opracowywania rozwiązań z dedykowaną pulą SQL i bezserwerową pulą usług w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9f52c3fd1284ce7e55680d051c5292361067fad9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673993"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>Widoki języka T-SQL z dedykowaną pulą SQL i bezserwerową pulą SQL w usłudze Azure Synapse Analytics

W tym artykule znajdziesz porady dotyczące korzystania z widoków T-SQL i opracowywania rozwiązań z dedykowaną pulą SQL i bezserwerową pulą SQL w usłudze Azure Synapse Analytics.

## <a name="why-use-views"></a>Dlaczego warto używać widoków

Widoki mogą być używane na wiele różnych sposobów ulepszania jakości rozwiązania.  W tym artykule przedstawiono kilka przykładów sposobu wzbogacania rozwiązania o widoki i uwzględniono ograniczenia, które należy wziąć pod uwagę.

### <a name="sql-pool---create-view"></a>Pula SQL — Tworzenie widoku

> [!NOTE]
> Składnia instrukcji CREATE VIEW nie została omówiona w tym artykule. Aby uzyskać więcej informacji, zobacz dokumentację [tworzenia widoku](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true) .

## <a name="architectural-abstraction"></a>Abstrakcja architektury

Typowym wzorcem aplikacji jest ponowne tworzenie tabel przy użyciu [CREATE TABLE jako SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) (CTAs), po którym następuje zmiana nazwy obiektu wzorca podczas ładowania danych.

Poniższy przykład dodaje nowe rekordy dat do wymiaru daty. Zwróć uwagę, jak nowa tabela, DimDate_New, została najpierw utworzona, a następnie zmieniono jej nazwę, aby zastąpić oryginalną wersję tabeli.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;
```

Należy pamiętać, że takie podejście może spowodować, że tabele pojawiają się i znikają z widoku użytkownika, a monit "tabela nie istnieje". Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji podczas zmiany nazwy obiektów bazowych.

Zapewniając dostęp do danych za pomocą widoków, użytkownicy nie potrzebują wglądu w tabele źródłowe. Oprócz spójnego środowiska użytkownika Ta warstwa zapewnia, że projektanci analiz mogą rozwijać model danych. Możliwość rozwijania tabel bazowych oznacza, że projektanci mogą używać CTAS do maksymalizowania wydajności podczas procesu ładowania danych.

## <a name="performance-optimization"></a>Optymalizacja wydajności

Widoki mogą być również używane do wymuszania przełączeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów sprzężenia, aby zminimalizować przenoszenie danych.

Wymuszanie konkretnego zapytania lub wskazówki sprzężenia jest kolejną zaletą korzystania z widoków T-SQL. W związku z tym funkcje widoków zapewniają, że sprzężenia są zawsze wykonywane w optymalny sposób. Pozwoli to uniknąć konieczności zapamiętania przez użytkowników poprawnej konstrukcji sprzężeń.

## <a name="limitations"></a>Ograniczenia

Widoki w Synapse SQL są przechowywane tylko jako metadane. W związku z tym następujące opcje nie są dostępne:

* Nie istnieje opcja powiązania schematu
* Nie można zaktualizować tabel bazowych w widoku
* Nie można tworzyć widoków za pośrednictwem tabel tymczasowych
* Brak obsługi wskazówek rozwiń/NOEXPAND
* Brak indeksowanych widoków w Synapse SQL

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Synapse Development SQL — Omówienie](develop-overview.md).



