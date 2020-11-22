---
title: Tworzenie App Service Environment
description: Dowiedz się, jak utworzyć App Service Environment.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 12c9fd43933f9f54e013752c5fe482ce31650536
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238398"
---
# <a name="create-an-app-service-environment"></a>Tworzenie App Service Environment

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v3 (wersja zapoznawcza)
> 

[App Service Environment (ASE)][Intro] jest wdrożeniem pojedynczej dzierżawy App Service, która wprowadza do Virtual Network platformy Azure.  ASEv3 obsługuje tylko udostępnianie aplikacji na prywatnym adresie w sieci wirtualnej. Po utworzeniu ASEv3 w wersji zapoznawczej do subskrypcji są dodawane trzy zasoby.

- Środowisko usługi App Service
- Strefa prywatna Azure DNS
- Prywatny punkt końcowy

Wdrożenie środowiska ASE wymaga użycia dwóch podsieci.  Jedna podsieć będzie przechowywać prywatny punkt końcowy.  Ta podsieć może być używana do innych elementów, takich jak maszyny wirtualne.  Druga podsieć jest używana na potrzeby wywołań wychodzących wykonanych z środowiska ASE.  Tej podsieci nie można użyć dla niczego innego niż środowisko ASE. 

## <a name="before-you-create-your-ase"></a>Przed utworzeniem środowiska ASE

Po utworzeniu środowiska ASE nie można zmienić:

- Lokalizacja
- Subskrypcja
- Grupa zasobów
- Używane Virtual Network platformy Azure
- Używane podsieci
- Rozmiar podsieci
- Nazwa środowiska ASE

Podsieć wychodząca musi być wystarczająco duża, aby pomieścić maksymalny rozmiar skalowania środowiska ASE. Wybierz odpowiednią podsieć, aby obsługiwać maksymalne potrzeby skalowania, ponieważ nie można jej zmienić po utworzeniu. Zalecany rozmiar to/24 z 256 adresami.

Po utworzeniu środowiska ASE można dodać do niego aplikacje. Gdy ASEv3 nie ma żadnych planów App Service, naliczane są opłaty tak, jakby istniało jedno wystąpienie planu I1v2 App Service w środowisku ASE.  

ASEv3 jest oferowana tylko w wybranych regionach. Więcej regionów zostanie dodanych w miarę poruszania się w wersji zapoznawczej. 

## <a name="creating-an-ase-in-the-portal"></a>Tworzenie środowiska ASE w portalu

1. Aby utworzyć ASEv3, Wyszukaj w witrynie Marketplace **App Service Environment (wersja zapoznawcza)**.  
2. Podstawowe informacje: wybierz subskrypcję, wybierz lub Utwórz grupę zasobów, a następnie wprowadź nazwę środowiska ASE.  Nazwa środowiska ASE zostanie również użyta dla sufiksu domeny w środowisku ASE.  Jeśli nazwa środowiska ASE to *contoso* , sufiks domeny zostanie *contoso.appserviceenvironment.NET*.  Ta nazwa zostanie automatycznie ustawiona w prywatnej strefie Azure DNS używanej przez sieć wirtualną, w której wdrożono środowisko ASE. 

    ![Karta App Service Environment tworzenie podstawy](./media/creation/creation-basics.png)

3. Hosting: Wybierz preferencję systemu operacyjnego, wdrożenie grupy hostów. Preferencja systemu operacyjnego wskazuje system operacyjny, który początkowo będziesz używać dla aplikacji w tym środowisku ASE. Po utworzeniu środowiska ASE można dodać aplikacje innego systemu operacyjnego. Wdrożenie grupy hostów służy do wybierania dedykowanego sprzętu. Za pomocą ASEv3 można wybrać opcję włączone, a następnie na dedykowanym sprzęcie. Opłaty są naliczane za cały dedykowany host za pomocą tworzenia środowiska ASE, a następnie obniżona cena dla wystąpień planu App Service. 

    ![App Service Environment wybór hostingu](./media/creation/creation-hosting.png)

4. Sieć: wybierz lub Utwórz Virtual Network, wybierz lub Utwórz podsieć przychodzącą, wybierz lub Utwórz podsieć wychodzącą. Każda podsieć użyta dla ruchu wychodzącego musi być pusta i delegowana do firmy Microsoft. Web/hostingEnvironments. Jeśli utworzysz podsieć za pomocą portalu, podsieć zostanie automatycznie delegowana.

    ![App Service Environment wybór sieci](./media/creation/creation-networking.png)

5. Przejrzyj i Utwórz: Sprawdź, czy konfiguracja jest poprawna, i wybierz pozycję Utwórz. Tworzenie środowiska ASE zajmie około godziny. 

    ![App Service Environment przegląd i tworzenie](./media/creation/creation-review.png)

Po zakończeniu tworzenia środowiska ASE można wybrać je jako lokalizację podczas tworzenia aplikacji. Aby dowiedzieć się więcej na temat tworzenia aplikacji w nowym środowisku ASE, Przeczytaj [przy użyciu App Service Environment][UsingASE]

## <a name="os-preference"></a>Preferencja systemu operacyjnego
W środowisku ASE można korzystać z aplikacji systemu Windows, aplikacji z systemem Linux lub obu. W programie ASEv2 wstępne preferencje wybrane podczas tworzenia dodaje infrastrukturę wysokiej dostępności dla tego systemu operacyjnego podczas tworzenia środowiska ASE. Aby dodać aplikacje innego systemu operacyjnego, po prostu ustaw aplikacje w zwykły sposób i wybierz żądany system operacyjny. W ASEv3 nie ma to wpływu na zachowanie zaplecza appreciatively.  

## <a name="dedicated-hosts"></a>Dedykowane hosty
Środowisko ASE jest zwykle wdrażane na maszynach wirtualnych, które są obsługiwane w ramach funkcji hypervisor z wieloma dzierżawcami. Jeśli konieczne jest wdrożenie w dedykowanych systemach, w tym sprzętu, można udostępnić środowisko ASE na dedykowanych hostach. W początkowej wersji zapoznawczej ASEv3, dedykowane hosty są dostępne w parze. Każdy dedykowany host znajduje się w osobnej strefie dostępności, jeśli zezwala na to region. W przypadku dedykowanych wdrożeń opartych na hoście środowisko ASE jest wyceniane inaczej niż normalne. Jest naliczana opłata za dedykowany host, a następnie inna opłata za każde wystąpienie planu App Service.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
