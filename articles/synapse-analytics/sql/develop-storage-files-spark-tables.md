---
title: Tabele programu Spark kwerendy przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: Omówienie sposobu wykonywania zapytań o tabele platformy Spark przy użyciu języka SQL na żądanie (wersja zapoznawcza)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424048"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Zapytania Spark tabel z usługi Azure Synapse Analytics przy użyciu SQL na żądanie (wersja zapoznawcza)

SQL na żądanie (wersja zapoznawcza) może automatycznie synchronizować metadane z pul platformy Spark w obszarze roboczym Synapse (wersja zapoznawcza). Baza danych SQL na żądanie zostanie utworzona dla każdej bazy danych istniejącej w pulach platformy Spark (wersja zapoznawcza). Dla każdej tabeli Platformy Spark opartej na platformie Parkiet lub CSV w bazie danych SQL na żądanie tworzona jest tabela zewnętrzna. W związku z tym można zamknąć puli platformy Spark i nadal wysyłać zapytania do tabel platformy Spark z języka SQL na żądanie.

Gdy tabela jest podzielona na partycje w aplikacji Spark, pliki w magazynie są zorganizowane według folderów. SQL na żądanie będzie korzystać z metadanych partycji i tylko docelowe odpowiednie foldery i pliki dla zapytania.

Synchronizacja metadanych jest automatycznie konfigurowana dla każdej puli platformy Spark aprowizowana w obszarze roboczym Usługi Azure Synapse. Możesz natychmiast rozpocząć wykonywanie zapytań o tabele platformy Spark.

Każda tabela Platformy Spark jest reprezentowana z tabelą zewnętrzną w schemacie dbo, który odpowiada bazie danych SQL na żądanie. W przypadku kwerend tabeli Platformy Spark uruchom kwerendę przeznaczoną na zewnętrzną [spark_table]. Przed uruchomieniem poniższego przykładu upewnij się, że masz poprawny [dostęp do konta magazynu,](develop-storage-files-storage-access-control.md) na którym znajdują się pliki.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Mapowanie typów danych platformy Spark do mapowania typów danych SQL

| Typ danych spark | Typ danych SQL               |
| --------------- | --------------------------- |
| Typ bajtów        | smallint                    |
| Krótki typ       | smallint                    |
| Typ liczby całkowitej     | int                         |
| LongType (Typ długi)        | bigint                      |
| Typ pływaka       | rzeczywiste                        |
| DoubleType (Typ dwuosobowy)      | float                       |
| Typ dziesiętny     | decimal                     |
| Typ sygnatury czasowych   | datetime2                   |
| Rodzaj daty        | date                        |
| Typ ciągu      | varchar(maks)*               |
| Typ binarny      | varbinary                   |
| Typ logiczny     | bit                         |
| ArrayType       | varchar(max)* (do JSON)** |
| Typ mapy         | varchar(max)* (do JSON)** |
| Typ struktury      | varchar(max)* (do JSON)** |

\*Sortowanie używane jest Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType i StructType są reprezentowane jako JSON.



## <a name="next-steps"></a>Następne kroki

Przejdź do artykułu Kontrola dostępu do [magazynu,](develop-storage-files-storage-access-control.md) aby dowiedzieć się więcej o kontroli dostępu do magazynu.
