---
title: Wprowadzenie do obsługi wersji — usługa Azure HDInsight
description: Dowiedz się, jak działa przechowywanie wersji w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493870"
---
# <a name="how-versioning-works-in-hdinsight"></a>Jak działa przechowywanie wersji w usłudze HDInsight

Usługa HDInsight ma dwa główne składniki: dostawcę zasobów i składniki Apache Hadoop, które są wdrożone w klastrze. 

## <a name="hdinsight-resource-provider"></a>Dostawca zasobów usługi HDInsight

Zasoby na platformie Azure są udostępniane przez dostawcę zasobów. Dostawca zasobów usługi HDInsight jest odpowiedzialny za tworzenie i usuwanie klastrów oraz zarządzanie nimi.

## <a name="hdinsight-images"></a>Obrazy usługi HDInsight

Usługa HDInsight używa obrazów do łączenia składników oprogramowania typu "open source" (OSS), które można wdrożyć w klastrze. Te obrazy zawierają podstawowy system operacyjny Ubuntu i podstawowe składniki, takie jak Spark, Hadoop, Kafka, HBase lub Hive.

## <a name="versioning-in-hdinsight"></a>Przechowywanie wersji w usłudze HDInsight

Firma Microsoft okresowo uaktualnia obrazy i dostawcę zasobów usługi HDInsight w celu uwzględnienia nowych ulepszeń i funkcji.

Nową wersję usługi HDInsight można utworzyć, gdy spełnione są co najmniej jedno z następujących warunków:

- Najważniejsze zmiany lub aktualizacje funkcjonalności dostawcy zasobów usługi HDInsight
- Główne wersje składników usługi OSS
- Istotne zmiany w systemie operacyjnym Ubuntu

Zostanie utworzona nowa wersja obrazu, gdy co najmniej jeden z następujących warunków jest spełniony:

- Wersje główne lub pomocnicze oraz aktualizacje składników usługi OSS
- Poprawki lub poprawki dla składnika w obrazie

## <a name="next-steps"></a>Następne kroki

- [Składniki i wersje oprogramowania Apache w usłudze HDInsight](./hdinsight-component-versioning.md)
