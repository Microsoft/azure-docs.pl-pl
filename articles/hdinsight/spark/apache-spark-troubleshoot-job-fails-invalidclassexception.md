---
title: Błąd InvalidClassException Apache Spark w usłudze Azure HDInsight
description: Zadanie Apache Spark nie powiodło się z InvalidClassException, niezgodność wersji klasy w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 6220c328d05e7cd68460b7bfd0708a9d393a290f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287894"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Zadanie Apache Spark nie powiodło się z InvalidClassException, niezgodność wersji klasy w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podjęto próbę utworzenia zadania Apache Spark w klastrze Spark 2. x. Kończy się niepowodzeniem z powodu błędu podobnego do poniższego:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany dodaniem dodatkowego jar do `spark.yarn.jars` konfiguracji, w odcieniu jar, który zawiera inną wersję `commons-lang3` pakietu i wprowadza niezgodność klas. Domyślnie platforma Spark 2.1/2/3 używa wersji 3,5 programu `commons-lang3` .

> [!TIP]
> Aby cieniować bibliotekę, należy umieścić jej zawartość we własnym jar, zmieniając jej pakiet. Różni się to od pakowania biblioteki, która umieszcza bibliotekę w twoim własnym jar bez ponownego pakowania.

## <a name="resolution"></a>Rozwiązanie

Usuń plik JAR lub ponownie skompiluj dostosowany plik JAR (AzureLogAppender), a następnie Przemieść klasy przy użyciu [Maven-odcieni-wtyczki](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) .

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]