---
title: Narzędzia do wprowadzania danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o narzędziach do pozyskiwania danych i narzędziach, które są wstępnie zainstalowane na Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519375"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Narzędzia do pozyskiwania danych Data Science Virtual Machine

Jako jeden z pierwszych kroków technicznych w projekcie analizy danych lub AI, należy zidentyfikować zestawy danych, które mają być używane, i umieścić je w środowisku analitycznym. Data Science Virtual Machine (DSVM) oferuje narzędzia i biblioteki do przenoszenia danych z różnych źródeł do magazynu danych analitycznych lokalnie na DSVM lub na platformę danych w chmurze lub lokalnie.

Oto kilka narzędzi do przenoszenia danych, które są dostępne w DSVM.

## <a name="adlcopy"></a>AdlCopy

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | Narzędzie do kopiowania danych z usługi Azure Blob Storage do Azure Data Lake Store. Może również kopiować dane między dwoma kontami Azure Data Lake Store.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie wielu obiektów blob z usługi Azure Blob Storage do Azure Data Lake Store.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz, `adlcopy` Aby uzyskać pomoc.    |
| Linki do przykładów      | [Korzystanie z narzędzia AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| Narzędzia pokrewne na DSVM      | AzCopy, interfejs wiersza polecenia platformy Azure     |

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | Narzędzie do zarządzania dla platformy Azure. Zawiera również zlecenia poleceń służące do przenoszenia danych z platform danych platformy Azure, takich jak Azure Blob Storage i Azure Data Lake Store.     |
| Obsługiwane wersje DSVM      | Windows, Linux     |
| Typowe zastosowania      | Importowanie i eksportowanie danych do i z usługi Azure Storage oraz do Azure Data Lake Store.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz, `az` Aby uzyskać pomoc.    |
| Linki do przykładów      | [Korzystanie z interfejsu wiersza polecenia platformy Azure](/cli/azure)     |
| Narzędzia pokrewne na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | Narzędzie do kopiowania danych do i z plików lokalnych, magazynu obiektów blob platformy Azure, plików i tabel.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Kopiowanie plików do usługi Azure Blob Storage i kopiowanie obiektów BLOB między kontami.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz, `azcopy` Aby uzyskać pomoc.    |
| Linki do przykładów      | [Narzędzie AzCopy w systemie Windows](../../storage/common/storage-use-azcopy-v10.md)      |
| Narzędzia pokrewne na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Narzędzie do migracji danych Azure Cosmos DB

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | Narzędzie do importowania danych z różnych źródeł do Azure Cosmos DB bazy danych NoSQL w chmurze. Te źródła obejmują pliki JSON, pliki CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB i Azure Cosmos DB kolekcje interfejsów API SQL.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie plików z maszyny wirtualnej do CosmosDB, importowanie danych z usługi Azure Table Storage do CosmosDB i importowanie danych z bazy danych Microsoft SQL Server do CosmosDB.     |
|  Jak używać/uruchamiać?    |   Aby użyć wersji wiersza polecenia, Otwórz wiersz polecenia i wpisz `dt` . Aby użyć narzędzia graficznego interfejsu użytkownika, Otwórz wiersz polecenia i wpisz `dtui` .    |
| Linki do przykładów      | [CosmosDB Importuj dane](../../cosmos-db/import-data.md)      |
| Narzędzia pokrewne na DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | Graficzny interfejs użytkownika służący do współpracy z plikami przechowywanymi w chmurze platformy Azure. |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie i eksportowanie danych z DSVM.    |
|  Jak używać/uruchamiać?    | Wyszukaj ciąg "Eksplorator usługi Azure Storage" w menu Start. |
| Linki do przykładów      | [Eksplorator usługi Azure Storage](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | SQL Server Narzędzie do kopiowania danych między SQL Server i plikiem danych.      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Importowanie pliku CSV do tabeli SQL Server i eksportowanie tabeli SQL Server do pliku.      |
|  Jak używać/uruchamiać?    |   Otwórz wiersz polecenia i wpisz, `bcp` Aby uzyskać pomoc.    |
| Linki do przykładów      | [Narzędzie bcp](/sql/tools/bcp-utility)      |
| Narzędzia pokrewne na DSVM      | SQL Server, SQLCMD      |

## <a name="blobfuse"></a>blobfuse

| Kategoria | Wartość |
| ------------- | ------------- |
| Co to?   | Narzędzie do instalowania kontenera magazynu obiektów blob platformy Azure w systemie plików Linux.      |
| Obsługiwane wersje DSVM      | Linux      |
| Typowe zastosowania      | Odczytywanie i zapisywanie obiektów BLOB w kontenerze.      |
|  Jak używać i uruchamiać je?    |   Uruchom _blobfuse_ w terminalu.    |
| Linki do przykładów      | [blobfuse w serwisie GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Narzędzia pokrewne na DSVM      | Interfejs wiersza polecenia platformy Azure      |