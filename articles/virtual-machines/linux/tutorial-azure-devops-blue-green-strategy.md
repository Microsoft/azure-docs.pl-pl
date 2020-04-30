---
title: Samouczek — Konfigurowanie wdrożeń programu Kanaryjskie dla Linux Virtual Machines platformy Azure
description: W tym samouczku dowiesz się, jak skonfigurować potok ciągłego wdrażania (CD) służący do aktualizowania grupy maszyn wirtualnych platformy Azure przy użyciu strategii wdrażania Blue-Green
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120492"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Samouczek — Konfigurowanie strategii wdrażania Blue-Green dla platformy Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS — Konfigurowanie ciągłej integracji/ciągłego dostarczania 
Azure Pipelines zawiera kompletny, w pełni funkcjonalny zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Potok ciągłego dostarczania można skonfigurować dla maszyny wirtualnej platformy Azure bezpośrednio z poziomu Azure Portal. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania, który używa strategii Blue-Green dla wdrożeń obejmujących wiele maszyn. Możesz również zapoznać się z innymi strategiami, takimi jak [stopniowe](https://aka.ms/AA7jlh8) i [Kanaryjskie](https://aka.ms/AA7jdrz), które są obsługiwane w Azure Portal. 

 
 **Konfigurowanie ciągłej integracji/ciągłego wdrażania Virtual Machines**

Maszyny wirtualne mogą być dodawane jako elementy docelowe do [grupy wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) i mogą być przeznaczone do aktualizacji na wiele maszyn. Po wdrożeniu **historia wdrożenia** w ramach grupy wdrożenia zapewnia możliwość śledzenia z maszyny wirtualnej do potoku, a następnie do zatwierdzenia. 
 
  
**Wdrożenia Blue-Green**: wdrożenie Blue-Green zmniejsza czas przestoju, mając identyczne środowisko w stanie wstrzymania. W dowolnym momencie w jednym z tych środowisk jest aktywny. Podczas przygotowywania do nowej wersji Ukończ etap testowania w środowisku zielonym. Gdy oprogramowanie działa w środowisku zielonym, należy przełączyć ruch, aby wszystkie żądania przychodzące przechodzą do zielonego środowiska — niebieskie środowisko jest teraz w stanie bezczynności.
Wdrożenia Blue-Green można skonfigurować dla "**maszyn wirtualnych**" z Azure Portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku.

1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej 
2. W lewym okienku maszyny wirtualnej przejdź do **ciągłego dostarczania**. Następnie kliknij przycisk **Konfiguruj**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. W panelu Konfiguracja kliknij pozycję **Azure DevOps Organization** , aby wybrać istniejące konto lub utwórz je. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne; na przykład "dev", "test", "przeprowadzających" i "Product". Można utworzyć nową grupę wdrożenia lub wybrać istniejącą grupę wdrożenia. 
5. Wybierz potok kompilacji, który publikuje pakiet przeznaczony do wdrożenia na maszynie wirtualnej. Należy zauważyć, że opublikowany pakiet powinien mieć skrypt wdrożenia _Deploy. ps1_ lub _Deploy.sh_ w `deployscripts` folderze głównym pakietu. Ten skrypt wdrażania zostanie wykonany przez potok Azure DevOps w czasie wykonywania.
6. Wybierz wybraną strategię wdrażania. Wybierz **niebieską i zieloną**.
7. Dodaj tag "Blue" lub "zielony" do maszyn wirtualnych, które mają być częścią wdrożeń Blue-Green. Jeśli maszyna wirtualna jest dla roli wstrzymania, należy oznaczyć ją jako "Green", w przeciwnym razie oznacz ją jako "niebieska".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Kliknij przycisk **OK** , aby skonfigurować potok ciągłego dostarczania. Teraz będzie dostępny potok ciągłego dostarczania skonfigurowany do wdrożenia na maszynie wirtualnej.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Kliknij pozycję **Edytuj** potok wydania na platformie Azure DevOps, aby wyświetlić konfigurację potoku. Potok składa się z trzech faz. Pierwsza faza to faza grupy wdrożenia i wdrożenia na maszynach wirtualnych, które są oznaczone jako _zielone_ (gotowość do zarezerwowania maszyn wirtualnych). Druga faza wstrzymuje potok i czeka na ręczną interwencję w celu wznowienia uruchomienia. Gdy użytkownik ma pewność, że wdrożenie jest stabilne, może teraz przekierować ruch do _zielonych_ maszyn wirtualnych i wznowić uruchomienie potoku, które następnie zastąpią _niebieskie_ i _zielone_ Tagi na maszynach wirtualnych. Pozwala to upewnić się, że maszyny wirtualne, które mają starszą wersję aplikacji, są oznaczone jako _zielone_ i są wdrażane w ramach następnego uruchomienia potoku.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. Zadanie wykonaj wdrożenie skryptu domyślnie wykonuje skrypt wdrożenia _Deploy. ps1_ lub _Deploy.sh_ w `deployscripts` folderze w katalogu głównym opublikowanego pakietu. Upewnij się, że wybrany potok kompilacji opublikuje go w folderze głównym pakietu.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Inne strategie wdrażania
- [Konfiguruj strategię wdrażania stopniowego](https://aka.ms/AA7jlh8)
- [Konfiguruj strategię wdrażania w programie Kanaryjskie](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Projekt usługi Azure DevOps 
Rozpocznij pracę z platformą Azure łatwiej niż kiedykolwiek wcześniej.
 
Za pomocą DevOps Projects Zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach: Wybierz język aplikacji, środowisko uruchomieniowe i usługę platformy Azure.
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Dodatkowe zasoby 
- [Wdrażanie na platformie Azure Virtual Machines przy użyciu projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
