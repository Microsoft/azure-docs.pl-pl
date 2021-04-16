---
title: Instalowanie Visual Studio 2019 r.
description: Instalowanie Visual Studio i SQL Server Development Tools (SSDT) dla Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: c8c07997b3ef8cb050ea4609a650a3e3b1bd21fb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568254"
---
# <a name="getting-started-with-visual-studio-2019"></a>Wprowadzenie do programu Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) to pojedyncze narzędzie umożliwiające:

- Nawiązywanie połączeń, tworzenie zapytań i tworzenie aplikacji
- Skorzystaj z eksploratora obiektów, aby wizualnie eksplorować wszystkie obiekty w modelu danych, w tym tabele, widoki, procedury składowane itd.
- Generowanie skryptów języka definicji danych T-SQL (DDL) dla obiektów
- Opracowywanie magazynu danych przy użyciu podejścia opartego na stanie w projektach baz danych SSDT
- Integrowanie projektu bazy danych z systemami kontroli źródła, takimi jak Git, z Azure Repos
- Konfigurowanie potoków ciągłej integracji i wdrażania przy użyciu serwerów automatyzacji, takich jak Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalowanie Visual Studio 2019 r.

Zobacz [download Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) to download and install Visual Studio 16.3 and above (Pobieranie i instalowanie programu w Visual Studio **16.3 lub 2019).** Podczas instalacji wybierz obciążenie magazynu danych i przetwarzania. Autonomiczna instalacja programu SSDT nie jest już wymagana Visual Studio 2019 r.

## <a name="unsupported-features-in-ssdt"></a>Nieobsługiwane funkcje narzędzi SSDT

Czasami wersje funkcji dla Synapse SQL mogą nie zawierać obsługi SSDT. Następujące funkcje nie są obecnie obsługiwane:


- [Zarządzanie obciążeniami](sql-data-warehouse-workload-management.md) — grupy obciążeń i klasyfikatory
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (w tym funkcje o wartościach tabeli)
  - Prześlij bilet [pomocy technicznej lub zagłosuj,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) aby uzyskać obsługiwaną funkcję.
  - Prześlij bilet [pomocy technicznej lub zagłosuj,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) aby uzyskać obsługiwaną funkcję.
- Niektóre funkcje języka T-SQL, takie jak:
   - *Klauzula WITHIN GROUP* w [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) ciągu.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz najnowszą wersję programu SSDT, możesz nawiązać [połączenie](sql-data-warehouse-query-visual-studio.md) z pulą SQL.