---
title: Omówienie usługi Azure DevOps Starter | Microsoft Docs
description: Informacje o wartości dla platformy Azure DevOps Starter
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
ms.openlocfilehash: 99a1fdb8caff9953041c996d0f5581318ce11c66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233690"
---
# <a name="overview-of-azure-devops-starter"></a>Omówienie usługi Azure DevOps Starter

 Usługa Azure DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Pomaga ona uruchomić ulubioną aplikację w usłudze platformy Azure wybranej w kilku szybkich krokach w witrynie Azure Portal. 

 DevOps Starter konfiguruje wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji. Możesz użyć pulpitu nawigacyjnego DevOps Starter do monitorowania zatwierdzeń kodu, kompilacji i wdrożeń, a wszystko to w jednym widoku w Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Zalety korzystania z DevOps Starter

  DevOps Starter automatyzuje instalację całego potoku ciągłej integracji i ciągłego dostarczania na platformę Azure.  Możesz rozpocząć z istniejącym kodem lub użyć jednej z dostarczonych aplikacji przykładowych. Następnie możesz szybko wdrożyć tę aplikację w różnych usługach platformy Azure, takich jak Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database i Azure Service Fabric.  

  DevOps Starter wykonuje wszystkie czynności związane z początkową konfiguracją potoku DevOps, w tym wszystko, od skonfigurowania początkowego repozytorium git, skonfigurowania potoku ciągłej integracji/ciągłego wdrażania, tworzenia zasobu Application Insights na potrzeby monitorowania i zapewnienia pojedynczego widoku całego rozwiązania przy tworzeniu DevOps Projects pulpitu nawigacyjnego w Azure Portal.

Możesz użyć DevOps Starter do:

* Szybkie wdrażanie aplikacji na platformie Azure
* Automatyzacja instalacji potoku ciągłej integracji/ciągłego wdrażania
* Wyświetlanie potoku ciągłej integracji/ciągłego wdrażania oraz poznanie sposobu jego prawidłowego konfigurowania
* Dalsze dostosowywanie potoków wydania w oparciu o określone scenariusze

## <a name="how-to-use-devops-starter"></a>Jak używać DevOps Starter?

  DevOps Starter jest dostępny w Azure Portal. Tworzysz zasób DevOps Start, tak jak w przypadku tworzenia dowolnego innego zasobu platformy Azure z poziomu portalu. Usługa DevOps Projects zapewnia podobne do kreatora środowisko z instrukcjami krok po kroku dotyczącymi różnych opcji konfiguracji.  

W ramach początkowej konfiguracji należy wybrać kilka opcji konfiguracji. Dostępne są następujące opcje:

* Używanie udostępnionej przykładowej aplikacji lub dodanie własnego kodu
* Wybieranie języka programowania aplikacji
* Wybieranie platformy aplikacji na podstawie języka
* Wybieranie usługi platformy Azure (cel wdrożenia)
* Tworzenie nowej organizacji usługi Azure DevOps lub wybieranie istniejącej organizacji 
* Wybieranie subskrypcji platformy Azure
* Wybieranie lokalizacji usług platformy Azure
* Wybieranie spośród różnych warstw cenowych usług platformy Azure

Po użyciu DevOps Starter można także usunąć wszystkie zasoby z jednego miejsca z pulpitu nawigacyjnego DevOps Starter na Azure Portal.

## <a name="devops-starter-and-azure-devops-integration"></a>Integracja DevOps Starter i Azure DevOps

DevOps Starter jest obsługiwany przez usługę Azure DevOps. DevOps Starter automatyzuje wszystkie zadania, które są konieczne w Azure Pipelines do skonfigurowania potoku ciągłej integracji/ciągłego wdrażania. Usługa DevOps Projects tworzy repozytorium Git w nowej lub istniejącej organizacji usługi Azure DevOps, a następnie zatwierdza aplikację przykładową lub istniejący kod w nowym repozytorium Git.  

Automatyzacja ustala również wyzwalacz ciągłej integracji na potrzeby kompilacji, aby każde nowe zatwierdzenie kodu inicjowało kompilację. DevOps Starter tworzy wyzwalacz CD i wdraża każdą nową pomyślnie kompilację do wybranej usługi platformy Azure.  

Potoki kompilacji i wydania można dostosować do dodatkowych scenariuszy. Potoki kompilacji i wydania można także sklonować w celu użycia w innych projektach.

Po utworzeniu DevOps Starter możesz:

* Dostosowywanie potoku kompilacji i wydania
* Zarządzanie przepływem kodu i zapewnianie wysokiej jakości za pomocą żądań ściągnięcia
* Testowanie i kompilowanie każdego zatwierdzenia przed scaleniem kodu, aby podnieść poziom jakości
* Śledzenie listy prac i problemów wraz z aplikacją

## <a name="getting-started-with-devops-starter"></a>Wprowadzenie do korzystania z DevOps Starter

* [Wprowadzenie do DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Filmy wideo DevOps Starter

* [Tworzenie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
