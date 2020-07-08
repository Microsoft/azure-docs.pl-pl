---
title: Korzystanie z widoków T-SQL
description: Porady dotyczące korzystania z widoków T-SQL i opracowywania rozwiązań w puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212296"
---
# <a name="views-in-synapse-sql-pool"></a>Widoki w puli SQL Synapse

Widoki mogą być używane na wiele różnych sposobów ulepszania jakości rozwiązania.

Pula SQL obsługuje widoki standardowe i materiałowe. Obie są tabelami wirtualnymi utworzonymi za pomocą SELECT Expressions i prezentowanych jako tabele logiczne.

Widoki hermetyzują złożoność wspólnych obliczeń danych i dodają warstwę abstrakcji do obliczeń zmian, aby nie trzeba było ponownie pisać zapytań.

## <a name="standard-view"></a>Widok standardowy

Widok standardowy oblicza swoje dane za każdym razem, gdy widok jest używany. Brak danych przechowywanych na dysku. Użytkownicy zazwyczaj używają widoków standardowych jako narzędzia, które ułatwiają organizowanie obiektów logicznych i zapytań w bazie danych.

Aby użyć widoku standardowego, zapytanie musi nawiązać bezpośrednie odwołanie do niego. Aby uzyskać więcej informacji, zobacz dokumentację [tworzenia widoku](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

Widoki w puli SQL są przechowywane jako metadane. W związku z tym następujące opcje nie są dostępne:

* Brak opcji powiązania schematu
* Nie można zaktualizować tabel bazowych w widoku
* Nie można tworzyć widoków za pośrednictwem tabel tymczasowych
* Nie ma obsługi wskazówek rozwiń/NOEXPAND
* Brak indeksowanych widoków w puli SQL

Widoki standardowe mogą być używane do wymuszania przełączeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów sprzężenia, aby zminimalizować przenoszenie danych.

Inną zaletą widoku może być wymuszenie konkretnej kwerendy lub dołączenia wskazówki. Użycie widoków w ten sposób gwarantuje, że sprzężenia są zawsze wykonywane w optymalny sposób, unikając potrzeb użytkowników do zapamiętania odpowiedniej konstrukcji sprzężeń.

## <a name="materialized-view"></a>Zmaterializowany widok

Widok z materiałami umożliwia wstępne obliczenie, przechowywanie i przechowywanie danych w puli SQL w taki sam sposób jak tabela. Za każdym razem, gdy jest używany widok materiałowy, nie jest wymagana żadna konieczność obliczenia.

W miarę ładowania danych do tabel bazowych, Pula SQL synchronicznie odświeża widoki z materiałami.  Optymalizator zapytań automatycznie używa wdrożonych widoków w celu zwiększenia wydajności zapytań, nawet jeśli w zapytaniu nie ma odwołań do widoków.  

Zapytania korzystające z większości widoków z materiałami są złożonymi zapytaniami (zwykle zapytania z sprzężeniami i agregacjami) w dużych tabelach, które tworzą mały zestaw wyników.  

Aby uzyskać szczegółowe informacje na temat składniowe widoku i innych wymagań, zapoznaj się z tematem [Tworzenie przykładowego widoku jako wyboru](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Aby uzyskać wskazówki dotyczące dostrajania zapytań, sprawdź [dostrajanie wydajności w widokach z materiałami](performance-tuning-materialized-views.md).

## <a name="example"></a>Przykład

Typowym wzorcem aplikacji jest ponowne tworzenie tabel przy użyciu CREATE TABLE jako SELECT (CTAS), a następnie zmiany nazwy wzorca obiektu podczas ładowania danych.  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Jednak takie podejście może spowodować, że tabele pojawiają się i znikają z widoku użytkownika wraz z wygenerowaniem komunikatów o błędach "tabela nie istnieje".

Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji podczas zmiany nazwy obiektów bazowych. Zapewniając dostęp do danych za pomocą widoków, użytkownicy nie potrzebują wglądu w tabele źródłowe.

Ta warstwa zapewnia spójne środowisko użytkownika, a jednocześnie zapewnia, że projektanci magazynu danych mogą rozwijać model danych. Możliwość rozwijania tabel bazowych oznacza, że projektanci mogą używać CTAS do maksymalizowania wydajności podczas procesu ładowania danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [programowanie puli SQL — Omówienie](sql-data-warehouse-overview-develop.md).
