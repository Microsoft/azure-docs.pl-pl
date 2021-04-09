---
title: Kompiluj & modele uczenia
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić modele przy użyciu Azure Machine Learning. Poznaj różne metody uczenia i wybierz odpowiednie dla projektu.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: de3d9aa60322cc3e6e189f6f16c35d6f42c0cf61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102500414"
---
# <a name="train-models-with-azure-machine-learning"></a>Uczenie modeli za pomocą Azure Machine Learning

Azure Machine Learning oferuje kilka sposobów uczenia modeli — od rozwiązań po pierwszym kodzie przy użyciu zestawu SDK do rozwiązań z niską ilością kodu, takich jak automatyczne Uczenie maszynowe i Visual Designer. Skorzystaj z poniższej listy, aby określić, która metoda szkoleniowa jest dla Ciebie odpowiednia:

+ [Azure Machine Learning SDK dla języka Python](#python-sdk): zestaw SDK języka Python oferuje kilka sposobów uczenia modeli, z których każdy może mieć różne możliwości.

    | Metoda uczenia | Opis |
    | ----- | ----- |
    | [Uruchom konfigurację](#run-configuration) | **Typowym sposobem uczenia modeli** jest użycie skryptu szkoleniowego i uruchomienie konfiguracji. Konfiguracja uruchamiania zawiera informacje konieczne do skonfigurowania środowiska szkoleniowego używanego do uczenia modelu. W konfiguracji przebiegu możesz określić swój skrypt szkoleniowy, obiekt docelowy obliczeń i środowisko Azure ML, a następnie uruchomić zadanie szkoleniowe. |
    | [Zautomatyzowane uczenie maszynowe](#automated-machine-learning) | Automatyczne Uczenie maszynowe umożliwia **uczenie modeli bez obszernej wiedzy o nauce lub programowaniu**. W przypadku osób z tłem i programowaniem danych zapewnia ona sposób oszczędzania czasu i zasobów dzięki automatyzowaniu wyboru algorytmu i dostrajania parametrów. Nie musisz martwić się o Definiowanie konfiguracji uruchamiania podczas korzystania z funkcji automatycznego uczenia maszynowego. |
    | [Potok uczenia maszynowego](#machine-learning-pipeline) | Potoki nie są różnymi metodami szkoleniowymi, ale **sposób definiowania przepływu pracy przy użyciu modularnych, wielokrotnych kroków**, które mogą obejmować szkolenie w ramach przepływu pracy. Potoki usługi Machine Learning obsługują korzystanie z funkcji automatycznego uczenia maszynowego i uruchamiania konfiguracji w celu uczenia modeli. Ponieważ potoki nie są ukierunkowane na szkolenie, przyczyny użycia potoku są bardziej zróżnicowane niż inne metody uczenia się. Ogólnie rzecz biorąc, można użyć potoku, gdy:<br>* Chcesz **zaplanować procesy nienadzorowane** , takie jak długotrwałe zadania szkoleniowe lub Przygotowywanie danych.<br>* Należy użyć **wielu kroków** , które są skoordynowane w niejednorodnych zasobach obliczeniowych i lokalizacjach magazynu.<br>* Użyj potoku jako **szablonu wielokrotnego użytku** dla konkretnych scenariuszy, takich jak przeszkolenie lub wsadowe ocenianie.<br>* **Śledź źródła danych, dane wejściowe i wyjściowe** dla przepływu pracy.<br>* Przepływ pracy jest **implementowany przez różne zespoły, które pracują niezależnie od konkretnych kroków**. Kroki można następnie połączyć w potoku w celu zaimplementowania przepływu pracy. |

+ [Azure Machine Learning SDK dla języka r (wersja zapoznawcza)](#r-sdk-preview): zestaw SDK dla języka r używa pakietu reticulate do powiązania z zestawem Python SDK Azure Machine Learning. Dzięki temu można uzyskać dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie Python SDK z dowolnego środowiska języka R.

+ **Projektant**: Projektant Azure Machine Learning umożliwia łatwe wprowadzenie do uczenia maszynowego w celu tworzenia weryfikacji koncepcji lub dla użytkowników z niewielkim doświadczeniem związanym z kodowaniem. Pozwala to na uczenie modeli przy użyciu funkcji przeciągania i upuszczania interfejsu użytkownika opartego na sieci Web. Możesz użyć kodu w języku Python jako części projektu lub pouczenia modeli bez pisania kodu.

+ **CLI**: interfejs wiersza polecenia usługi Machine Learning umożliwia wykonywanie typowych zadań przy użyciu Azure Machine Learning i jest często używany do **tworzenia skryptów i automatyzowania zadań**. Na przykład po utworzeniu skryptu szkoleniowego lub potoku można użyć interfejsu wiersza polecenia, aby rozpocząć szkolenie zgodnie z harmonogramem lub w przypadku aktualizowania plików danych używanych do szkoleń. W przypadku modeli szkoleniowych dostępne są polecenia, które przesyłają zadania szkoleniowe. Może przesyłać zadania przy użyciu konfiguracji uruchamiania lub potoków.

Każda z tych metod szkoleniowych może używać różnych typów zasobów obliczeniowych do szkoleń. Zbiorowo te zasoby są określane jako [__cele obliczeniowe__](concept-azure-machine-learning-architecture.md#compute-targets). Obiekt docelowy obliczeń może być maszyną lokalną lub zasobem w chmurze, takim jak Azure Machine Learning COMPUTE, Azure HDInsight lub zdalną maszynę wirtualną.

## <a name="python-sdk"></a>Zestaw SDK dla języka Python

Zestaw Azure Machine Learning SDK dla języka Python umożliwia tworzenie i uruchamianie przepływów pracy uczenia maszynowego przy użyciu Azure Machine Learning. Można korzystać z usługi z interaktywnej sesji języka Python, Jupyter notesów, Visual Studio Code lub innego środowiska IDE.

* [Co to jest zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro)
* [Instalowanie/aktualizowanie zestawu SDK](/python/api/overview/azure/ml/install)
* [Skonfiguruj środowisko programistyczne dla Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Uruchom konfigurację

Ogólne zadanie szkoleniowe z Azure Machine Learning można zdefiniować za pomocą [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig). Następnie zostanie użyta konfiguracja uruchomienia skryptu wraz ze skryptami szkoleniowymi do uczenia modelu w obiekcie docelowym obliczeń.

Możesz rozpocząć od konfiguracji uruchamiania komputera lokalnego, a następnie w razie potrzeby przełączyć się na jeden dla docelowej wartości obliczeniowej opartej na chmurze. Podczas zmieniania elementu docelowego obliczeń można zmienić tylko używaną konfigurację uruchamiania. Uruchomienie rejestruje również informacje o zadaniu szkoleniowym, takie jak dane wejściowe, wyjściowe i dzienniki.

* [Co to jest Konfiguracja przebiegu?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Samouczek: uczenie swojego pierwszego modelu ML](tutorial-1st-experiment-sdk-train.md)
* [Przykłady: Przykłady modeli szkoleń Jupyter Notebook i Python](https://github.com/Azure/azureml-examples)
* [Instrukcje: Konfigurowanie przebiegu szkoleniowego](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Zdefiniuj iteracje, ustawienia parametrów, cechowania i inne ustawienia. Podczas uczenia Azure Machine Learning próbuje jednocześnie różne algorytmy i parametry. Szkolenie zostaje zatrzymane po trafieniu zdefiniowanych kryteriów zakończenia.

> [!TIP]
> Oprócz zestawu SDK języka Python można także użyć zautomatyzowanej ML za pomocą [Azure Machine Learning Studio](https://ml.azure.com).

* [Co to jest zautomatyzowane uczenie maszynowe?](concept-automated-ml.md)
* [Samouczek: Tworzenie pierwszego modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego](tutorial-first-experiment-automated-ml.md)
* [Samouczek: Przewidywanie opłat za taksówkę za pomocą zautomatyzowanego uczenia maszynowego](tutorial-auto-train-models.md)
* [Przykłady: Jupyter Notebook przykłady dla automatycznej uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Instrukcje: Konfigurowanie zautomatyzowanych eksperymentów ML w języku Python](how-to-configure-auto-train.md)
* [Instrukcje: autouczenie modelu prognozy szeregów czasowych](how-to-auto-train-forecast.md)
* [Instrukcje: Tworzenie, eksplorowanie i wdrażanie zautomatyzowanych eksperymentów uczenia maszynowego za pomocą programu Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Potok uczenia maszynowego

Potoki uczenia maszynowego mogą korzystać z wymienionych wcześniej metod szkoleniowych. Potoki zawierają więcej informacji na temat tworzenia przepływu pracy, co obejmuje więcej niż tylko szkolenia modeli. W potoku można szkolić model przy użyciu zautomatyzowanej uczenia maszynowego lub uruchamiania konfiguracji.

* [Co to są potoki ML w Azure Machine Learning?](concept-ml-pipelines.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
* [Samouczek: korzystanie z potoków Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md)
* [Przykłady: Jupyter Notebook przykłady potoków uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Przykłady: potok z automatyczną usługą uczenia maszynowego](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Informacje o tym, co się stanie po przesłaniu zadania szkoleniowego

Cykl życia szkoleń platformy Azure składa się z:

1. Zapakowywanie plików w folderze projektu, ignorując te określone w _. amlignore_ lub _. gitignore_
1. Skalowanie w górę klastra obliczeniowego 
1. Kompilowanie lub pobieranie pliku dockerfile do węzła obliczeniowego 
    1. System oblicza wartość skrótu: 
        - Obraz podstawowy 
        - Niestandardowe kroki platformy Docker (zobacz [Wdrażanie modelu przy użyciu niestandardowego obrazu platformy Docker](./how-to-deploy-custom-docker-image.md))
        - Conda Definition YAML (zobacz [tworzenie & używanie środowisk oprogramowania w Azure Machine Learning](./how-to-use-environments.md))
    1. System używa tego skrótu jako klucza w odnośniku Azure Container Registry obszaru roboczego (ACR)
    1. Jeśli nie zostanie znaleziona, szuka dopasowania w ACR globalnym
    1. Jeśli nie zostanie znaleziona, system kompiluje nowy obraz (który zostanie zapisany w pamięci podręcznej i zarejestrowany w obszarze roboczym ACR)
1. Pobieranie spakowanego pliku projektu do tymczasowego magazynu w węźle obliczeniowym
1. Rozpakowywanie pliku projektu
1. Wykonywanie węzła obliczeniowego `python <entry script> <arguments>`
1. Zapisywanie dzienników, plików modelu i innych plików zapisywanych na `./outputs` koncie magazynu skojarzonym z obszarem roboczym
1. Skalowanie w dół obliczeniowe, w tym Usuwanie magazynu tymczasowego 

Jeśli zdecydujesz się na nauczenie się na komputerze lokalnym ("Konfigurowanie jako lokalne uruchomienie"), nie musisz używać platformy Docker. W przypadku wybrania opcji platformy Docker można używać lokalnie (zobacz sekcję [Konfigurowanie potoku](./how-to-debug-pipelines.md) dla przykładu).

## <a name="r-sdk-preview"></a>Zestaw SDK języka R (wersja zapoznawcza)

Zestaw R SDK umożliwia korzystanie z języka R z Azure Machine Learning. Zestaw SDK używa pakietu reticulate do powiązania z zestawem Python SDK Azure Machine Learning. Zapewnia to dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie Python SDK z dowolnego środowiska języka R.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Dokumentacja zestawu Azure Machine Learning SDK dla języka R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Projektant usługi Azure Machine Learning

Projektant umożliwia nauczenie modeli przy użyciu interfejsu przeciągania i upuszczania w przeglądarce internetowej.

+ [Co to jest projektant?](concept-designer.md)
+ [Samouczek: przewidywanie ceny samochodów](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Akcelerator rozwiązań wielu modeli

[Akcelerator rozwiązań wielu modeli](https://aka.ms/many-models) (wersja zapoznawcza) jest oparty na Azure Machine Learning i umożliwia uczenie, obsługę setek lub nawet tysięcy modeli uczenia maszynowego oraz zarządzanie nimi.

Na przykład Kompilowanie modelu __dla każdego wystąpienia lub poszczególnych osób__ w następujących scenariuszach może prowadzić do udoskonalonych wyników:

* Przewidywanie sprzedaży dla poszczególnych magazynów
* Konserwacja predykcyjna dla setek ropy naftowej
* Dostosowanie środowiska dla poszczególnych użytkowników.

Aby uzyskać więcej informacji, zobacz [Akcelerator rozwiązania wielu modeli](https://aka.ms/many-models) w serwisie GitHub.

## <a name="cli"></a>Interfejs wiersza polecenia

Interfejs wiersza polecenia uczenia maszynowego to rozszerzenie interfejsu wiersza polecenia platformy Azure. Udostępnia on Międzyplatformowe polecenia interfejsu CLI do pracy z Azure Machine Learning. Zazwyczaj używasz interfejsu wiersza polecenia do automatyzowania zadań, takich jak uczenie modelu uczenia maszynowego.

* [Użyj rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps na platformie Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

Za pomocą rozszerzenia VS Code można uruchamiać zadania szkoleniowe i zarządzać nimi. Aby dowiedzieć się więcej, zobacz Przewodnik dotyczący [zarządzania zasobami vs Code](how-to-manage-resources-vscode.md#experiments) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować przebieg szkoleniowy](how-to-set-up-training-targets.md).