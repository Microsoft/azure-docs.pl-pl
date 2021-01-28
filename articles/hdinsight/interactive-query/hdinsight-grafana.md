---
title: Korzystanie z Grafana w usłudze Azure HDInsight
description: Dowiedz się, jak uzyskać dostęp do pulpitu nawigacyjnego Grafana za pomocą klastrów Apache Hadoop w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: cd2a09b6c3196302dcb74fd363481706021a4d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940877"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Dostęp do narzędzia Grafana w usłudze Azure HDInsight

[Grafana](https://grafana.com/) to popularny, oparty na grafie i Konstruktor pulpitu nawigacyjnego. Grafana to funkcja rozbudowana; Umożliwia to nie tylko użytkownikom tworzenie dostosowywalnych i udostępnianych pulpitów nawigacyjnych, a także udostępnia pulpity nawigacyjne z szablonami, integrację z protokołem LDAP, wiele źródeł danych i nie tylko.

Obecnie w usłudze Azure HDInsight Grafana jest obsługiwana w przypadku typów klastra Spark, HBase, Kafka i Interactive. Nie jest obsługiwana w przypadku klastrów z włączonym pakietem Enterprise Security Pack.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-an-apache-hadoop-cluster"></a>Tworzenie klastra Apache Hadoop

Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md). W **obszarze Typ klastra** wybierz pozycję **Spark**, **Kafka**, **HBase** lub **zapytanie interaktywne**.

## <a name="access-the-grafana-dashboard"></a>Dostęp do pulpitu nawigacyjnego Grafana

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/grafana/` lokalizacji, gdzie ClusterName jest nazwą klastra.

1. Wprowadź poświadczenia użytkownika klastra usługi Hadoop.

1. Zostanie wyświetlony pulpit nawigacyjny Grafana, który wygląda następująco:

    ![Pulpit nawigacyjny sieci Web usługi HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Pulpit nawigacyjny usługi HDInsight Grafana")

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń klaster, który został utworzony, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.

1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.

1. Na liście wyświetlonych klastrów usługi HDInsight wybierz pozycję **...** obok utworzonego klastra.

1. Wybierz pozycję **Usuń**. Wybierz pozycję **Tak**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat analizowania danych za pomocą usługi HDInsight, zobacz następujące artykuły:

* [Użyj Apache Hive z usługą HDInsight](../hadoop/hdinsight-use-hive.md).

* [Korzystanie z MapReduce z usługą HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Zacznij korzystać z narzędzi programu Visual Studio Hadoop dla usługi HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
