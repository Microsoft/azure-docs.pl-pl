---
title: 'Samouczek: Rozpoczynanie analizy danych przy użyciu języka SQL serverles'
description: W tym samouczku dowiesz się, jak analizować dane przy użyciu programu SQL na żądanie przy użyciu danych znajdujących się w bazach danych Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: fa87ee06e0ed01828148a58b2d2f754a40d8803a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088469"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analizowanie danych za pomocą programu SQL na żądanie

W tym samouczku dowiesz się, jak analizować dane za pomocą SQL Server, korzystając z puli SQL na żądanie, korzystając z danych znajdujących się w bazach danych Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu puli na żądanie SQL

1. W obszarze **połączone**centrum **danych** kliknij prawym przyciskiem myszy pozycję **Azure Blob Storage > przykładowe zestawy danych > NYC_TLC_YELLOW** i wybierz pozycję **Wybierz pierwsze 100 wierszy**
1. Spowoduje to utworzenie nowego skryptu SQL o następującym kodzie:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Kliknij przycisk **Uruchom**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analizowanie danych z NYC taksówki w bazach danych platformy Spark przy użyciu programu SQL na żądanie

Tabele w bazach danych platformy Spark są automatycznie widoczne i mogą być wysyłane do nich zapytania przez SQL na żądanie.

1. W programie Synapse Studio przejdź do centrum **tworzenia** i Utwórz nowy skrypt SQL.
1. Ustaw wartość opcji **Połącz** z na **żądanie na SQL**.
1. Wklej następujący tekst do skryptu i uruchom skrypt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Przy pierwszym uruchomieniu zapytania korzystającego z SQL na żądanie trwa około 10 sekund dla SQL na żądanie w celu zebrania zasobów SQL potrzebnych do uruchamiania zapytań. Kolejne zapytania będą znacznie szybsze.
  


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych w magazynie](get-started-analyze-storage.md)
