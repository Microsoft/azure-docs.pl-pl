---
title: Samouczek — Konfigurowanie wdrożeń programu Kanaryjskie dla maszyn wirtualnych platformy Azure z systemem Linux
description: W tym samouczku dowiesz się, jak skonfigurować potok ciągłego wdrażania (CD). Ten potok aktualizuje grupę maszyn wirtualnych platformy Azure z systemem Linux przy użyciu strategii wdrażania niebiesko-zielonego.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: f349ff62fe211f0610341864a4c7528ee6bfe9c5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961531"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Samouczek — Konfigurowanie strategii wdrażania Blue-Green dla maszyn wirtualnych platformy Azure z systemem Linux

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako usługa (IaaS) — Konfigurowanie ciągłej integracji/ciągłego wdrażania

Azure Pipelines udostępnia w pełni polecany zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Można skonfigurować potok ciągłego dostarczania dla maszyny wirtualnej platformy Azure z poziomu Azure Portal.

W tym artykule przedstawiono sposób konfigurowania potoku ciągłej integracji/ciągłego wdrażania, który używa strategii Blue-Green dla wdrożeń wielomaszynowych. Azure Portal obsługuje również inne strategie, takie jak [stopniowe](./tutorial-devops-azure-pipelines-classic.md) i [Kanaryjskie](./tutorial-azure-devops-canary-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurowanie ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych

Maszyny wirtualne można dodać jako obiekty docelowe do [grupy wdrożenia](/azure/devops/pipelines/release/deployment-groups). Następnie można wskazać je na potrzeby aktualizacji wielomaszynowych. Po wdrożeniu na maszynach należy wyświetlić **historię wdrożenia** w ramach grupy wdrożenia. Ten widok umożliwia śledzenie z maszyny wirtualnej do potoku, a następnie do zatwierdzenia.

### <a name="blue-green-deployments"></a>Wdrożenia Blue-Green

Wdrożenie Blue-Green zmniejsza czas przestoju, mając identyczne środowisko w stanie wstrzymania. W dowolnym momencie tylko jedno środowisko jest aktywne.

Podczas przygotowywania do nowej wersji Dokończ końcowy etap testowania w środowisku zielonym. Gdy oprogramowanie działa w środowisku zielonego, przełącz ruch, aby wszystkie żądania przychodzące przechodzą do zielonego środowiska. Niebieskie środowisko jest w stanie bezczynności.

Korzystając z opcji ciągłego dostarczania, można skonfigurować wdrożenia Blue-Green na maszynach wirtualnych z poziomu Azure Portal. Oto instrukcje krok po kroku:

1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej.
1. W okienku po lewej stronie ustawień maszyny wirtualnej wybierz pozycję **ciągłe dostarczanie**. Następnie wybierz pozycję **Konfiguruj**.

   ![Okienko ciągłe dostarczanie z przyciskiem Konfiguruj](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. W panelu konfiguracja wybierz pozycję **Azure DevOps Organization (organizacja** ), aby wybrać istniejące konto lub Utwórz nowe. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  

   ![Panel ciągłego dostarczania](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne. Przykłady dla deweloperów, testów, przeprowadzających i produkcji. Można utworzyć nową grupę wdrożenia lub wybrać istniejącą.
1. Wybierz potok kompilacji, który publikuje pakiet przeznaczony do wdrożenia na maszynie wirtualnej. Opublikowany pakiet powinien mieć skrypt wdrożenia o nazwie deploy.ps1 lub deploy.sh w folderze deployscripts w folderze głównym pakietu. Potok uruchamia ten skrypt wdrożenia.
1. W obszarze **strategia wdrażania**wybierz pozycję **niebieska i zielona**.
1. Dodaj tag "Blue" lub "zielony" do maszyn wirtualnych, które mają być częścią wdrożeń Blue-Green. Jeśli maszyna wirtualna jest dla roli w stanie wstrzymania, oznacz ją jako "Green" (zielony). W przeciwnym razie oznacz ją jako "niebieska".

   ![Panel dostarczania ciągłego z wybraną wartością strategii wdrażania Blue-Green](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Wybierz **przycisk OK** , aby skonfigurować potok ciągłego dostarczania do wdrożenia na maszynie wirtualnej.

   ![Potok Blue-zielony](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Zostaną wyświetlone szczegóły wdrożenia maszyny wirtualnej. Możesz wybrać link, aby przejść do potoku wydania w usłudze Azure DevOps. W potoku wydania wybierz pozycję **Edytuj** , aby wyświetlić konfigurację potoku. Potok ma trzy fazy:

   1. Ta faza to faza grupy wdrożenia. Aplikacje są wdrażane na maszynach wirtualnych w stanie wstrzymania, które są oznaczone jako "zielone".
   1. W tej fazie potok zatrzymuje się i czeka na ręczną interwencję w celu wznowienia uruchomienia. Użytkownicy mogą wznowić uruchomienie potoku po ręcznym zapewnieniu stabilności wdrożenia na maszynach wirtualnych oznaczonych jako "zielone".
   1. Ta faza zamienia Tagi "Blue" i "Green" na maszynach wirtualnych. Dzięki temu maszyny wirtualne ze starszymi wersjami aplikacji będą teraz oznaczone jako "zielone". Podczas następnego uruchomienia potoku aplikacje zostaną wdrożone na tych maszynach wirtualnych.

      ![Okienko grupy wdrożenia dla zadania wdrażania Blue-Green](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Zadanie wykonaj wdrożenie skryptu domyślnie uruchamia skrypt wdrażania deploy.ps1 lub deploy.sh. Skrypt znajduje się w folderze deployscripts w folderze głównym opublikowanego pakietu. Upewnij się, że wybrany potok kompilacji opublikuje wdrożenie w folderze głównym pakietu.

   ![Okienko artefakty pokazujące deploy.sh w folderze deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Inne strategie wdrażania

- [Konfigurowanie strategii wdrażania stopniowego](./tutorial-devops-azure-pipelines-classic.md)
- [Konfigurowanie strategii wdrażania programu Kanaryjskie](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Możesz szybko rozpocząć pracę z platformą Azure. Korzystając z Azure DevOps Projects, zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach, wybierając:

- Język aplikacji
- Środowisko uruchomieniowe
- Usługa platformy Azure

[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Wdrażanie na maszynach wirtualnych platformy Azure przy użyciu Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)