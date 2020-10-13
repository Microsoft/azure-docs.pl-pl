---
title: Monitorowanie użycia i szacowane koszty w Azure Monitor
description: Przegląd procesu korzystania z Azure Monitor użycia i szacowanych kosztów
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: eb96537f67c61fb31759da020068f784d0e89993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323404"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorowanie użycia i szacowane koszty w Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób wyświetlania użycia i szacowanych kosztów w ramach wielu funkcji monitorowania platformy Azure. Pokrewne artykuły dla określonych składników Azure Monitor obejmują:
> - [Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](manage-cost-storage.md) opisuje sposób kontrolowania kosztów przez zmianę okresu przechowywania danych oraz analizowanie i zgłaszanie alertów dotyczących użycia danych.
> - [Zarządzanie użyciem i kosztami Application Insights](../app/pricing.md) opisuje sposób analizowania użycia danych w programie Application Insights.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor model cenowy

Model rozliczeń w warstwie Podstawowa Azure Monitor jest przyjazny dla chmury, oparte na użyciu ceny ("płatność zgodnie z rzeczywistym użyciem"). Płaci się wyłącznie za użyte zasoby. Szczegóły cennika są dostępne dla [alertów, metryk, powiadomień](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) i [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Oprócz modelu płatność zgodnie z rzeczywistym użyciem dla danych dzienników Log Analytics ma rezerwacje pojemności, co pozwala zaoszczędzić do 25% w porównaniu z ceną płatność zgodnie z rzeczywistym użyciem. Cennik rezerwacji zdolności produkcyjnych umożliwia zakupienie rezerwacji rozpoczynającej się o 100 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) o cenach rezerwacji pojemności.

