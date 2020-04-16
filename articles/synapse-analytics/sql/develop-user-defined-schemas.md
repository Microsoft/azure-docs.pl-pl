---
title: Schematy zdefiniowane przez użytkownika w języku Synapse SQL
description: W poniższych sekcjach znajdziesz różne wskazówki dotyczące używania schematów zdefiniowanych przez użytkownika T-SQL do opracowywania rozwiązań z możliwością Synapse SQL usługi Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428708"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Schematy zdefiniowane przez użytkownika w języku Synapse SQL

W poniższych sekcjach znajdziesz różne wskazówki dotyczące używania schematów zdefiniowanych przez użytkownika T-SQL do opracowywania rozwiązań w ramach synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Schematy dla granic aplikacji

Tradycyjna architektura analizy często używa oddzielnych baz danych do tworzenia granic aplikacji na podstawie obciążenia, domeny lub zabezpieczeń. Na przykład tradycyjna infrastruktura analizy programu SQL Server może zawierać tymczasową bazę danych, bazę danych analizy i bazy danych składnicy danych. W tej topologii każda baza danych działa jako obwiednia obciążenia i zabezpieczeń w architekturze.

Zamiast tego Synapse SQL uruchamia całe obciążenie analizy w jednej bazie danych. Sprzężenia między bazami danych nie są dozwolone. Synapse SQL oczekuje, że wszystkie tabele używane przez magazyn mają być przechowywane w jednej bazie danych.

> [!NOTE]
> Pule SQL nie obsługują zapytań między bazami danych dowolnego rodzaju. W związku z tym implementacje analityczne, które wykorzystują ten wzorzec, będą musiały zostać zmienione. SQL na żądanie (wersja zapoznawcza) obsługuje kwerendy między bazami danych.

## <a name="user-defined-schema-recommendations"></a>Zalecenia dotyczące schematów zdefiniowane przez użytkownika

Uwzględnione są zalecenia dotyczące konsolidacji obciążeń, zabezpieczeń, domeny i granic funkcjonalnych przy użyciu schematów zdefiniowanych przez użytkownika:

- Użyj jednej bazy danych, aby uruchomić całe obciążenie analityczne.
- Skonsoliduj istniejące środowisko analityczne, aby użyć jednej bazy danych.
- Wykorzystaj **schematy zdefiniowane przez użytkownika,** aby zapewnić granicę wcześniej zaimplementowane przy użyciu baz danych.

Jeśli schematy zdefiniowane przez użytkownika nie były używane wcześniej, masz czystą planszę. Użyj starej nazwy bazy danych jako podstawy dla schematów zdefiniowanych przez użytkownika w bazie danych Synapse SQL.

Jeśli schematy zostały już użyte, masz kilka opcji:

- Usuwanie nazw starszych schematów i rozpoczynanie od nowa
- Zachowaj starsze nazwy schematów przez wstępnie oczekujące na nazwę starszego schematu do nazwy tabeli
- Zachowaj nazwy schematu starszej, implementując widoki nad tabelą w dodatkowym schemacie, który ponownie tworzy starą strukturę schematu.

> [!NOTE]
> Przy pierwszej kontroli opcja 3 może wydawać się najbardziej atrakcyjnym wyborem. Widoki są odczytywane tylko w synapse SQL. Wszelkie dane lub modyfikacji tabeli należy wykonać względem tabeli bazowej. Opcja 3 wprowadza również warstwę widoków do systemu. Warto nadać tej dodatkowej myśli, jeśli już używasz widoków w architekturze.
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

Zachowaj nazwy schematów starszych przez wstępnie oczekujące je do nazwy tabeli. Użyj schematów dla granicy obciążenia.

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
> Wszelkie zmiany w strategii schematu wymaga przeglądu modelu zabezpieczeń dla bazy danych. W wielu przypadkach można uprościć model zabezpieczeń, przypisując uprawnienia na poziomie schematu.

Jeśli wymagane są bardziej szczegółowe uprawnienia, można użyć ról bazy danych. Aby uzyskać więcej informacji na temat ról bazy danych, zobacz zarządzanie [rolami bazy danych i użytkownikami](../../analysis-services/analysis-services-database-users.md) artykułu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [Omówienie rozwoju języka SQL Synapse](develop-overview.md).
