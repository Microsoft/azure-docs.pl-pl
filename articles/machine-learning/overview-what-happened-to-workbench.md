---
title: Co się stało z aplikacją Workbench?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning to zintegrowane rozwiązanie do nauki o danych, które umożliwia modelowanie i wdrażanie aplikacji w skali chmury. Funkcja Workbench została wycofana.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: c800901ae139b16e75613b0cc8de3a9485186896
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521377"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Co się stało z aplikacją Azure Machine Learning Workbench?

Aplikacja Azure Machine Learning Workbench i niektóre inne wczesne funkcje były przestarzałe i zastąpione w wersji **2018 września** w celu zapewnienia lepszej [architektury](concept-azure-machine-learning-architecture.md).

Wersja zawiera wiele znaczących aktualizacji wprowadzonych w oparciu o opinie klientów, które mają na celu udoskonalenie środowiska użytkownika. Podstawowe funkcje z przebiegów eksperymentu w zakresie wdrażania modelu nie uległy zmianie. Ale teraz można użyć niezawodnego <a href="/python/api/overview/azure/ml/intro" target="_blank">zestawu SDK języka Python</a>, zestawu R SDK i [interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) do wykonywania zadań i potoków uczenia maszynowego.

Większość artefaktów, które zostały utworzone we wcześniejszej wersji Azure Machine Learning są przechowywane w magazynie lokalnym lub w chmurze. Te artefakty nigdy nie zostaną usunięte.

Z tego artykułu dowiesz się, co się zmieniło i jak zmiany wpłynęły na zadania wykonywane obecnie przy użyciu aplikacji Azure Machine Learning Workbench i jej interfejsów API.

>[!Warning]
>Ten artykuł nie jest przeznaczony dla użytkowników środowiska Azure Machine Learning Studio. Dotyczy to Azure Machine Learning klientów, którzy zainstalowali aplikację Workbench (wersja zapoznawcza) i/lub mają konta eksperymentowanie i Zarządzanie modelami w wersji zapoznawczej.


## <a name="what-changed"></a>Co się zmieniło?

Najnowsza wersja Azure Machine Learning obejmuje następujące funkcje:
+ [Uproszczony model zasobów platformy Azure](concept-azure-machine-learning-architecture.md).
+ [Nowy interfejs użytkownika portalu](how-to-track-experiments.md) służący do zarządzania eksperymentami i celami obliczeniowymi.
+ Nowy, bardziej kompleksowy <a href="/python/api/overview/azure/ml/intro" target="_blank">zestaw SDK</a>dla języka Python.
+ Nowe, rozbudowane [rozszerzenie interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) na potrzeby uczenia maszynowego.

[Architektura](concept-azure-machine-learning-architecture.md) została zaprojektowana od nowa, aby ułatwić obsługę. Zamiast wielu zasobów i kont platformy Azure, będziesz potrzebować tylko aplikacji [Azure Machine Learning Workspace](concept-workspace.md). Obszary robocze można szybko tworzyć w witrynie [Azure Portal](how-to-manage-workspace.md). Dzięki użyciu obszaru roboczego wielu użytkowników może przechowywać cele obliczeniowe szkoleń i wdrożeń, eksperymenty dotyczące modeli, obrazy platformy Docker, wdrożone modele itd.

Bieżąca wersja udostępnia nowe, ulepszone klienty interfejsu wiersza polecenia i zestawu SDK, ale sama klasyczna aplikacja Workbench została wycofana z użytku. Eksperymenty mogą być zarządzane za pomocą [pulpitu nawigacyjnego obszaru roboczego w programie Azure Machine Learning Studio](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal). Pulpit nawigacyjny umożliwia pobieranie historii, zarządzanie celami obliczeniowymi dołączonymi do obszaru roboczego, zarządzanie modelami i obrazami platformy Docker, a nawet wdrażanie usług internetowych.

<a name="timeline"></a>

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

9 stycznia 2019 pomoc techniczną dla Machine Learning kont Workbench, Eksperymentowanie w usłudze Azure Machine Learning i Zarządzanie modelami oraz skojarzonych z nimi zestawów SDK i interfejsu wiersza polecenia.

