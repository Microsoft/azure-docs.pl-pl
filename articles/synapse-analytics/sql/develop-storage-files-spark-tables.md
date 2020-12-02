---
title: Synchronizuj Apache Spark definicji tabel zewnętrznych w puli SQL bezserwerowej
description: Omówienie sposobu wykonywania zapytań w tabelach platformy Spark przy użyciu bezserwerowej puli SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 057a69881b8b407e5d75fa3510ca1c3eb1830bc7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446487"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Synchronizuj Apache Spark dla definicji tabeli zewnętrznej Synapse platformy Azure w bezserwerowej puli SQL

Pula SQL bezserwerowa może automatycznie synchronizować metadane z Apache Spark. Bezserwerowa baza danych puli SQL zostanie utworzona dla każdej bazy danych istniejącej w puli Apache Spark bezserwerowych. 

Dla każdej tabeli zewnętrznej platformy Spark opartej na Parquet i znajdującej się w usłudze Azure Storage tabela zewnętrzna jest tworzona w bazie danych puli SQL bezserwerowej. W związku z tym można wyłączyć pule Spark i nadal wykonywać zapytania dotyczące zewnętrznych tabel platformy Spark z puli SQL bezserwerowej.

Gdy tabela jest partycjonowana na platformie Spark, pliki w magazynie są zorganizowane według folderów. Pula SQL bezserwerowa będzie używać metadanych partycji i dotyczy tylko odpowiednich folderów i plików dla zapytania.

Synchronizacja metadanych jest automatycznie konfigurowana dla każdej puli Apache Spark bezserwerowej, która została zainicjowana w obszarze roboczym usługi Azure Synapse. Możesz natychmiast zacząć wykonywać zapytania dotyczące zewnętrznych tabel platformy Spark.

Każda tabela zewnętrzna Parquet platformy Spark znajdująca się w usłudze Azure Storage jest reprezentowana w tabeli zewnętrznej w schemacie dbo, która odpowiada bezserwerowej bazie danych puli SQL. 

W przypadku zapytań dotyczących tabel zewnętrznych platformy Spark uruchom zapytanie, które jest przeznaczone dla zewnętrznego [spark_table]. Przed uruchomieniem poniższego przykładu upewnij się, że masz prawidłowy [dostęp do konta magazynu](develop-storage-files-storage-access-control.md) , w którym znajdują się pliki.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Dodawanie, upuszczanie lub zmienianie zewnętrznych poleceń tabeli platformy Spark dla kolumny nie zostanie odzwierciedlone w tabeli zewnętrznej w puli SQL bezserwerowej.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark typów danych do mapowania typów danych SQL

| Typ danych Spark | Typ danych SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Typ Krótki      | smallint                    |
| IntegerType     | int                         |
| Typ LongType        | bigint                      |
| Typ floattype       | liczba rzeczywista                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| Timestamptype   | datetime2                   |
| DataType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| Wartość logicznatype     | bit                         |
| ArrayType       | varchar (max) \* (w formacie JSON)\** |
| MapType         | varchar (max) \* (w formacie JSON)\** |
| Typ struktury      | varchar (max) \* (w formacie JSON)\** |

\* Używane sortowanie jest Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType i Structtype są reprezentowane jako dane JSON.



## <a name="next-steps"></a>Następne kroki

Przejdź do artykułu [Access Control magazynu](develop-storage-files-storage-access-control.md) , aby dowiedzieć się więcej o kontroli dostępu do magazynu.
