---
title: 'Samouczek: wprowadzenie do analizy danych przy użyciu programu SQL na żądanie'
description: W tym samouczku dowiesz się, jak analizować dane przy użyciu programu SQL na żądanie przy użyciu danych znajdujących się w bazach danych Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101612"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analizowanie danych za pomocą programu SQL na żądanie

W tym samouczku dowiesz się, jak analizować dane przy użyciu programu SQL na żądanie przy użyciu danych znajdujących się w bazach danych Spark. 

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
> [Analizowanie przy użyciu platformy Spark](get-started-analyze-spark.md)
