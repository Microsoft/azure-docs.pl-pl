---
title: Funkcje daty i godziny w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcjach systemu SQL Data i godzina w Azure Cosmos DB do wykonywania operacji typu DateTime i timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224955"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funkcje daty i godziny (Azure Cosmos DB)

Funkcje daty i godziny umożliwiają wykonywanie operacji DateTime i timestamp w Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funkcje do uzyskiwania daty i godziny

Poniższe funkcje skalarne umożliwiają uzyskanie aktualnej daty i godziny UTC w dwóch formach: ciąg, który jest zgodny z formatem ISO 8601 lub liczbową sygnaturą czasową, której wartość to epoka systemu UNIX w milisekundach:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funkcje do pracy z wartościami DateTime

Poniższe funkcje umożliwiają łatwe manipulowanie wartościami typu DateTime:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
