---
title: Machine Learning w usłudze Azure Synapse Analytics
description: Omówienie możliwości uczenia maszynowego w usłudze Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222143"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Możliwości uczenia maszynowego w usłudze Azure Synapse Analytics

Usługa Azure Synapse Analytics oferuje różne możliwości uczenia maszynowego. Ten artykuł zawiera omówienie sposobu stosowania Machine Learning w kontekście usługi Azure Synapse.

To omówienie obejmuje różne możliwości Synapse związane z uczeniem maszynowym z perspektywy procesu nauki o danych.

Być może wiesz, jak wygląda typowy proces nauki o danych. Jest to dobrze znany proces, który jest zgodny z większością projektów uczenia maszynowego.

Na wysokim poziomie proces obejmuje następujące kroki:
* (Zrozumienie biznesowe)
* Pozyskiwanie danych i ich analiza
* Modelowanie
* Wdrażanie i ocenianie modelu

W tym artykule opisano możliwości uczenia maszynowego w usłudze Azure Synapse w różnych aparatach analizy z perspektywy przetwarzania danych. Dla każdego kroku w procesie nauki o danych można uzyskać podsumowanie możliwości usługi Azure Synapse.

## <a name="azure-synapse-machine-learning-capabilities"></a>Możliwości uczenia maszynowego na platformie Azure Synapse

### <a name="data-acquisition-and-understanding"></a>Pozyskiwanie danych i ich analiza

Większość projektów uczenia maszynowego obejmuje dobrze ustanowione kroki, a jednym z tych kroków jest uzyskanie dostępu do danych i ich zrozumienie.

#### <a name="data-source-and-pipelines"></a>Źródło danych i potoki

Dzięki [Azure Data Factory](../../data-factory/introduction.md)wbudowanej w sposób natywny części usługi Azure Synapse istnieje zaawansowany zestaw narzędzi do pozyskiwania danych i potoków aranżacji danych. Dzięki temu można łatwo tworzyć potoki danych w celu uzyskiwania dostępu do danych i przekształcania ich w format, który można wykorzystać do uczenia maszynowego. [Dowiedz się więcej na temat potoków danych](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) w programie Synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Przygotowywanie i Eksploracja i Wizualizacja danych

Ważną częścią procesu uczenia maszynowego jest zrozumienie danych przez eksplorowanie i wizualizacje.

W zależności od tego, gdzie są przechowywane dane, Synapse oferuje zestaw różnych narzędzi do eksplorowania i przygotowania do analizy i uczenia maszynowego. Jednym z najszybszych sposobów rozpoczynania pracy z eksploracją danych jest używanie Apache Spark lub bezserwerowych pul SQL bezpośrednio za pośrednictwem danych w usłudze Data Lake.

* [Apache Spark usługi Azure Synapse](../spark/apache-spark-overview.md) oferuje funkcje umożliwiające przekształcanie, przygotowywanie i Eksplorowanie danych w odpowiedniej skali. Te pule platformy Spark oferują narzędzia takie jak PySpark/Python, Scala i .NET na potrzeby przetwarzania danych na dużą skalę. Korzystając z zaawansowanych bibliotek wizualizacji, środowisko eksploracji danych można ulepszyć, aby lepiej zrozumieć dane. [Dowiedz się więcej o sposobach eksplorowania i wizualizowania danych w programie Synapse przy użyciu platformy Spark](../get-started-analyze-spark.md).

