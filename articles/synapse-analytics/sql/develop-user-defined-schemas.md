---
title: Schematy zdefiniowane przez użytkownika w programie Synapse SQL
description: W poniższych sekcjach znajdziesz różne porady dotyczące korzystania ze schematów zdefiniowanych przez użytkownika w języku T-SQL w celu opracowywania rozwiązań z możliwością Synapseymi w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: aebe1d995f3cb6da4663876b8d39d36a1a8b16c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030171"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Schematy zdefiniowane przez użytkownika w programie Synapse SQL

W poniższych sekcjach znajdziesz różne porady dotyczące korzystania ze schematów zdefiniowanych przez użytkownika w języku T-SQL w celu opracowywania rozwiązań w programie Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Schematy granic aplikacji

Tradycyjna architektura analityczna często używa oddzielnych baz danych do tworzenia granic aplikacji na podstawie obciążenia, domeny lub zabezpieczeń. Na przykład tradycyjna infrastruktura analityczna SQL Server może obejmować tymczasową bazę danych, bazę danych analitycznych i bazy danych składnicy. W tej topologii każda baza danych działa jako granica obciążenia i zabezpieczeń w architekturze.

Zamiast tego Synapse SQL uruchamia całe obciążenie analizy w jednej bazie danych. Sprzężenia między bazami danych nie są dozwolone. Synapse SQL oczekuje, że wszystkie tabele używane przez magazyn będą przechowywane w jednej bazie danych.

> [!NOTE]
> Pule SQL nie obsługują zapytań między bazami danych dowolnego rodzaju. W związku z tym należy skorygować implementacje analityczne korzystające z tego wzorca. SQL na żądanie (wersja zapoznawcza) obsługuje zapytania obejmujące wiele baz danych.

## <a name="user-defined-schema-recommendations"></a>Zalecenia dotyczące schematu zdefiniowanego przez użytkownika

Uwzględniono zalecenia dotyczące konsolidowania obciążeń, zabezpieczeń, domeny i granic funkcjonalnych przy użyciu schematów zdefiniowanych przez użytkownika:

- Użyj jednej bazy danych, aby uruchomić całe obciążenie analizy.
- Konsoliduj istniejące środowisko analityczne, aby korzystać z jednej bazy danych.
- Korzystaj ze **schematów zdefiniowanych przez użytkownika** , aby zapewnić granicę zaimplementowaną wcześniej przy użyciu baz danych.

Jeśli schemat zdefiniowany przez użytkownika nie był wcześniej używany, oznacza to, że masz czysty. Użyj starej nazwy bazy danych jako podstawy dla schematów zdefiniowanych przez użytkownika w bazie danych SQL Synapse.

Jeśli schematy zostały już użyte, możesz skorzystać z kilku opcji:

- Usuń starsze nazwy schematów i zacznij od nowa
- Zachowaj starsze nazwy schematów przed oczekiwaniem na nazwę starszego schematu w nazwie tabeli
- Zachowaj starsze nazwy schematów przez zaimplementowanie widoków w tabeli w dodatkowym schemacie, co spowoduje ponowne utworzenie starej struktury schematu.

> [!NOTE]
> Przy pierwszej inspekcji Opcja 3 może wyglądać jak najbardziej atrakcyjny wybór. Widoki są tylko do odczytu w języku SQL Synapse. Wszelkie modyfikacje danych lub tabel należy wykonać w odniesieniu do tabeli podstawowej. Opcja 3 wprowadza również warstwę widoków w systemie. W przypadku korzystania z widoków w architekturze warto podać inne warunki.
> 
> 

### <a name="examples"></a>Przykłady

Implementowanie schematów zdefiniowanych przez użytkownika na podstawie nazw baz danych.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachowaj starsze nazwy schematów przed ich nazwą tabeli. Użyj schematów dla granicy obciążenia.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachowaj starsze nazwy schematów przy użyciu widoków.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Wszelkie zmiany strategii schematu wymagają przeglądu modelu zabezpieczeń bazy danych. W wielu przypadkach może być możliwe uproszczenie modelu zabezpieczeń przez przypisanie uprawnień na poziomie schematu.

Jeśli wymagane są bardziej szczegółowe uprawnienia, można użyć ról bazy danych. Aby uzyskać więcej informacji na temat ról bazy danych, zobacz artykuł [Zarządzanie rolami bazy danych i użytkownikami](../../analysis-services/analysis-services-database-users.md) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Synapse Development SQL — Omówienie](develop-overview.md).
