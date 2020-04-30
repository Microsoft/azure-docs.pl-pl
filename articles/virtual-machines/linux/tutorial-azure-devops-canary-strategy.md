---
title: Samouczek — Konfigurowanie wdrożeń programu Kanaryjskie dla Linux Virtual Machines platformy Azure
description: W tym samouczku dowiesz się, jak skonfigurować potok ciągłego wdrażania (CD), który aktualizuje grupę platformy Azure Linux Virtual Machines przy użyciu strategii wdrażania programu Kanaryjskie
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120478"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Samouczek — Konfigurowanie strategii wdrażania programu Kanaryjskie dla usługi Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS — Konfigurowanie ciągłej integracji/ciągłego dostarczania 
Azure Pipelines zawiera kompletny, w pełni funkcjonalny zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Potok ciągłego dostarczania można skonfigurować dla maszyny wirtualnej platformy Azure bezpośrednio z poziomu Azure Portal. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania, który używa strategii Kanaryjskich w przypadku wdrożeń obejmujących wiele maszyn. Możesz również zapoznać się z innymi strategiami, takimi jak [stopniowe](https://aka.ms/AA7jlh8) i [niebieskie](https://aka.ms/AA83fwu), które są obsługiwane w Azure Portal. 


**Konfigurowanie ciągłej integracji/ciągłego wdrażania Virtual Machines**

Maszyny wirtualne mogą być dodawane jako elementy docelowe do [grupy wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) i mogą być przeznaczone do aktualizacji na wiele maszyn. Po wdrożeniu **historia wdrożenia** w ramach grupy wdrożenia zapewnia możliwość śledzenia z maszyny wirtualnej do potoku, a następnie do zatwierdzenia. 
 
  
**Wdrożenia programu Kanaryjskie**: wdrożenie w programie Kanaryjskie zmniejsza ryzyko przez spowolnienie w przypadku zmiany do małego podzbioru użytkowników. W miarę zwiększania zaufania do nowej wersji można rozpocząć zwalnianie jej na więcej serwerów w infrastrukturze i kierowanie do niej większej liczby użytkowników. Wdrożenia programu Kanaryjskie można konfigurować na maszynach wirtualnych Azure Portal za pomocą opcji ciągłego dostarczania. Oto przewodnik krok po kroku. 
1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej 
2. W lewym okienku maszyny wirtualnej przejdź do menu **ciągłe dostarczanie** . Kliknij przycisk **Konfiguruj**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. W panelu Konfiguracja kliknij pozycję **Azure DevOps Organization** , aby wybrać istniejące konto lub utwórz je. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne; na przykład "dev", "test", "przeprowadzających" i "Product". Można utworzyć nową grupę wdrożenia lub wybrać istniejącą grupę wdrożenia. 
5. Wybierz potok kompilacji, który publikuje pakiet przeznaczony do wdrożenia na maszynie wirtualnej. Należy pamiętać, że opublikowany pakiet powinien mieć _wdrożony skrypt wdrożenia. ps1_ lub _Deploy.sh_ w `deployscripts` folderze w katalogu głównym pakietu. Ten skrypt wdrażania zostanie wykonany przez potok Azure DevOps w czasie wykonywania.
6. Wybierz wybraną strategię wdrażania. Wybierz pozycję **Kanaryjskie**.
7. Dodaj tag "Kanaryjskie" do maszyn wirtualnych, które mają być częścią wdrożeń Kanaryjskich, oraz tag "prod" do maszyn wirtualnych, które są częścią wdrożeń po pomyślnym wdrożeniu programu Kanaryjskie. Tagi ułatwiają kierowanie maszyn wirtualnych, które mają tylko określoną rolę.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Kliknij przycisk **OK** w oknie dialogowym, aby skonfigurować potok ciągłego dostarczania. Teraz będzie dostępny potok ciągłego dostarczania skonfigurowany do wdrożenia na maszynie wirtualnej.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Kliknij pozycję **Edytuj** potok wydania na platformie Azure DevOps, aby wyświetlić konfigurację potoku. Potok składa się z trzech faz. Pierwsza faza to faza grupy wdrożenia i wdrożenia na maszynach wirtualnych, które są oznaczone jako _Kanaryjskie_. Druga faza, wstrzymuje potok i czeka na ręczną interwencję w celu wznowienia uruchomienia. Gdy użytkownik ma pewność, że wdrożenie narzędzia Kanaryjskie jest stabilne, może wznowić uruchomienie potoku, a następnie uruchomi trzecią fazę, która jest wdrażana na maszynach wirtualnych oznaczonych jako _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Przed wznowieniem przebiegu potoku upewnij się, że co najmniej jedna maszyna wirtualna jest oznaczona jako _produkcyjna_. W trzeciej fazie potoku aplikacja zostanie wdrożona tylko na tych maszynach wirtualnych, które mają tag _produkcyjny_ .

11. Zadanie wykonaj wdrożenie skryptu domyślnie wykona skrypt wdrożenia _Deploy. ps1_ lub _Deploy.sh_ w folderze "deployscripts" w katalogu głównym opublikowanego pakietu. Upewnij się, że wybrany potok kompilacji opublikuje go w folderze głównym pakietu. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Inne strategie wdrażania
- [Konfiguruj strategię wdrażania stopniowego](https://aka.ms/AA7jlh8)
- [Konfigurowanie strategii wdrażania Blue-Green](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Projekt usługi Azure DevOps 
Rozpocznij pracę z platformą Azure łatwiej niż kiedykolwiek wcześniej.
 
Za pomocą DevOps Projects Zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach: Wybierz język aplikacji, środowisko uruchomieniowe i usługę platformy Azure.
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Dodatkowe zasoby 
- [Wdrażanie na platformie Azure Virtual Machines przy użyciu projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
