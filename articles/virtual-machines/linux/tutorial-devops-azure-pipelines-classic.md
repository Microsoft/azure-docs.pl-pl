---
title: Samouczek — Konfigurowanie wdrożeń stopniowych dla maszyn wirtualnych platformy Azure z systemem Linux
description: W tym samouczku dowiesz się, jak skonfigurować potok ciągłego wdrażania (CD). Ten potok stopniowo aktualizuje grupę maszyn wirtualnych platformy Azure z systemem Linux przy użyciu strategii wdrażania stopniowego.
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
ms.openlocfilehash: 6cc4fdb82e98c30ca33eec58675f2175dfcc2607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89612786"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Samouczek — Konfigurowanie strategii wdrażania stopniowego dla maszyn wirtualnych platformy Azure z systemem Linux

Azure DevOps to wbudowana usługa platformy Azure, która automatyzuje każdą część procesu DevOps dla dowolnego zasobu platformy Azure. Niezależnie od tego, czy aplikacja korzysta z maszyn wirtualnych, aplikacji sieci Web, Kubernetes czy innego zasobu, można zaimplementować infrastrukturę jako kod (IaC), ciągłej integracji, ciągłego testowania, ciągłego dostarczania i ciągłego monitorowania za pomocą platformy Azure i usługi Azure DevOps.

![Azure Portal z usługą Azure DevOps wybraną w obszarze usługi](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako usługa (IaaS) — Konfigurowanie ciągłej integracji/ciągłego wdrażania

Azure Pipelines udostępnia w pełni polecany zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Można skonfigurować potok ciągłego dostarczania dla maszyny wirtualnej platformy Azure z poziomu Azure Portal.

W tym artykule przedstawiono sposób konfigurowania potoku ciągłej integracji/ciągłego wdrażania z poziomu Azure Portal. Azure Portal obsługuje również inne strategie, takie jak [Kanaryjskie](https://aka.ms/AA7jdrz) i [niebieskie](https://aka.ms/AA83fwu).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurowanie ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych

Maszyny wirtualne można dodać jako obiekty docelowe do [grupy wdrożenia](/azure/devops/pipelines/release/deployment-groups). Następnie można wskazać je na potrzeby aktualizacji wielomaszynowych. Po wdrożeniu na maszynach należy wyświetlić **historię wdrożenia** w ramach grupy wdrożenia. Ten widok umożliwia śledzenie z maszyny wirtualnej do potoku, a następnie do zatwierdzenia.

### <a name="rolling-deployments"></a>Wdrożenia stopniowe

W każdej iteracji wdrożenie stopniowe zastępuje wystąpienia poprzedniej wersji aplikacji. Zastępuje je wystąpieniami nowej wersji na stałym zestawie maszyn (zestaw stopniowy). Poniższy przewodnik przedstawia sposób konfigurowania aktualizacji stopniowych na maszynach wirtualnych.

Korzystając z opcji ciągłego dostarczania, można skonfigurować aktualizacje stopniowe na maszynach wirtualnych w ramach Azure Portal. Oto instrukcje krok po kroku:

1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej.
1. W okienku po lewej stronie ustawień maszyny wirtualnej wybierz pozycję **ciągłe dostarczanie**. Następnie wybierz pozycję **Konfiguruj**.

   ![Okienko ciągłe dostarczanie z przyciskiem Konfiguruj](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. W panelu konfiguracja wybierz pozycję **Azure DevOps Organization (organizacja** ), aby wybrać istniejące konto lub Utwórz nowe. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  

   ![Panel ciągłego dostarczania](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne. Przykłady dla deweloperów, testów, przeprowadzających i produkcji. Można utworzyć nową grupę wdrożenia lub wybrać istniejącą.
1. Wybierz potok kompilacji, który publikuje pakiet przeznaczony do wdrożenia na maszynie wirtualnej. Opublikowany pakiet powinien mieć skrypt wdrożenia o nazwie deploy.ps1 lub deploy.sh w folderze deployscripts w folderze głównym pakietu. Potok uruchamia ten skrypt wdrożenia.
1. W obszarze **strategia wdrażania**wybierz pozycję **stopniowe**.
1. Opcjonalnie można oznaczyć każdą maszynę z jej rolą. Przykłady tagów "Web" i "DB". Tagi te ułatwiają kierowanie tylko do maszyn wirtualnych, które mają określoną rolę.
1. Wybierz **przycisk OK** , aby skonfigurować potok ciągłego dostarczania.
1. Po zakończeniu konfiguracji masz potok ciągłego dostarczania skonfigurowany do wdrożenia na maszynie wirtualnej.  

   ![Panel ciągłego dostarczania przedstawiający historię wdrożenia](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Zostaną wyświetlone szczegóły wdrożenia maszyny wirtualnej. Możesz wybrać łącze, aby przejść do potoku, **wydanie-1** , aby wyświetlić wdrożenie, lub **edytować** , aby zmodyfikować definicję potoku wydania.

1. W przypadku konfigurowania wielu maszyn wirtualnych Powtórz kroki od 2 do 4 dla innych maszyn wirtualnych, które mają zostać dodane do grupy wdrożenia. W przypadku wybrania grupy wdrożenia, która ma już uruchomienie potoku, maszyny wirtualne zostaną dodane do grupy wdrożenia. Nie są tworzone żadne nowe potoki.
1. Po zakończeniu konfiguracji wybierz definicję potoku, przejdź do organizacji usługi Azure DevOps, a następnie wybierz pozycję **Edytuj** dla potoku wydania.

   ![Edytowanie potoku kroczącego](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Wybierz **1 zadanie, 1 zadanie** w fazie **dev** . Wybierz fazę **Wdróż** .

   ![Równoległe zadania potokowe z wybranym zadaniem Wdróż](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. W okienku konfiguracji po prawej stronie można określić liczbę maszyn, które mają zostać wdrożone równolegle w każdej iteracji. Jeśli chcesz wdrożyć na wielu maszynach jednocześnie, możesz określić liczbę maszyn jako wartość procentową za pomocą suwaka.  

1. Zadanie wykonaj wdrożenie skryptu domyślnie wykonuje skrypt wdrażania deploy.ps1 lub deploy.sh. Skrypt znajduje się w folderze deployscripts w folderze głównym opublikowanego pakietu.

   ![Okienko artefakty pokazujące deploy.sh w folderze deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Inne strategie wdrażania

- [Konfigurowanie strategii wdrażania programu Kanaryjskie](https://aka.ms/AA7jdrz)
- [Konfigurowanie strategii wdrażania Blue-Green](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Możesz szybko rozpocząć pracę z platformą Azure. Korzystając z Azure DevOps Projects, zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach, wybierając:

- Język aplikacji
- Środowisko uruchomieniowe
- Usługa platformy Azure
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Zasoby dodatkowe

- [Wdrażanie na maszynach wirtualnych platformy Azure przy użyciu Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
