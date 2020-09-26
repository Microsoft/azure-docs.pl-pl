---
title: Bezpieczny dostęp do danych w chmurze
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak bezpiecznie łączyć się z danymi za pomocą Azure Machine Learning oraz jak używać zestawów danych i magazynów danych dla zadań w usłudze ML. Magazyny danych mogą przechowywać dane z obiektu blob platformy Azure, Azure Data Lake Gen 1 & 2, SQL DB i Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: ac0b3c151b379096ffb60e049d18b8f6219b1445
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359685"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Bezpieczny dostęp do danych w Azure Machine Learning

Azure Machine Learning ułatwia łączenie się z danymi w chmurze.  Zapewnia warstwę abstrakcji za pośrednictwem podstawowej usługi magazynu, dzięki czemu można bezpiecznie uzyskać dostęp do danych i korzystać z nich bez konieczności pisania kodu specyficznego dla typu magazynu. Azure Machine Learning udostępnia również następujące możliwości danych:

*    Współdziałanie z Pandas i Spark dataframes
*    Przechowywanie wersji i śledzenie elementów odnoszących się do danych
*    Etykietowanie danych 
*    Monitorowanie dryfu danych
    
## <a name="data-workflow"></a>Przepływ danych

Gdy wszystko będzie gotowe do użycia danych w rozwiązaniu do magazynowania w chmurze, zalecamy wykonanie następującego przepływu pracy dostarczania danych. W tym przepływie pracy założono, że masz [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) i dane w usłudze magazynu w chmurze na platformie Azure. 

