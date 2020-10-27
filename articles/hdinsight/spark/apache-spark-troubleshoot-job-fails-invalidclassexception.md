---
title: Błąd InvalidClassException Apache Spark w usłudze Azure HDInsight
description: Zadanie Apache Spark nie powiodło się z InvalidClassException, niezgodność wersji klasy w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 070dd50f76f5c7ae2289b93b7c2a9b069fb0b0a2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539075"
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

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).