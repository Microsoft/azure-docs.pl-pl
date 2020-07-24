---
title: 'Samouczek: Rozpoczynanie pracy z usługą Azure Synapse Analytics'
description: W tym samouczku przedstawiono podstawowe kroki konfigurowania usługi Azure Synapse Analytics i korzystania z niej.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075870"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Rozpoczynanie pracy z usługą Azure Synapse Analytics

Ten samouczek to przewodnik krok po kroku dotyczący głównych obszarów funkcji usługi Azure Synapse Analytics. Samouczek to idealny punkt wyjścia dla kogoś, kto chce zapoznać się z przewodnikiem w najważniejszych scenariuszach analizy usługi Azure Synapse. Po wykonaniu kroków opisanych w samouczku będziesz mieć w pełni funkcjonalny obszar roboczy Synapse, w którym można rozpocząć analizowanie danych przy użyciu SQL, SQL na żądanie i Apache Spark.

Dowiesz się:
* Udostępnianie obszaru roboczego Synapse w ramach subskrypcji platformy Azure
* Konfigurowanie kontroli dostępu na koncie ADLSGEN2, dzięki czemu bezproblemowo współpracuje z obszarem roboczym Synapse
* Ładowanie przykładowych danych NYCTaxi do obszaru roboczego Synapse, dzięki czemu mogą one być używane przez SQL, SQL na żądanie i Spark
* Edytuj i uruchamiaj skrypty SQL i notesy Synapse przy użyciu programu Synapse Studio
* Kwerenda tabel SQL i tabel platformy Spark
* Ładowanie danych z tabel SQL do usługi Spark dataframes
* Ładowanie danych do tabel SQL z programu Spark dataframes
* Eksplorowanie zawartości konta ADLSGEN2
* Analizowanie Parquet datafiles na kontach ADLSGEN2 przy użyciu platformy Spark i SQL na żądanie 
* Tworzenie potoku danych, który automatycznie uruchamia Notes Synapse co godzinę

Wykonaj *kroki opisane poniżej, aby skorzystać* z wielu możliwości i dowiedzieć się, jak korzystać z podstawowych funkcji programu.

* [Krok 1 — Tworzenie i Konfigurowanie obszaru roboczego Synapse](get-started-create-workspace.md)
* [Krok 2. analizowanie przy użyciu puli SQL](get-started-analyze-sql-pool.md)
* [Krok 3. analizowanie przy użyciu platformy Spark](get-started-analyze-spark.md)
* [Krok 4. analizowanie przy użyciu programu SQL na żądanie](get-started-analyze-sql-on-demand.md)
* [Krok 5 — analizowanie danych na koncie magazynu](get-started-analyze-storage.md)
* [Krok 6 — organizowanie przy użyciu potoków](get-started-pipelines.md)
* [Krok 7. Wizualizacja danych za pomocą Power BI](get-started-visualize-power-bi.md)
