---
title: Konfigurowanie i używanie środowisk publicznych w programie Azure DevTest Labs | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania i używania środowisk publicznych (Azure Resource Manager szablonów w repozytorium Git) w programie Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 61cabdb296c3fff75137c7ce7e87652241fd2926
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482670"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurowanie i używanie środowisk publicznych w programie Azure DevTest Labs
Azure DevTest Labs ma [publiczne repozytorium szablonów Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , których można użyć do tworzenia środowisk bez konieczności nawiązywania połączenia z zewnętrznym źródłem GitHub przez siebie. To repozytorium obejmuje często używane szablony, takie jak Azure Web Apps, klaster Service Fabric i środowisko farmy deweloperów programu SharePoint. Ta funkcja jest podobna do publicznego repozytorium artefaktów, które są dołączone do każdego tworzonego laboratorium. Repozytorium środowiska umożliwia szybkie rozpoczynanie pracy z szablonami wstępnie utworzonych środowisk z minimalnymi parametrami wejściowymi w celu zapewnienia sprawnego środowiska uruchamiania dla zasobów PaaS w ramach laboratoriów. 

## <a name="configuring-public-environments"></a>Konfigurowanie środowisk publicznych
Jako właściciel laboratorium możesz włączyć repozytorium środowiska publicznego dla laboratorium podczas tworzenia laboratorium. Aby włączyć środowiska publiczne dla laboratorium, wybierz opcję **włączone** dla **środowiska publicznego** podczas tworzenia laboratorium. 

![Włącz środowisko publiczne dla nowego laboratorium](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


W przypadku istniejących laboratoriów repozytorium środowiska publicznego nie jest włączone. Ręcznie włącz go, aby używać szablonów w repozytorium. W przypadku laboratoriów utworzonych przy użyciu szablonów Menedżer zasobów repozytorium jest również domyślnie wyłączone.

Możesz włączyć/wyłączyć środowiska publiczne dla laboratorium, a także udostępnić tylko określone środowiska użytkownikom laboratorium, wykonując następujące czynności: 

1. Wybierz pozycję **Konfiguracja i zasady** dla laboratorium. 
2. W sekcji **bazy maszyn wirtualnych** wybierz pozycję **środowiska publiczne**.
3. Aby włączyć środowiska publiczne dla laboratorium, wybierz pozycję **tak**. W przeciwnym razie wybierz opcję **Nie**. 
4. W przypadku włączenia środowiska publicznego wszystkie środowiska w repozytorium są domyślnie włączone. Możesz cofnąć wybór środowiska, aby nie było dostępne dla użytkowników laboratorium. 

![Strona środowiska publicznego](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Używanie szablonów środowiska jako użytkownika laboratorium
Jako użytkownik laboratorium można utworzyć nowe środowisko na podstawie listy włączonych szablonów środowiska, zaznaczając opcję **+ Dodaj** na pasku narzędzi na stronie laboratorium. Lista baz danych zawiera szablony środowisk publicznych włączonych przez administratora laboratorium w górnej części listy.

![Szablony środowiska publicznego](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Następne kroki
To repozytorium jest repozytorium typu "open source", które umożliwia Współtworzenie często używanych i przydatnych Menedżer zasobów szablonów. Aby współtworzyć, wystarczy przesłać żądanie ściągnięcia względem repozytorium.  
