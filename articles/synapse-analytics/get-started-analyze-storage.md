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
ms.date: 07/20/2020
ms.openlocfilehash: fabfdce72202f79e2ac5bad08d124df7ce2de542
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592587"
---
# <a name="analyze-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

W tym samouczku dowiesz się, jak analizować dane znajdujące się na koncie magazynu.

## <a name="overview"></a>Omówienie

Do tej pory omówione zostały scenariusze, w których dane znajdują się w bazach danych w obszarze roboczym. Teraz pokażemy, jak korzystać z plików na kontach magazynu. W tym scenariuszu użyjemy podstawowego konta magazynu obszaru roboczego i kontenera, który został określony podczas tworzenia obszaru roboczego.

* Nazwa konta magazynu: **contosolake**
* Nazwa kontenera na koncie magazynu: **Użytkownicy**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Tworzenie plików CSV i Parquet na koncie magazynu

Uruchom następujący kod w notesie. Tworzy plik CSV i plik Parquet na koncie magazynu.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizowanie danych na koncie magazynu

1. W programie Synapse Studio przejdź do centrum **danych** , a następnie wybierz pozycję **połączone**.
1. Przejdź do pozycji **konta magazynu** mój  >  **obszar roboczy (podstawowe-contosolake)**.
1. Wybierz pozycję **Użytkownicy (podstawowy)**. Powinien zostać wyświetlony folder **NYCTaxi** . Wewnątrz powinny być widoczne dwa foldery o nazwie **PassengerCountStats_csvformat** i **PassengerCountStats_parquetformat**.
1. Otwórz folder **PassengerCountStats_parquetformat** . W programie zobaczysz plik Parquet o takiej samej nazwie `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Kliknij prawym przyciskiem myszy pozycję **Parquet** , a następnie wybierz pozycję **Nowy Notes**. Tworzy Notes o takiej samej komórce:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Uruchom komórkę.
1. Kliknij prawym przyciskiem myszy plik Parquet wewnątrz, a następnie wybierz pozycję **Nowy skrypt SQL**  >  **zaznacz pierwsze 100 wierszy**. Tworzy skrypt SQL podobny do tego:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    W oknie Skrypt pole **Połącz z** jest ustawione na **pulę SQL bez serwera**.

1. Uruchom skrypt.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Organizowanie działań przy użyciu potoków](get-started-pipelines.md)
