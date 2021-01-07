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
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780216"
---
# <a name="supported-data-stores"></a>Obsługiwane magazyny danych

Program kontrolą obsługuje następujące magazyny danych. Kliknij każdy magazyn danych, aby poznać obsługiwane możliwości i odpowiednie konfiguracje w szczegółach.

## <a name="purview-data-sources"></a>Kontrolą źródła danych

|**Kategoria**|  **Magazyn danych**  |**Wyodrębnianie metadanych**|**Pełne skanowanie**|**Skanowanie przyrostowe**|**Skanowanie w zakresie**|**Klasyfikacja**|**Pochodzenie**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Usługa Azure Data Lake Storage 1. generacji](register-scan-adls-gen1.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Yes| Yes| Nie| Yes| Yes| Yes|
||[Wystąpienie zarządzane usługi Azure SQL Database](register-scan-azure-sql-database-managed-instance.md)|Yes| Yes| Nie| Yes| Yes| Yes|
||[Azure Synapse Analytics (dawniej SQL DW)](register-scan-azure-synapse-analytics.md)|Yes| Yes| Nie| Yes| Yes| Yes|
|baza danych|[SQL Server](register-scan-on-premises-sql-server.md)|Yes| Yes| Nie| Yes| Yes| Yes|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Yes| Yes| Nie| Nie| Nie| Tak|

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie i skanowanie źródła magazynu obiektów blob platformy Azure](register-scan-azure-blob-storage-source.md)