Niektórzy klienci będą mieli dostęp do [starszych warstw cenowych log Analytics](./manage-cost-storage.md#legacy-pricing-tiers) i [starszej warstwy cenowej Application Insights Enterprise](../app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Informacje o kosztach Azure Monitor

Istnieją dwie fazy dotyczące poznania kosztów. W pierwszej kolejności rozważa się Azure Monitor jako rozwiązanie do monitorowania. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Szacowanie kosztów związanych z zarządzaniem środowiskiem

Jeśli jeszcze nie korzystasz z dzienników Azure Monitor, możesz użyć [kalkulatora cen Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) , aby oszacować koszt używania Azure monitor. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia kafelka Azure Monitor wyników. Przewiń w dół stronę do Azure Monitor i wybierz jedną z opcji z listy rozwijanej Typ:

- Zapytania i alerty metryk  
- Log Analytics
- Application Insights

W każdym z tych elementów Kalkulator cen pomoże ci oszacować przewidywane koszty na podstawie oczekiwanego użycia.

Na przykład przy Log Analytics można wprowadzić liczbę maszyn wirtualnych i GB danych, które powinny być zbierane z każdej maszyny wirtualnej. Zwykle 1 GB do 3 GB danych miesiąc jest pobieranych z typowej maszyny wirtualnej platformy Azure. Jeśli już już oceniasz dzienniki Azure Monitor, możesz użyć statystyk danych z własnego środowiska. Poniżej znajdują się informacje na temat określania [liczby monitorowanych maszyn wirtualnych](./manage-cost-storage.md#understanding-nodes-sending-data) i [ilości danych pobieranych przez obszar roboczy](./manage-cost-storage.md#understanding-ingested-data-volume).

Podobnie w przypadku Application Insights, jeśli włączysz funkcję "oszacowanie ilości danych opartych na działaniu aplikacji", możesz dostarczyć dane wejściowe dotyczące aplikacji (żądania miesięcznie i wyświetlenia stron miesięcznie), w przypadku zbierania danych telemetrycznych po stronie klienta, a następnie Kalkulator wskaże medianę i 90-procentową ilość uzyskaną przez podobne aplikacje. Te aplikacje obejmują zakres konfiguracji Application Insights (np. niektóre z próbkami domyślnymi, niektóre nie mają próbek itp.), dlatego nadal masz kontrolę, aby zmniejszyć ilość danych, które są znacznie mniejsze niż poziom średni przy użyciu próbkowania. Jest to jednak punkt początkowy, który umożliwia zapoznanie się z innymi, podobnym klientom. [Dowiedz się więcej](../app/pricing.md#estimating-the-costs-to-manage-your-application) o szacowaniu kosztów Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Zrozumienie użycia i szacowanych kosztów

Ważne jest, aby zrozumieć i śledzić użycie za pomocą Azure Monitor, i istnieje bogaty zestaw narzędzi umożliwiających takie ułatwienia. 

Platforma Azure oferuje bardzo przydatne funkcje w [Azure Cost Management i centrum rozliczeń](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) . Po otwarciu Centrum **Rozliczeń Azure Cost Management** i kliknij przycisk **Cost Management** i wybierz [zakres](../../cost-management-billing/costs/understand-work-scopes.md) (zestaw zasobów do zbadania). 

Następnie, aby zobaczyć koszty Azure Monitor w ciągu ostatnich 30 dni, kliknij kafelek **dzienne koszty** , wybierz pozycję "ostatnie 30 dni" w polu względne daty i Dodaj filtr, który wybiera nazwy usług:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Wgląd w dane i analizy

Spowoduje to wyświetlenie widoku, takiego jak:

![Zrzut ekranu Azure Cost Management](./media/usage-estimated-costs/010.png)

W tym miejscu możesz przejść do szczegółów z tego skumulowanego podsumowania kosztów, aby uzyskać bardziej szczegółowe informacje w widoku "koszt według zasobów". W bieżących warstwach cenowych dane dziennika platformy Azure są obciążane tym samym zestawem liczników, niezależnie od tego, czy pochodzą z Log Analytics czy Application Insights. Aby oddzielić koszty Log Analytics lub Application Insights użycie, można dodać filtr dla **typu zasobu**. Aby wyświetlić wszystkie Application Insights koszty, przefiltruj typ zasobu na "Microsoft. Insights/Components" i dla Log Analytics kosztów, filtr typu zasobu do "Microsoft. operationalinsights/Workspaces". 

Więcej szczegółów dotyczących użycia można uzyskać, [pobierając użycie z Azure Portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). W pobranym arkuszu kalkulacyjnym można zobaczyć użycie na zasób platformy Azure dziennie. W tym arkuszu kalkulacyjnym programu Excel można sprawdzić użycie zasobów Application Insights przez pierwsze filtrowanie w kolumnie "kategoria licznika", aby pokazać "Application Insights" i "Log Analytics", a następnie dodać filtr w kolumnie "identyfikator wystąpienia", która jest "zawiera Microsoft. Insights/Components".  Większość Application Insights użycie jest raportowane na miernikach z kategorią licznika Log Analytics, ponieważ istnieje pojedyncze zaplecze dzienników dla wszystkich składników Azure Monitor.  Tylko zasoby Application Insights ze starszych warstw cenowych i wieloetapowych testów sieci Web są zgłaszane z kategorią miernika Application Insights.  Użycie jest wyświetlane w kolumnie "zużyta ilość", a jednostka dla każdego wpisu jest pokazywana w kolumnie "jednostka miary".  Temat dotyczący [interpretacji rachunku za platformę Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md) zawiera więcej szczegółowych informacji. 

> [!NOTE]
> Używanie **Cost Management** w centrum **rozliczeniowym Azure Cost Management i** jest preferowanym podejściem do szerokiego analizowania kosztów monitorowania.  **Sposób użycia i szacowane koszty** dla [log Analytics](./manage-cost-storage.md#understand-your-usage-and-estimate-costs) i [Application Insights](../app/pricing.md#understand-your-usage-and-estimate-costs) zapewniają dokładniejsze informacje dla każdej z tych części Azure monitor.

Kolejną opcją wyświetlania Azure Monitor użycia jest strona **użycie i szacowane koszty** w centrum monitora. Przedstawiono w nim użycie podstawowych funkcji monitorowania, takich jak [alerty, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)i [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). W przypadku klientów korzystających z planów cenowych dostępnych przed 2018 kwietnia będzie to również Log Analytics użycie zakupione w ramach oferty usługi Insights i Analytics.

Na tej stronie użytkownicy mogą wyświetlić użycie zasobów przez ostatnie 31 dni, zagregowane na subskrypcję. `Drill-ins` Pokaż trendy użycia w ciągu 31-dniowego okresu. Do tego oszacowania należy wiele danych, dlatego należy poczekać, aż strona zostanie załadowana.

W tym przykładzie przedstawiono monitorowanie użycia i szacunkowe koszty:

![Zrzut ekranu portalu użycia i szacowanej kosztów](./media/usage-estimated-costs/001.png)

Wybierz łącze w kolumnie użycie miesięczne, aby otworzyć wykres pokazujący trendy użycia w ciągu ostatnich 31 dni: 

![Zrzut ekranu przedstawiający wykres słupkowy na węzeł](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Uprawnienia do subskrypcji pakietu Operations Management Suite

Klienci, którzy zakupili Microsoft Operations Management Suite E1 i E2, mają uprawnienia do pozyskiwania danych na węzłach dla [log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) i [Application Insights](../app/pricing.md). Aby otrzymywać te uprawnienia dla Log Analytics obszarów roboczych lub Application Insights zasobów w ramach danej subskrypcji: 

- Obszary robocze Log Analytics powinny korzystać z warstwy cenowej "na węzeł (OMS)".
- Zasoby Application Insights powinny korzystać z warstwy cenowej "Enterprise".

W zależności od liczby węzłów pakietu zakupionej przez Twoją organizację przeniesienie niektórych subskrypcji do warstwy cenowej płatność zgodnie z rzeczywistym użyciem (za GB) może być korzystne, ale wymaga to starannej uwagi.

> [!WARNING]
> Jeśli Twoja organizacja ma bieżące Microsoft Operations Management Suite E1 i E2, zazwyczaj najlepszym rozwiązaniem jest utrzymywanie Log Analytics obszarów roboczych w warstwie cenowej "na węzeł (OMS)" oraz zasobach Application Insights w warstwie cenowej "Enterprise". 
>

