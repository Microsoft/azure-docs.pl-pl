---
title: Apache Spark przesyłania strumieniowego przy użyciu Apache Kafka — Azure HDInsight
description: Dowiedz się, jak używać Apache Spark do przesyłania strumieniowego danych do Apache Kafka lub z nich przy użyciu DStreams. Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e2f4bbe6157e1affa4975f6dcee2ad0f1d98a71b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086096"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Przykład Apache Spark streaming (DStream) z Apache Kafka w usłudze HDInsight

Dowiedz się, jak używać [Apache Spark](https://spark.apache.org/) do przesyłania strumieniowego danych do [Apache Kafka](https://kafka.apache.org/) i z nich w usłudze HDInsight przy użyciu [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). W tym przykładzie zastosowano [Jupyter Notebook](https://jupyter.org/) , które są uruchamiane w klastrze Spark.

> [!NOTE]  
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

> [!IMPORTANT]  
> W tym przykładzie jest używany DStreams, który jest starszą technologią przetwarzania strumieniowego Spark. Aby zapoznać się z przykładem używającym nowszych funkcji przesyłania strumieniowego platformy Spark, zobacz dokumentowanie [strukturalne Streaming with Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) .

## <a name="create-the-clusters"></a>Tworzenie klastrów

Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystkie rozmowy do Kafka muszą znajdować się w tej samej sieci wirtualnej platformy Azure co węzły w klastrze Kafka. W tym przykładzie zarówno klastry Kafka, jak i Spark znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono sposób przepływu komunikacji między klastrami:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Mimo że Kafka samo jest ograniczone do komunikacji w ramach sieci wirtualnej, inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Mimo że można utworzyć ręcznie klastry usługi Azure Virtual Network, Kafka i Spark, łatwiej jest użyć szablonu Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć usługi Azure Virtual Network, Kafka i Spark w ramach subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon Azure Resource Manager znajduje się w lokalizacji **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia utworzenie klastra Kafka zawierającego trzy węzły procesu roboczego.

    Ten szablon służy do tworzenia klastra usługi HDInsight 3,6 dla programów Kafka i Spark.

1. Poniższe informacje służą do wypełniania wpisów w sekcji **wdrożenie niestandardowe** :

    |Właściwość |Wartość |
    |---|---|
    |Grupa zasobów|Utwórz grupę lub wybierz istniejącą.|
    |Lokalizacja|Wybierz lokalizację geograficzną blisko siebie.|
    |Nazwa klastra podstawowego|Ta wartość jest używana jako podstawowa nazwa klastrów Spark i Kafka. Na przykład wprowadzenie **hdistreaming** tworzy klaster Spark o nazwie __Spark-Hdistreaming__ i klaster Kafka o nazwie **Kafka-hdistreaming**.|
    |Nazwa użytkownika logowania klastra|Nazwa użytkownika administratora dla klastrów Spark i Kafka.|
    |Hasło logowania klastra|Hasło administratora dla klastrów Spark i Kafka.|
    |Nazwa użytkownika SSH|Użytkownik SSH do utworzenia dla klastrów Spark i Kafka.|
    |Hasło SSH|Hasło użytkownika SSH dla klastrów Spark i Kafka.|

    ![Niestandardowe parametry wdrożenia usługi HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

1. Na koniec wybierz pozycję **Kup**. Utworzenie klastrów trwa około 20 minut.

Po utworzeniu zasobów zostanie wyświetlona strona podsumowanie.

![Podsumowanie grupy zasobów dla sieci wirtualnej i klastrów](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Zwróć uwagę, że nazwy klastrów usługi HDInsight to **Spark-basename** i **Kafka-basename**, gdzie basename jest nazwą dostarczoną z szablonem. Te nazwy są używane w dalszych krokach podczas łączenia się z klastrami.

## <a name="use-the-notebooks"></a>Korzystanie z notesów

Kod dla przykładu opisanego w tym dokumencie jest dostępny pod adresem [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki przedstawione w tym dokumencie tworzą oba klastry w tej samej grupie zasobów platformy Azure, można usunąć grupę zasobów w Azure Portal. Usunięcie grupy spowoduje usunięcie wszystkich zasobów utworzonych przez następujący dokument, Virtual Network platformy Azure i konto magazynu używane przez klastry.

## <a name="next-steps"></a>Następne kroki

W tym przykładzie przedstawiono sposób odczytu i zapisu do Kafka przy użyciu platformy Spark. Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Kafka:

* [Wprowadzenie do Apache Kafka w usłudze HDInsight](kafka/apache-kafka-get-started.md)
* [Tworzenie repliki Apache Kafka w usłudze HDInsight przy użyciu programu narzędzia MirrorMaker](kafka/apache-kafka-mirroring.md)
* [Używanie Apache Storm z Apache Kafka w usłudze HDInsight](hdinsight-apache-storm-with-kafka.md)
