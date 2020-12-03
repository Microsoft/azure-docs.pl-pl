---
title: Omówienie łącznika kontrolą
description: W tym artykule opisano różne magazyny danych i funkcje obsługiwane w programie kontrolą
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555141"
---
# <a name="supported-data-stores"></a>Obsługiwane magazyny danych

Program kontrolą obsługuje następujące magazyny danych. Kliknij każdy magazyn danych, aby poznać obsługiwane możliwości i odpowiednie konfiguracje w szczegółach.

## <a name="purview-data-sources"></a>Kontrolą źródła danych

|**Kategoria**|  **Magazyn danych**  |**Wyodrębnianie metadanych**|**Pełne skanowanie**|**Skanowanie przyrostowe**|**Skanowanie w zakresie**|**Klasyfikacja**|**Pochodzenie**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Tak| Tak| Tak| Tak| Tak| Tak|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Tak| Tak| Tak| Tak| Tak| Tak|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Tak| Tak| Tak| Tak| Tak| Tak|
||[Usługa Azure Data Lake Storage 1. generacji](register-scan-adls-gen1.md)|Tak| Tak| Tak| Tak| Tak| Tak|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Tak| Tak| Tak| Tak| Tak| Tak|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Tak| Tak| Nie| Tak| Tak| Tak|
||[Azure SQL Database wystąpienie zarządzane](register-scan-azure-sql-database-managed-instance.md)|Tak| Tak| Nie| Tak| Tak| Tak|
||[Azure Synapse Analytics (dawniej SQL DW)](register-scan-azure-synapse-analytics.md)|Tak| Tak| Nie| Tak| Tak| Tak|
|baza danych|[SQL Server](register-scan-on-premises-sql-server.md)|Tak| Tak| Nie| Tak| Tak| Tak|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Tak| Tak| Nie| Nie| Nie| Tak|

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie i skanowanie źródła magazynu obiektów blob platformy Azure](register-scan-azure-blob-storage-source.md)