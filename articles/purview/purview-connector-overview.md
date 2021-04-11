---
title: Omówienie łącznika kontrolą
description: W tym artykule opisano różne magazyny danych i funkcje obsługiwane w programie kontrolą
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695774"
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
|baza danych|[Oracle DB](register-scan-oracle-source.md)|Tak| Tak| Nie| Nie| Nie| Tak|
||[SQL Server](register-scan-on-premises-sql-server.md)|Tak| Tak| Nie| Tak| Tak| Tak|
||[Teradata](register-scan-teradata-source.md)|Tak| Tak| Nie| Nie| Nie| Tak|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Tak| Tak| Nie| Nie| Nie| Tak|
|Usługi i aplikacje|[SAP ECC](register-scan-sapecc-source.md)|Tak| Tak| Nie| Tak| Tak| Tak|
||[S4HANA SAP](register-scan-saps4hana-source.md)|Tak| Tak| Nie| Tak| Tak| Tak|

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie i skanowanie źródła magazynu obiektów blob platformy Azure](register-scan-azure-blob-storage-source.md)