* [Pule SQL bezserwerowe](../sql/on-demand-workspace-overview.md) umożliwiają Eksplorowanie danych za pomocą usługi TSQL bezpośrednio za pośrednictwem usługi Data Lake. Pule SQL bezserwerowe oferują również wbudowane wizualizacje w programie Synapse Studio. [Dowiedz się więcej o tym, jak eksplorować dane przy użyciu pul SQL bezserwerowych](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modelowanie

W usłudze Azure Synapse można przeprowadzić szkolenia modeli uczenia maszynowego na pulach Apache Spark przy użyciu narzędzi, takich jak PySpark/Python, Scala i .NET.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Uczenie modeli w pulach Spark za pomocą MLlib

Modele uczenia maszynowego mogą być szkolony z pomocą różnych algorytmów i bibliotek. [Platforma Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) oferuje skalowalne algorytmy uczenia maszynowego, które mogą pomóc w rozwiązywaniu typowych problemów z uczeniem maszynowym. W [tym samouczku](../spark/apache-spark-machine-learning-mllib-notebook.md) opisano sposób uczenia modelu przy użyciu MLlib w Synapse.

Oprócz MLlib, popularne biblioteki, takie jak [Scikit](https://scikit-learn.org/stable/) , mogą być również używane do tworzenia modeli. Aby uzyskać szczegółowe informacje na temat instalowania bibliotek w pulach Synapse Spark, zobacz [Zarządzanie bibliotekami Apache Spark w usłudze Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md) .

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Uczenie modeli przy użyciu Azure Machine Learning zautomatyzowanej ML

Innym sposobem uczenia modeli uczenia maszynowego, które nie wymagają dużej znajomości uczenia maszynowego, jest użycie zautomatyzowanej ML. [Automatyczna ml](../../machine-learning/concept-automated-ml.md) to funkcja, która automatycznie pociąga za zestaw modeli uczenia maszynowego i umożliwia użytkownikowi wybranie najlepszego modelu na podstawie określonych metryk. Dzięki bezproblemowej integracji z Azure Machine Learningami z notesów platformy Azure Synapse użytkownicy mogą łatwo korzystać z zautomatyzowanej ML w Synapse przy użyciu uwierzytelniania Azure Active Directory przekazywania.  Oznacza to, że wystarczy wskazać obszar roboczy Azure Machine Learning i nie musisz wprowadzać żadnych poświadczeń. Poniżej znajduje się [samouczek zautomatyzowanych](../spark/apache-spark-azure-machine-learning-tutorial.md) tablic, który opisuje sposób uczenia modeli przy użyciu Azure Machine Learning zautomatyzowanej ml w pulach Synapse Spark.

### <a name="model-deployment-and-scoring"></a>Wdrażanie i ocenianie modelu

Do oceniania partii można łatwo używać modeli, które zostały przeszkolone na platformie Azure Synapse lub poza platformą Azure Synapse. Obecnie w programie Synapse istnieją dwa sposoby uruchamiania oceniania wsadowego.

* Możesz użyć [funkcji przewidywania TSQL](../sql-data-warehouse/sql-data-warehouse-predict.md) w Synapse pule SQL, aby uruchamiać przewidywania, w których znajdują się dane. Ta zaawansowana i skalowalna funkcja umożliwia Wzbogacanie danych bez konieczności przechodzenia do magazynu danych. Wprowadzono nowe [środowisko modelu uczenia maszynowego w programie Synapse Studio](./tutorial-sql-pool-model-scoring-wizard.md) , w którym można WDROŻYĆ model ONNX z rejestru modelu Azure Machine Learning w Synapse pule SQL na potrzeby oceniania wsadowego przy użyciu funkcji przewidywania.

* Innym rozwiązaniem wsadowym modeli uczenia maszynowego w usłudze Azure Synapse jest wykorzystanie pul Apache Spark dla usługi Azure Synapse. W zależności od bibliotek używanych do uczenia modeli możesz użyć środowiska kodu do uruchomienia oceniania partii.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Synapse Analytics](../get-started.md)
* [Tworzenie obszaru roboczego](../get-started-create-workspace.md)
* [Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w programie Synapse](quickstart-integrate-azure-machine-learning.md)
* [Samouczek: Kreator oceniania modelu uczenia maszynowego — dedykowana Pula SQL](tutorial-sql-pool-model-scoring-wizard.md)