---
title: Magazyny danych obsługiwane w usłudze Azure Data Share
description: Dowiedz się więcej na temat magazynów danych, które są obsługiwane w przypadku używania udziału danych platformy Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ddd586bf9a88b1246fcf20fc19493f5003bbb530
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151749"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Magazyny danych obsługiwane w usłudze Azure Data Share

Udział danych platformy Azure zapewnia otwarte i elastyczne udostępnianie danych, w tym możliwość udostępniania z i do różnych magazynów danych. Dostawcy danych mogą udostępniać dane z jednego typu magazynu danych, a ich konsumenci danych mogą wybrać magazyn danych, do którego mają być wysyłane dane. 

Ten artykuł zawiera informacje o rozbudowanym zestawie magazynów danych systemu Azure, które są obsługiwane w udziale danych platformy Azure. Możesz również znaleźć informacje dotyczące kombinacji magazynów danych, które mogą być wykorzystywane przez dostawców danych i użytkowników danych. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Jakie magazyny danych są obsługiwane w udziale danych platformy Azure? 

Poniższa tabela zawiera szczegółowe informacje o obsługiwanych źródłach danych dla udziału danych platformy Azure. 

| Magazyn danych | Udostępnianie oparte na migawce | Udostępnianie w miejscu 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Usługa Azure Data Lake Storage 1. generacji |✓ | |
| Usługa Azure Data Lake Storage 2. generacji |✓ ||
| Azure SQL Database |Publiczna wersja zapoznawcza | |
| Azure Synapse Analytics (dawniej: Azure SQL DW) |Publiczna wersja zapoznawcza | |
| Azure Data Explorer | |✓ |

## <a name="data-store-support-matrix"></a>Macierz obsługi magazynu danych

Udział danych platformy Azure oferuje klientom danych elastyczność podczas wybierania magazynu danych w celu akceptowania danych do programu. Na przykład dane udostępniane przez Azure SQL Database można odbierać do Azure Data Lake Store Gen2, Azure SQL Database lub Azure Synapse Analytics. Klienci mogą wybrać format, w którym będą wysyłane dane podczas konfigurowania otrzymanego udziału danych. 

Poniższa tabela zawiera szczegółowe informacje o różnych kombinacjach i wyborach, które użytkownicy danych mają podczas akceptowania i konfigurowania udziału danych. Aby uzyskać więcej informacji na temat konfigurowania mapowań zestawu danych, zobacz [jak skonfigurować mapowania zestawu danych](how-to-configure-mapping.md).

| Magazyn danych | Azure Blob Storage | Usługa Azure Data Lake Storage 1. generacji | Usługa Azure Data Lake Storage 2. generacji | Azure SQL Database | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓ ||
| Usługa Azure Data Lake Storage 1. generacji | ✓ | | ✓ ||
| Usługa Azure Data Lake Storage 2. generacji | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (dawniej: Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu
Udział danych platformy Azure obsługuje udostępnianie plików, folderów i systemów plików z Azure Data Lake Gen1 i Azure Data Lake Gen2. Obsługuje także udostępnianie obiektów blob, folderów i kontenerów z usługi Azure Blob Storage. Obecnie jest obsługiwany tylko blokowy obiekt BLOB. Gdy systemy plików, kontenery lub foldery są udostępniane w ramach udostępniania opartego na migawce, konsument danych może wybrać pełną kopię danych udziału lub użyć funkcji przyrostowej migawki w celu skopiowania tylko nowych lub zaktualizowanych plików. Przyrostowa migawka jest określana na podstawie czasu ostatniej modyfikacji plików. Istniejące pliki o tej samej nazwie zostaną zastąpione.

Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [udostępnianie i odbieranie danych z usługi Azure Blob Storage i Azure Data Lake Storage](how-to-share-from-storage.md) .

## <a name="share-from-a-sql-based-source"></a>Udostępnianie z poziomu źródła opartego na języku SQL
Udział danych platformy Azure obsługuje udostępnianie tabel lub widoków z usług Azure SQL Database i Azure Synapse Analytics (dawniej Azure SQL DW). Konsumenci danych mogą zdecydować się na zaakceptowanie danych do Azure Data Lake Storage Gen2 lub Blob Storage platformy Azure jako plików CSV lub Parquet, a także w Azure SQL Database i Azure Synapse Analytics jako tabele.

Gdy akceptujesz dane do Azure Data Lake Store Gen2 lub Azure Blob Storage, pełne migawki zastąpią zawartość pliku docelowego, jeśli już istnieje.
Gdy dane są odbierane do tabeli, a tabela docelowa jeszcze nie istnieje, udział danych platformy Azure tworzy tabelę SQL ze schematem źródłowym. Jeśli tabela docelowa już istnieje o tej samej nazwie, zostanie porzucona i zastąpiona najnowszą pełną migawką. Migawki przyrostowe nie są obecnie obsługiwane.

Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [udostępnianie i odbieranie danych z Azure SQL Database i usługi Azure Synapse Analytics](how-to-share-from-sql.md) .

## <a name="share-from-azure-data-explorer"></a>Udostępnianie z usługi Azure Data Explorer
Udział danych platformy Azure umożliwia udostępnianie baz danych w miejscu z klastrów Eksplorator danych platformy Azure. Dostawca danych może współdzielić na poziomie bazy danych lub klastra. Po udostępnieniu na poziomie bazy danych konsument danych będzie mógł uzyskać dostęp do określonych baz danych udostępnionych przez dostawcę danych. Po udostępnieniu na poziomie klastra konsument danych może uzyskać dostęp do wszystkich baz danych z klastra dostawcy, w tym wszystkich przyszłych baz danych utworzonych przez dostawcę danych.

Aby uzyskać dostęp do udostępnionych baz danych, konsument danych musi mieć własny klaster Eksplorator danych platformy Azure. Klaster danych platformy Azure dla użytkowników Eksplorator danych musi znajdować się w tym samym centrum danych platformy Azure co klaster usługi Azure Eksplorator danych dostawcy danych. Po ustanowieniu relacji udostępniania udział danych platformy Azure tworzy link symboliczny między klastrami Eksplorator danych platformy Azure dostawcy i klientem. Dane pozyskiwane przy użyciu trybu wsadowego w źródłowym klastrze Eksplorator danych platformy Azure będą widoczne w klastrze docelowym w ciągu kilku sekund do kilku minut.

Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [udostępnianie i odbieranie danych z usługi Azure Eksplorator danych](/azure/data-explorer/data-share) . 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
