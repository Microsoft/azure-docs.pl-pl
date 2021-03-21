---
title: Optymalizowanie klastrów przy użyciu platformy Apache Ambari w usłudze Azure HDInsight
description: Użyj interfejsu użytkownika sieci Web Apache Ambari w celu skonfigurowania i zoptymalizowania klastrów usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 14268e58296554cedc9488e48b41719421797a57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933274"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Optymalizowanie klastrów przy użyciu platformy Apache Ambari w usłudze Azure HDInsight

Usługa HDInsight udostępnia klastry Apache Hadoop dla aplikacji do przetwarzania danych na dużą skalę. Zarządzanie, monitorowanie i optymalizowanie złożonych klastrów wielowęzłowych może być trudne. Apache Ambari to interfejs sieci Web umożliwiający zarządzanie i monitorowanie klastrów usługi HDInsight w systemie Linux.

Aby zapoznać się z wprowadzeniem do korzystania z interfejsu użytkownika sieci Web Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Zaloguj się do Ambari przy `https://CLUSTERNAME.azurehdidnsight.net` użyciu poświadczeń klastra. Na ekranie początkowym zostanie wyświetlony pulpit nawigacyjny przegląd.

![Wyświetlany pulpit nawigacyjny użytkownika Apache Ambari](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Interfejs użytkownika sieci Web Ambari służy do zarządzania hostami, usługami, alertami, konfiguracjami i widokami. Nie można użyć Ambari do utworzenia klastra usługi HDInsight lub usług uaktualnienia. Nie można też zarządzać stosami i wersjami, likwidowaniem lub rewizją hostów ani dodawać usług do klastra.

## <a name="manage-your-clusters-configuration"></a>Zarządzanie konfiguracją klastra

Ustawienia konfiguracji ułatwiają dostrajanie określonej usługi. Aby zmodyfikować ustawienia konfiguracji usługi, wybierz usługę z paska bocznego **usług** (po lewej stronie). Następnie przejdź **do karty konfiguracje** na stronie Szczegóły usługi.

![Pasek boczny usług Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Modyfikuj rozmiar sterty Java NameNode

Rozmiar sterty Java NameNode zależy od wielu czynników, takich jak obciążenie klastra. Ponadto liczby plików i numery bloków. Domyślny rozmiar 1 GB działa dobrze w przypadku większości klastrów, chociaż niektóre obciążenia mogą wymagać więcej lub mniejszej ilości pamięci.

Aby zmodyfikować rozmiar sterty języka Java NameNode:

1. Na pasku bocznym usługi wybierz pozycję **HDFS** i przejdź **do karty konfiguracje** .

    ![Konfiguracja systemu Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Znajdź ustawienie **NameNode rozmiar sterty Java**. Możesz również użyć pola tekstowego **Filtr** , aby wpisać i znaleźć określone ustawienie. Wybierz ikonę **pióra** obok nazwy ustawienia.

    ![Rozmiar sterty Java Ambari NameNode](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Wpisz nową wartość w polu tekstowym, a następnie naciśnij klawisz **Enter** , aby zapisać zmiany.

    ![Ambari Edytuj NameNode stertę Java size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Rozmiar sterty Java NameNode jest zmieniany na 1 GB z 2 GB.

    ![Edytowanie sterty NameNode Java Size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Zapisz zmiany, klikając zielony przycisk **Zapisz** znajdujący się u góry ekranu konfiguracja.

    !["Ambari zapisywania konfiguracji" Apache](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optymalizowanie usługi Apache HBase](./optimize-hbase-ambari.md)
* [Optymalizowanie technologii Apache Hive](./optimize-hive-ambari.md)
* [Optymalizowanie platformy Apache Pig](./optimize-pig-ambari.md)
