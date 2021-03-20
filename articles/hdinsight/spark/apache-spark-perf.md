---
title: Optymalizowanie zadań platformy Spark pod kątem wydajności — usługa Azure HDInsight
description: Pokaż typowe strategie dla najlepszej wydajności klastrów Apache Spark w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 567fc2637538408d9727d07d3185a5b0e3cf20c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929939"
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
