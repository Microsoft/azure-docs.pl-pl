---
title: 'Samouczek: wprowadzenie do analizowania danych na kontach magazynu'
description: Z tego samouczka dowiesz się, jak analizować dane znajdujące się na koncie magazynu.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b88a7e6a9851018fce255fac0e39a30563b9bf4
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363839"
---
# <a name="analyze-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

Z tego samouczka dowiesz się, jak analizować dane znajdujące się na koncie magazynu.

## <a name="overview"></a>Omówienie

Do tej pory osłanialiśmy scenariusze, w których dane znajdują się w bazach danych w obszarze roboczym. Teraz pokażemy, jak pracować z plikami na kontach magazynu. W tym scenariuszu użyjemy podstawowego konta magazynu obszaru roboczego i kontenera określonego podczas tworzenia obszaru roboczego.

* Nazwa konta magazynu: **contosolake**
* Nazwa kontenera na koncie magazynu: **użytkownicy**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Tworzenie plików CSV i Parquet na koncie magazynu

Uruchom następujący kod w notesie w nowej komórce kodu. Tworzy plik CSV i plik Parquet na koncie magazynu.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

Możesz analizować dane na domyślnym koncie usługi ADLS Gen2 obszaru roboczego lub połączyć konto usługi ADLS Gen2 lub Blob Storage z obszarem roboczym za pomocą obszaru roboczego **"** Zarządzanie " >**"** Połączone usługi " > "**Nowe"**(poniższe kroki dotyczą podstawowego konta ADLS Gen2).

1. W Synapse Studio przejdź do centrum **Danych,** a następnie wybierz pozycję **Połączone.**
1. Przejdź do **Azure Data Lake Storage Gen2**  >  **myworkspace (Podstawowa — contosolake).**
1. Wybierz **użytkowników (podstawowy).** Powinien zostać wyświetlony folder **NYCTaxi.** Wewnątrz powinny być dwa foldery **o nazwie PassengerCountStats_csvformat** i **PassengerCountStats_parquetformat**.
1. Otwórz **PassengerCountStats_parquetformat** folder. Wewnątrz zobaczysz plik parquet o nazwie, `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` np. .
1. Kliknij prawym przyciskiem myszy **pozycję parquet,** wybierz pozycję **Nowy notes,** a następnie wybierz **pozycję Załaduj do ramki danych.** Zostanie utworzony nowy notes z komórką w ten sposób:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. Dołącz do puli platformy Spark o **nazwie Spark1.** Uruchom komórkę.
1. Wróć do **folderu** users. Ponownie kliknij prawym przyciskiem **myszy plik parquet,** a następnie wybierz pozycję Nowy skrypt **SQL** SELECT  >  **TOP 100 rows (100 najlepszych wierszy).** Tworzy skrypt SQL w ten sposób:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    W oknie skryptu upewnij się, że pole **Połącz** z jest ustawione na wbudowaną bez **serwera pulę** SQL.

1. Uruchom skrypt.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Organizowanie działań przy użyciu potoków](get-started-pipelines.md)
