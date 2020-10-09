---
title: Analiza na platformie HDInsight Spark z PySpark, Scala — zespołowe przetwarzanie danych
description: Przykłady procesu nauka danych zespołu, który przeprowadzi Cię przez użycie PySpark i scala na Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75864149"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Przewodniki dotyczące analizy danych w usłudze HDInsight Spark przy użyciu PySpark i scala na platformie Azure

Te instruktaże wykorzystują PySpark i Scala w klastrze platformy Azure Spark w celu przeprowadzania analiz predykcyjnych. Postępują zgodnie z krokami opisanymi w procesie nauki o danych zespołowych. Aby zapoznać się z omówieniem procesu nauki o danych zespołowych, zobacz [proces nauki o danych](overview.md). Aby zapoznać się z omówieniem platformy Spark w usłudze HDInsight, zobacz [wprowadzenie do platformy Spark w usłudze HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Dodatkowe przewodniki dotyczące analizy danych, które wykonują proces nauki o danych zespołowych, są pogrupowane według **platformy** , z której korzystają. Zobacz [przewodniki wykonujące proces nauki danych zespołu](walkthroughs.md) , aby zapoznać się z przykładami.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Przewidywanie wskazówek dotyczących taksówki przy użyciu PySpark na platformie Azure Spark

Za pomocą nowych danych o taksówkach w [usłudze Azure HDInsight wskazówki dotyczące korzystania z platformy Spark w ramach przewodnika dotyczącego usługi](spark-overview.md) , w której znajduje się Porada i zakres przewidywanych kwot. W tym przykładzie użyto zespołowego procesu nauki o danych w scenariuszu korzystającego z [Azure HDInsight Spark klastra](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania i opisywania danych inżynierów z publicznie dostępnego zestawu danych podróży i opłat za taksówkę z NYC. Ten temat zawiera informacje dotyczące klastrów usługi HDInsight Spark i Jupyter PySpark. Te notesy pokazują, jak eksplorować dane, a następnie jak tworzyć i korzystać z modeli. Zaawansowany Notes eksploracji i modelowania danych pokazuje, jak uwzględnić wzajemne sprawdzanie poprawności, czyszczenie parametrów funkcji Hyper-i obliczanie modelu.

### <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie danych za pomocą platformy Spark 
Eksplorowanie zestawu danych i tworzenie, Ocena i Ocena modeli uczenia maszynowego przez pracę w temacie [Create Binary Modeling and regresji dla danych za pomocą narzędzia Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

### <a name="model-consumption"></a>Użycie modelu
Aby dowiedzieć się, jak oszacować modele klasyfikacji i regresji utworzone w tym temacie, zobacz [ocenę i ocenę modeli uczenia maszynowego opartych na platformie Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Czyszczenie z przekroczeniem poprawności i parametry
Zobacz [Zaawansowane eksplorowanie i modelowanie danych za pomocą platformy Spark](spark-advanced-data-exploration-modeling.md) , w jaki sposób można przeszkoleć modele przy użyciu weryfikacji krzyżowej i funkcji Hyper-Parameter.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Przewidywanie wskazówek dotyczących taksówki przy użyciu Scala na platformie Azure Spark

W instruktażu [Scala z platformą Spark w systemie Azure](scala-walkthrough.md) jest przewidywane, czy Porada jest płatna, oraz jaki jest zakres kwot, które należy zapłacić. Pokazano, jak używać Scala do nadzorowanych zadań uczenia maszynowego z pakietami MLlib (Spark Machine Learning Library) i SparkML w klastrze Azure HDInsight Spark. Przeprowadzi Cię przez zadania, które stanowią [proces analizy danych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): pozyskiwanie i Eksploracja danych, Wizualizacja, Inżynieria funkcji, modelowanie i użycie modelu. Modele skompilowane obejmują regresję logistyczną i liniową, lasy losowe i podwyższające gradienty drzew.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem procesu nauki o danych zespołowych, zobacz [Omówienie procesu nauka danych zespołu](overview.md).

Aby zapoznać się z omówieniem cyklu życia procesu nauki o danych zespołowych, zobacz [cykl życia procesu nauki o danych zespołowych](lifecycle.md). Ten cykl życia przedstawia kroki od początku do końca, które zwykle są wykonywane podczas wykonywania projektów. 

