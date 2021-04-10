---
title: Rozwiązywanie problemów Apache Spark w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługami Apache Spark i Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: b54b9d932505ada890ac21c1b8de3178ad2f0042
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867512"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformą Apache Spark za pomocą usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania podczas pracy z Apache Spark ładunkiami w programie [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą usługi Apache Ambari w klastrach?

Wartości konfiguracji platformy Spark można dostrajać w celu uniknięcia wyjątku aplikacji Apache Spark `OutofMemoryError` . Poniższe kroki pokazują domyślne wartości konfiguracji platformy Spark w usłudze Azure HDInsight:

1. Zaloguj się do Ambari przy `https://CLUSTERNAME.azurehdidnsight.net` użyciu poświadczeń klastra. Na ekranie początkowym zostanie wyświetlony pulpit nawigacyjny przegląd. Istnieją niewielkie różnice między usługami HDInsight 3,6 i 4,0.

1. Przejdź do **Spark2**  >  **configs**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Wybierz kartę konfiguracje" border="true":::

1. Na liście konfiguracji wybierz i rozwiń pozycję **Custom-spark2-Defaults**.

1. Wyszukaj ustawienie wartości, które należy dostosować, takie jak **spark.executor. Memory**. W tym przypadku wartość **9728m** jest zbyt wysoka.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Wybierz pozycję niestandardowe-Spark-Defaults" border="true":::

1. Ustaw wartość na zalecane ustawienie. Wartość **2048m** jest zalecana dla tego ustawienia.

1. Zapisz wartość, a następnie Zapisz konfigurację. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Zmień wartość na 2048m" border="true":::

    Napisz uwagi dotyczące zmian konfiguracji, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Wprowadź adnotację dotyczącą wprowadzonych zmian" border="true":::

    Otrzymasz powiadomienie, jeśli jakieś konfiguracje wymagają uwagi. Zanotuj elementy, a następnie wybierz pozycję **kontynuować mimo wszystko**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Wybierz pozycję nadal pomimo" border="true":::

1. Po zapisaniu konfiguracji zostanie wyświetlony monit o ponowne uruchomienie usługi. Wybierz pozycję **Uruchom ponownie**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Wybierz pozycję Uruchom ponownie" border="true":::

    Potwierdź ponowne uruchomienie.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Wybierz pozycję Potwierdź ponowne uruchomienie wszystkich" border="true":::

    Można przejrzeć procesy, które są uruchomione.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Przeglądanie uruchomionych procesów" border="true":::

1. Można dodać konfiguracje. Na liście konfiguracji wybierz pozycję **Custom-spark2-Defaults**, a następnie wybierz pozycję **Dodaj właściwość**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Wybierz pozycję Dodaj właściwość" border="true":::

1. Zdefiniuj nową właściwość. Można zdefiniować pojedynczą właściwość przy użyciu okna dialogowego dla określonych ustawień, takich jak typ danych. Lub można zdefiniować wiele właściwości przy użyciu jednej definicji dla każdego wiersza.

    W tym przykładzie właściwość **Spark. Driver. Memory** jest zdefiniowana z wartością **4G**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Definiuj nową właściwość" border="true":::

1. Zapisz konfigurację, a następnie uruchom ponownie usługę zgodnie z opisem w sekcji kroki 6 i 7.

Te zmiany są w całym klastrze, ale można je zastąpić podczas przesyłania zadania platformy Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak mogę skonfigurować aplikację Apache Spark przy użyciu Jupyter Notebook w klastrach?

W pierwszej komórce Jupyter Notebook po dyrektywie **konfiguracji%%** Określ konfiguracje platformy Spark w prawidłowym formacie JSON. W razie potrzeby zmień wartości rzeczywiste:

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Dodawanie konfiguracji" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą usługi Apache Livy w klastrach?

Prześlij aplikację Spark do usługi Livy przy użyciu klienta REST, takiego jak zwinięcie. Użyj polecenia podobnego do poniższego. W razie potrzeby zmień wartości rzeczywiste:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak skonfigurować aplikację platformy Apache Spark za pomocą polecenia spark-submit w klastrach?

Uruchom powłokę Spark, używając polecenia podobnego do poniższego. W razie potrzeby zmień rzeczywistą wartość konfiguracji:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Materiały uzupełniające

[Apache Spark przesyłanie zadania w klastrach usługi HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* [Zarządzanie pamięcią Spark — Omówienie](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugowanie aplikacji Spark w klastrach usługi HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).