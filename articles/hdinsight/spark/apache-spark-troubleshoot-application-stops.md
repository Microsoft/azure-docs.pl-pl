---
title: Apache Spark aplikacji przesyłania strumieniowego zatrzymanej po upływie 24 dni w usłudze Azure HDInsight
description: Aplikacja przesyłania strumieniowego Apache Spark przestanie działać po upływie 24 dni i nie ma żadnych błędów w plikach dziennika.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288025"
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