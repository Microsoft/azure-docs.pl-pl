---
title: Samouczek — Konfigurowanie wdrożeń programu Kanaryjskie dla Linux Virtual Machines platformy Azure
description: W tym samouczku dowiesz się, jak skonfigurować potok ciągłego wdrażania (CD). Ten potok aktualizuje grupę maszyn wirtualnych platformy Azure z systemem Linux przy użyciu strategii wdrażania programu Kanaryjskie.
author: moala
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552511"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Samouczek — Konfigurowanie strategii wdrażania programu Kanaryjskie dla usługi Azure Linux Virtual Machines

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktura jako usługa (IaaS) — Konfigurowanie ciągłej integracji/ciągłego wdrażania

Azure Pipelines udostępnia w pełni polecany zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Można skonfigurować potok ciągłego dostarczania dla maszyny wirtualnej platformy Azure z poziomu Azure Portal.

W tym artykule przedstawiono sposób konfigurowania potoku ciągłej integracji/ciągłego wdrażania, który używa strategii Kanaryjskich dla wdrożeń wielomaszynowych. Azure Portal obsługuje również inne strategie, takie jak [stopniowe](./tutorial-devops-azure-pipelines-classic.md) i [niebieskie](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>Konfigurowanie ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych

Maszyny wirtualne można dodać jako obiekty docelowe do [grupy wdrożenia](/azure/devops/pipelines/release/deployment-groups). Następnie można wskazać je na potrzeby aktualizacji wielomaszynowych. Po wdrożeniu na maszynach należy wyświetlić **historię wdrożenia** w ramach grupy wdrożenia. Ten widok umożliwia śledzenie z maszyny wirtualnej do potoku, a następnie do zatwierdzenia.

### <a name="canary-deployments"></a>Wdrożenia Kanaryjskich

Wdrożenie programu Kanaryjskie zmniejsza ryzyko przez spowolnienie wprowadzania zmian do małego podzbioru użytkowników. W celu uzyskania pewności w nowej wersji można wydać ją na więcej serwerów w infrastrukturze i skierować do niej więcej użytkowników.

Korzystając z opcji ciągłego dostarczania, można skonfigurować wdrożenia programu Kanaryjskie na maszynach wirtualnych z poziomu Azure Portal. Oto instrukcje krok po kroku:

1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej.
1. W okienku po lewej stronie ustawień maszyny wirtualnej wybierz pozycję **ciągłe dostarczanie**. Następnie wybierz pozycję **Konfiguruj**.

   ![Okienko ciągłe dostarczanie z przyciskiem Konfiguruj](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. W panelu konfiguracja wybierz pozycję **Azure DevOps Organization (organizacja** ), aby wybrać istniejące konto lub Utwórz nowe. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  

   ![Panel ciągłego dostarczania](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne. Przykłady dla deweloperów, testów, przeprowadzających i produkcji. Można utworzyć nową grupę wdrożenia lub wybrać istniejącą.
1. Wybierz potok kompilacji, który publikuje pakiet przeznaczony do wdrożenia na maszynie wirtualnej. Opublikowany pakiet powinien mieć skrypt wdrożenia o nazwie deploy.ps1 lub deploy.sh w folderze deployscripts w folderze głównym pakietu. Potok uruchamia ten skrypt wdrożenia.
1. W obszarze **strategia wdrażania** wybierz pozycję **Kanaryjskie**.
1. Dodaj tag "Kanaryjskie" do maszyn wirtualnych, które będą częścią wdrożeń Kanaryjskich. Dodaj tag "prod" do maszyn wirtualnych, które są częścią wdrożeń wykonanych po pomyślnym wdrożeniu programu Kanaryjskie. Tagi ułatwiają kierowanie wyłącznie do maszyn wirtualnych, które mają określoną rolę.

   ![Panel dostarczania ciągłego z wybraną wartością strategii wdrożenia Kanaryjskich](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Wybierz **przycisk OK** , aby skonfigurować potok ciągłego dostarczania do wdrożenia na maszynie wirtualnej.

   ![Potok Kanaryjskie](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Zostaną wyświetlone szczegóły wdrożenia maszyny wirtualnej. Możesz wybrać link, aby przejść do potoku wydania w usłudze Azure DevOps. W potoku wydania wybierz pozycję **Edytuj** , aby wyświetlić konfigurację potoku. Potok ma trzy fazy:

   1. Ta faza to faza grupy wdrożenia. Aplikacje są wdrażane na maszynach wirtualnych, które są oznaczone jako "Kanaryjskie".
   1. W tej fazie potok zatrzymuje się i czeka na ręczną interwencję w celu wznowienia uruchomienia.
   1. Jest to ponownie faza grupy wdrożenia. Aktualizacja została teraz wdrożona na maszynach wirtualnych oznaczonych jako "prod".

      ![Okienko grupy wdrożenia dla zadania wdrażania programu Kanaryjskie](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Przed wznowieniem uruchomienia potoku upewnij się, że co najmniej jedna maszyna wirtualna jest oznaczona jako "prod". W trzeciej fazie potoku aplikacje są wdrażane tylko na tych maszynach wirtualnych, które mają tag "prod".

1. Zadanie wykonaj wdrożenie skryptu domyślnie uruchamia skrypt wdrażania deploy.ps1 lub deploy.sh. Skrypt znajduje się w folderze deployscripts w folderze głównym opublikowanego pakietu. Upewnij się, że wybrany potok kompilacji opublikuje wdrożenie w folderze głównym pakietu.

   ![Okienko artefakty pokazujące deploy.sh w folderze deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Inne strategie wdrażania
- [Konfigurowanie strategii wdrażania stopniowego](./tutorial-devops-azure-pipelines-classic.md)
- [Konfigurowanie strategii wdrażania Blue-Green](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Możesz szybko rozpocząć pracę z platformą Azure. Korzystając z Azure DevOps Projects, zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach, wybierając:

- Język aplikacji
- Środowisko uruchomieniowe
- Usługa platformy Azure

[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Wdrażanie na maszynach wirtualnych platformy Azure przy użyciu Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)