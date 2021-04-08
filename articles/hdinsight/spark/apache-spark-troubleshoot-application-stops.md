---
title: Apache Spark aplikacji przesyłania strumieniowego zatrzymanej po upływie 24 dni w usłudze Azure HDInsight
description: Aplikacja przesyłania strumieniowego Apache Spark przestanie działać po upływie 24 dni i nie ma żadnych błędów w plikach dziennika.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929471"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenariusz: Apache Spark przetrzymywanie aplikacji przesyłania strumieniowego po 24 dniach w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja przesyłania strumieniowego Apache Spark przestanie działać po upływie 24 dni i nie ma żadnych błędów w plikach dziennika.

## <a name="cause"></a>Przyczyna

`livy.server.session.timeout`Wartość określa, jak długo Apache usługi Livy powinien czekać na zakończenie sesji. Gdy długość sesji osiągnie `session.timeout` wartość, sesja usługi Livy i aplikacja są automatycznie zabijane.

## <a name="resolution"></a>Rozwiązanie

W przypadku długotrwałych zadań Zwiększ wartość `livy.server.session.timeout` przy użyciu interfejsu użytkownika Ambari. Możesz uzyskać dostęp do konfiguracji usługi Livy z interfejsu użytkownika Ambari przy użyciu adresu URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Zamień `<yourclustername>` na nazwę klastra usługi HDInsight, jak pokazano w portalu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]