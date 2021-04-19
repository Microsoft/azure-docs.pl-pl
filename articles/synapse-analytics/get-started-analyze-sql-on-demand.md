---
title: 'Samouczek: wprowadzenie do analizowania danych przy użyciu bez serwera puli SQL'
description: Z tego samouczka dowiesz się, jak analizować dane za pomocą bez serwera w puli SQL przy użyciu danych znajdujących się w bazach danych platformy Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567557"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analizowanie danych przy użyciu bez serwera puli SQL

Z tego samouczka dowiesz się, jak analizować dane przy użyciu bez serwera puli SQL. 

## <a name="the-built-in-serverless-sql-pool"></a>Wbudowana bez serwera pula SQL

Bez serwerowe pule SQL umożliwiają korzystanie z bazy danych SQL bez konieczności rezerwy pojemności. Rozliczanie bez serwera puli SQL jest oparte na ilości danych przetworzonych w celu uruchomienia zapytania, a nie na liczbie węzłów użytych do uruchomienia zapytania.

Każdy obszar roboczy zawiera wstępnie skonfigurowaną bez serwera pulę SQL o nazwie **Wbudowana**. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analizowanie danych dotyczących taksówek w NYC przy użyciu bez serwera puli SQL

1. W Synapse Studio przejdź do centrum **Opracowywanie**
1. Utwórz nowy skrypt SQL.
1. Wklej następujący kod do skryptu.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Kliknij przycisk **Uruchom**. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą bez serwera puli platformy Spark](get-started-analyze-spark.md)
