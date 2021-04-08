---
title: Analiza na Azure HDInsight Hadoop przy użyciu procesu analizy danych w zespole Hive
description: Przykłady procesu nauka danych zespołu, który przeprowadzi Cię przez użycie usługi Hive w Azure HDInsight Hadoop w celu przeprowadzenia analizy predykcyjnej.
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
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864166"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Przewodniki dotyczące analizy danych usługi Hadoop w usłudze HDInsight przy użyciu programu Hive na platformie Azure 

Te instruktaże używają usługi Hive z klastrem usługi HDInsight Hadoop w celu przeprowadzania analizy predykcyjnej. Postępują zgodnie z krokami opisanymi w procesie nauki o danych zespołowych. Aby zapoznać się z omówieniem procesu nauki o danych zespołowych, zobacz [proces nauki o danych](overview.md). Aby zapoznać się z wprowadzeniem do usługi Azure HDInsight, zobacz [wprowadzenie do usługi Azure HDInsight, stosu technologii Hadoop i klastrów Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Dodatkowe przewodniki dotyczące analizy danych, które wykonują proces nauki o danych zespołowych, są pogrupowane według **platformy** , z której korzystają. Zobacz [przewodniki wykonujące proces nauki danych zespołu](walkthroughs.md) , aby zapoznać się z przykładami.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Przewidywanie wskazówek dotyczących taksówki przy użyciu platformy Hive z usługą HDInsight Hadoop

W instruktażu [Korzystanie z klastrów usługi HDInsight Hadoop](hive-walkthrough.md) wykorzystuje dane z nowych taksówki do przewidywania: 

- Czy Porada jest płatna 
- Rozkład kwot TIP

Scenariusz jest implementowany przy użyciu programu Hive z [klastrem Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Dowiesz się, jak przechowywać i eksplorować dane inżynierów oraz korzystać z nich z publicznie dostępnego zestawu danych o wykorzystaniu i taryfy za taksówkę NYC. Do kompilowania i wdrażania modeli służy również Azure Machine Learning.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Przewidywanie kliknięć anonsów przy użyciu programu Hive z usługą HDInsight Hadoop

W przypadku [klastrów użycia Azure HDInsight Hadoop na 1 TB zestawu danych](hive-criteo-walkthrough.md) są używane publicznie dostępne [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) kliknij zestaw danych, aby przewidzieć, czy Porada jest płatna i oczekiwane kwoty. Scenariusz jest implementowany przy użyciu programu Hive z [klastrem Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania, tworzenia inżynierów funkcji i w dół przykładowych danych. Używa Azure Machine Learning do kompilowania, uczenia i oceny binarnego modelu klasyfikacji, który przewidywalnuje, czy użytkownik kliknie anons. Instruktaż zawiera informacje na temat publikowania jednego z tych modeli jako usługi sieci Web.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem najważniejszych składników, które składają się na proces nauki o danych zespołowych, zobacz [Omówienie procesu nauki o danych zespołowych](overview.md).

Aby zapoznać się z omówieniem cyklu życia procesu nauki o danych zespołu, którego możesz użyć do struktury projektów analizy danych, zobacz [cykl życia procesu nauki o danych zespołowych](lifecycle.md). Cykl życia przedstawia kroki od początku do końca, które projekty zwykle są wykonywane podczas wykonywania. 

