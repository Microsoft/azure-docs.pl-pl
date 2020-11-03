---
title: Usługa Apache Ambari tez umożliwia spowolnienie ładowania w usłudze Azure HDInsight
description: Widok tez Apache Ambari może ładować się wolno lub nie ładować w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288844"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenariusz: usługa Apache Ambari tez umożliwia spowolnienie ładowania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Widok tez Apache Ambari może ładować się wolno lub nie można go załadować. Podczas ładowania widoku Ambari tez mogą być widoczne procesy na węzłów głównych, które nie odpowiadają.

## <a name="cause"></a>Przyczyna

Dostęp do interfejsów API przędzy ATS może czasami mieć niską wydajność w przypadku klastrów utworzonych przed OCT 2017, gdy w klastrze jest uruchomionych wiele zadań Hive.

## <a name="resolution"></a>Rozwiązanie

Jest to problem, który został rozwiązany w październiku 2017. Ponowne utworzenie klastra spowoduje, że ten problem nie zostanie uruchomiony ponownie.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]