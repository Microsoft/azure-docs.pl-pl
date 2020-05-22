---
title: Optymalizowanie zadań platformy Spark pod kątem wydajności — usługa Azure HDInsight
description: Pokaż typowe strategie dla najlepszej wydajności klastrów Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780106"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optymalizowanie Apache Spark zadań w usłudze HDInsight

Ten artykuł zawiera omówienie strategii optymalizacji Apache Spark zadań w usłudze Azure HDInsight.

## <a name="overview"></a>Omówienie

Wydajność zadań Apache Spark zależy od wielu czynników. Te czynniki wydajności obejmują: sposób przechowywania danych, sposób konfigurowania klastra oraz operacje, które są używane podczas przetwarzania danych.

Często spotykane wyzwania mogą obejmować ograniczenia dotyczące pamięci z powodu nieprawidłowych procesów wykonawców, długotrwałych operacji i zadań, które powodują operacje kartezjańskiego.

Istnieją również różne strategie, które mogą pomóc w pokonaniu tych wyzwań, takich jak buforowanie i Zezwalanie na pochylenie danych.

W każdym z poniższych artykułów można znaleźć typowe wyzwania i rozwiązania dla różnych aspektów optymalizacji platformy Spark.

* [Optymalizowanie magazynu danych](optimize-data-storage.md)
* [Optymalizowanie przetwarzania danych](optimize-data-processing.md)
* [Optymalizuj użycie pamięci](optimize-memory-usage.md)
* [Optymalizowanie konfiguracji klastra](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Następne kroki

* [Debugowanie zadań platformy Apache Spark uruchomionych w usłudze Azure HDInsight](apache-spark-job-debugging.md)
* [Zarządzanie zasobami klastra Apache Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)
