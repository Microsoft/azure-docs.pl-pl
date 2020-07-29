---
title: Optymalizowanie zadań platformy Spark pod kątem wydajności — usługa Azure HDInsight
description: Pokaż typowe strategie dla najlepszej wydajności klastrów Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117963"
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
