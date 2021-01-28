---
title: Usługa Apache HBase nie odpowiada na żądania w usłudze Azure HDInsight
description: Rozwiązywanie problemu z usługą Apache HBase nie odpowiada na żądania w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: b42a37d752f245eb66f3952b56ee4e36ae954874
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936960"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenariusz: Platforma Apache HBase nie odpowiada na żądania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Usługa Apache HBase REST nie odpowiada na żądania w usłudze Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyną może być to, że w tym przypadku usługa Apache HBase REST jest przyczyną przecieków gniazd, które są szczególnie powszechne, gdy usługa była uruchomiona przez długi czas (na przykład miesiące). W zestawie SDK klienta może zostać wyświetlony komunikat o błędzie podobny do:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Rozwiązanie

Uruchom ponownie HBase REST przy użyciu poniższego polecenia po SSHing do hosta. Możesz również użyć akcji skryptu, aby ponownie uruchomić tę usługę na wszystkich węzłach procesu roboczego:

```bash
sudo service hdinsight-hbrest restart
```

To polecenie spowoduje zatrzymanie serwera regionu HBase na tym samym hoście. Można ręcznie uruchomić serwer regionu HBase za pomocą Ambari lub pozwolić Ambari funkcji automatycznego ponownego uruchamiania, aby przywrócić automatyczne odzyskiwanie serwera regionu HBase.

Jeśli problem nadal występuje, można zainstalować następujący skrypt zaradczy jako zadanie firmy CRONUS, które jest uruchamiane co 5 minut na każdym węźle procesu roboczego. Ten skrypt zaradczy wysyła polecenie ping do usługi REST i uruchamia ją ponownie na wypadek, gdyby usługa REST nie odpowiadała.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]