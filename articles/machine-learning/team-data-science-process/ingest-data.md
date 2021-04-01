---
title: Ładowanie danych do środowisk usługi Azure Storage — proces nauki danych zespołu
description: Dowiedz się więcej o sposobie pozyskiwania danych w różnych środowiskach docelowych, w których dane są przechowywane i przetwarzane.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96452658"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Ładowanie danych w środowiskach magazynowania do celów analizy

Proces nauki danych zespołu wymaga pozyskania danych lub załadowania ich do najbardziej odpowiedniego sposobu na każdym etapie. Miejsca docelowe danych mogą obejmować Blob Storage Azure, bazy danych SQL Azure, SQL Server na maszynach wirtualnych platformy Azure, usłudze HDInsight (Hadoop), usłudze Azure Synapse Analytics i Azure Machine Learning. 

W poniższych artykułach opisano sposób pozyskiwania danych w różnych środowiskach docelowych, w których dane są przechowywane i przetwarzane.

* Do/z [usługi Azure Blob Storage](move-azure-blob.md)
* Aby [SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md)
* Aby [Azure SQL Database](move-sql-azure.md)
* Do [tabel programu Hive](move-hive-tables.md)
* Na [partycjonowane tabele SQL](parallel-load-sql-partitioned-tables.md)
* Z [SQL Server lokalnych](move-sql-azure-adf.md)

Wymagania techniczne i biznesowe, a także początkowa lokalizacja, format i rozmiar danych określają najlepszy plan pozyskiwania danych. Najlepszym planem jest wykonanie kilku kroków. Ta sekwencja zadań może obejmować na przykład eksplorację danych, wstępne przetwarzanie, czyszczenie, próbkowanie w dół i uczenie modeli.  Azure Data Factory to zalecany zasób platformy Azure służący do organizowania przenoszenia i przekształcania danych.
