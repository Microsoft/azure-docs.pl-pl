---
title: Ciągłe monitorowanie potoku wydania usługi DevOps przy użyciu Azure Pipelines i platformy Azure Application Insights | Microsoft Docs
description: Zawiera instrukcje umożliwiające szybkie konfigurowanie ciągłego monitorowania za pomocą Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655399"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Dodawanie ciągłego monitorowania do potoku wydania

Azure Pipelines integruje się z usługą Azure Application Insights w celu zapewnienia ciągłego monitorowania potoku wydania DevOps w całym cyklu projektowania oprogramowania. 

W przypadku ciągłego monitorowania potoki wersji mogą zawierać dane monitorowania z Application Insights i innych zasobów platformy Azure. Gdy potok wydania wykryje alert Application Insights, potok może lub wycofać wdrożenie do momentu rozwiązania alertu. Jeśli wszystkie testy zostaną zakończone, wdrożenia mogą przebiegać automatycznie z testu do produkcji, bez konieczności ręcznej interwencji. 

## <a name="configure-continuous-monitoring"></a>Konfigurowanie monitorowania ciągłego

1. W [usłudze Azure DevOps](https://dev.azure.com)wybierz organizację i projekt.
   
1. W menu po lewej stronie projektu wybierz pozycję**wersje** **potoków** > . 
   
1. Rozwiń strzałkę obok pozycji **Nowy** i wybierz pozycję **Nowy potok wersji**. Lub, jeśli nie masz jeszcze potoku, wybierz pozycję **Nowy potok** na wyświetlonej stronie.
   
1. W okienku **Wybierz szablon** Wyszukaj i wybierz pozycję **Azure App Service wdrożenie z ciągłym monitorowaniem**, a następnie wybierz pozycję **Zastosuj**. 

   ![Nowy potok wersji Azure Pipelines](media/continuous-monitoring/001.png)

1. W polu **etap 1** wybierz hiperlink, aby **wyświetlić zadania etapów.**

   ![Wyświetl zadania etapów](media/continuous-monitoring/002.png)

1. W okienku Konfiguracja **etapu 1** wykonaj następujące pola: 

    | Parametr        | Wartość |
   | ------------- |:-----|
   | **Nazwa etapu**      | Podaj nazwę etapu lub pozostaw ją na **etapie 1**. |
   | **Subskrypcja platformy Azure** | Wybierz połączoną subskrypcję platformy Azure, której chcesz użyć.|
   | **Typ aplikacji** | Lista rozwijana i wybierz typ aplikacji. |
   | **Nazwa App Service** | Wprowadź nazwę Azure App Service. |
   | **Nazwa grupy zasobów dla Application Insights**    | Wybierz grupę zasobów, której chcesz użyć. |
   | **Nazwa zasobu Application Insights** | Wybierz pozycję Application Insights zasób dla wybranej grupy zasobów.

1. Aby zapisać potok z domyślnymi ustawieniami reguł alertów, wybierz pozycję **Zapisz** w prawym górnym rogu okna Azure DevOps. Wprowadź komentarz opisowy, a następnie wybierz przycisk **OK**.

## <a name="modify-alert-rules"></a>Modyfikuj reguły alertów

**Wdrożenie Azure App Service z szablonem ciągłego monitorowania** ma cztery reguły alertów: **dostępność**, **żądania zakończone niepowodzeniem**, **czas odpowiedzi serwera**i **wyjątki serwera**. Możesz dodać więcej reguł lub zmienić ustawienia reguły, aby spełniały wymagania poziomu usługi. 

Aby zmodyfikować ustawienia reguły alertu:

1. W lewym okienku na stronie potoku wydania wybierz pozycję **konfiguruj Application Insights alerty**.

1. W okienku **alerty Azure monitor** wybierz przycisk wielokropka **...** obok **reguły alertów**.
   
1. W oknie dialogowym **reguły alertów** wybierz symbol listy rozwijanej obok reguły alertu, np. **dostępność**. 
   
1. Zmodyfikuj **wartość progową** i inne ustawienia w celu spełnienia wymagań.
   
   ![Modyfikuj alert](media/continuous-monitoring/003.png)
   
1. Wybierz pozycję **OK**, a następnie wybierz pozycję **Zapisz** w prawym górnym rogu okna Azure DevOps. Wprowadź komentarz opisowy, a następnie wybierz przycisk **OK**.

## <a name="add-deployment-conditions"></a>Dodaj warunki wdrażania

Po dodaniu bram wdrażania do potoku wydania alert przekraczający ustalone progi uniemożliwiają podwyższenie poziomu. Po rozwiązaniu alertu wdrożenie może być realizowane automatycznie.

Aby dodać bramy wdrażania:

1. Na stronie główny potok w obszarze **etapy**wybierz opcję **warunki przed wdrożeniem** lub postanowień **po wdrożeniu** , w zależności od tego, który etap wymaga bramy ciągłego monitorowania.
   
   ![Warunki przed wdrożeniem](media/continuous-monitoring/004.png)
   
1. W okienku Konfiguracja **warunków przed wdrożeniem** Ustaw **bramy** na **włączone**.
   
1. Obok pozycji **bramy wdrażania**wybierz pozycję **Dodaj**.
   
1. Wybierz pozycję **zapytanie Azure monitor alerty** z menu rozwijanego. Ta opcja umożliwia dostęp do alertów Azure Monitor i Application Insights.
   
   ![Alerty Azure Monitor zapytań](media/continuous-monitoring/005.png)
   
1. W obszarze **Opcje oceny**wprowadź wartości dla ustawień, takie jak **czas między ponowną oceną bram** i **limit czasu, po którym bramy kończą się niepowodzeniem**. 

## <a name="view-release-logs"></a>Wyświetlanie dzienników wydań

W dziennikach wydań można zobaczyć zachowanie bramy wdrażania i inne kroki wydania. Aby otworzyć dzienniki:

1. Wybierz pozycję **wydania** z menu po lewej stronie potoku. 
   
1. Wybierz dowolne wydanie. 
   
1. W obszarze **etapy**wybierz dowolny etap, aby wyświetlić podsumowanie wydania. 
   
1. Aby wyświetlić dzienniki, wybierz pozycję **Wyświetl dzienniki** w podsumowaniu wersji, zaznacz hiperlink **zakończone powodzeniem** lub **niepowodzeniem** na dowolnym etapie lub umieść kursor na dowolnym etapie i wybierz pozycję **dzienniki**. 
   
   ![Wyświetlanie dzienników wydań](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Pipelines, zobacz [dokumentację Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
