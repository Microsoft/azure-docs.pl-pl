---
title: RpcTimeoutException Apache Spark Thrift — Azure HDInsight
description: Podczas przetwarzania dużych zestawów danych przy użyciu Apache Spark Thrift Server są wyświetlane błędy 502
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932703"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenariusz: RpcTimeoutException dla Apache Spark Thrift Server w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja Spark kończy się niepowodzeniem z `org.apache.spark.rpc.RpcTimeoutException` powodu wyjątku i komunikatu: `Futures timed out` , jak w poniższym przykładzie:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` i `overhead limit exceeded` błędy mogą również pojawić się w `sparkthriftdriver.log` jak w poniższym przykładzie:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Przyczyna

Te błędy są spowodowane brakiem zasobów pamięci podczas przetwarzania danych. Jeśli proces odzyskiwania pamięci Java zostanie uruchomiony, może to spowodować, że aplikacja Spark przestanie odpowiadać. Zapytania zaczynają przekroczyć limit czasu i przestaną być przetwarzane. Ten `Futures timed out` błąd wskazuje klaster pod silnym obciążeniem.

## <a name="resolution"></a>Rozwiązanie

Zwiększ rozmiar klastra, dodając więcej węzłów procesów roboczych lub zwiększając pojemność pamięci istniejących węzłów klastra. Możesz również dostosować Potok danych, aby zmniejszyć ilość danych przetwarzanych jednocześnie.

`spark.network.timeout`Kontroluje limit czasu dla wszystkich połączeń sieciowych. Zwiększenie limitu czasu sieci może spowodować więcej czasu na zakończenie niektórych krytycznych operacji, ale nie spowoduje to całkowitego rozwiązania problemu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]