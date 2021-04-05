---
title: Obsługiwane formaty plików przez działanie kopiowania w Azure Data Factory
description: W tym temacie opisano formaty plików i kody kompresji, które są obsługiwane przez działanie kopiowania w Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364617"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Obsługiwane formaty plików i kodeki kompresji przez działanie kopiowania w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Ten artykuł ma zastosowanie do następujących łączników [: Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Możesz użyć [działania kopiowania](copy-activity-overview.md) , aby skopiować pliki między dwoma magazynami danych opartymi na plikach. w takim przypadku dane są kopiowane efektywnie bez serializacji ani deserializacji. 

Ponadto można również analizować lub generować pliki danego formatu. Można na przykład wykonać następujące czynności:

* Skopiuj dane z bazy danych SQL Server i Zapisz do Azure Data Lake Storage Gen2 w formacie Parquet.
* Skopiuj pliki w formacie tekstu (CSV) z lokalnego systemu plików i Zapisz w usłudze Azure Blob Storage w formacie Avro.
* Skopiuj pliki spakowane z lokalnego systemu plików, Dekompresuj je na bieżąco i napisz wyodrębnione pliki do Azure Data Lake Storage Gen2.
* Skopiuj dane w formacie skompresowanego tekstu (CSV) w usłudze Azure Blob Storage i Zapisz je w Azure SQL Database.
* Wiele innych działań, które wymagają serializacji/deserializacji lub kompresji/dekompresji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Wydajność działania kopiowania](copy-activity-performance.md)