1. Utwórz [Magazyn danych Azure Machine Learning](#datastores) , aby przechowywać informacje o połączeniu z usługą Azure Storage.

2. Z tego magazynu [danych utwórz Azure Machine Learning DataSet](#datasets) , aby wskazywał określone pliki w magazynie bazowym. 

3. Aby użyć tego zestawu danych w eksperymentie uczenia maszynowego, możesz
    1. Zainstaluj go w miejscu docelowym obliczeń eksperymentu na potrzeby szkolenia modeli.

        **ORAZ** 

    1. Korzystaj bezpośrednio z rozwiązań Azure Machine Learning, takich jak zautomatyzowany przebiegi eksperymentów uczenia maszynowego (zautomatyzowany ML), potoki uczenia maszynowego lub [projektant Azure Machine Learning](concept-designer.md).

4. Utwórz [Monitory zestawu danych](#data-drift) dla wyjściowego zestawu danych modelu, aby wykryć dryf danych. 

5. Jeśli wykryto dryfowanie danych, zaktualizuj wejściowy zestaw danych i odpowiednio ponownie naszkol model.

Na poniższym diagramie przedstawiono wizualizację tego zalecanego przepływu pracy.

![Na diagramie przedstawiono usługę Azure Storage, która jest przesyłana do magazynu danych, który jest przesyłany do elementu DataSet. Zestaw danych przenosi do szkolenia modelu, który przechodzi do dryfu danych, który przechodzi z powrotem do zestawu danych.](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Magazyny danych

Azure Machine Learning magazyny danych bezpiecznie przechowują informacje o połączeniu z usługą Azure Storage, dzięki czemu nie trzeba będzie kodu w skryptach. [Zarejestruj i Utwórz magazyn](how-to-access-data.md) danych, aby łatwo nawiązać połączenie z kontem magazynu i uzyskać dostęp do danych w podstawowej usłudze Azure Storage. 

Obsługiwane usługi magazynu w chmurze na platformie Azure, które mogą być rejestrowane jako magazyny danych:

+ Kontener obiektów blob platformy Azure
+ Udział plików platformy Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ System plików usługi Databricks
+ Azure Database for MySQL

## <a name="datasets"></a>Zestawy danych

Azure Machine Learning zestawy danych to odwołania wskazujące na dane w usłudze magazynu. Nie są to kopie dataBy, które tworzą zestaw danych Azure Machine Learning, utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. 

Ponieważ zestawy danych są opóźnieniem oceniane, a dane pozostają w istniejącej lokalizacji, należy

* Nie Powiąż dodatkowego kosztu magazynowania.
* Nie ryzykuj przypadkowego zmiany oryginalnych źródeł danych.
* Zwiększ szybkość działania przepływu pracy w usłudze ML.

Aby móc korzystać z danych w magazynie, [Utwórz zestaw danych](how-to-create-register-datasets.md) , który będzie pakować dane do obiektu, który można wywoływać, w celu wykonywania zadań uczenia maszynowego. Zarejestruj zestaw danych w obszarze roboczym, aby udostępnić go i ponownie użyć w różnych eksperymentach bez złożonych danych.

Zestawy danych mogą być tworzone na podstawie plików lokalnych, publicznych adresów URL, [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/)lub usług Azure Storage za pośrednictwem magazynów danych. 

Istnieją dwa typy zestawów danych: 

+ [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py&preserve-view=true) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Jeśli dane zostały już oczyszczone i gotowe do użycia w eksperymentach szkoleniowych, możesz [pobrać lub zainstalować pliki](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) , do których odwołuje się FileDatasets, do obiektu docelowego obliczeń.

+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Aby ułatwić manipulowanie i czyszczenie, można załadować TabularDataset do Pandas lub Spark Dataframe. Aby uzyskać pełną listę formatów danych, z których można utworzyć TabularDatasets, zobacz [Klasa TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

Dodatkowe możliwości zestawów danych można znaleźć w następującej dokumentacji:

+ [Wersja i śledzenie](how-to-version-track-datasets.md) zestawu danych.
+ [Monitoruj zestaw danych](how-to-monitor-datasets.md) , aby pomóc w wykrywaniu dryfowania danych.    

## <a name="work-with-your-data"></a>Pracuj z danymi

Zestawy danych umożliwiają wykonywanie wielu zadań uczenia maszynowego poprzez bezproblemowe integrację z funkcjami Azure Machine Learning. 

+ Utwórz [projekt etykietowania danych](#label).
+ Uczenie modeli uczenia maszynowego:
     + [zautomatyzowane eksperymenty ML](how-to-use-automated-ml-for-ml-models.md)
     + [Projektant](tutorial-designer-automobile-price-train-score.md#import-data)
     + [żadnym](how-to-train-with-datasets.md)
     + [Potoki Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Uzyskaj dostęp do zestawów danych do oceniania za pomocą [wnioskowania wsadowego](how-to-use-parallel-run-step.md) w [potokach uczenia maszynowego](how-to-create-your-first-pipeline.md).
+ Skonfiguruj monitor zestawu danych na potrzeby wykrywania [dryfowania danych](#drift) .

<a name="label"></a>

## <a name="data-labeling"></a>Etykietowanie danych

Etykietowanie dużych ilości danych często było kłopotliwej w projektach uczenia maszynowego. Te ze składnikiem przetwarzania obrazów, takie jak Klasyfikacja obrazu lub wykrywanie obiektów, zwykle wymagają tysięcy obrazów i odpowiednich etykiet.

Azure Machine Learning stanowi centralną lokalizację do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi. Etykietowanie projektów ułatwia koordynowanie danych, etykiet i członków zespołu, co pozwala efektywniej zarządzać zadaniami etykietowania. Obecnie obsługiwane zadania to klasyfikacja obrazów, wiele etykiet lub wiele klas oraz identyfikacja obiektów przy użyciu powiązanych pól.

Utwórz [projekt etykietowania danych](how-to-create-labeling-projects.md)i wyjściowy zestaw danych do użycia w eksperymentach uczenia maszynowego.

<a name="drift"></a>

## <a name="data-drift"></a>Dryfowanie danych

W kontekście uczenia maszynowego, dryfowanie danych to zmiana danych wejściowych modelu, które prowadzą do obniżenia wydajności modelu. Jest to jedna z najważniejszych przyczyn, które pogorszą się w miarę upływu czasu, dlatego monitorowanie dryfowania danych pomaga wykrywać problemy z wydajnością modelu.

Zapoznaj się z artykułem [Tworzenie monitora zestawu danych](how-to-monitor-datasets.md) , aby dowiedzieć się więcej na temat wykrywania i generowania alertów dotyczących dryfowania danych w nowych danych w zestawie danych.

## <a name="next-steps"></a>Następne kroki 

+ Utwórz zestaw danych w programie Azure Machine Learning Studio lub za pomocą zestawu SDK języka Python, [wykonując następujące kroki.](how-to-create-register-datasets.md)
+ Wypróbuj przykłady szkoleniowe dotyczące zestawu danych, korzystając z naszych [przykładowych notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
