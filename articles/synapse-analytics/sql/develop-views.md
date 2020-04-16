---
title: Widoki T-SQL przy użyciu synapsy SQL
description: Porady dotyczące korzystania z widoków T-SQL i tworzenia rozwiązań za pomocą synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428656"
---
# <a name="t-sql-views-using-synapse-sql"></a>Widoki T-SQL przy użyciu synapsy SQL
W tym artykule znajdziesz wskazówki dotyczące korzystania z widoków T-SQL i opracowywania rozwiązań za pomocą synapse SQL. 

## <a name="why-use-views"></a>Dlaczego warto korzystać z widoków?

Widoki mogą być używane na wiele różnych sposobów, aby poprawić jakość rozwiązania.  W tym artykule przedstawiono kilka przykładów, jak wzbogacić rozwiązanie o widoki i zawiera ograniczenia, które należy wziąć pod uwagę.

### <a name="sql-pool---create-view"></a>Pula SQL — tworzenie widoku

> [!NOTE]
> **Pula SQL:** Składnia create view nie została omówiona w tym artykule. Aby uzyskać więcej informacji, zobacz tworzenie dokumentacji [WIDOKU.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="sql-on-demand-preview---create-view"></a>SQL na żądanie (wersja zapoznawcza) - tworzenie widoku

> [!NOTE]
> **SQL na żądanie**: Składnia create view nie jest omówiona w tym artykule. Aby uzyskać więcej informacji, zobacz tworzenie dokumentacji [WIDOKU.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="architectural-abstraction"></a>Abstrakcja architektoniczna

Typowy wzorzec aplikacji jest ponowne tworzenie tabel przy użyciu [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), po którym następuje wzorzec zmiany nazwy obiektu podczas ładowania danych.

Poniższy przykład dodaje nowe rekordy daty do wymiaru daty. Zwróć uwagę, jak najpierw tworzona jest nowa tabela, DimDate_New, a następnie zmieniona nazwa, aby zastąpić oryginalną wersję tabeli.

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

Należy pamiętać, że takie podejście może spowodować tabele pojawiające się i znikają z widoku użytkownika i monituje "tabela nie istnieje" komunikaty o błędach. Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji, podczas gdy nazwy obiektów leżących pod spodem są zmieniane.

Zapewniając dostęp do danych za pośrednictwem widoków, użytkownicy nie potrzebują widoczności w tabelach źródłowych. Oprócz spójnego środowiska użytkownika ta warstwa zapewnia, że projektanci analityki mogą ewoluować model danych. Możliwość ewoluowania podstawowych tabel oznacza, że projektanci mogą używać CTAS, aby zmaksymalizować wydajność podczas procesu ładowania danych.

## <a name="performance-optimization"></a>Optymalizacja wydajności

Widoki mogą być również używane do wymuszania sprzężeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów łączenia, aby zminimalizować przenoszenie danych.

Wymuszanie określonej kwerendy lub dołączanie wskazówki jest kolejną zaletą przy użyciu widoków T-SQL. W związku z tym funkcja widoków zapewnia, że sprzężenia są zawsze wykonywane w optymalny sposób. Unikniesz konieczności zapamiętywania przez użytkowników poprawnej konstrukcji sprzężenia.

## <a name="limitations"></a>Ograniczenia

Widoki w synapse SQL są przechowywane tylko jako metadane. W związku z tym następujące opcje nie są dostępne:

* Nie ma opcji wiązania schematu
* Nie można aktualizować tabel bazowych za pomocą widoku
* Widoki nie mogą być tworzone za ich doami zaaukańctwo
* Nie ma wsparcia dla podpowiedzi EXPAND / NOEXPAND
* W programie Synapse SQL nie ma widoków indeksowanych

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [Omówienie rozwoju języka SQL Synapse](develop-overview.md).



