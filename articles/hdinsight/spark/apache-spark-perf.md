---
title: Optymalizowanie zadań platformy Spark pod kątem wydajności — usługa Azure HDInsight
description: Pokaż typowe strategie dla najlepszej wydajności klastrów Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperfq1
ms.openlocfilehash: fbd0da43e79ee2c27f654f9bd07614e08c12fd30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756932"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optymalizowanie Apache Spark zadań w usłudze HDInsight

Ten artykuł zawiera omówienie strategii optymalizacji Apache Spark zadań w usłudze Azure HDInsight.

## <a name="overview"></a>Omówienie

Wydajność zadań Apache Spark zależy od wielu czynników. Te czynniki wydajności obejmują: sposób przechowywania danych, sposób konfigurowania klastra oraz operacje, które są używane podczas przetwarzania danych.

Często spotykane wyzwania mogą dotyczyć: ograniczenia pamięci z powodu nieprawidłowych wykonawców rozmiarów, długotrwałych operacji i zadań, które powodują operacje kartezjańskiego.

Istnieje również wiele optymalizacji, które mogą pomóc w pokonaniu tych wyzwań, takich jak buforowanie i Zezwalanie na pochylenie danych.

W każdym z poniższych artykułów można znaleźć informacje o różnych aspektach optymalizacji platformy Spark.

* [Optymalizuj magazyn danych dla Apache Spark](optimize-data-storage.md)
* [Optymalizowanie przetwarzania danych dla Apache Spark](optimize-data-processing.md)
* [Optymalizuj użycie pamięci przez Apache Spark](optimize-memory-usage.md)
* [Optymalizuj konfigurację klastra usługi HDInsight dla Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Następne kroki

* [Debugowanie zadań platformy Apache Spark uruchomionych w usłudze Azure HDInsight](apache-spark-job-debugging.md)
* [Zarządzanie zasobami klastra Apache Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)
