---
title: Bezpieczny dostęp do danych w chmurze
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak bezpiecznie łączyć się z magazynem danych na platformie Azure, korzystając z Azure Machine Learning danych i DataSets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 8b73676adbb9aa12e6f3b42dd26bed94b22780a8
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539900"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Bezpieczny dostęp do danych w Azure Machine Learning

Azure Machine Learning ułatwia łączenie się z danymi w chmurze.  Zapewnia warstwę abstrakcji za pośrednictwem podstawowej usługi magazynu, dzięki czemu można bezpiecznie uzyskać dostęp do danych i korzystać z nich bez konieczności pisania kodu specyficznego dla typu magazynu. Azure Machine Learning udostępnia również następujące możliwości danych:

*    Współdziałanie z Pandas i Spark dataframes
*    Przechowywanie wersji i śledzenie elementów odnoszących się do danych
*    Etykietowanie danych 
*    Monitorowanie dryfu danych
    
## <a name="data-workflow"></a>Przepływ danych

Gdy wszystko będzie gotowe do użycia danych w rozwiązaniu do magazynowania w chmurze, zalecamy wykonanie następującego przepływu pracy dostarczania danych. W tym przepływie pracy założono, że masz [konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) i dane w usłudze magazynu w chmurze na platformie Azure. 

1. Utwórz [Magazyn danych Azure Machine Learning](#datastores) , aby przechowywać informacje o połączeniu z usługą Azure Storage.

2. Z tego magazynu [danych utwórz Azure Machine Learning DataSet](#datasets) , aby wskazywał określone pliki w magazynie bazowym. 

3. Aby użyć tego zestawu danych w eksperymentie uczenia maszynowego, możesz
    1. Zainstaluj go w miejscu docelowym obliczeń eksperymentu na potrzeby szkolenia modeli.

        **OR** 

    1. Korzystaj bezpośrednio z rozwiązań Azure Machine Learning, takich jak zautomatyzowany przebiegi eksperymentów uczenia maszynowego (zautomatyzowany ML), potoki uczenia maszynowego lub [projektant Azure Machine Learning](concept-designer.md).

4. Utwórz [Monitory zestawu danych](#drift) dla wyjściowego zestawu danych modelu, aby wykryć dryf danych. 

5. Jeśli wykryto dryfowanie danych, zaktualizuj wejściowy zestaw danych i odpowiednio ponownie naszkol model.

Na poniższym diagramie przedstawiono wizualizację tego zalecanego przepływu pracy.

![Na diagramie przedstawiono usługę Azure Storage, która jest przesyłana do magazynu danych, który jest przesyłany do elementu DataSet. Zestaw danych przenosi do szkolenia modelu, który przechodzi do dryfu danych, który przechodzi z powrotem do zestawu danych.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Łączenie z magazynem za pomocą magazynów danych

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

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>Dane referencyjne w magazynie z zestawami danych

Zbiory danych Azure Machine Learning nie są kopiami Twoich dane. Tworząc zestaw danych, można utworzyć odwołanie do danych w usłudze Storage wraz z kopią jej metadanych. 

Ponieważ zestawy danych są opóźnieniem oceniane, a dane pozostają w istniejącej lokalizacji, należy

* Nie Powiąż dodatkowego kosztu magazynowania.
* Nie ryzykuj przypadkowego zmiany oryginalnych źródeł danych.
* Zwiększ szybkość działania przepływu pracy w usłudze ML.

Aby móc korzystać z danych w magazynie, [Utwórz zestaw danych](how-to-create-register-datasets.md) , który będzie pakować dane do obiektu, który można wywoływać, w celu wykonywania zadań uczenia maszynowego. Zarejestruj zestaw danych w obszarze roboczym, aby udostępnić go i ponownie użyć w różnych eksperymentach bez złożonych danych.

Zestawy danych mogą być tworzone na podstawie plików lokalnych, publicznych adresów URL, [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/)lub usług Azure Storage za pośrednictwem magazynów danych. 

Istnieją dwa typy zestawów danych: 

+ [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset?preserve-view=true&view=azure-ml-py) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Jeśli dane zostały już oczyszczone i gotowe do użycia w eksperymentach szkoleniowych, możesz [pobrać lub zainstalować pliki](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) , do których odwołuje się FileDatasets, do obiektu docelowego obliczeń.

+ [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Aby ułatwić manipulowanie i czyszczenie, można załadować TabularDataset do Pandas lub Spark Dataframe. Aby uzyskać pełną listę formatów danych, z których można utworzyć TabularDatasets, zobacz [Klasa TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

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
+ Uzyskaj dostęp do zestawów danych do oceniania za pomocą [wnioskowania wsadowego](./tutorial-pipeline-batch-scoring-classification.md) w [potokach uczenia maszynowego](how-to-create-your-first-pipeline.md).
+ Skonfiguruj monitor zestawu danych na potrzeby wykrywania [dryfowania danych](#drift) .

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Etykieta danych z projektami etykietowania danych

Etykietowanie dużych ilości danych często było kłopotliwej w projektach uczenia maszynowego. Te ze składnikiem przetwarzania obrazów, takie jak Klasyfikacja obrazu lub wykrywanie obiektów, zwykle wymagają tysięcy obrazów i odpowiednich etykiet.

Azure Machine Learning stanowi centralną lokalizację do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi. Etykietowanie projektów ułatwia koordynowanie danych, etykiet i członków zespołu, co pozwala efektywniej zarządzać zadaniami etykietowania. Obecnie obsługiwane zadania to klasyfikacja obrazów, wiele etykiet lub wiele klas oraz identyfikacja obiektów przy użyciu powiązanych pól.

Utwórz [projekt etykietowania danych](how-to-create-labeling-projects.md)i wyjściowy zestaw danych do użycia w eksperymentach uczenia maszynowego.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Monitorowanie wydajności modelu z dryfem danych

W kontekście uczenia maszynowego, dryfowanie danych to zmiana danych wejściowych modelu, które prowadzą do obniżenia wydajności modelu. Jest to jedna z najważniejszych przyczyn, które pogorszą się w miarę upływu czasu, dlatego monitorowanie dryfowania danych pomaga wykrywać problemy z wydajnością modelu.

Zapoznaj się z artykułem [Tworzenie monitora zestawu danych](how-to-monitor-datasets.md) , aby dowiedzieć się więcej na temat wykrywania i generowania alertów dotyczących dryfowania danych w nowych danych w zestawie danych.

## <a name="next-steps"></a>Następne kroki 

+ Utwórz zestaw danych w programie Azure Machine Learning Studio lub za pomocą zestawu SDK języka Python, [wykonując następujące kroki.](how-to-create-register-datasets.md)
+ Wypróbuj przykłady szkoleniowe dotyczące zestawu danych, korzystając z naszych [przykładowych notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).