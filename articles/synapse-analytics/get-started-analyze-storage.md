---
title: 'Samouczek: wprowadzenie analizowanie danych na kontach magazynu'
description: W tym samouczku dowiesz się, jak analizować dane znajdujące się na koncie magazynu.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: f18977bb92b37546d5980134cba858b1f76b464c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720019"
---
# <a name="analyze-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

W tym samouczku dowiesz się, jak analizować dane znajdujące się na koncie magazynu.

## <a name="overview"></a>Omówienie

Do tej pory omówione zostały scenariusze, w których dane znajdują się w bazach danych w obszarze roboczym. Teraz pokażemy, jak korzystać z plików na kontach magazynu. W tym scenariuszu użyjemy podstawowego konta magazynu obszaru roboczego i kontenera, który został określony podczas tworzenia obszaru roboczego.

* Nazwa konta magazynu: **contosolake**
* Nazwa kontenera na koncie magazynu: **Użytkownicy**

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

Dane można analizować w obszarze roboczym domyślne konto ADLS Gen2. Możesz też połączyć ADLS Gen2 lub konto usługi BLOB Storage z obszarem roboczym za pomocą funkcji "**Zarządzaj**" > "**połączone usługi**" > "**Nowy**" (poniższe kroki odnoszą się do podstawowego konta ADLS Gen2).

1. W programie Synapse Studio przejdź do centrum **danych** , a następnie wybierz pozycję **połączone**.
1. Przejdź do **usługi Azure Data Lake Storage Gen2** z  >  **obszarem roboczym (podstawowa-contosolake)**.
1. Wybierz pozycję **Użytkownicy (podstawowy)**. Powinien zostać wyświetlony folder **NYCTaxi** . Wewnątrz powinny być widoczne dwa foldery o nazwie **PassengerCountStats_csvformat** i **PassengerCountStats_parquetformat**.
1. Otwórz folder **PassengerCountStats_parquetformat** . W programie zobaczysz plik Parquet o takiej samej nazwie `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Kliknij prawym przyciskiem myszy pozycję **. Parquet**, a następnie wybierz pozycję **Nowy Notes**, a następnie wybierz pozycję **Załaduj do ramki Dataframe**. Tworzony jest nowy Notes z komórką podobną do tej:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. Dołącz do puli platformy Spark o nazwie **Spark1**. Uruchom komórkę.
1. Kliknij przycisk z powrotem do folderu **Users (Użytkownicy** ). Kliknij prawym przyciskiem myszy plik **. Parquet** ponownie, a następnie wybierz pozycję **Nowy skrypt SQL**  >  **Wybierz pozycję pierwsze 100 wierszy**. Tworzy skrypt SQL podobny do tego:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    W oknie Skrypt upewnij się, że pole **Połącz z** jest ustawione na **wbudowaną** bezserwerową pulę SQL.

1. Uruchom skrypt.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Organizowanie działań przy użyciu potoków](get-started-pipelines.md)
