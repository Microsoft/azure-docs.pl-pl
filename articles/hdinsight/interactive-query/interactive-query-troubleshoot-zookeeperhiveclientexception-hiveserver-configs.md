---
title: Błąd interpretera Zeppelin Apache Hive — usługa Azure HDInsight
description: Interpreter JDBCa Apache Zeppelin Hive wskazuje na nieprawidłowy adres URL w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8e66db96f895d0d29d6c5018478e98c1f39e62ad
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288702"
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