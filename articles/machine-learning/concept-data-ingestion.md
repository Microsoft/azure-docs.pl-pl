---
title: Automatyzacja & pozyskiwania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o opcjach pozyskiwania danych służących do uczenia modeli uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python
ms.openlocfilehash: 401398da4d71f32973f720dd0ca5cc9b550892e8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323039"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Opcje pozyskiwania danych dla przepływów pracy Azure Machine Learning

Ten artykuł zawiera informacje o profesjonalistach i wadach opcji pozyskiwania danych dostępnych w programie Azure Machine Learning. 

Wybierz spośród opcji:
+ [Azure Data Factory](#azure-data-factory) potoki, specjalnie utworzone do wyodrębniania, ładowania i przekształcania danych

+ [Azure Machine Learning Python SDK](#azure-machine-learning-python-sdk), dostarczając niestandardowego rozwiązania do przetwarzania danych.

+ połączenie obu

Pozyskiwanie danych to proces, w którym dane bez struktury są wyodrębniane z jednego lub wielu źródeł, a następnie przygotowane do szkolenia modeli uczenia maszynowego. Jest to również czasochłonne, szczególnie jeśli jest wykonywane ręcznie, i jeśli masz duże ilości danych z wielu źródeł. Automatyzacja tego wysiłku powoduje zwolnienie zasobów i gwarantuje, że modele korzystają z najnowszych i odpowiednich danych.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) oferuje natywną obsługę monitorowania i wyzwalaczy źródła danych na potrzeby potoków pozyskiwania danych.  

W poniższej tabeli zestawiono zalety i wady korzystania z Azure Data Factory dla przepływów pracy pozyskiwania danych.

|Zalety|Wady
---|---
Specjalnie skompilowany do wyodrębniania, ładowania i przekształcania danych.|Obecnie oferuje ograniczony zestaw Azure Data Factory zadań potoku 
Umożliwia tworzenie przepływów pracy opartych na danych na potrzeby organizowania przenoszenia i przekształcania danych na dużą skalę.|Kosztowne Konstruowanie i konserwowanie. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) Azure Data Factory.
Integracja z różnymi narzędziami platformy Azure, takimi jak [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) i [Azure Functions](../data-factory/control-flow-azure-function-activity.md) | Nie uruchamiaj natywnie skryptów, zamiast polega na osobnym obliczeniu dla przebiegów skryptów 
Natywnie obsługuje pozyskiwanie danych wyzwalane przez źródło danych| 
Procesy przygotowywania i uczenia modeli są niezależne.|
Możliwość odtworzenia danych osadzonych dla Azure Data Factory dataflows|
Zapewnia [interfejs użytkownika](../data-factory/quickstart-create-data-factory-portal.md) o małym kodzie dla podejść niezwiązanych ze skryptami |

Te kroki i Poniższy diagram ilustrują przepływ pracy pozyskiwania danych Azure Data Factory.

1. Ściąganie danych ze źródeł
1. Przekształcanie i zapisywanie danych w docelowym kontenerze obiektów blob, który służy jako magazyn danych dla Azure Machine Learning
1. Przy zachowaniu przygotowanych danych potok Azure Data Factory wywołuje potok Machine Learning szkolenia, który odbiera przygotowane dane do szkolenia modelu


    ![Pozyskiwanie danych ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Dowiedz się, jak utworzyć potok pozyskiwania danych dla Machine Learning z [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK 

Za pomocą [zestawu SDK języka Python](/python/api/overview/azure/ml)można uwzględnić zadania pozyskiwania danych w ramach kroku [potoku Azure Machine Learning](how-to-create-your-first-pipeline.md) .

Poniższa tabela zawiera podsumowanie specjalistów i con przy użyciu zestawu SDK oraz krok potoku w przypadku zadań pozyskiwania danych.

Zalety| Wady
---|---
Konfigurowanie własnych skryptów języka Python | Nie obsługuje natywnie obsługi zmiany źródła danych. Wymaga aplikacji logiki lub implementacji funkcji platformy Azure
Przygotowanie danych w ramach każdego modelu wykonywania szkoleń|Wymaga umiejętności programistycznych do utworzenia skryptu pozyskiwania danych
Obsługuje skrypty przygotowywania danych w różnych obiektach docelowych obliczeń, w tym [Azure Machine Learning COMPUTE](concept-compute-target.md#azure-machine-learning-compute-managed) |Nie zapewnia interfejsu użytkownika do tworzenia mechanizmu pozyskiwania

Na poniższym diagramie potok Azure Machine Learning składa się z dwóch kroków: pozyskiwanie danych i uczenie modeli. Krok pozyskiwania danych obejmuje zadania, które można wykonać przy użyciu bibliotek języka Python i zestawu SDK języka Python, takich jak wyodrębnianie danych ze źródeł lokalnych/internetowych i przekształceń danych, takich jak brak przypisywania wartości. Krok szkolenia następnie używa przygotowanego danych jako danych wejściowych do skryptu szkoleniowego, aby szkolić model uczenia maszynowego. 

![Pozyskiwanie danych z usługi Azure Pipeline + zestaw SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z następującymi artykułami:
* [Tworzenie potoku pozyskiwania danych za pomocą Azure Data Factory](how-to-data-ingest-adf.md)

* [Automatyzuj potoki pozyskiwania danych i zarządzaj nimi za pomocą Azure Pipelines](how-to-cicd-data-ingestion.md).