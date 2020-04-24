---
title: Samouczek — Konfigurowanie wdrożeń stopniowych dla usługi Azure Linux Virtual Machines
description: W tym samouczku dowiesz się, jak skonfigurować potok ciągłego wdrażania (CD), który przyrostowo aktualizuje grupę platformy Azure Linux Virtual Machines przy użyciu strategii wdrażania stopniowego
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
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113491"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Samouczek — Konfigurowanie strategii wdrażania stopniowego dla platformy Azure Linux Virtual Machines

Azure DevOps to wbudowana usługa platformy Azure, która automatyzuje każdą część procesu DevOps z ciągłą integracją i ciągłym dostarczaniem dla dowolnego zasobu platformy Azure.
Niezależnie od tego, czy aplikacja korzysta z maszyn wirtualnych, aplikacji sieci Web, Kubernetes czy innego zasobu, można zaimplementować infrastrukturę jako kod, ciągłą integrację, ciągłe testowanie, ciągłe dostarczanie i ciągłe monitorowanie za pomocą platformy Azure i usługi Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS — Konfigurowanie ciągłej integracji/ciągłego dostarczania 
Azure Pipelines zawiera kompletny, w pełni funkcjonalny zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Potok ciągłego dostarczania można skonfigurować dla maszyny wirtualnej platformy Azure bezpośrednio z poziomu Azure Portal. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania z Azure Portal. Możesz również zapoznać się z innymi strategiami, takimi jak wyspy [Kanaryjskie](https://aka.ms/AA7jdrz) i [Blue-Green](https://aka.ms/AA83fwu), które są obsługiwane przez program Azure Portal. 


**Konfigurowanie ciągłej integracji/ciągłego wdrażania Virtual Machines**

Maszyny wirtualne mogą być dodawane jako elementy docelowe do [grupy wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) i mogą być przeznaczone do aktualizacji wielomaszynowych. Po wdrożeniu **historia wdrożenia** w ramach grupy wdrożenia zapewnia możliwość śledzenia z maszyny wirtualnej do potoku, a następnie do zatwierdzenia. 
 

**Wdrożenia stopniowe**: wdrożenie stopniowe zastępuje wystąpienia poprzedniej wersji aplikacji wystąpieniami nowej wersji aplikacji na stałym zestawie maszyn (zestaw stopniowy) w każdej iteracji. Zobaczmy, jak skonfigurować aktualizację stopniową dla maszyn wirtualnych.  
Można skonfigurować aktualizacje stopniowe dla "**maszyn wirtualnych**" w Azure Portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku. 
1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej. 
2. W lewym okienku maszyny wirtualnej przejdź do menu **ciągłe dostarczanie** . Następnie kliknij pozycję **Konfiguruj**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. W panelu Konfiguracja kliknij pozycję "organizacja usługi Azure DevOps", aby wybrać istniejące konto, lub utwórz je. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne; na przykład "dev", "test", "przeprowadzających" i "Product". Można utworzyć nową grupę wdrożenia lub wybrać istniejącą grupę wdrożenia. 
5. Wybierz potok kompilacji, który publikuje pakiet przeznaczony do wdrożenia na maszynie wirtualnej. Należy pamiętać, że opublikowany pakiet powinien mieć _wdrożony skrypt wdrożenia. ps1_ lub _Deploy.sh_ w `deployscripts` folderze w katalogu głównym pakietu. Ten skrypt wdrażania zostanie uruchomiony przez potok Azure DevOps w czasie wykonywania.
6. Wybierz wybraną strategię wdrażania. W takim przypadku program umożliwia wybranie instrukcji "roll".
7. Opcjonalnie można oznaczyć maszynę z rolą. Na przykład "Web", "DB" itd. Ułatwia to kierowanie maszyn wirtualnych, które mają tylko określoną rolę.
8. Kliknij przycisk **OK** w oknie dialogowym, aby skonfigurować potok ciągłego dostarczania. 
9. Po wykonaniu tej czynności będziesz mieć potok ciągłego dostarczania skonfigurowany do wdrożenia na maszynie wirtualnej.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Zobaczysz, że wdrożenie maszyny wirtualnej jest w toku. Możesz kliknąć link, aby przejść do potoku. Kliknij pozycję **Release-1** , aby wyświetlić wdrożenie. Możesz też kliknąć pozycję **Edytuj** , aby zmodyfikować definicję potoku wydania. 
11. Jeśli masz wiele maszyn wirtualnych do skonfigurowania, powtórz kroki 2-4 dla innych maszyn wirtualnych, które mają zostać dodane do grupy wdrożenia. Należy pamiętać, że w przypadku wybrania grupy wdrożenia, dla której uruchomienie potoku już istnieje, maszyna wirtualna zostanie dodana do grupy wdrożenia bez tworzenia żadnych nowych potoków. 
12. Po zakończeniu kliknij definicję potoku, przejdź do organizacji usługi Azure DevOps, a następnie kliknij pozycję **Edytuj** potok wersji. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Kliknij **zadanie Link 1, 1 zadanie** w fazie **deweloperskiej** . Kliknij fazę **Wdróż** .
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. W okienku Konfiguracja po prawej stronie możesz określić liczbę maszyn, które mają zostać wdrożone równolegle w każdej iteracji. Jeśli chcesz wdrożyć na wielu maszynach jednocześnie, możesz określić ją w procentach przy użyciu suwaka.  

15. Zadanie wykonaj wdrożenie skryptu domyślnie wykona skrypt wdrożenia _Deploy. ps1_ lub _Deploy.sh_ w folderze "deployscripts" w katalogu głównym opublikowanego pakietu.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Inne strategie wdrażania

- [Konfiguruj strategię wdrażania w programie Kanaryjskie](https://aka.ms/AA7jdrz)
- [Konfigurowanie strategii wdrażania Blue-Green](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Projekt usługi Azure DevOps 
Rozpocznij pracę z platformą Azure łatwiej niż kiedykolwiek wcześniej.
 
Za pomocą DevOps Projects Zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach: Wybierz język aplikacji, środowisko uruchomieniowe i usługę platformy Azure.
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Dodatkowe zasoby 
- [Wdrażanie na platformie Azure Virtual Machines przy użyciu projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
