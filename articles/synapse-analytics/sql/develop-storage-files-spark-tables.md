---
title: Synchronizowanie Apache Spark definicji tabeli zewnętrznej na żądanie SQL (wersja zapoznawcza)
description: Omówienie sposobu wykonywania zapytań dotyczących tabel platformy Spark przy użyciu programu SQL na żądanie (wersja zapoznawcza)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3e9f688a31d2847505e974ab6a1557aa6a7b2047
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87046846"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Synchronizowanie Apache Spark dla definicji tabel zewnętrznych Synapse platformy Azure na żądanie SQL (wersja zapoznawcza)

Program SQL na żądanie (wersja zapoznawcza) może automatycznie synchronizować metadane z Apache Spark dla pul Synapse platformy Azure. Baza danych SQL na żądanie zostanie utworzona dla każdej bazy danych istniejącej w pulach platformy Spark (wersja zapoznawcza). 

Dla każdej tabeli zewnętrznej platformy Spark opartej na Parquet i znajdującej się w usłudze Azure Storage tabela zewnętrzna jest tworzona w bazie danych SQL na żądanie. W związku z tym można wyłączyć pule platformy Spark i nadal wykonywać zapytania dotyczące zewnętrznych tabel platformy Spark z poziomu serwera SQL na żądanie.

Gdy tabela jest partycjonowana na platformie Spark, pliki w magazynie są zorganizowane według folderów. Funkcja SQL na żądanie będzie używać metadanych partycji i dotyczy tylko odpowiednich folderów i plików dla zapytania.

Synchronizacja metadanych jest konfigurowana automatycznie dla każdej puli platformy Spark, która została zainicjowana w obszarze roboczym usługi Azure Synapse. Możesz natychmiast zacząć wykonywać zapytania dotyczące zewnętrznych tabel platformy Spark.

Każda zewnętrzna tabela usług Spark Parquet znajdująca się w usłudze Azure Storage jest reprezentowana w tabeli zewnętrznej w schemacie dbo, który odpowiada bazie danych SQL na żądanie. 

W przypadku zapytań dotyczących tabel zewnętrznych platformy Spark uruchom zapytanie, które jest przeznaczone dla zewnętrznego [spark_table]. Przed uruchomieniem poniższego przykładu upewnij się, że masz prawidłowy [dostęp do konta magazynu](develop-storage-files-storage-access-control.md) , w którym znajdują się pliki.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Dodawanie, upuszczanie lub zmienianie zewnętrznych poleceń tabeli platformy Spark dla kolumny nie zostanie odzwierciedlone w tabeli zewnętrznej w SQL na żądanie.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark typów danych do mapowania typów danych SQL

| Typ danych Spark | Typ danych SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Typ Krótki       | smallint                    |
| IntegerType     | int                         |
| Typ LongType        | bigint                      |
| Typ floattype       | liczba rzeczywista                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| Timestamptype   | datetime2                   |
| DataType        | data                        |
| StringType      | varchar (max) *               |
| BinaryType      | varbinary                   |
| Wartość logicznatype     | bit                         |
| ArrayType       | varchar (max) * (w formacie JSON) * * |
| MapType         | varchar (max) * (w formacie JSON) * * |
| Typ struktury      | varchar (max) * (w formacie JSON) * * |

\* Używane sortowanie jest Latin1_General_100_BIN2_UTF8.

* * ArrayType, MapType i Structtype są reprezentowane jako dane JSON.



## <a name="next-steps"></a>Następne kroki

Przejdź do artykułu [Access Control magazynu](develop-storage-files-storage-access-control.md) , aby dowiedzieć się więcej o kontroli dostępu do magazynu.
