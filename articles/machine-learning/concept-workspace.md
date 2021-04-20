---
title: Co to jest obszar roboczy
titleSuffix: Azure Machine Learning
description: Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning. Przechowuje historię wszystkich przebiegów trenowania, dzienników, metryk, danych wyjściowych i migawki skryptów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 215da0e38045a2e66a4a11b54204c26e7720815c
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719065"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co to jest obszar roboczy usługi Azure Machine Learning?

Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning, zapewniając scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz podczas korzystania z Azure Machine Learning.  Obszar roboczy przechowuje historię wszystkich przebiegów trenowania, w tym dzienniki, metryki, dane wyjściowe i migawkę skryptów. Te informacje są służące do określenia, który przebieg trenowania tworzy najlepszy model.  

Gdy już masz model, który Ci się podoba, zarejestruj go w obszarze roboczym. Następnie należy użyć zarejestrowanego modelu i skryptów oceniania do wdrożenia w programie Azure Container Instances, Azure Kubernetes Service lub w programowalnej w terenie tablicy bram (FPGA) jako punktu końcowego HTTP opartego na rest-based. Model można również wdrożyć na urządzeniu Azure IoT Edge jako moduł.

## <a name="taxonomy"></a>Taksonomii 

Taksonomia obszaru roboczego jest zilustrowana na poniższym diagramie:

