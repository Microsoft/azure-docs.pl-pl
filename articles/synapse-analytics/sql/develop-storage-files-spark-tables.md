---
title: Zapytanie tabel platformy Spark przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: Omówienie sposobu wykonywania zapytań dotyczących tabel platformy Spark przy użyciu programu SQL na żądanie (wersja zapoznawcza)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424048"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Zapytanie tabel platformy Spark z usługą Azure Synapse Analytics przy użyciu programu SQL na żądanie (wersja zapoznawcza)

SQL na żądanie (wersja zapoznawcza) może automatycznie synchronizować metadane z pul Spark w obszarze roboczym Synapse (wersja zapoznawcza). Baza danych SQL na żądanie zostanie utworzona dla każdej bazy danych istniejącej w pulach platformy Spark (wersja zapoznawcza). Dla każdej tabeli Spark opartej na Parquet lub CSV tabela zewnętrzna jest tworzona w bazie danych SQL na żądanie. W związku z tym można wyłączyć pule platformy Spark i nadal wykonywać zapytania dotyczące tabel platformy Spark z poziomu bazy danych SQL na żądanie.

Gdy tabela jest partycjonowana na platformie Spark, pliki w magazynie są zorganizowane według folderów. SQL na żądanie będzie używać metadanych partycji i dotyczy tylko odpowiednich folderów i plików dla zapytania.

Synchronizacja metadanych jest konfigurowana automatycznie dla każdej puli platformy Spark, która została zainicjowana w obszarze roboczym usługi Azure Synapse. Możesz natychmiast rozpocząć wykonywanie zapytań względem tabel platformy Spark.

Każda tabela platformy Spark jest reprezentowana w tabeli zewnętrznej w schemacie dbo, która odpowiada bazie danych SQL na żądanie. W przypadku zapytań tabeli Spark uruchom zapytanie, które jest przeznaczone dla zewnętrznego [spark_table]. Przed uruchomieniem poniższego przykładu upewnij się, że masz prawidłowy [dostęp do konta magazynu](develop-storage-files-storage-access-control.md) , w którym znajdują się pliki.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Mapowanie typów danych platformy Spark na typy danych SQL

| Typ danych Spark | Typ danych SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| Typ LongType        | bigint                      |
| Typ floattype       | liczba rzeczywista                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| Timestamptype   | datetime2                   |
| DataType        | date                        |
| StringType      | varchar (max) *               |
| BinaryType      | varbinary                   |
| Wartość logicznatype     | bit                         |
| ArrayType       | varchar (max) * (w formacie JSON) * * |
| MapType         | varchar (max) * (w formacie JSON) * * |
| Typ struktury      | varchar (max) * (w formacie JSON) * * |

\*Używane sortowanie jest Latin1_General_100_BIN2_UTF8.

* * ArrayType, MapType i Structtype są reprezentowane jako dane JSON.



## <a name="next-steps"></a>Następne kroki

Przejdź do artykułu [Access Control magazynu](develop-storage-files-storage-access-control.md) , aby dowiedzieć się więcej o kontroli dostępu do magazynu.
