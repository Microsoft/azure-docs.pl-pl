---
title: Co to jest obszar roboczy
titleSuffix: Azure Machine Learning
description: Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning. Zachowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawek skryptów. Te informacje służą do określenia, które uruchomienie szkoleniowe produkuje najlepszy model
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 5b40ce0951e2d9c8933d4f0ea9d24b1673e254d6
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495721"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co to jest obszar roboczy usługi Azure Machine Learning?

Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning, co zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami tworzonymi podczas korzystania z Azure Machine Learning.  Obszar roboczy zachowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawek skryptów. Te informacje służą do określenia, które uruchomienie szkoleniowe produkuje najlepszy model.  

Gdy masz model, możesz zarejestrować go w obszarze roboczym. Następnie można użyć zarejestrowanego modelu i skryptów oceniania w celu wdrożenia do Azure Container Instances, usługi Azure Kubernetes lub do nieprogramowalnej bramy polowej (FPGA) jako punkt końcowy HTTP oparty na protokole REST. Możesz również wdrożyć model na urządzeniu Azure IoT Edge jako moduł.

## <a name="taxonomy"></a>Taksonomii 

Taksonomia obszaru roboczego przedstawiono na poniższym diagramie:

[![Taksonomia obszaru roboczego](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Na diagramie przedstawiono następujące składniki obszaru roboczego:

+ Obszar roboczy może zawierać [Azure Machine Learning wystąpienia obliczeniowe](concept-compute-instance.md), zasoby w chmurze skonfigurowane ze środowiskiem Python niezbędne do uruchomienia Azure Machine Learning.

+ [Role użytkowników](how-to-assign-roles.md) umożliwiają udostępnianie obszaru roboczego innym użytkownikom, zespołom lub projektom.
+ [Cele obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) są używane do uruchamiania eksperymentów.
+ Podczas tworzenia obszaru roboczego są również tworzone [skojarzone zasoby](#resources) .
+ [Eksperymenty](concept-azure-machine-learning-architecture.md#experiments) to przebiegi szkoleniowe, które służą do kompilowania modeli.  
+ [Potoki](concept-azure-machine-learning-architecture.md#ml-pipelines) to przepływy pracy wielokrotnego użytku na potrzeby szkoleń i ponownego uczenia modelu.
+ [Zestawy](concept-azure-machine-learning-architecture.md#datasets-and-datastores) danych ułatwiają zarządzanie danymi używanymi do szkolenia modelu i tworzenia potoku.
+ Po utworzeniu modelu do wdrożenia należy utworzyć zarejestrowany model.
+ Użyj zarejestrowanego modelu i skryptu oceniania, aby utworzyć [punkt końcowy wdrożenia](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Narzędzia do interakcji z obszarem roboczym

Z obszarem roboczym można korzystać w następujący sposób:

> [!IMPORTANT]
> Narzędzia oznaczone poniżej (wersja zapoznawcza) są obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ W sieci Web:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Projektant usługi Azure Machine Learning](concept-designer.md) 
+ W dowolnym środowisku języka Python z [zestawem SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).
+ W dowolnym środowisku R z [zestawem SDK Azure Machine Learning dla języka r (wersja zapoznawcza)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ W wierszu polecenia przy użyciu [rozszerzenia CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) Azure Machine Learning
+ [Rozszerzenie VS Code Azure Machine Learning](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Uczenie maszynowe za pomocą obszaru roboczego

Zadania uczenia maszynowego odczytują i/lub zapisują artefakty w obszarze roboczym.

+ Uruchom eksperyment, aby szkolić model — zapisuje wyniki przebiegu eksperymentu w obszarze roboczym.
+ Użyj zautomatyzowanej ML do uczenia modelu — zapisuje wyniki szkolenia do obszaru roboczego.
+ Zarejestruj model w obszarze roboczym.
+ Wdrażanie modelu — program używa zarejestrowanego modelu do utworzenia wdrożenia.
+ Twórz i uruchamiaj przepływy pracy wielokrotnego użytku.
+ Wyświetlanie artefaktów uczenia maszynowego, takich jak eksperymenty, potoki, modele, wdrożenia.
+ Śledź i monitoruj modele.

## <a name="workspace-management"></a>Zarządzanie obszarem roboczym

Można również wykonać następujące zadania zarządzania obszarem roboczym:

| Zadanie zarządzania obszarem roboczym   | Portal              | Studio | Zestaw SDK języka Python/SDK       | Interfejs wiersza polecenia        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Tworzenie obszaru roboczego        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Zarządzanie dostępem do obszaru roboczego    | **&check;**   || |  **&check;**    ||
| Tworzenie zasobów obliczeniowych i zarządzanie nimi    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Tworzenie maszyny wirtualnej notesu |   | **&check;** | |     ||

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Tworzenie obszaru roboczego

Istnieje wiele sposobów tworzenia obszaru roboczego:  

* Użyj [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) dla interfejsu typu punkt-i-kliknięcie, aby przeprowadzić Cię przez każdy krok.
* Użyj [zestawu SDK Azure Machine Learning dla języka Python](how-to-manage-workspace.md?tabs=python#create-a-workspace) , aby utworzyć obszar roboczy na bieżąco ze skryptów języka Python lub notesów Jupiter
* Użyj [szablonu Azure Resource Manager](how-to-create-workspace-template.md) lub [interfejsu wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md) , gdy zachodzi potrzeba automatyzacji lub dostosowania tworzenia przy użyciu firmowych standardów zabezpieczeń.
* Jeśli pracujesz w Visual Studio Code, użyj [rozszerzenia vs Code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> W nazwie obszaru roboczego nie jest rozróżniana wielkość liter.

## <a name="associated-resources"></a><a name="resources"></a> Skojarzone zasoby

Podczas tworzenia nowego obszaru roboczego program automatycznie tworzy kilka zasobów platformy Azure, które są używane przez obszar roboczy:

+ [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/): służy jako domyślny magazyn danych dla obszaru roboczego.  Tutaj są przechowywane notesy Jupyter, które są używane z wystąpieniami obliczeniowymi Azure Machine Learning. 
  
  > [!IMPORTANT]
  > Domyślnie konto magazynu jest kontem ogólnego przeznaczenia w wersji 1. Po utworzeniu obszaru roboczego możesz [uaktualnić go do ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) . Nie włączaj hierarchicznej przestrzeni nazw na koncie magazynu po uaktualnieniu do ogólnego przeznaczenia w wersji 2.

  Aby użyć istniejącego konta usługi Azure Storage, nie może ono być kontem Premium (Premium_LRS i Premium_GRS). Nie może ona również mieć hierarchicznej przestrzeni nazw (używane z Azure Data Lake Storage Gen2). W przypadku _domyślnego_ konta magazynu obszaru roboczego nie są obsługiwane żadne magazyny w warstwie Premium ani hierarchiczne przestrzenie nazw. Możesz użyć magazynu w warstwie Premium lub hierarchicznej przestrzeni nazw z kontami magazynu _innego niż domyślne_ .
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): rejestruje kontenery platformy Docker, które są używane podczas uczenia i wdrażania modelu. Aby zminimalizować koszty, ACR jest **załadowane z opóźnieniem** do momentu utworzenia obrazów wdrożenia.

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): przechowuje informacje o monitorowaniu dla modeli.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): przechowuje wpisy tajne, które są używane przez cele obliczeniowe i inne poufne informacje, które są zbędne w obszarze roboczym.

> [!NOTE]
> Możesz zamiast tego używać istniejących wystąpień zasobów platformy Azure podczas tworzenia obszaru roboczego za pomocą [zestawu SDK języka Python](how-to-manage-workspace.md?tabs=python#create-a-workspace), [zestawu SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)w języku R lub interfejsu wiersza polecenia Azure Machine Learning [przy użyciu szablonu ARM](how-to-create-workspace-template.md).

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Co się stało z wersją Enterprise Edition

Od września 2020 wszystkie funkcje, które były dostępne w obszarze roboczym wersji Enterprise Edition, są teraz również dostępne w obszarze roboczym Basic Edition. Nie można już tworzyć nowych obszarów roboczych przedsiębiorstwa.  Wszystkie wywołania zestawu SDK, interfejsu wiersza polecenia lub Azure Resource Manager, które używają `sku` parametru, będą nadal działać, ale zostanie zainicjowana podstawowa przestrzeń robocza.

Od 21 grudnia wszystkie obszary robocze wersji Enterprise Edition zostaną automatycznie ustawione na wersję Basic, która ma takie same możliwości. W trakcie tego procesu nie wystąpi Przestój. 1 stycznia 2021, wersja Enterprise zostanie formalnie wycofana. 

W obu wersjach klienci są odpowiedzialni za koszty zużywanych zasobów platformy Azure i nie będą musieli płacić żadnych dodatkowych opłat za Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) .

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Azure Machine Learning, zobacz:

+ [Przegląd Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Tworzenie obszaru roboczego i zarządzanie nim](how-to-manage-workspace.md)
+ [Samouczek: wprowadzenie do Azure Machine Learning w środowisku deweloperskim](tutorial-1st-experiment-sdk-setup-local.md)
+ [Samouczek: wprowadzenie do tworzenia pierwszego eksperymentu w ramach wystąpienia obliczeniowego](tutorial-1st-experiment-sdk-setup.md)
+ [Samouczek: wprowadzenie do Azure Machine Learning za pomocą zestawu SDK języka R](tutorial-1st-r-experiment.md)
+ [Samouczek: Tworzenie pierwszego modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego](tutorial-first-experiment-automated-ml.md) 
+ [Samouczek: przewidywanie ceny za samochód przy użyciu narzędzia Projektant](tutorial-designer-automobile-price-train-score.md)
