---
title: Funkcje daty i godziny w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcjach systemu SQL Data i godzina w Azure Cosmos DB do wykonywania operacji typu DateTime i timestamp.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8b6e932f4cd5f4119d93f3fed7db524f65cbc1f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338955"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funkcje daty i godziny (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Funkcje daty i godziny umożliwiają wykonywanie operacji DateTime i timestamp w Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funkcje do uzyskiwania daty i godziny

Poniższe funkcje skalarne umożliwiają uzyskanie bieżącej daty i godziny UTC w trzech formach: ciągu, który jest zgodny z formatem ISO 8601, liczbową sygnaturą czasową, której wartość jest liczba milisekund, które upłynęły od epoki systemu UNIX lub liczbowych taktów, których wartość jest liczbą 100 nanosekund, które upłynęły od epoki systemu UNIX :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funkcje do pracy z wartościami DateTime

Poniższe funkcje umożliwiają łatwe manipulowanie wartościami typu DateTime, timestamp i Tick:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
