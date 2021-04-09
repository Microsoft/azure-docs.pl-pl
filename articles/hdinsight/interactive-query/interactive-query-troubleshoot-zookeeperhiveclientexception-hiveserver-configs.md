---
title: Błąd interpretera Zeppelin Apache Hive — usługa Azure HDInsight
description: Interpreter JDBCa Apache Zeppelin Hive wskazuje na nieprawidłowy adres URL w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930743"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenariusz: Apache Hive interpreter Zeppelin daje błąd dozorcy w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

W klastrze programu Apache Hive LLAP interpreter Zeppelin wyświetla następujący komunikat o błędzie podczas próby wykonania zapytania:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Przyczyna

Interpreter JDBCa Zeppelin Hive wskazuje na nieprawidłowy adres URL.

## <a name="resolution"></a>Rozwiązanie

1. Przejdź do podsumowania składnika Hive i skopiuj adres URL "Hive JDBC" do Schowka.

1. Przejdź do strony `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Edytuj ustawienia JDBC: zaktualizuj wartość Hive. URL do adresu URL JDBC programu Hive skopiowanego w kroku 1

1. Zapisz, a następnie ponów próbę wykonania zapytania

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]