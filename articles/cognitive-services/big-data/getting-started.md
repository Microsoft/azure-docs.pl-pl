---
title: Wprowadzenie do Cognitive Services na potrzeby danych Big Data
description: Skonfiguruj potok MMLSpark za pomocą Cognitive Services w Azure Databricks i uruchom przykład.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460509"
---
# <a name="getting-started"></a>Wprowadzenie

Skonfigurowanie środowiska to pierwszy krok tworzenia potoku dla danych. Gdy środowisko będzie gotowe, uruchamianie przykładu jest szybkie i łatwe.

W tym artykule wykonamy następujące kroki, aby rozpocząć pracę:

1. [Tworzenie zasobu usług Cognitive Services](#create-a-cognitive-services-resource)
1. [Tworzenie klastra Apache Spark](#create-an-apache-spark-cluster)
1. [Wypróbuj przykład](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

Aby używać Cognitive Services danych Big Data, należy najpierw utworzyć usługę poznawczej dla naszego przepływu pracy. Istnieją dwa główne typy Cognitive Services: usługi w chmurze hostowane na platformie Azure i usługi kontenerowe zarządzane przez użytkowników. Zalecamy rozpoczęcie od prostszej Cognitive Services opartej na chmurze.

### <a name="cloud-services"></a>Usługi w chmurze

Cognitive Services oparte na chmurze to inteligentne algorytmy hostowane na platformie Azure. Te usługi są gotowe do użycia bez uczenia się, że wystarczy tylko połączenie internetowe. [Usługę poznawczej można utworzyć w Azure Portal](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Usługi kontenerowe (opcjonalnie)

Jeśli w aplikacji lub obciążeniu są używane duże zestawy danych, wymagana jest sieć prywatna lub nie można skontaktować się z chmurą, dzięki czemu komunikacja z usługami w chmurze może być niemożliwa. W tej sytuacji kontener Cognitive Services ma następujące korzyści:

* **Niska łączność**: można wdrożyć kontenery Cognitive Services w dowolnym środowisku obliczeniowym, zarówno w chmurze, jak i wyłączone. Jeśli aplikacja nie może skontaktować się z chmurą, rozważ wdrożenie Cognitive Services kontenerów w aplikacji.

* **Małe opóźnienia**: ponieważ usługi kontenerów nie wymagają komunikacji dwukierunkowej z chmurą, odpowiedzi są zwracane z znacznie mniejszymi opóźnieniami.

* **Prywatność i bezpieczeństwo danych**: można wdrożyć usługi kontenerów w sieciach prywatnych, dzięki czemu poufne dane nie opuszczają sieci.

* **Wysoka skalowalność**: usługi kontenerowe nie mają "limitów szybkości" i działają na komputerach zarządzanych przez użytkownika. Dzięki temu można skalować Cognitive Services bez końca do obsługi znacznie większych obciążeń.

Postępuj zgodnie z [tym przewodnikiem](../cognitive-services-container-support.md?tabs=luis) , aby utworzyć kontener usługi poznawczej.

## <a name="create-an-apache-spark-cluster"></a>Tworzenie klastra platformy Apache Spark

[Apache Spark &trade; ](http://spark.apache.org/) jest rozproszonym środowiskiem obliczeniowym przeznaczonym do przetwarzania danych Big Data. Użytkownicy mogą korzystać z Apache Spark na platformie Azure z usługami takimi jak Azure Databricks, Azure Synapse Analytics, HDInsight i Azure Kubernetes Services. Aby użyć Cognitive Services danych Big Data, należy najpierw utworzyć klaster. Jeśli masz już klaster Spark, możesz spróbować skorzystać z przykładu.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks to platforma analityczna oparta na Apache Spark z jednym kliknięciem, usprawnione przepływy pracy i interaktywny obszar roboczy. Często są one używane do współpracy między analitykami danych, inżynierami i specjalistami biznesowymi. Aby użyć Cognitive Services danych Big Data na Azure Databricks, wykonaj następujące kroki:

1. [Tworzenie obszaru roboczego usługi Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Tworzenie klastra Spark w usłudze Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Zainstaluj Cognitive Services danych Big Data
    * Utwórz nową bibliotekę w obszarze roboczym datakosteks  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Wprowadź następujące współrzędne Maven współrzędne:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` repozytorium: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Instalowanie biblioteki w klastrze  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Analiza usługi Azure Synapse (opcjonalnie)

Opcjonalnie możesz użyć Synapse Analytics, aby utworzyć klaster Spark. Usługa Azure Synapse Analytics umożliwia łączenie z magazynem danych w przedsiębiorstwie i analizą danych Big Data. Zapewnia to swobodę wykonywania zapytań dotyczących danych na Twoich warunkach przy użyciu niezależnych od serwera zasobów na żądanie lub aprowizacji. Aby rozpocząć korzystanie z usługi Azure Synapse Analytics, wykonaj następujące kroki:

1. [Utwórz obszar roboczy Synapse (wersja zapoznawcza)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Utwórz nową pulę Apache Spark bezserwerową (wersja zapoznawcza) przy użyciu Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

W usłudze Azure Synapse Analytics dane big data dla Cognitive Services są instalowane domyślnie.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Jeśli używasz kontenerów Cognitive Services, jedną popularną opcją wdrożenia platformy Spark obok kontenerów jest usługa Azure Kubernetes.

Aby rozpocząć pracę z usługą Azure Kubernetes, wykonaj następujące kroki:

1. [wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu witryny Azure Portal](../../aks/kubernetes-walkthrough-portal.md)
1. [Instalowanie wykresu Apache Spark 2.4.0 Helm](https://hub.helm.sh/charts/microsoft/spark)
1. [Instalowanie kontenera usługi poznawczej przy użyciu Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Wypróbuj przykład

Po skonfigurowaniu klastra i środowiska Spark można uruchomić krótki przykład. W tej sekcji pokazano, jak używać danych Big Data dla Cognitive Services w Azure Databricks.

Najpierw można utworzyć Notes w Azure Databricks. W przypadku innych dostawców klastrów platformy Spark Użyj swoich notesów lub przesyłania na platformę Spark.

1. Utwórz nowy Notes datacegły, wybierając pozycję **Nowy Notes** z menu **Azure Databricks** .

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. W oknie dialogowym **Tworzenie notesu** wprowadź nazwę, wybierz opcję **Python** jako język i wybierz utworzony wcześniej klaster Spark.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Wybierz pozycję **Utwórz**.

1. Wklej ten fragment kodu do nowego notesu.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Pobierz klucz subskrypcji z menu **klucze i punkt końcowy** z pulpitu nawigacyjnego analiza tekstu w Azure Portal.
1. Zastąp symbol zastępczy klucza subskrypcji w kodzie notesu usługi datamarkets kluczem subskrypcji.
1. Wybierz opcję Odtwórz lub Trójkąt, symbol w prawym górnym rogu komórki notesu, aby uruchomić przykład. Opcjonalnie wybierz opcję **Uruchom wszystko** w górnej części notesu, aby uruchomić wszystkie komórki. Odpowiedzi będą wyświetlane poniżej komórki w tabeli.

### <a name="expected-results"></a>Oczekiwane wyniki

| tekst                                      |   tonacji |
|:------------------------------------------|------------:|
| Dziś mam szczęście, Sunny!           |   0,978959  |
| Jestem sfrustrowani przez ten szczytuy ruch godzinny |   0,0237956 |
| Usługi poznawcze w usłudze Spark aint są nieprawidłowe  |   0,888896  |

## <a name="next-steps"></a>Następne kroki

- [Krótkie przykłady w języku Python](samples-python.md)
- [Krótkie przykłady Scala](samples-scala.md)
- [Przepis: konserwacja predykcyjna](recipes/anomaly-detection.md)
- [Przepis: inteligentne Eksploracja](recipes/art-explorer.md)