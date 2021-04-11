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
ms.date: 12/31/2020
ms.openlocfilehash: 0ef76be2c083d6185b8b919d174de28aa3b65446
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293774"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analizowanie danych za pomocą bezserwerowej puli SQL

W tym samouczku dowiesz się, jak analizować dane za pomocą puli SQL bezserwerowej. 

## <a name="the-built-in-serverless-sql-pool"></a>Wbudowana Pula SQL bezserwerowa

Pule SQL bezserwerowe umożliwiają korzystanie z języka SQL bez konieczności rezerwowania pojemności. Rozliczanie dla bezserwerowej puli SQL jest zależne od ilości danych przetworzonych w celu uruchomienia zapytania, a nie liczby węzłów używanych do uruchomienia zapytania.

Każdy obszar roboczy jest dostarczany ze wstępnie skonfigurowaną bezserwerową pulą SQL o nazwie **wbudowane**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu puli SQL bezserwerowej

W tej sekcji użyjesz bezserwerowej puli SQL do analizowania danych NYC na platformie Blob Storage Azure.

1. W programie Synapse Studio przejdź do centrum **opracowywania**
1. Utwórz nowy skrypt SQL.
1. Wklej poniższy kod do skryptu.

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą bezserwerowej puli platformy Spark](get-started-analyze-spark.md)