Wszystkie najnowsze możliwości są dostępne za pomocą tego <a href="/python/api/overview/azure/ml/intro" target="_blank">zestawu SDK</a>, [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) i [portalu](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>A co z historią uruchamiania?

Starsze historie uruchamiania nie są już dostępne, ale nadal można wyświetlać uruchomienia w najnowszej wersji.

Historie uruchamiania są teraz nazywane **eksperymentami**. Można zbierać eksperymenty modelu i eksplorować je za pomocą zestawu SDK, interfejsu wiersza polecenia lub Azure Machine Learning Studio.

Pulpit nawigacyjny obszaru roboczego w portalu jest obsługiwany tylko w przeglądarkach Microsoft Edge, Chrome i Firefox:

[![Portal online](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Rozpocznij uczenie modeli i śledzenie historii uruchamiania przy użyciu nowego interfejsu wiersza polecenia i zestawu SDK. Możesz dowiedzieć się, jak za pomocą [samouczka: uczenie modeli za pomocą Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Czy projekty będą utrwalane?

Nie stracisz żadnego kodu ani wykonanej pracy. W starszej wersji projekty są jednostkami chmury z katalogiem lokalnym. W najnowszej wersji dołączysz katalogi lokalne do obszaru roboczego Azure Machine Learning przy użyciu lokalnego pliku konfiguracji. Zobacz [diagram przedstawiający najnowszą architekturę](concept-azure-machine-learning-architecture.md).

Większa część zawartości projektu znajduje się już na komputerze lokalnym. Wystarczy utworzyć w odpowiednim katalogu plik konfiguracji i odwołać się do niego w kodzie, aby nawiązać połączenie z obszarem roboczym. Aby nadal korzystać z lokalnego katalogu zawierającego pliki i skrypty, należy określić nazwę katalogu w poleceniu ["eksperyment. Submit"](/python/api/azureml-core/azureml.core.experiment.experiment) języka Python lub przy użyciu `az ml project attach` interfejsu wiersza polecenia.  Na przykład:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Utwórz obszar roboczy](how-to-manage-workspace.md) , aby rozpocząć.

## <a name="what-about-my-registered-models-and-images"></a>Co się stanie z moimi zarejestrowanymi modelami i obrazami?

Modele zarejestrowane w starym rejestrze modeli trzeba migrować do nowego obszaru roboczego, aby nadal z nich korzystać. Aby przeprowadzić migrację modeli, pobierz modele i ponownie zarejestruj je w nowym obszarze roboczym.

Obrazów utworzonych w starym rejestrze obrazu nie można bezpośrednio migrować do nowego obszaru roboczego. W większości przypadków model można wdrożyć bez konieczności tworzenia obrazu. W razie potrzeby można utworzyć obraz dla modelu w nowym obszarze roboczym. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie, rejestrowanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Co się stanie z wdrożonymi usługami internetowymi?

Teraz, gdy obsługa starego interfejsu wiersza polecenia została zakończona, nie możesz już ponownie wdrażać modeli ani zarządzać usługami internetowymi, które pierwotnie wdrożono za pomocą konta Zarządzania modelami. Jednak te usługi internetowe będą dalej działać tak długo, jak będzie obsługiwana usługa Azure Container Service (ACS).

W najnowszej wersji modele są wdrażane jako usługi internetowe w klastrach usługi Azure Container Instances (ACI) lub Azure Kubernetes Service (AKS). Można je również wdrażać w układach FPGA oraz w usłudze Azure IoT Edge.

Aby dowiedzieć się więcej, zobacz następujące artykuły:
+ [Where and how to deploy models (Gdzie i jak wdrażać modele)](how-to-deploy-and-where.md)
+ [Samouczek: Wdrażanie modeli przy użyciu Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Następne kroki

Poznaj [najnowszą architekturę Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Aby zapoznać się z omówieniem usługi, przeczytaj artykuł [co to jest Azure Machine Learning?](overview-what-is-azure-ml.md).

Utwórz pierwszy eksperyment przy użyciu preferowanej metody:

  + [Korzystanie z własnego środowiska](tutorial-1st-experiment-sdk-setup-local.md)
  + [Korzystanie z notesów języka Python](tutorial-1st-experiment-sdk-setup.md)
  + [Użyj R Markdown](https://github.com/Azure/azureml-sdk-for-r) 
  + [Korzystanie z automatycznej uczenia maszynowego](tutorial-designer-automobile-price-train-score.md) 
  + [Korzystanie z funkcji przeciągania & w projektancie](tutorial-first-experiment-automated-ml.md) 
  + [Użyj rozszerzenia ML do interfejsu wiersza polecenia](tutorial-train-deploy-model-cli.md)
