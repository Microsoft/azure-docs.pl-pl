---
title: Usługa Apache Ambari tez umożliwia spowolnienie ładowania w usłudze Azure HDInsight
description: Widok tez Apache Ambari może ładować się wolno lub nie ładować w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930765"
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