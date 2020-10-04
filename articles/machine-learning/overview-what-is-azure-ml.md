---
title: Co to jest Azure Machine Learning
description: Omówienie Azure Machine Learning zintegrowanego, kompleksowego rozwiązania do nauki o danych dla specjalistów ds. danych w celu opracowywania, eksperymentowania i wdrażania zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: e214607b82a44fb1b6859a3c73d58907fbb279d9
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710740"
---
# <a name="what-is-azure-machine-learning"></a>Co to jest usługa Azure Machine Learning?

Ten artykuł zawiera informacje o Azure Machine Learning, środowisku opartym na chmurze, które służy do uczenia, wdrażania, automatyzowania i śledzenia modeli ML oraz zarządzania nimi. 

Azure Machine Learning mogą być używane dla dowolnego rodzaju uczenia maszynowego, od klasycznej maszyny do uczenia głębokiego, nadzorowanego i nienadzorowanego uczenia się. Bez względu na to, czy wolisz pisać kod w języku Python, czy R z zestawem SDK, czy pracować z opcjami No-Code/Low-Code w programie [Studio](#build-ml-models-in-the-studio), możesz tworzyć, uczenia i śledzić modele uczenia maszynowego i uczenia głębokiego w obszar roboczy usługi Azure Machine Learning. 

Rozpocznij szkolenie na komputerze lokalnym, a następnie Skaluj w poziomie do chmury. 

Usługa współpracuje również z popularnymi narzędziami typu "open source", takimi jak PyTorch, TensorFlow, scikit-Learning i Ray RLlib. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Bezpłatna wersja próbna!**  Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj. Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.


## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Przy użyciu techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, którymi kupujący może być zainteresowany, na podstawie jego dotychczasowych zakupów. Gdy Twoja karta kredytowa jest używana, uczenie maszynowe porównuje tę transakcję z bazą danych transakcji i pomaga wykryć oszustwo. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została zakończona.

## <a name="machine-learning-tools-to-fit-each-task"></a>Narzędzia uczenia maszynowego do dopasowania poszczególnych zadań 

Azure Machine Learning udostępnia wszystkie narzędzia dla deweloperów i analityków danych, które są potrzebne dla swoich przepływów pracy uczenia maszynowego, w tym:
+ [Projektant Azure Machine Learning](tutorial-designer-automobile-price-train-score.md): Przeciągnij i upuść moduły, aby skompilować eksperymenty, a następnie wdrożyć potoki.

+ Notesy Jupyter: Użyj naszych [przykładowych notesów](https://github.com/Azure/MachineLearningNotebooks) lub Utwórz własne notesy, aby korzystać z naszego <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK dla</a> przykładów języka Python dla uczenia maszynowego. 

+ Skrypty i notesy języka r, w których używasz <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">zestawu SDK dla języka r</a> , do pisania własnego kodu lub korzystania z modułów r w projektancie.

+ + [Akcelerator rozwiązań wielu modeli](https://aka.ms/many-models) (wersja zapoznawcza) jest oparty na Azure Machine Learning i umożliwia uczenie, obsługę setek lub nawet tysięcy modeli uczenia maszynowego oraz zarządzanie nimi.

+ [Rozszerzenie programu Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [Interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md)

+ Platformy typu open source, takie jak PyTorch, TensorFlow i scikit — uczyć się i wielu innych

+ [Uczenie wzmacniające](how-to-use-reinforcement-learning.md) dzięki RLlib ray

Możesz nawet używać [MLflow do śledzenia metryk i wdrażania modeli](how-to-use-mlflow.md) lub Kubeflow, aby [tworzyć kompleksowe potoki przepływu pracy](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Tworzenie modeli ML w języku Python lub R

Rozpocznij szkolenie na maszynie lokalnej przy użyciu <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK</a> języka Azure Machine Learning Python lub <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">zestawu R SDK</a>. Następnie można skalować w poziomie do chmury. 

Dzięki wielu dostępnym [docelowym obiektom obliczeniowym](how-to-create-attach-compute-studio.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), można tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Możesz również [zautomatyzować szkolenia i dostrajanie modeli](tutorial-auto-train-models.md) za pomocą zestawu SDK.

## <a name="build-ml-models-in-the-studio"></a>Tworzenie modeli ML w Studio

[Azure Machine Learning Studio](https://studio.azureml.net) to portal sieci web w Azure Machine Learning dla opcji niskiego kodu i braku kodu dla celów związanych z modelem szkoleń, wdrażania i zarządzania zasobami. Program Studio integruje się z zestawem SDK Azure Machine Learning, aby zapewnić bezproblemowe działanie. Aby uzyskać więcej informacji, zobacz [co to jest Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

+ **Projektant usługi Azure Machine Learning**

  Użyj [projektanta,](concept-designer.md) aby nauczyć i wdrożyć modele uczenia maszynowego bez konieczności pisania kodu. Wypróbuj [samouczek projektanta](tutorial-designer-automobile-price-train-score.md) , aby rozpocząć pracę. 

  ![Animowany plik GIF interfejsu przeciągania i upuszczania programu Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Śledzenie eksperymentów**

  Dowiedz się [, jak śledzić i wizualizować eksperymenty analizy danych](tutorial-first-experiment-automated-ml.md) w programie Studio. 

    ![Szczegóły uruchamiania w programie Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)


+ **I wiele więcej...**

  Odwiedź Azure Machine Learning Studio pod adresem [ml.Azure.com](https://studio.azureml.net). 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: wdrażanie & zarządzania cyklem życia
Jeśli dysponujesz odpowiednim modelem, możesz go łatwo używać w usłudze sieci Web, na urządzeniu IoT lub z Power BI. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md).

Następnie możesz zarządzać wdrożonymi modelami przy użyciu [zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true), [Azure Machine Learning Studio](https://ml.azure.com)lub [interfejsu wiersza polecenia Machine Learning](reference-azure-machine-learning-cli.md).

Te modele mogą być używane i zwracać przewidywania w [czasie rzeczywistym](how-to-consume-web-service.md) lub [asynchronicznie](how-to-use-parallel-run-step.md) w przypadku dużych ilości danych.

Dzięki zaawansowanym [potokom uczenia maszynowego](concept-ml-pipelines.md)można współpracować nad każdym krokiem w procesie przygotowywania danych, uczenia modelu i oceny, dzięki wdrożeniu. Potoki umożliwiają:

* Automatyzuj kompleksowy proces uczenia maszynowego w chmurze
* Ponownie Użyj składników i ponownie wykonaj kroki w razie konieczności
* Użyj innych zasobów obliczeniowych w każdym kroku
* Uruchamianie zadań oceniania partii

Jeśli chcesz używać skryptów do automatyzowania przepływu pracy uczenia maszynowego, [interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md) udostępnia narzędzia do obsługi typowych zadań, takie jak przesyłanie szkoleń lub Wdrażanie modelu.

Aby rozpocząć korzystanie z Azure Machine Learning, zobacz [następne kroki](#next-steps).

## <a name="integration-with-other-services"></a>Integracja z innymi usługami

Azure Machine Learning współpracuje z innymi usługami na platformie Azure, a także integruje się z narzędziami typu open source, takimi jak Git i MLFlow.

+ Cele obliczeniowe, takie jak __usługa Azure Kubernetes__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__i __Azure HDInsight__. Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz [co to są cele obliczeniowe?](concept-compute-target.md).
+ __Azure Event Grid__. Aby uzyskać więcej informacji, zobacz temat [Korzystanie z zdarzeń Azure Machine Learning](concept-event-grid-integration.md).
+ __Azure monitor__. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Machine Learning](monitor-azure-machine-learning.md).
+ Magazyny danych, takie jak __konta usługi Azure Storage__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__i __otwarte zestawy danych platformy Azure__. Aby uzyskać więcej informacji, zobacz [dostęp do danych w usługach Azure Storage](how-to-access-data.md) i [Tworzenie zestawów danych za pomocą otwartych zestawów danych platformy Azure](how-to-create-register-datasets.md).
+ __Sieci wirtualne platformy Azure__. Aby uzyskać więcej informacji, zobacz [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md).
+ __Azure Pipelines__. Aby uzyskać więcej informacji, zobacz [uczenie i wdrażanie modeli uczenia maszynowego](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Dzienniki repozytorium git__. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git](concept-train-model-git-integration.md).
+ __MLFlow__. Aby uzyskać więcej informacji, zobacz [MLflow do śledzenia metryk i wdrażania modeli](how-to-use-mlflow.md) 
+ __Kubeflow__. Aby uzyskać więcej informacji, zobacz [Tworzenie kompleksowych potoków przepływu pracy](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Bezpieczna komunikacja

Twoje konto usługi Azure Storage, cele obliczeniowe i inne zasoby mogą być bezpiecznie używane w sieci wirtualnej do uczenia modeli i wykonywania wnioskowania. Aby uzyskać więcej informacji, zobacz [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md).

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszy eksperyment przy użyciu preferowanej metody:
  + [Korzystanie z notesów Python do uczenia & Wdrażanie modeli ML](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie R Markdown do uczenia & wdrażania modeli ML](tutorial-1st-r-experiment.md) 
  + [Używanie automatycznej uczenia maszynowego do uczenia & Wdrażanie modeli ML](tutorial-first-experiment-automated-ml.md) 
  + [Korzystanie z funkcji upuszczania & przez projektanta w celu uczenia & wdrożenia](tutorial-designer-automobile-price-train-score.md) 
  + [Korzystanie z interfejsu wiersza polecenia Machine Learning w celu uczenia i wdrożenia modelu](tutorial-train-deploy-model-cli.md)

- Dowiedz się więcej o [potokach uczenia maszynowego](concept-ml-pipelines.md) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Zapoznaj się z szczegółowymi artykułami [dotyczącymi architektury i koncepcji Azure Machine Learning](concept-azure-machine-learning-architecture.md) .