[![Taksonomia obszaru roboczego](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diagram przedstawia następujące składniki obszaru roboczego:

+ Obszar roboczy może zawierać [Azure Machine Learning obliczeniowe,](concept-compute-instance.md)zasoby w chmurze skonfigurowane przy użyciu środowiska Python niezbędnego do uruchamiania Azure Machine Learning.

+ [Role użytkownika](how-to-assign-roles.md) umożliwiają udostępnianie obszaru roboczego innym użytkownikom, zespołom lub projektom.
+ [Docelowe obiekty obliczeniowe](concept-azure-machine-learning-architecture.md#compute-targets) są używane do uruchamiania eksperymentów.
+ Podczas tworzenia obszaru roboczego [zostaną](#resources) również utworzone skojarzone zasoby.
+ [Eksperymenty](concept-azure-machine-learning-architecture.md#experiments) to przebiegi trenowania, których używasz do tworzenia modeli.  
+ [Potoki to](concept-azure-machine-learning-architecture.md#ml-pipelines) przepływy pracy wielokrotnego użytku do trenowania i ponownego trenowania modelu.
+ [Zestawy danych](concept-azure-machine-learning-architecture.md#datasets-and-datastores) wspomagają zarządzanie danymi, których używasz do trenowania modelu i tworzenia potoku.
+ Po utworzeniu modelu, który chcesz wdrożyć, należy utworzyć zarejestrowany model.
+ Użyj zarejestrowanego modelu i skryptu oceniania, aby utworzyć punkt [końcowy wdrożenia](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Narzędzia do interakcji z obszarem roboczym

Z obszarem roboczym można wchodzić w interakcje w następujący sposób:

> [!IMPORTANT]
> Narzędzia oznaczone poniżej (wersja zapoznawcza) są obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ W Internecie:
    + [Azure Machine Learning studio ](https://ml.azure.com) 
    + [Projektant usługi Azure Machine Learning](concept-designer.md) 
+ W dowolnym środowisku języka Python z [zestawem AZURE MACHINE LEARNING SDK dla języka Python.](/python/api/overview/azure/ml/intro)
+ W dowolnym środowisku języka R z Azure Machine Learning [SDK dla języka R (wersja zapoznawcza).](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ W wierszu polecenia przy użyciu Azure Machine Learning [interfejsu wiersza polecenia](./reference-azure-machine-learning-cli.md)
+ [Azure Machine Learning VS Code rozszerzenia](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Uczenie maszynowe z obszarem roboczym

Zadania uczenia maszynowego odczytuje i/lub zapisuje artefakty w obszarze roboczym.

+ Uruchamianie eksperymentu w celu trenowania modelu — zapisuje wyniki uruchomienia eksperymentu w obszarze roboczym.
+ Trenowanie modelu przy użyciu zautomatyzowanego uczenia maszynowego — zapisuje wyniki trenowania w obszarze roboczym.
+ Rejestrowanie modelu w obszarze roboczym.
+ Wdrażanie modelu — używa zarejestrowanego modelu do utworzenia wdrożenia.
+ Tworzenie i uruchamianie przepływów pracy wielokrotnego użytku.
+ Wyświetlanie artefaktów uczenia maszynowego, takich jak eksperymenty, potoki, modele i wdrożenia.
+ Śledzenie i monitorowanie modeli.

## <a name="workspace-management"></a>Zarządzanie obszarem roboczym

Można również wykonać następujące zadania zarządzania obszarem roboczym:

| Zadanie zarządzania obszarem roboczym   | Portal              | Studio | Zestaw SDK języka Python/zestaw SDK języka R       | Interfejs wiersza polecenia        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Tworzenie obszaru roboczego        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Zarządzanie dostępem do obszaru roboczego    | **&check;**   || |  **&check;**    ||
| Tworzenie zasobów obliczeniowych i zarządzanie nimi    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Tworzenie maszyny wirtualnej notesu |   | **&check;** | |     ||

> [!WARNING]
> Przenoszenie Azure Machine Learning obszaru roboczego do innej subskrypcji lub przenoszenie subskrypcji jako właściciel do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Tworzenie obszaru roboczego

Istnieje wiele sposobów tworzenia obszaru roboczego:  

* Użyj [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) interfejsu typu "wskaż i kliknij", aby przejść przez poszczególne kroki.
* Korzystanie z [Azure Machine Learning SDK dla języka Python do](how-to-manage-workspace.md?tabs=python#create-a-workspace) tworzenia obszaru roboczego na bieżąco ze skryptów języka Python lub notesów Jupyter
* Użyj szablonu [Azure Resource Manager lub](how-to-create-workspace-template.md) interfejsu wiersza [Azure Machine Learning,](reference-azure-machine-learning-cli.md) gdy musisz zautomatyzować lub dostosować tworzenie przy użyciu firmowych standardów zabezpieczeń.
* Jeśli pracujesz w Visual Studio Code, użyj [rozszerzenia VS Code .](how-to-manage-resources-vscode.md#create-a-workspace)

> [!NOTE]
> W nazwie obszaru roboczego nie jest uwzględniania ich litera.

## <a name="associated-resources"></a><a name="resources"></a> Skojarzone zasoby

Podczas tworzenia nowego obszaru roboczego automatycznie tworzy on kilka zasobów platformy Azure, które są używane przez obszar roboczy:

+ [Konto usługi Azure Storage:](https://azure.microsoft.com/services/storage/)jest używane jako domyślny magazyn danych dla obszaru roboczego.  W tym miejscu są również przechowywane notesy Jupyter, które są używane Azure Machine Learning wystąpienia obliczeniowe. 
  
  > [!IMPORTANT]
  > Domyślnie konto magazynu jest kontem ogólnego przeznaczenia w wersji 1. Po [utworzeniu obszaru roboczego](../storage/common/storage-account-upgrade.md) można uaktualnić go do wersji 2 ogólnego przeznaczenia. Nie należy włączać hierarchicznej przestrzeni nazw na koncie magazynu po uaktualnieniu do wersji 2 ogólnego przeznaczenia.

  Aby użyć istniejącego konta usługi Azure Storage, nie może być typu BlobStorage ani konta Premium (konto Premium_LRS i Premium_GRS). Nie może również mieć hierarchicznej przestrzeni nazw (używanej z Azure Data Lake Storage Gen2). Domyślne konto magazynu obszaru roboczego nie  obsługuje ani magazynu w chmurze Premium, ani hierarchicznych przestrzeni nazw. Z kontami magazynu innego niż _domyślne_ można używać magazynu w chmurze Premium Storage lub hierarchicznej przestrzeni nazw.
  
+ [Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)rejestruje kontenery platformy Docker, które są stosowane podczas trenowania i wdrażania modelu. Aby zminimalizować koszty, do momentu utworzenia obrazów wdrożenia jest **ładowana** z opóźnieniem.

+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)przechowuje informacje o monitorowaniu modeli.

+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)przechowuje wpisy tajne używane przez docelowe obiekty obliczeniowe i inne poufne informacje wymagane przez obszar roboczy.

> [!NOTE]
> Zamiast tego można użyć istniejących wystąpień zasobów platformy Azure podczas tworzenia obszaru roboczego przy użyciu zestawu SDK języka [Python,](how-to-manage-workspace.md?tabs=python#create-a-workspace)zestawu [SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)języka R lub interfejsu wiersza Azure Machine Learning przy użyciu [szablonu usługi ARM.](how-to-create-workspace-template.md)

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Co się stało z wydaniem Enterprise

Od września 2020 r. wszystkie funkcje dostępne w obszarach roboczych wersji Enterprise są teraz również dostępne w obszarach roboczych w wersji Podstawowa. Nie można już tworzyć nowych obszarów roboczych przedsiębiorstwa.  Wszystkie wywołania zestawu SDK, interfejsu wiersza Azure Resource Manager interfejsu wiersza polecenia, które używają parametru , będą nadal działać, ale zostanie zaaprowizowany `sku` podstawowy obszar roboczy.

Od 21 grudnia wszystkie Enterprise Edition robocze zostaną automatycznie ustawione na wersję Podstawowa, która ma te same możliwości. W trakcie tego procesu nie wystąpi przestój. 1 stycznia 2021 r. Enterprise Edition zostanie formalnie wycofany. 

W obu wersjach klienci są odpowiedzialni za koszty zużywanych zasobów platformy Azure i nie muszą ponosić żadnych dodatkowych opłat za Azure Machine Learning. Aby uzyskać więcej [informacji, zobacz Azure Machine Learning cennika.](https://azure.microsoft.com/pricing/details/machine-learning/)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Azure Machine Learning, zobacz:

+ [Azure Machine Learning omówienie](overview-what-is-azure-ml.md)
+ [Tworzenie obszaru roboczego i zarządzanie nimi](how-to-manage-workspace.md)
+ [Samouczek: rozpoczynanie pracy z Azure Machine Learning w środowisku dewelopera](tutorial-1st-experiment-sdk-setup-local.md)
+ [Samouczek: wprowadzenie do tworzenia pierwszego eksperymentu uczenia maszynowego w wystąpieniu obliczeniowym](tutorial-1st-experiment-sdk-setup.md)
+ [Samouczek: Tworzenie pierwszego modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego](tutorial-first-experiment-automated-ml.md) 
+ [Samouczek: Przewidywanie ceny samochodów w projektancie](tutorial-designer-automobile-price-train-score.md)
