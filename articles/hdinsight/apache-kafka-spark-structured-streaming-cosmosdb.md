---
title: Apache Spark & Apache Kafka z usługą Cosmos DB Azure HDInsight
description: Dowiedz się, jak za pomocą Apache Spark strukturalnych przesyłania strumieniowego odczytywać dane z Apache Kafka a następnie zapisywać je w Azure Cosmos DB. W tym przykładzie dane są przesyłane strumieniowo przy użyciu Jupyter Notebook z platformy Spark w usłudze HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/18/2019
ms.openlocfilehash: d78b629e90903c58b98de86f425f0c1225d90997
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867053"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Używanie Apache Spark strukturalnych przesyłania strumieniowego z Apache Kafka i Azure Cosmos DB

Dowiedz się, jak za pomocą [Apache Spark](https://spark.apache.org/) [strukturalnych przesyłania strumieniowego](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) odczytywać dane z [Apache Kafka](https://kafka.apache.org/) w usłudze Azure HDInsight, a następnie przechowywać je w Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie dystrybuowana, wielomodelowa baza danych. W tym przykładzie zastosowano model bazy danych interfejsu API SQL. Aby uzyskać więcej informacji, zobacz dokument [Witamy w Azure Cosmos DB](../cosmos-db/introduction.md) .

Przesyłanie strumieniowe ze strukturą platformy Spark korzysta z aparatu przetwarzania strumienia opartego na module Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych. Aby uzyskać więcej informacji na temat przesyłania strumieniowego ze strukturą, zobacz [Przewodnik programowania przesyłania strumieniowego](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) w programie Apache.org.

> [!IMPORTANT]  
> W tym przykładzie użyto platformy Spark 2,2 w usłudze HDInsight 3,6.
>
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystkie rozmowy do Kafka muszą znajdować się w tej samej sieci wirtualnej platformy Azure co węzły w klastrze Kafka. W tym przykładzie zarówno klastry Kafka, jak i Spark znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono sposób przepływu komunikacji między klastrami:

:::image type="content" source="./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png" alt-text="Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure" border="false":::

> [!NOTE]  
> Komunikacja usługi Kafka jest ograniczona do sieci wirtualnej. Inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Mimo że można utworzyć ręcznie klastry usługi Azure Virtual Network, Kafka i Spark, łatwiej jest użyć szablonu Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć usługi Azure Virtual Network, Kafka i Spark w ramach subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Szablon Azure Resource Manager znajduje się w repozytorium GitHub dla tego projektu ( [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) ).

    Ten szablon umożliwia utworzenie następujących zasobów:

   * Klaster Kafka w usłudze HDInsight 3.6.

   * Klaster Spark w usłudze HDInsight 3,6.

   * Sieć wirtualna platformy Azure zawierająca klastry usługi HDInsight. Sieć wirtualna utworzona przez szablon używa przestrzeni adresowej 10.0.0.0/16.

   * Azure Cosmos DB baza danych interfejsu API SQL.

    > [!IMPORTANT]  
    > Używany w tym przykładzie notes do przesyłania strumieniowego ze strukturą wymaga platformy Spark w usłudze HDInsight 3.6. W przypadku użycia starszej wersji platformy Spark w usłudze HDInsight podczas korzystania z notesu wystąpią błędy.

1. Poniższe informacje służą do wypełniania wpisów w sekcji **wdrożenie niestandardowe** :

    |Właściwość |Wartość |
    |---|---|
    |Subskrypcja|Wybierz swoją subskrypcję platformy Azure.|
    |Grupa zasobów|Utwórz grupę lub wybierz istniejącą. Ta grupa zawiera klaster usługi HDInsight.|
    |Nazwa konta Cosmos DB|Ta wartość jest używana jako nazwa konta Cosmos DB. Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.|
    |Nazwa klastra podstawowego|Ta wartość jest używana jako podstawowa nazwa klastrów Spark i Kafka. Na przykład wprowadzenie **myhdi** tworzy klaster Spark o nazwie __Spark-Myhdi__ i klaster Kafka o nazwie **Kafka-myhdi**.|
    |Wersja klastra|Wersja klastra usługi HDInsight. Ten przykład jest testowany z usługą HDInsight 3,6 i może nie współpracować z innymi typami klastrów.|
    |Nazwa użytkownika logowania klastra|Nazwa użytkownika administratora dla klastrów Spark i Kafka.|
    |Hasło logowania klastra|Hasło administratora dla klastrów Spark i Kafka.|
    |Nazwa użytkownika SSH|Użytkownik SSH do utworzenia dla klastrów Spark i Kafka.|
    |Hasło ssh|Hasło użytkownika SSH dla klastrów Spark i Kafka.|

    :::image type="content" source="./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png" alt-text="Niestandardowe wartości wdrożenia usługi HDInsight":::

1. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

1. Na koniec wybierz pozycję **Kup**. Utworzenie klastrów, sieci wirtualnych i konta Cosmos DB może potrwać do 45 minut.

## <a name="create-the-cosmos-db-database-and-collection"></a>Tworzenie Cosmos DB bazy danych i kolekcji

W projekcie używanym w tym dokumencie są przechowywane dane w Cosmos DB. Przed uruchomieniem kodu należy najpierw utworzyć _bazę danych_ i _kolekcję_ w wystąpieniu Cosmos DB. Należy również pobrać punkt końcowy dokumentu oraz _klucz_ używany do uwierzytelniania żądań do Cosmos DB.

Jednym ze sposobów jest użycie [interfejsu wiersza polecenia platformy Azure](/cli/azure/). Poniższy skrypt spowoduje utworzenie bazy danych o nazwie `kafkadata` i kolekcji o nazwie `kafkacollection` . Następnie zwraca klucz podstawowy.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter Notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

Informacje o punkcie końcowym dokumentu i kluczu podstawowym są podobne do następujących:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Zapisz punkt końcowy i wartości klucza, ponieważ są one zbędne w notesach Jupyter.

## <a name="get-the-notebooks"></a>Pobierz notesy

Kod dla przykładu opisanego w tym dokumencie jest dostępny pod adresem [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) .

## <a name="upload-the-notebooks"></a>Przekazywanie notesów

Wykonaj następujące kroki, aby przekazać notesy z projektu do platformy Spark w klastrze usługi HDInsight:

1. W przeglądarce sieci Web Połącz się z Jupyter Notebook w klastrze Spark. Zastąp element `CLUSTERNAME` w poniższym adresie URL nazwą klastra __Spark__:

    ```http
    https://CLUSTERNAME.azurehdinsight.net/jupyter
    ```

    Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

2. W prawym górnym rogu strony Użyj przycisku __upload (Przekaż__ ), aby przekazać plik __Stream-taksówks-Data-Kafka. ipynb__ do klastra. Wybierz pozycję __Otwórz__, aby rozpocząć przekazywanie.

3. Znajdź wpis __Stream-taksówks-Data-Kafka. ipynb__ na liście notesów, a następnie wybierz przycisk __Przekaż__ obok niego.

4. Powtórz kroki 1-3, aby załadować Notes __Stream-Data-from-Kafka-to-Cosmos-DB. ipynb__ .

## <a name="load-taxi-data-into-kafka"></a>Ładowanie danych z taksówki do Kafka

Po przekazaniu plików wybierz wpis __Stream-taksówks-Data-to-Kafka. ipynb__ , aby otworzyć Notes. Postępuj zgodnie z instrukcjami w notesie, aby załadować dane do Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Przetwarzanie danych z taksówki przy użyciu funkcji przetwarzania strumieniowego platformy Spark

Na stronie głównej [Jupyter Notebook](https://jupyter.org/) wybierz wpis __Stream-Data-from-Kafka-to-Cosmos-DB. ipynb__ . Postępuj zgodnie z instrukcjami w notesie, aby przesyłać strumieniowo dane z Kafka i do Azure Cosmos DB przy użyciu funkcji przesyłania strumieniowego platformy Spark.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać Apache Spark ze strukturą przesyłania strumieniowego, zobacz następujące dokumenty, aby dowiedzieć się więcej o pracy z Apache Spark, Apache Kafka i Azure Cosmos DB:

* [Jak używać Apache Spark streaming (DStream) z Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Rozpocznij od Jupyter Notebook i Apache Spark w usłudze HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Azure Cosmos DB — Zapraszamy!](../cosmos-db/introduction.md)
