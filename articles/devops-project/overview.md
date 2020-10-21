---
title: Omówienie DevOps Starter dla platformy Azure | Microsoft Docs
description: Zrozumienie wartości DevOps Starter
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330652"
---
# <a name="overview-of-devops-starter"></a>Przegląd DevOps Starter

 DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure przy użyciu akcji GitHub lub Azure DevOps. Pomaga ona uruchomić ulubioną aplikację w usłudze platformy Azure wybranej w kilku szybkich krokach w witrynie Azure Portal. 

 DevOps Starter konfiguruje wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji. Możesz użyć pulpitu nawigacyjnego DevOps Starter do monitorowania zatwierdzeń kodu, kompilacji i wdrożeń, a wszystko to w jednym widoku w Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Zalety korzystania z DevOps Starter

  DevOps Starter następujące obsługuje dwóch dostawców ciągłej integracji/ciągłego wdrażania, aby zautomatyzować wdrożenia
  * [Funkcja GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter automatyzuje instalację całej ciągłej integracji (CI) i ciągłego dostarczania (CD) dla aplikacji na platformie Azure.  Możesz rozpocząć z istniejącym kodem lub użyć jednej z dostarczonych aplikacji przykładowych. Następnie możesz szybko wdrożyć tę aplikację w różnych usługach platformy Azure, takich jak Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database i Azure Service Fabric.  

  DevOps Starter wykonuje wszystkie czynności związane z początkową konfiguracją potoku DevOps, w tym wszystko, od skonfigurowania początkowego repozytorium git, skonfigurowania potoku ciągłej integracji/ciągłego wdrażania, tworzenia zasobu Application Insights na potrzeby monitorowania i zapewnienia pojedynczego widoku całego rozwiązania przy tworzeniu pulpitu nawigacyjnego DevOps Starter w Azure Portal.

Możesz użyć DevOps Starter do:

* Szybkie wdrażanie aplikacji na platformie Azure
* Automatyzowanie instalacji przepływu pracy ciągłej integracji/ciągłego wdrażania
* Wyświetl i poznanie sposobu poprawnego skonfigurowania przepływu pracy ciągłej integracji/ciągłego wdrażania lub potoku
* Dalsze dostosowywanie potoków wydania w oparciu o określone scenariusze

## <a name="how-to-use-devops-starter"></a>Jak używać DevOps Starter?

  DevOps Starter jest dostępny w Azure Portal. Tworzysz zasób DevOps Start, tak jak w przypadku tworzenia dowolnego innego zasobu platformy Azure z poziomu portalu. Usługa DevOps Projects zapewnia podobne do kreatora środowisko z instrukcjami krok po kroku dotyczącymi różnych opcji konfiguracji.  

W ramach początkowej konfiguracji należy wybrać kilka opcji konfiguracji. Dostępne są następujące opcje:

* Wybieranie preferowanego dostawcy CI/CD
* Korzystanie z dostarczonej przykładowej aplikacji lub nadawanie własnego kodu (tylko w przypadku platformy Azure DevOps)
* Wybieranie języka programowania aplikacji
* Wybieranie platformy aplikacji na podstawie języka
* Wybieranie usługi platformy Azure (cel wdrożenia)
* Wybierz swoją organizację usług GitHub lub Azure DevOps
* Wybieranie subskrypcji platformy Azure
* Wybieranie lokalizacji usług platformy Azure
* Wybieranie spośród różnych warstw cenowych usług platformy Azure

Po utworzeniu DevOps Starter możesz:

* Dostosowywanie przepływu pracy w usłudze GitHub lub potoku usługi Azure DevOps
* Zarządzanie przepływem kodu i zapewnianie wysokiej jakości za pomocą żądań ściągnięcia
* Testowanie i kompilowanie każdego zatwierdzenia przed scaleniem kodu, aby podnieść poziom jakości

Po użyciu DevOps Starter można także usunąć wszystkie zasoby z jednego miejsca z pulpitu nawigacyjnego DevOps Starter na Azure Portal.

## <a name="devops-starter-and-github-integration"></a>DevOps Starter i integracja z usługą GitHub

DevOps Starter obsługuje teraz akcje usługi GitHub jako dostawcę ciągłej integracji/ciągłego dostarczania. Automatyzuje wszystkie zadania, które są niezbędne w serwisie GitHub, aby skonfigurować przepływ pracy ciągłej integracji/ciągłego wdrażania za pomocą akcji usługi GitHub. Tworzy repozytorium GitHub w istniejącej organizacji usługi GitHub, a następnie zatwierdza przykładową aplikację do nowego repozytorium GitHub.  

Automatyzacja również ustanawia wyzwalacz dla przepływu pracy, tak aby każde nowe zatwierdzenie kodu inicjuje zadanie kompilowania i wdrażania w ramach przepływu pracy. Aplikacja jest wdrażana w wybranym usłudze platformy Azure. Przepływ pracy w usłudze GitHub można dostosować do dodatkowych scenariuszy. 

## <a name="devops-starter-and-azure-devops-integration"></a>Integracja DevOps Starter i Azure DevOps

DevOps Starter przy użyciu usługi Azure DevOps automatyzuje wszystkie zadania, które są konieczne w Azure Pipelines, aby skonfigurować potok ciągłej integracji/ciągłego wdrażania. Usługa DevOps Projects tworzy repozytorium Git w nowej lub istniejącej organizacji usługi Azure DevOps, a następnie zatwierdza aplikację przykładową lub istniejący kod w nowym repozytorium Git.  

Automatyzacja ustala również wyzwalacz ciągłej integracji na potrzeby kompilacji, aby każde nowe zatwierdzenie kodu inicjowało kompilację. DevOps Starter tworzy wyzwalacz CD i wdraża każdą nową pomyślnie kompilację do wybranej usługi platformy Azure.  

Potoki kompilacji i wydania można dostosować do dodatkowych scenariuszy. Potoki kompilacji i wydania można także sklonować w celu użycia w innych projektach.

## <a name="getting-started-with-devops-starter"></a>Wprowadzenie do korzystania z DevOps Starter

* [Wprowadzenie do DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Filmy wideo DevOps Starter

* [Tworzenie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
