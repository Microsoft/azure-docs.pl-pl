---
title: Ochrona maszyn i aplikacji
description: Ten dokument zawiera zalecenia dotyczące Security Center, które pomagają chronić maszyny wirtualne i komputery oraz aplikacje sieci Web i środowiska App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 8faff37e127e80447e0f066ef17e2885d4bcb597
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799738"
---
# <a name="protect-your-machines-and-applications"></a>Ochrona maszyn i aplikacji
Gdy Azure Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

W tym artykule opisano stronę **obliczenia i aplikacje** w sekcji zabezpieczenia zasobów Security Center.

Aby zapoznać się z pełną listą zaleceń, które mogą zostać wyświetlone na tej stronie, zobacz [zalecenia dotyczące obliczeń i aplikacji](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Wyświetlanie zabezpieczeń zasobów obliczeniowych i aplikacji

[![Pulpit nawigacyjny usługi Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Aby wyświetlić stan zasobów obliczeniowych i aplikacji, w okienku po lewej stronie w obszarze Security Center wybierz pozycję **compute & Apps**. Dostępne są następujące karty:

* **Przegląd**: zawiera listę zaleceń dotyczących wszystkich zasobów obliczeniowych i aplikacji, a także ich bieżący stan zabezpieczeń 

* [**Maszyny wirtualne i serwery**](#vms-and-computers): zawiera listę zaleceń dotyczących maszyn wirtualnych, komputerów i bieżącego stanu zabezpieczeń każdego

* [**Zestawy skalowania maszyn wirtualnych**](#vmscale-sets): zawiera listę zaleceń dla zestawów skalowania, 

* [**Cloud Services**](#cloud-services): zawiera listę zaleceń dotyczących ról sieci Web i procesów roboczych monitorowanych przez Security Center

* [**App Services**](#app-services): zawiera listę zaleceń dla środowisk usługi App Service i bieżący stan zabezpieczeń każdego

* [**Kontenery**](#containers): zawiera listę zaleceń dotyczących kontenerów i oceny zabezpieczeń ich konfiguracji

* **Zasoby obliczeniowe**: zawiera listę zaleceń dotyczących zasobów obliczeniowych, takich jak klastry Service Fabric i centra zdarzeń

### <a name="whats-in-each-tab"></a>Co znajduje się na każdej karcie?

Każda karta zawiera wiele sekcji, a w każdej sekcji można przejść do szczegółów, aby wyświetlić dodatkowe szczegóły dotyczące wyświetlanego elementu.

Na każdej karcie zobaczysz również zalecenia dotyczące odpowiednich zasobów w monitorowanym środowisku. Pierwsza kolumna zawiera listę zaleceń, drugi pokazuje łączną liczbę zasobów, a trzecia pokazuje ważność problemu.

Każde zalecenie zawiera zestaw akcji, które można wykonać po ich wybraniu. Na przykład w przypadku wybrania opcji **brakujące aktualizacje systemu**zostanie wyświetlona liczba maszyn wirtualnych i komputerów, na których brakuje poprawek, oraz ważność brakującej aktualizacji.

> [!NOTE]
> Zalecenia dotyczące zabezpieczeń są takie same jak te na stronie **zalecenia** , ale w tym miejscu są one filtrowane według wybranego typu zasobu. Aby uzyskać więcej informacji o sposobach rozwiązywania zaleceń, zobacz [implementowanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Maszyny wirtualne i serwery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących zabezpieczeń dla maszyn wirtualnych i komputerów. Uwzględniono cztery typy maszyn:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Azure Resource Manager maszynę wirtualną](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager maszynę wirtualną.

![Klasyczna maszyna wirtualna platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.

![Maszyny wirtualne zidentyfikowane w obszarze roboczym](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które są raportowane do obszaru roboczego w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane przy użyciu agenta Operations Manager bezpośredniego, i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko określić maszynę wirtualną i komputer wymagający uwagi oraz typ zalecenia. Możesz również użyć filtrów, aby przeszukać listę według **typu zasobów** i według **ważności**.

Aby przejść do szczegółów zaleceń dotyczących zabezpieczeń dla każdej maszyny wirtualnej, kliknij maszynę wirtualną.
W tym miejscu są wyświetlane szczegóły zabezpieczeń dotyczące maszyny wirtualnej lub komputera. Na dole można zobaczyć zalecaną akcję i ważność poszczególnych problemów.

[![Cloud Services](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Zestawy skalowania maszyn wirtualnych
Security Center automatycznie wykrywa, czy masz zestawy skalowania i zaleca się zainstalowanie na nich agenta Log Analytics.

Aby zainstalować agenta Log Analytics: 

1. Wybierz zalecenie **Zainstaluj agenta monitorowania w zestawie skalowania maszyn wirtualnych**. Otrzymujesz listę niemonitorowanych zestawów skalowania.

1. Wybierz zestaw skalowania w złej kondycji. Postępuj zgodnie z instrukcjami, aby zainstalować agenta monitorowania przy użyciu istniejącego wypełnionego obszaru roboczego lub utworzyć nowy. Upewnij się, że ustawiono [warstwę cenową](security-center-pricing.md) obszaru roboczego, jeśli nie została ustawiona.

   ![Instalowanie MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Aby ustawić nowe zestawy skalowania w celu automatycznej instalacji agenta Log Analytics:
1. Przejdź do Azure Policy i kliknij pozycję **definicje**.

1. Wyszukaj zasady **wdróż log Analytics Agent dla systemu Windows Virtual Machine Scale Sets** i kliknij je.

1. Kliknij przycisk **Przypisz**.

1. Ustaw **zakres** i **obszar roboczy log Analytics** i kliknij przycisk **Przypisz**.

Jeśli chcesz ustawić wszystkie istniejące zestawy skalowania w celu zainstalowania agenta Log Analytics, w Azure Policy, przejdź do **korygowania** i Zastosuj istniejące zasady do istniejących zestawów skalowania.





### <a name="cloud-services"></a><a name="cloud-services"></a>Cloud Services
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie, postępuj zgodnie z instrukcjami w zaleceniu, aby zaktualizować system operacyjny. Po udostępnieniu aktualizacji zostanie wystawiony alert (czerwony lub pomarańczowy — w zależności od wagi problemu). Aby uzyskać pełne wyjaśnienie tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **Opis** .






### <a name="app-services"></a><a name="app-services"></a>App Services
Aby wyświetlić informacje o App Service, musisz mieć Security Center standardowej warstwy cenowej i włączyć App Service w ramach subskrypcji. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [ochrona App Service przy użyciu Azure Security Center](security-center-app-services.md).

W obszarze **usługi App Services**znajdziesz listę środowisk usługi App Service oraz podsumowanie kondycji w oparciu o wykonane Security Center oceny.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Są wyświetlane trzy typy usług aplikacji:

![Środowisko usługi App Services](./media/security-center-virtual-machine-recommendations/ase.png) Środowisko usługi App Services

![Aplikacja internetowa](./media/security-center-virtual-machine-recommendations/web-app.png) Aplikacja internetowa

![Aplikacja funkcji](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikacja funkcji

W przypadku wybrania aplikacji sieci Web zostanie otwarty widok podsumowania z trzema kartami:

   - **Zalecenia**: na podstawie ocen wykonanych przez Security Center zakończonych niepowodzeniem.
   - Przeprowadzono **oceny**: Lista ocen wykonanych przez Security Center, które zakończono.
   - **Niedostępne oceny**: Lista ocen, których uruchomienie nie powiodło się z powodu błędu lub zalecenie nie jest istotne dla określonej usługi App Service

   W obszarze **zalecenia** znajduje się lista zaleceń dotyczących wybranej aplikacji sieci Web i ważności poszczególnych zaleceń.

   ![Zalecenia dotyczące App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Wybierz zalecenie, aby wyświetlić opis zalecenia i listę zasobów w złej kondycji, zdrowe zasoby i niezeskanowane zasoby.

   - W kolumnie **przekazana oceny** jest wyświetlana lista zakończonych ocen. Ważność tych ocen jest zawsze zielona.

   - Wybierz przekazaną ocenę z listy, aby zapoznać się z opisem oceny, listą nieprawidłowych i zdrowych zasobów oraz listą niezeskanowanych zasobów. Istnieje karta dla zasobów o złej kondycji, ale ta lista jest zawsze pusta od czasu przeprowadzenia oceny.





### <a name="containers"></a><a name="containers"></a>Containers

Po otwarciu karty **kontenery** w zależności od środowiska mogą pojawić się dowolne z trzech typów zasobów:

![Host kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hosty kontenerów — maszyny wirtualne korzystające z platformy Docker 

![Kubernetes usługi ](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes Service (AKS). [Dowiedz się więcej o pakiecie AKS Security Center](azure-kubernetes-service-integration.md)

![Rejestry kontenerów ](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure Container Registry (ACR). [Dowiedz się więcej o pakiecie ACR Security Center](azure-container-registry-integration.md)

Aby uzyskać instrukcje dotyczące korzystania z funkcji zabezpieczeń kontenerów, zobacz [monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).



[![Karta kontenery](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Aby zapoznać się z zaleceniami dotyczącymi określonego zasobu na liście, kliknij ten zasób.

#### <a name="visibility-into-container-registries"></a>Widoczność rejestrów kontenerów

Na przykład kliknięcie rejestru ASC-demonstracyjnej ACR z listy pokazanej w grafice powyżej prowadzi do tej strony szczegółów:

[![Zalecenia dotyczące określonego rejestru ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Wgląd w kontenery hostowane na maszynach z systemem Linux IaaS

Po kliknięciu jednej z maszyn wirtualnych z systemem Docker zostanie wyświetlona strona szczegółów z informacjami dotyczącymi kontenerów na komputerze, takich jak wersja platformy Docker i Liczba obrazów uruchomionych na hoście.

![Zalecenia dotyczące maszyny wirtualnej z systemem Docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Zalecenia dotyczące zabezpieczeń oparte na teście usług CIS dla platformy Docker

Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Security Center zawiera wytyczne ułatwiające szybkie rozwiązywanie tych problemów i oszczędność czasu. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.

![Karta kontener](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

* [Pełna lista referencyjna zaleceń dotyczących zabezpieczeń Azure Security Center](recommendations-reference.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Azure Security Center](security-center-sql-service-recommendations.md)