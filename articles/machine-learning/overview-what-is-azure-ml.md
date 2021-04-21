---
title: Co to jest Azure Machine Learning
description: Azure Machine Learning to zintegrowane rozwiązanie do nauki o danych dla naukowców i inżynierów danych służące do modelowania i wdrażania aplikacji uczenia maszynowego na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: cd395f0ff29eed1e09f5bdac882482b201052c90
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814836"
---
# <a name="what-is-azure-machine-learning"></a>Co to jest usługa Azure Machine Learning?

Ten artykuł zawiera informacje na temat Azure Machine Learning, czyli środowiska opartego na chmurze, które umożliwia trenowanie, wdrażanie, automatyzowanie i śledzenie modeli uczenia maszynowego oraz zarządzanie nimi. 

Azure Machine Learning mogą być używane do dowolnego rodzaju uczenia maszynowego, od klasycznego uczenia maszynowego po uczenie głębokie, nadzorowane i nienadzorowane. Bez względu na to, czy wolisz pisać kod w języku Python lub R za pomocą zestawu SDK, czy pracować z opcjami bez kodu/z małą kodem w [studio,](#build-ml-models-in-the-studio)możesz tworzyć, trenować i śledzić modele uczenia maszynowego i uczenia głębokiego w Obszar roboczy usługi Azure Machine Learning. 

Rozpocznij szkolenie na maszynie lokalnej, a następnie przeskaluj w poziomie do chmury. 

Usługa współdziała również z popularnymi narzędziami typu open source do uczenia głębokiego i wzmacniania, takimi jak PyTorch, TensorFlow, scikit-learn i Ray RLlib. 

> [!Tip]
> **Bezpłatna wersja próbna!**  Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś. Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.


## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Przy użyciu techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, którymi kupujący może być zainteresowany, na podstawie jego dotychczasowych zakupów. Gdy Twoja karta kredytowa jest używana, uczenie maszynowe porównuje tę transakcję z bazą danych transakcji i pomaga wykryć oszustwo. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została zakończona.

## <a name="machine-learning-tools-to-fit-each-task"></a>Narzędzia uczenia maszynowego dopasowane do poszczególnych zadań 

Azure Machine Learning udostępnia wszystkie narzędzia potrzebne deweloperom i badaczom danych w przepływach pracy uczenia maszynowego, w tym:
+ Projektant [Azure Machine Learning:](tutorial-designer-automobile-price-train-score.md)przeciąganie i upuszczanie modułów w celu kompilowania eksperymentów, a następnie wdrażania potoków w środowisku z małą kodem.

+ Notesy Jupyter: użyj naszych [przykładowych notesów](https://github.com/Azure/MachineLearningNotebooks) lub utwórz własne notesy, aby wykorzystać nasz zestaw SDK dla przykładów języka <a href="/python/api/overview/azure/ml/intro" target="_blank">Python</a> na użytek uczenia maszynowego. 

+ Skrypty języka R lub notesy, w których używasz <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">zestawu SDK</a> dla języka R do pisania własnego kodu lub używasz modułów języka R w projektancie.

+ [Akcelerator](https://aka.ms/many-models) rozwiązań do obsługi wielu modeli (wersja zapoznawcza) jest Azure Machine Learning i umożliwia trenować setki, a nawet tysiące modeli uczenia maszynowego oraz zarządzać nimi.

+ [Rozszerzenie uczenia maszynowego dla Visual Studio Code (wersja zapoznawcza)](how-to-set-up-vs-code-remote.md) zapewnia w pełni funkcjonalne środowisko projektowe do tworzenia projektów uczenia maszynowego i zarządzania nimi.

+ [Interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md) to rozszerzenie interfejsu wiersza polecenia platformy Azure, które udostępnia polecenia do zarządzania Azure Machine Learning zasobami z wiersza polecenia.

+ Integracja z [platformami typu open source,](concept-open-source.md) takimi jak PyTorch, TensorFlow i scikit-learn, oraz wiele innych na temat trenowania i wdrażania procesów uczenia maszynowego oraz zarządzania nimi.

+ [Uczenie przez wzmacnianie](how-to-use-reinforcement-learning.md) za pomocą Raya RLlib

Za pomocą przepływu [MLflow](how-to-use-mlflow.md) można nawet śledzić metryki i wdrażać modele, lub za pomocą rozwiązania Kubeflow tworzyć potoki przepływu [pracy.](https://www.kubeflow.org/docs/azure/)

## <a name="build-ml-models-in-python-or-r"></a>Tworzenie modeli uczenia maszynowego w języku Python lub R

Rozpocznij szkolenie na komputerze lokalnym przy użyciu zestawu SDK Azure Machine Learning <a href="/python/api/overview/azure/ml/intro" target="_blank">Python lub</a> <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">zestawu R SDK.</a> Następnie możesz skalować w zewnątrz do chmury. 

Dzięki wielu dostępnym [docelowym obiektom obliczeniowym](how-to-create-attach-compute-studio.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), można tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Możesz również [zautomatyzować trenowanie i dostrajanie modelu przy](tutorial-auto-train-models.md) użyciu zestawu SDK.

## <a name="build-ml-models-in-the-studio"></a>Tworzenie modeli uczenia maszynowego w studio

[Azure Machine Learning studio](https://studio.azureml.net) to portal internetowy dostępny w Azure Machine Learning dla opcji z małą kodem i bez kodu na potrzeby trenowania modelu, wdrażania i zarządzania zasobami. Studio integruje się z zestawem AZURE MACHINE LEARNING SDK w celu bezproblemowego środowiska. Aby uzyskać więcej informacji, [zobacz Co to jest Azure Machine Learning studio](overview-what-is-machine-learning-studio.md).

+ **Projektant usługi Azure Machine Learning**

  Użyj [projektanta,](concept-designer.md) aby trenować i wdrażać modele uczenia maszynowego bez pisania jakiegokolwiek kodu. Wypróbuj [samouczek projektanta,](tutorial-designer-automobile-price-train-score.md) aby rozpocząć pracę. 

  ![Animowany plik GIF interfejsu przeciągania i upuszczania Azure Machine Learning projektanta](media/concept-designer/designer-drag-and-drop.gif)

+ **Śledzenie eksperymentów**

  Dowiedz się, jak [śledzić i wizualizować eksperymenty z nauką](how-to-track-monitor-analyze-runs.md) o danych w studio. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Szczegóły uruchamiania w Azure Machine Learning studio":::


+ **I wiele więcej...**

  Odwiedź Azure Machine Learning studio pod [ml.azure.com](https://studio.azureml.net).


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: wdrażanie & zarządzania cyklem życia
Jeśli masz właściwy model, możesz go łatwo użyć w usłudze internetowej, na urządzeniu IoT lub z Power BI. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md).

Następnie możesz zarządzać wdrożonych modelami przy użyciu zestawu SDK usługi Azure Machine Learning dla języka [Python,](/python/api/overview/azure/ml/)Azure Machine Learning studio [lub](https://ml.azure.com)interfejsu wiersza polecenia [uczenia maszynowego.](reference-azure-machine-learning-cli.md)

Te modele mogą być używane i zwracać przewidywania w czasie [rzeczywistym](how-to-consume-web-service.md) lub [asynchronicznie](./tutorial-pipeline-batch-scoring-classification.md) w przypadku dużych ilości danych.

Za pomocą zaawansowanych [potoków uczenia maszynowego](concept-ml-pipelines.md)możesz współpracować nad każdym krokiem od przygotowania danych, trenowania modelu i oceny przez wdrożenie. Potoki umożliwiają:

* Automatyzacja procesów end-to-end uczenia maszynowego w chmurze
* Ponowne użycie składników i ponowne kroki tylko w razie potrzeby
* Używanie różnych zasobów obliczeniowych w każdym kroku
* Uruchamianie zadań oceniania wsadowego

Jeśli chcesz użyć skryptów do zautomatyzowania przepływu pracy uczenia maszynowego, interfejs wiersza polecenia uczenia maszynowego udostępnia narzędzia wiersza polecenia, które wykonują typowe zadania, takie jak przesyłanie przebiegów szkoleniowych lub wdrażanie modelu. [](reference-azure-machine-learning-cli.md)

Aby rozpocząć korzystanie z Azure Machine Learning, zobacz [Następne kroki.](#next-steps)

## <a name="integration-with-other-services"></a>Integracja z innymi usługami

Azure Machine Learning współpracuje z innymi usługami na platformie Azure, a także integruje się z narzędziami open source, takimi jak Git i MLFlow.

+ Docelowe obiekty obliczeniowe, __takie jak Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ i __Azure HDInsight__. Aby uzyskać więcej informacji na temat docelowych obiektów obliczeniowych, [zobacz Co to są docelowe obiekty obliczeniowe?](concept-compute-target.md).
+ __Azure Event Grid .__ Aby uzyskać więcej informacji, zobacz [Consume Azure Machine Learning events (Zużywanie Azure Machine Learning zdarzeń).](./how-to-use-event-grid.md)
+ __Azure Monitor .__ Aby uzyskać więcej informacji, zobacz [Monitorowanie Azure Machine Learning](monitor-azure-machine-learning.md).
+ Magazyny danych, takie jak konta usługi __Azure Storage,__ __Azure Data Lake Storage,__ __Azure SQL Database,__ __Azure Database for PostgreSQL__ i __Azure Open Datasets__. Aby uzyskać więcej informacji, zobacz Access data in Azure storage services (Uzyskiwanie dostępu do danych w [usługach Azure Storage)](how-to-access-data.md) i Create datasets with Azure Open Datasets (Tworzenie [zestawów danych za pomocą Azure Open Datasets).](how-to-create-register-datasets.md)
+ __Sieci wirtualne platformy Azure.__ Aby uzyskać więcej informacji, zobacz Omówienie izolacji [i prywatności sieci wirtualnej.](how-to-network-security-overview.md)
+ __Azure Pipelines__. Aby uzyskać więcej informacji, zobacz [Train and deploy machine learning models (Trenuj i wdrażaj modele uczenia maszynowego).](/azure/devops/pipelines/targets/azure-machine-learning)
+ __Repozytorium Git rejestruje .__ Aby uzyskać więcej informacji, zobacz Integracja [z usługą Git.](concept-train-model-git-integration.md)
+ __MLFlow__. Aby uzyskać więcej informacji, zobacz [MLflow,](how-to-use-mlflow.md)  aby śledzić metryki, i [Wdrażanie modeli mlflow jako usługi internetowej](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__. Aby uzyskać więcej informacji, zobacz [build end-to-end workflow pipelines (Tworzenie potoków przepływu pracy typu end-to-end).](https://www.kubeflow.org/docs/azure/)

### <a name="secure-communications"></a>Bezpieczna komunikacja

Konta usługi Azure Storage, docelowych obiektów obliczeniowych i innych zasobów można bezpiecznie używać wewnątrz sieci wirtualnej do trenowania modeli i wnioskowania. Aby uzyskać więcej informacji, zobacz Omówienie izolacji [i prywatności sieci wirtualnej.](how-to-network-security-overview.md)

## <a name="next-steps"></a>Następne kroki

- Utwórz swój pierwszy eksperyment przy użyciu preferowanej metody:
- + [Wprowadzenie do własnego środowiska projektowego](tutorial-1st-experiment-sdk-setup-local.md)
  + [Używanie notesów Jupyter w wystąpieniu obliczeniowym do trenowania & modeli uczenia maszynowego](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie zautomatyzowanego uczenia maszynowego do trenowania & wdrażania modeli uczenia maszynowego](tutorial-first-experiment-automated-ml.md) 
  + [Zarządzanie zasobami w Visual Studio Code](how-to-manage-resources-vscode.md)
  + [Używanie Visual Studio Code do trenowania i wdrażania modelu klasyfikacji obrazów](tutorial-train-deploy-image-classification-model-vscode.md)
  + [Korzystanie z funkcji przeciągania i upuszczania & projektanta w celu trenowania & wdrażania](tutorial-designer-automobile-price-train-score.md) 
  + [Trenowanie i wdrażanie modelu przy użyciu interfejsu wiersza polecenia uczenia maszynowego](tutorial-train-deploy-model-cli.md)

- Dowiedz się więcej o [potokach uczenia maszynowego](concept-ml-pipelines.md) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Przeczytaj szczegółowe informacje na [temat Azure Machine Learning i pojęć.](concept-azure-machine-learning-architecture.md)
