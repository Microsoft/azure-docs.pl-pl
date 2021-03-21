---
title: Samouczek SaaS z jedną dzierżawą
description: Wdróż i Eksploruj autonomiczną aplikację SaaS z jedną dzierżawą, która używa Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793283"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Wdróż i Eksploruj autonomiczną aplikację z jedną dzierżawą, która używa Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku opisano wdrażanie i eksplorowanie przykładowej aplikacji Wingtip bilety SaaS opracowanej przy użyciu aplikacji autonomicznej lub aplikacji dla dzierżawy.  Aplikacja została zaprojektowana w celu pokazania funkcji Azure SQL Database, które upraszczają Włączanie wielodostępnych scenariuszy SaaS.

Aplikacja autonomiczna lub wzorzec aplikacji dla dzierżawy wdraża wystąpienie aplikacji dla każdej dzierżawy.  Każda aplikacja jest skonfigurowana dla określonej dzierżawy i wdrożona w oddzielnej grupie zasobów platformy Azure. Obsługa wielu wystąpień aplikacji zapewnia rozwiązanie z wieloma dzierżawcami. Ten wzorzec najlepiej nadaje się do mniejszych liczb, dzierżawców, w których izolacja dzierżawy jest najwyższym priorytetem. Platforma Azure oferuje programy partnerskie, które umożliwiają wdrażanie zasobów w ramach subskrypcji dzierżawy i zarządzane przez dostawcę usług w imieniu dzierżawy. 

W tym samouczku zostaną wdrożone trzy aplikacje autonomiczne dla trzech dzierżawców w ramach subskrypcji platformy Azure.  Masz pełny dostęp do eksplorowania i pracy z pojedynczymi składnikami aplikacji.

Kod źródłowy aplikacji i skrypty zarządzania są dostępne w repozytorium GitHub [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) . Aplikacja została utworzona przy użyciu programu Visual Studio 2015 i nie została pomyślnie otwarta i skompilowana w programie Visual Studio 2019 bez aktualizacji.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikację autonomiczną SaaS biletów Wingtip.
> * Gdzie można uzyskać kod źródłowy aplikacji oraz skrypty zarządzania.
> * Informacje o serwerach i bazach danych, które tworzą aplikację.

Zostaną wydane dodatkowe samouczki. Umożliwiają one Eksplorowanie wielu scenariuszy zarządzania opartych na tym wzorcu aplikacji.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wdróż aplikację autonomiczną Wingtip biletów SaaS

Wdróż aplikację dla trzech dostarczonych dzierżawców:

