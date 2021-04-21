---
title: Magazyny danych obsługiwane w usłudze Azure Data Share
description: Dowiedz się więcej o magazynach danych, które są obsługiwane do użycia w Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812628"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Magazyny danych obsługiwane w usłudze Azure Data Share

Azure Data Share udostępnia otwarte i elastyczne dane, w tym możliwość udostępniania z i do różnych magazynów danych. Dostawcy danych mogą udostępniać dane z jednego typu magazynu danych, a konsumenci danych mogą wybrać magazyn danych do odbierania danych. 

W tym artykule znajdziesz informacje na temat bogatego zestawu magazynów danych platformy Azure, które Azure Data Share obsługuje. Dowiesz się również, jak dostawcy danych i konsumenci danych mogą łączyć różne magazyny danych. 

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych 

W poniższej tabeli wyjaśniono magazyny danych, które Azure Data Share obsługuje. 

| Magazyn danych | Udostępnianie na podstawie pełnych migawek | Udostępnianie na podstawie migawek przyrostowych | Udostępnianie na miejscu 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Usługa Azure Data Lake Storage 1. generacji |✓ |✓ | |
| Usługa Azure Data Lake Storage 2. generacji |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics dedykowana pula SQL (obszar roboczy) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Macierz obsługi magazynu danych

Azure Data Share umożliwia użytkownikom danych wybranie magazynu danych do akceptowania danych. Na przykład dane udostępniane z Azure SQL Database można odbierać do Azure Data Lake Storage Gen2, Azure SQL Database lub Azure Synapse Analytics. Gdy klienci skonfigurują odbierający udział danych, mogą wybrać format, w którym mają być odbierane dane. 

W poniższej tabeli wyjaśniono kombinacje i opcje, które użytkownicy danych mogą wybrać podczas akceptowania i konfigurowania udziału danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie mapowania zestawu danych.](how-to-configure-mapping.md)

| Magazyn danych | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (dawniej SQL Data Warehouse) | Synapse Analytics dedykowana pula SQL (obszar roboczy) | Eksplorator danych
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (dawniej SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics dedykowana pula SQL (obszar roboczy) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Eksplorator danych ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu
Azure Data Share obsługuje udostępnianie plików, folderów i systemów plików z Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2. Obsługuje również udostępnianie obiektów blob, folderów i kontenerów z Azure Blob Storage. Możesz udostępniać blokowe, uzupełnialne lub stronicowe obiekty blob i są one odbierane jako blokowe obiekty blob.

Gdy systemy plików, kontenery lub foldery są udostępniane w trybie udostępniania opartego na migawkach, użytkownicy danych mogą zdecydować się na tworzenie pełnej kopii udostępnionych danych. Mogą też używać funkcji migawki przyrostowej do kopiowania tylko nowych lub zaktualizowanych plików. 

Migawka przyrostowa jest oparta na czasie ostatniej modyfikacji plików. Istniejące pliki, które mają taką samą nazwę jak pliki w odebranych danych, są zastępowane w migawce. Pliki, które są usuwane ze źródła, nie są usuwane na komputerze docelowym. 

Aby uzyskać więcej informacji, zobacz [Udostępnianie i odbieranie danych z Azure Blob Storage i Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Udostępnianie ze źródła opartego na języku SQL
Azure Data Share obsługuje udostępnianie tabel i widoków z Azure SQL Database i Azure Synapse Analytics (wcześniej Azure SQL Data Warehouse). Obsługuje ona udostępnianie tabel z Azure Synapse Analytics (obszaru roboczego) dedykowanej puli SQL. Udostępnianie z Azure Synapse Analytics (obszaru roboczego) nie jest obecnie obsługiwane. 

Konsumenci danych mogą zdecydować się na akceptowanie danych w Azure Data Lake Storage Gen2 lub Azure Blob Storage jako plik CSV lub plik parquet. Mogą również akceptować dane jako tabele w Azure SQL Database i Azure Synapse Analytics.

Gdy odbiorcy akceptują dane Azure Data Lake Storage Gen2 lub Azure Blob Storage, pełne migawki zastępują zawartość pliku docelowego, jeśli plik już istnieje. Gdy dane są odbierane do tabeli, a tabela docelowa jeszcze nie istnieje, Azure Data Share tworzy tabelę SQL przy użyciu schematu źródłowego. Jeśli tabela docelowa już istnieje i ma taką samą nazwę, zostanie porzucona i zastąpiona najnowszą pełną migawką. Migawki przyrostowe nie są obecnie obsługiwane.

Aby uzyskać więcej informacji, zobacz [Udostępnianie i odbieranie danych z Azure SQL Database i Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Udostępnianie z Eksplorator danych
Azure Data Share obsługuje możliwość udostępniania baz danych w miejscu z Azure Data Explorer klastrów. Dostawca danych może udostępniać dane na poziomie bazy danych lub klastra. 

Gdy dane są udostępniane na poziomie bazy danych, konsumenci danych mogą uzyskać dostęp tylko do baz danych udostępnionych przez dostawcę danych. Gdy dostawca udostępnia dane na poziomie klastra, użytkownicy danych mogą uzyskać dostęp do wszystkich baz danych z klastra dostawcy, w tym do wszystkich przyszłych baz danych, które tworzy dostawca danych.

Aby uzyskać dostęp do udostępnionych baz danych, użytkownicy danych potrzebują własnego Azure Data Explorer danych. Klaster musi znajdować się w tym samym centrum danych platformy Azure, w Azure Data Explorer danych. 

Po nawiązyniu relacji Azure Data Share tworzy łącze symboliczne między klastrem dostawcy a klastrem konsumenta. Dane pozyskane do klastra źródłowego przy użyciu trybu wsadowego pojawiają się w klastrze docelowym w ciągu kilku minut.

Aby uzyskać więcej informacji, zobacz [Udostępnianie i odbieranie danych z Azure Data Explorer](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka Udostępnianie](share-your-data.md) danych.
