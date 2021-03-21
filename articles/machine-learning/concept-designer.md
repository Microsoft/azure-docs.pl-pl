---
title: Tworzenie modeli ML przy użyciu narzędzia Projektant
titleSuffix: Azure Machine Learning
description: Poznaj warunki, koncepcje i przepływ pracy, które tworzą projektanta dla Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: e6738bf944c5a80d0cb54432ade7555ebdcfbd51
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503544"
---
# <a name="what-is-azure-machine-learning-designer"></a>Co to jest projektant usługi Azure Machine Learning? 


Projektant Azure Machine Learning umożliwia wizualne łączenie [zestawów danych](#datasets) i [modułów](#module) na interaktywnej kanwie w celu tworzenia modeli uczenia maszynowego. Aby dowiedzieć się, jak rozpocząć pracę z projektantem, zobacz [Samouczek: przewidywanie ceny za samochód dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md)

![Przykład projektanta Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

Projektant używa [obszaru roboczego](concept-workspace.md) Azure Machine Learning do organizowania zasobów udostępnionych, takich jak:

+ [Pipelines](#pipeline)
+ [Zestawy danych](#datasets)
+ [Zasoby obliczeniowe](#compute)
+ [Zarejestrowane modele](concept-azure-machine-learning-architecture.md#models)
+ [Opublikowane potoki](#publish)
+ [Punkty końcowe w czasie rzeczywistym](#deploy)

## <a name="model-training-and-deployment"></a>Modelowanie szkoleń i wdrożeń

Projektant umożliwia wizualizację kanwy do kompilowania, testowania i wdrażania modeli uczenia maszynowego. Za pomocą projektanta można:

+ Przeciągnij i upuść [zestawy danych](#datasets) i [moduły](#module) na kanwę.
+ Połącz moduły, aby utworzyć [wersję roboczą potoku](#pipeline-draft).
+ Prześlij [przebieg potoku](#pipeline-run) przy użyciu zasobów obliczeniowych w obszarze roboczym Azure Machine Learning.
+ Przekonwertuj **potoki szkoleniowe** na **potoki wnioskowania**.
+ [Opublikuj](#publish) potoki w **punkcie końcowym potoku** REST, aby przesłać nowy potok, który jest uruchamiany z innymi parametrami i zestawami danych.
    + Publikuj **potok szkoleniowy** , aby ponownie wykorzystać pojedynczy potok do uczenia wielu modeli przy zmianie parametrów i zestawów danych.
    + Publikuj **potok wnioskowania o partiach** , aby przetworzyć prognozy dotyczące nowych danych przy użyciu wcześniej nauczonego modelu.
+ [Wdróż](#deploy) **potok wnioskowania** w czasie rzeczywistym do punktu końcowego w czasie rzeczywistym, aby tworzyć prognozy dotyczące nowych danych w czasie rzeczywistym.

![Diagram przepływu pracy na potrzeby szkoleń, wnioskowania partii i wnioskowania w czasie rzeczywistym w projektancie](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Potok

[Potok](concept-azure-machine-learning-architecture.md#ml-pipelines) składa się z zestawów danych i modułów analitycznych, które są połączone. Potoki mają wiele użycia: można utworzyć potok, który pociąga za sobą jeden model, lub jeden z wielu modeli. Można utworzyć potok, który wykonuje przewidywania w czasie rzeczywistym lub w partii, lub utworzyć potok, który czyści dane. Potoki umożliwiają ponowne użycie pracy i zorganizowanie projektów.

### <a name="pipeline-draft"></a>Wersja robocza potoku

Podczas edytowania potoku w projektancie postęp jest zapisywany jako **wersja robocza potoku**. Można edytować wersję roboczą potoku w dowolnym momencie przez dodanie lub usunięcie modułów, skonfigurowanie obiektów docelowych obliczeń, utworzenie parametrów itd.

Prawidłowy potok ma następujące cechy:

* Zestawy danych mogą łączyć się tylko z modułami.
* Moduły mogą łączyć się tylko z zestawami danych lub innymi modułami.
* Wszystkie porty wejściowe dla modułów muszą mieć połączenie z przepływem danych.
* Należy ustawić wszystkie wymagane parametry dla każdego modułu.

Gdy wszystko będzie gotowe do uruchomienia potoku, przesyłasz uruchomienie potoku.

### <a name="pipeline-run"></a>Uruchomienie potoku

Za każdym razem, gdy uruchamiasz potok, konfiguracja potoku i jego wyniki są przechowywane w obszarze roboczym jako **uruchomienie potoku**. Można wrócić do dowolnego uruchomienia potoku, aby sprawdzić go pod kątem rozwiązywania problemów lub inspekcji. **Sklonuj** uruchomienie potoku, aby utworzyć nową wersję roboczą potoku do edycji.

Uruchomienia potoków są pogrupowane w [eksperymenty](concept-azure-machine-learning-architecture.md#experiments) w celu zorganizowania historii uruchamiania. Możesz ustawić eksperyment dla każdego uruchomienia potoku. 

## <a name="datasets"></a>Zestawy danych

Zestaw danych uczenia maszynowego ułatwia uzyskiwanie dostępu do danych i korzystanie z nich. W projektancie dołączono kilka przykładowych zestawów danych. W razie potrzeby możesz [zarejestrować](how-to-create-register-datasets.md) więcej zestawów danych.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Projektant ma kilka modułów z przedziału od funkcji transferu danych do szkoleń, oceniania i weryfikacji.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku Properties (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model. Można ustawić zasoby obliczeniowe dla poszczególnych modułów w projektancie. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Właściwości modułu":::


Aby uzyskać pomoc dotyczącą przechodzenia przez bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [Omówienie algorytmu & modułu](algorithm-module-reference/module-reference.md). Aby uzyskać pomoc dotyczącą wybierania algorytmu, zobacz [arkusz Azure Machine Learning Algorithm Ściągawka](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a> Zasoby obliczeniowe

Użyj zasobów obliczeniowych z obszaru roboczego, aby uruchomić potok i hostować wdrożone modele jako punkty końcowe w czasie rzeczywistym lub punkty końcowe potoku (na potrzeby wnioskowania wsadowego). Obsługiwane elementy docelowe obliczeń są następujące:

| Docelowy zasób obliczeniowy | Szkolenia | Wdrożenie |
| ---- |:----:|:----:|
| Azure Machine Learning obliczeń | ✓ | |
| Wystąpienie obliczeniowe usługi Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Elementy docelowe obliczeń są dołączone do [obszaru roboczego Azure Machine Learning](concept-workspace.md). Obiektami docelowymi obliczeń można zarządzać w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Wdróż

Aby wykonać inferencing w czasie rzeczywistym, należy wdrożyć potok jako **punkt końcowy w czasie rzeczywistym**. Punkt końcowy w czasie rzeczywistym tworzy interfejs między aplikacją zewnętrzną a modelem oceniania. Wywołanie punktu końcowego w czasie rzeczywistym zwraca wyniki prognozowania do aplikacji w czasie rzeczywistym. Aby wykonać wywołanie do punktu końcowego w czasie rzeczywistym, należy przekazać klucz interfejsu API, który został utworzony podczas wdrażania punktu końcowego. Punkt końcowy jest oparty na pozostałej, popularnej architekturze dla projektów programowania w sieci Web.

Punkty końcowe w czasie rzeczywistym muszą zostać wdrożone w klastrze usługi Azure Kubernetes Service.

Aby dowiedzieć się, jak wdrożyć model, zobacz [Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą projektanta](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publikowanie

Możesz również opublikować potok w **punkcie końcowym potoku**. Podobnie jak w przypadku punktu końcowego w czasie rzeczywistym, punkt końcowy potoku umożliwia przesyłanie nowych uruchomień potoków z aplikacji zewnętrznych przy użyciu wywołań REST. Nie można jednak wysyłać ani odbierać danych w czasie rzeczywistym za pomocą punktu końcowego potoku.

Opublikowane potoki są elastyczne, ale mogą być używane do uczenia lub ponownego uczenia modeli, [wykonywania zadań wsadowych inferencing](how-to-run-batch-predictions-designer.md), przetwarzania nowych danych i wiele innych. Można opublikować wiele potoków w jednym punkcie końcowym potoku i określić, która wersja potoku ma zostać uruchomiona.

Opublikowany potok jest uruchamiany na zasobach obliczeniowych zdefiniowanych w wersji roboczej potoku dla każdego modułu.

Projektant tworzy ten sam obiekt [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline) , co zestaw SDK.

## <a name="next-steps"></a>Następne kroki

* Poznaj podstawowe informacje na temat analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny samochodów dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md)
* Dowiedz się, jak modyfikować istniejące [przykłady projektanta](samples-designer.md) , aby dostosować je do swoich potrzeb.