1. Kliknij każde niebieskie polecenie **Wdróż na platformie Azure** , aby otworzyć szablon wdrożenia w [Azure Portal](https://portal.azure.com). Każdy szablon wymaga dwóch wartości parametrów; Nazwa nowej grupy zasobów oraz nazwa użytkownika, która odróżnia to wdrożenie od innych wdrożeń aplikacji. Następny krok zawiera szczegółowe informacje dotyczące ustawiania tych wartości.

   **Korytarz uzgadniania contoso**   
   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Klub z fabrikam Jazz**   
   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Wprowadź wymagane wartości parametrów dla każdego wdrożenia.

    > [!IMPORTANT]
    > Pewne uwierzytelnianie i zapory serwera są celowo niezabezpieczone w celach demonstracyjnych. **Utwórz nową grupę zasobów** dla każdego wdrożenia aplikacji.  Nie należy używać istniejącej grupy zasobów. Nie należy używać tej aplikacji ani żadnych tworzonych przez nie zasobów dla środowiska produkcyjnego. Usuń wszystkie grupy zasobów po zakończeniu pracy z aplikacjami, aby zatrzymać powiązane rozliczenia.

    Najlepiej używać małych liter, cyfr i łączników w nazwach zasobów.
    * W obszarze **Grupa zasobów** wybierz pozycję Utwórz nową, a następnie podaj nazwę z małymi literami dla grupy zasobów. **Wingtip-sa- \<venueName\> - \<user\>** jest zalecanym wzorcem.  W przypadku programu \<venueName\> Zastąp wartość Nazwa miejsca bez spacji. W przypadku programu \<user\> Zastąp wartość użytkownika poniżej.  W tym wzorcu nazwy grup zasobów mogą być *Wingtip-sa-contosoconcerthall-AF1*, *Wingtip-sa-dogwooddojo-AF1*, *Wingtip-sa-fabrikamjazzclub-AF1*.
    * Z listy rozwijanej wybierz **lokalizację** .

    * Dla **użytkownika** — zaleca się użycie krótkiej wartości, takiej jak inicjały oraz cyfra: na przykład *AF1*.


3. **Wdróż aplikację**.

    * Kliknij, aby zaakceptować warunki i postanowienia.
    * Kliknij pozycję **Kup**.

4. Monitoruj stan wszystkich trzech wdrożeń, klikając pozycję **powiadomienia** (ikona dzwonka z prawej strony pola wyszukiwania). Wdrażanie aplikacji trwa około 5 minut.


## <a name="run-the-applications"></a>Uruchamianie aplikacji

W tej aplikacji zaprezentowane są zdarzenia hosta.  Miejsca są dzierżawcami aplikacji. Każdy z miejsc otrzymuje spersonalizowaną witrynę sieci Web, aby wyświetlić listę swoich wydarzeń i sprzedawać bilety. Typy miejsc obejmują korytarze wspólne, trefle jazzowe i trefle sportowe. W przykładzie typ miejsca określa fotografię tła wyświetlaną w witrynie sieci Web na miejscu.   W modelu aplikacji autonomicznej każdy z nich ma oddzielne wystąpienie aplikacji z własnym Azure SQL Database autonomicznym.

1. Otwórz stronę zdarzeń dla każdej z trzech dzierżawców na osobnych kartach przeglądarki:

   - http://events.contosoconcerthall.&lt; User &gt; . trafficmanager.NET
   - http://events.dogwooddojo.&lt; User &gt; . trafficmanager.NET
   - http://events.fabrikamjazzclub.&lt; User &gt; . trafficmanager.NET

     (W każdym adresie URL Zastąp &lt; &gt; wartość User swoją wdrożeniem).

   ![Zdarzenia](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Aby kontrolować dystrybucję żądań przychodzących, aplikacja używa [*platformy Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md). Każde wystąpienie aplikacji specyficzne dla dzierżawy zawiera nazwę dzierżawy jako część nazwy domeny w adresie URL. Wszystkie adresy URL dzierżawy zawierają konkretną wartość **użytkownika** . Adresy URL są zgodne z następującym formatem:
- http://events.&lt; miejscu &gt; . &lt; User &gt; . trafficmanager.NET

**Lokalizacja** bazy danych każdej dzierżawy jest uwzględniana w ustawieniach aplikacji odpowiedniej wdrożonej aplikacji.

W środowisku produkcyjnym zazwyczaj należy utworzyć rekord DNS CNAME, aby [*wskazywał domenę internetową firmy*](../../traffic-manager/traffic-manager-point-internet-domain.md) na adres URL profilu usługi Traffic Manager.


## <a name="explore-the-servers-and-tenant-databases"></a>Eksplorowanie serwerów i baz danych dzierżawy

Przyjrzyjmy się pewnym zasobom, które zostały wdrożone:

1. W [Azure Portal](https://portal.azure.com)przejdź do listy grup zasobów.
2. Powinny zostać wyświetlone trzy grupy zasobów dzierżawy.
3. Otwórz grupę zasobów **Wingtip-sa-Fabrikam &lt; - &gt; User** , która zawiera zasoby dla wdrożenia klubu firmy Fabrikam Jazz.  Serwer **fabrikamjazzclub- &lt; User &gt;** zawiera bazę danych **fabrikamjazzclub** .

Każda baza danych dzierżawy jest 50ą *autonomiczną* bazą danych DTU.

## <a name="additional-resources"></a>Dodatkowe zasoby

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Aby dowiedzieć się więcej o wielodostępnych aplikacjach SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Usuń grupy zasobów, aby zatrzymać rozliczanie ##

Po zakończeniu korzystania z przykładu usuń wszystkie utworzone przez siebie grupy zasobów, aby zatrzymać powiązane rozliczenia.

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikację autonomiczną SaaS biletów Wingtip.
> * Informacje o serwerach i bazach danych, które tworzą aplikację.
> * Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Następnie Wypróbuj samouczek [dotyczący aprowizacji i katalogu](saas-standaloneapp-provision-and-catalog.md) , w którym można zapoznać się z użyciem wykazu dzierżawców, który umożliwia szereg scenariuszy obejmujących wiele dzierżawców, takich jak zarządzanie schematami i analiza dzierżawców.
