---
title: Magazyny danych obsługiwane w usłudze Azure Data Share
description: Dowiedz się więcej na temat magazynów danych, które są obsługiwane do użycia w udziale danych platformy Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963683"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Magazyny danych obsługiwane w usłudze Azure Data Share

Udział danych platformy Azure zapewnia otwarte i elastyczne udostępnianie danych, w tym możliwość udostępniania z i do różnych magazynów danych. Dostawcy danych mogą udostępniać dane z jednego typu magazynu danych, a konsumenci danych mogą wybrać magazyn danych, który będzie otrzymywał dane. 

Ten artykuł zawiera informacje o rozbudowanym zestawie magazynów danych systemu Azure, które obsługuje udział danych platformy Azure. Dowiesz się również, jak dostawcy danych i konsumenci danych mogą łączyć różne magazyny danych. 

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych 

W poniższej tabeli objaśniono magazyny danych obsługiwane przez udział danych platformy Azure. 

| Magazyn danych | Udostępnianie na podstawie pełnych migawek | Udostępnianie na podstawie migawek przyrostowych | Udostępnianie na miejscu 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Usługa Azure Data Lake Storage 1. generacji |✓ |✓ | |
| Usługa Azure Data Lake Storage 2. generacji |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) |✓ | | |
| Dedykowana Pula SQL usługi Azure Synapse Analytics (Workspace) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Macierz obsługi magazynu danych

Udział danych platformy Azure umożliwia klientom danych Wybieranie magazynu danych w celu zaakceptowania danych. Na przykład dane, które są udostępniane z Azure SQL Database można odbierać do Azure Data Lake Storage Gen2, Azure SQL Database lub Azure Synapse Analytics. Gdy klienci skonfigurują udział danych odbierający, mogą wybrać format do odbierania danych. 

W poniższej tabeli objaśniono kombinacje i opcje, które użytkownicy danych mogą wybrać, gdy akceptują i konfigurują udział danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie mapowania zestawu danych](how-to-configure-mapping.md).

| Magazyn danych | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (dawniej SQL Data Warehouse) | Dedykowana Pula SQL Synapse Analytics (Workspace) | Eksplorator danych
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (dawniej SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Dedykowana Pula SQL Synapse Analytics (Workspace) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Eksplorator danych ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu
Udział danych platformy Azure obsługuje udostępnianie plików, folderów i systemów plików z Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2. Obsługuje także udostępnianie obiektów blob, folderów i kontenerów z usługi Azure Blob Storage. Obecnie obsługiwane są tylko blokowe obiekty blob. 

Gdy systemy plików, kontenery i foldery są udostępniane w ramach udostępniania opartego na migawce, użytkownicy danych mogą wybrać pełną kopię udostępnionych danych. Można też użyć funkcji migawki przyrostowej, aby skopiować tylko nowe pliki lub zaktualizowane pliki. 

Migawka przyrostowa jest oparta na czasie ostatniej modyfikacji plików. Istniejące pliki, które mają taką samą nazwę jak pliki w odebranych danych, są zastępowane w migawce. Pliki usunięte ze źródła nie są usuwane w miejscu docelowym. 

Aby uzyskać więcej informacji, zobacz [udostępnianie i odbieranie danych z platformy Azure Blob Storage i Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Udostępnianie z poziomu źródła opartego na języku SQL
Udział danych platformy Azure obsługuje udostępnianie zarówno tabel, jak i widoków z Azure SQL Database i usługi Azure Synapse Analytics (dawniej Azure SQL Data Warehouse). Obsługuje ona udostępnianie tabel z dedykowanej puli SQL usługi Azure Synapse Analytics (Workspace). Udostępnianie za pomocą puli SQL bez serwera usługi Azure Synapse Analytics (Workspace) nie jest obecnie obsługiwane. 

Odbiorcy danych mogą zdecydować się na zaakceptowanie danych do Azure Data Lake Storage Gen2 lub Blob Storage platformy Azure jako pliku CSV lub pliku Parquet. Mogą również akceptować dane w formie tabel do Azure SQL Database i usługi Azure Synapse Analytics.

Gdy Użytkownicy akceptują dane w Azure Data Lake Storage Gen2 lub Blob Storage platformy Azure, pełne migawki zastąpią zawartość pliku docelowego, jeśli plik już istnieje. Gdy dane są odbierane do tabeli, a tabela docelowa jeszcze nie istnieje, udział danych platformy Azure tworzy tabelę SQL przy użyciu schematu źródłowego. Jeśli tabela docelowa już istnieje i ma taką samą nazwę, zostanie porzucona i zastąpiona najnowszą pełną migawką. Migawki przyrostowe nie są obecnie obsługiwane.

Aby uzyskać więcej informacji, zobacz [udostępnianie i odbieranie danych z Azure SQL Database i usługi Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Udostępnij z Eksplorator danych
Udział danych platformy Azure umożliwia udostępnianie baz danych w miejscu z klastrów Eksplorator danych platformy Azure. Dostawca danych może współużytkować na poziomie bazy danych lub klastra. 

Gdy dane są współużytkowane na poziomie bazy danych, odbiorcy danych mogą uzyskać dostęp tylko do baz danych udostępnionych przez dostawcę danych. Gdy dostawca udostępnia dane na poziomie klastra, odbiorcy danych mogą uzyskać dostęp do wszystkich baz danych z klastra dostawcy, w tym wszystkich przyszłych baz danych tworzonych przez dostawcę danych.

Aby uzyskać dostęp do udostępnionych baz danych, konsumenci danych potrzebują własnego klastra Eksplorator danych platformy Azure. Ich klaster musi znajdować się w tym samym centrum danych platformy Azure, co klaster usługi Azure Eksplorator danych. 

Po ustanowieniu relacji udostępniania udział danych platformy Azure tworzy link symboliczny między klastrem dostawcy a klastrem użytkownika. Dane pozyskane do klastra źródłowego przy użyciu trybu wsadowego pojawiają się w klastrze docelowym w ciągu kilku minut.

Aby uzyskać więcej informacji, zobacz [udostępnianie i odbieranie danych z usługi Azure Eksplorator danych](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
