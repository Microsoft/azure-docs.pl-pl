---
title: Nieustalony procesor CPU w klastrze Apache HBase — Azure HDInsight
description: Rozwiązywanie problemów z ustalonym procesorem CPU na serwerze regionów w klastrze Apache HBase w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 7d0cf139f06bb296b486d2932d95b53fc1167a5d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937025"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenariusz: niezależny procesor CPU na serwerze regionów w klastrze Apache HBase w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Proces serwera regionu Apache HBase zajmuje się bliskością 200% procesora CPU, co powoduje, że alerty w celu uruchomienia HBase Master proces i klaster nie działają w pełnej pojemności.

## <a name="cause"></a>Przyczyna

Jeśli korzystasz z klastra HBase w wersji 3.4, być może napotkasz potencjalną usterkę, która została spowodowana przez uaktualnienie JDK do wersja 1.7.0 _151. Widocznym objawem jest proces serwera regionu, który rozpoczyna się w pobliżu 200% procesora CPU (aby sprawdzić, czy `top` jest to polecenie. Jeśli istnieje proces zamieszkania w pobliżu 200% procesora CPU, należy uzyskać identyfikator PID i potwierdzić, że jest to proces serwera regionu przez uruchomienie `ps -aux | grep` ).

## <a name="resolution"></a>Rozwiązanie

1. Zainstaluj program JDK 1,8 na wszystkich węzłach klastra w następujący sposób:

    * Uruchom akcję skryptu `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Upewnij się, że wybrano opcję uruchamiania na wszystkich węzłach.

    * Alternatywnie możesz zalogować się do każdego pojedynczego węzła i uruchomić polecenie `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Przejdź do interfejsu użytkownika Ambari — `https://<clusterdnsname>.azurehdinsight.net` .

1. Przejdź do **HBase >konfiguracjami->Advanced->Advanced** `hbase-env configs` i zmień wartość zmiennej `JAVA_HOME` na `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Zapisz zmianę konfiguracji.

1. [Opcjonalne, ale zalecane] [Opróżnianie wszystkich tabel w klastrze](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Z poziomu interfejsu użytkownika Ambari ponownie uruchom wszystkie usługi HBase, które wymagają ponownego uruchomienia.

1. W zależności od danych w klastrze może upłynąć kilka minut, zanim klaster osiągnie stan stabilny. Aby potwierdzić, że klaster osiągnie stan stabilny, należy sprawdzić serwera hmaster interfejs użytkownika (wszystkie serwery regionów powinny być aktywne) z Ambari (Refresh) lub z węzła głównego uruchomić powłoki HBase, a następnie uruchomić polecenie stanu.

Aby sprawdzić, czy uaktualnienie zakończyło się pomyślnie, sprawdź, czy odpowiednie procesy HBase są uruchamiane przy użyciu odpowiedniej wersji języka Java — na przykład sprawdź, czy na serwerze regionów:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]