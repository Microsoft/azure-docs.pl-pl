---
title: Używanie schematów zdefiniowanych przez użytkownika
description: Wskazówki dotyczące korzystania ze schematów zdefiniowanych przez użytkownika w języku T-SQL do tworzenia rozwiązań dla dedykowanych pul SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460447"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Schematy zdefiniowane przez użytkownika dla dedykowanych pul SQL w usłudze Azure Synapse Analytics
Ten artykuł koncentruje się na dostarczaniu kilku porad dotyczących korzystania ze schematów zdefiniowanych przez użytkownika w języku T-SQL do tworzenia rozwiązań w dedykowanej puli SQL.

## <a name="schemas-for-application-boundaries"></a>Schematy granic aplikacji

Tradycyjne magazyny danych często używają osobnych baz danych do tworzenia granic aplikacji na podstawie obciążenia, domeny lub zabezpieczeń. 

Przykładowo uproszczony magazyn danych SQL Server może obejmować tymczasową bazę danych, bazę danych i dane składni danych. W tej topologii każda baza danych działa jako granica obciążenia i zabezpieczeń w architekturze.

Z kolei dedykowana Pula SQL uruchamia całe obciążenie magazynu danych w jednej bazie danych. Sprzężenia między bazami danych nie są dozwolone. Dedykowana Pula SQL oczekuje, że wszystkie tabele używane przez magazyn są przechowywane w jednej bazie danych.

> [!NOTE]
> Pula SQL nie obsługuje zapytań między bazami danych dowolnego rodzaju. W związku z tym należy skorygować implementacje magazynu danych korzystające z tego wzorca.
> 
> 

## <a name="recommendations"></a>Zalecenia
Poniżej znajdują się zalecenia dotyczące konsolidowania obciążeń, zabezpieczeń, domeny i granic funkcjonalnych przy użyciu schematów zdefiniowanych przez użytkownika:

- Korzystaj z jednej bazy danych w dedykowanej puli SQL do uruchamiania całego obciążenia magazynu danych.
- Konsoliduj istniejące środowisko magazynu danych, aby korzystać z jednej dedykowanej bazy danych puli SQL.
- Korzystaj ze **schematów zdefiniowanych przez użytkownika** , aby zapewnić granicę zaimplementowaną wcześniej przy użyciu baz danych.

Jeśli schemat zdefiniowany przez użytkownika nie był wcześniej używany, to masz czysty. Użyj starej nazwy bazy danych jako podstawy dla schematów zdefiniowanych przez użytkownika w dedykowanej bazie danych puli SQL.

Jeśli schematy zostały już użyte, możesz skorzystać z kilku opcji:

- Usuń starsze nazwy schematów i zacznij od nowa.
- Zachowaj starsze nazwy schematów, przede wszystkim do nazwy tabeli w starszej wersji.
- Zachowaj starsze nazwy schematów, implementując widoki w tabeli w dodatkowym schemacie, aby ponownie utworzyć starą strukturę schematu.

> [!NOTE]
> Przy pierwszej inspekcji Opcja 3 może wyglądać jak najbardziej atrakcyjny opcję. Devil jest jednak szczegółowy. Widoki są odczytywane tylko w dedykowanej puli SQL. Wszelkie modyfikacje danych lub tabel należy wykonać w odniesieniu do tabeli podstawowej. Opcja 3 wprowadza również warstwę widoków w systemie. Jeśli używasz już widoków w danej architekturze, możesz zadawać to kilka dodatkowych myśli.
> 
> 

### <a name="examples"></a>Przykłady:
Implementowanie schematów zdefiniowanych przez użytkownika na podstawie nazw baz danych:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachowaj starsze nazwy schematów przed zaczekaniem ich na nazwę tabeli. Użyj schematów dla granicy obciążenia:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachowaj starsze nazwy schematów przy użyciu widoków:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Wszelkie zmiany strategii schematu wymagają przeglądu modelu zabezpieczeń bazy danych. W wielu przypadkach może być możliwe uproszczenie modelu zabezpieczeń przez przypisanie uprawnień na poziomie schematu. Jeśli wymagane są bardziej szczegółowe uprawnienia, można użyć ról bazy danych.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).

