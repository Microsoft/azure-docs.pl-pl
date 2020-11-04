---
title: 'Samouczek: Rozpoczynanie analizy danych za pomocą puli SQL bezserwerowej'
description: W tym samouczku dowiesz się, jak analizować dane za pomocą puli SQL bezserwerowej przy użyciu danych znajdujących się w bazach danych platformy Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4ca9ababbeb7843f1a014a4bd51a5e24a74acbae
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322944"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analizowanie danych za pomocą puli SQL bezserwerowej w usłudze Azure Synapse Analytics

W ramach tego samouczka dowiesz się, jak analizować dane za pomocą puli SQL bezserwerowej przy użyciu danych znajdujących się w bazach danych Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu puli SQL bezserwerowej

1. W obszarze **połączone** centrum **danych** kliknij prawym przyciskiem myszy pozycję **Azure Blob Storage > przykładowe zestawy danych > NYC_TLC_YELLOW** i wybierz pozycję **Wybierz pierwsze 100 wierszy**
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

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analizowanie danych NYC taksówki w bazach danych Spark przy użyciu puli SQL bezserwerowej

Tabele w bazach danych platformy Spark są automatycznie widoczne i mogą być wysyłane do nich zapytania za pomocą puli SQL bezserwerowej.

1. W programie Synapse Studio przejdź do centrum **tworzenia** i Utwórz nowy skrypt SQL.
1. Ustaw wartość **Połącz** z na **pulę SQL bezserwerową**.
1. Wklej następujący tekst do skryptu i uruchom skrypt.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Przy pierwszym uruchomieniu zapytania, które korzysta z puli SQL bezserwerowej, trwa około 10 sekund dla puli SQL bezserwerowej do zbierania zasobów SQL potrzebnych do uruchamiania zapytań. Kolejne zapytania będą znacznie szybsze.
  


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych w magazynie](get-started-analyze-storage.md)
