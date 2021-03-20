---
title: Omówienie autoskalowania w zestawach skalowania maszyn wirtualnych platformy Azure
description: Poznaj różne sposoby automatycznego skalowania zestawu skalowania maszyn wirtualnych platformy Azure na podstawie wydajności lub ustalonego harmonogramu
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591407"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Omówienie autoskalowania w zestawach skalowania maszyn wirtualnych platformy Azure
Zestaw skalowania maszyn wirtualnych platformy Azure może automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych, na których działa aplikacja. To automatyczne i elastyczne zachowanie zmniejsza obciążenie związane z zarządzaniem w celu monitorowania i optymalizowania wydajności aplikacji. Tworzysz reguły, które definiują akceptowalną wydajność dla pozytywnego środowiska klienta. Po spełnieniu tych zdefiniowanych progów reguły automatycznego skalowania podejmują działania w celu dostosowania pojemności zestawu skalowania. Możesz również zaplanować zdarzenia, aby automatycznie zwiększać lub zmniejszać pojemność zestawu skalowania w ustalonych odstępach czasu. Ten artykuł zawiera omówienie dostępnych metryk wydajności i działań, które mogą być wykonywane automatycznie.


## <a name="benefits-of-autoscale"></a>Zalety automatycznego skalowania
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania.

> [!NOTE]
> W przypadku korzystania z automatycznych napraw wystąpienia w zestawie skalowania Maksymalna liczba wystąpień w zestawie skalowania może wynosić 200. Dowiedz się więcej o [automatycznym naprawianiu wystąpień](./virtual-machine-scale-sets-automatic-instance-repairs.md).

Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Użytkownik kontroluje, jakie metryki mają być monitorowane, takie jak procesor CPU lub pamięć, jak długo obciążenie aplikacji musi spełniać daną wartość progową oraz liczbę wystąpień maszyn wirtualnych do dodania do zestawu skalowania.

Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.


## <a name="use-host-based-metrics"></a>Korzystanie z metryk opartych na hoście
Można tworzyć reguły automatycznego skalowania, które są wbudowane metryki hosta dostępne z wystąpień maszyn wirtualnych. Metryki hosta zapewniają wgląd w wydajność wystąpień maszyn wirtualnych w zestawie skalowania bez konieczności instalowania lub konfigurowania dodatkowych agentów i kolekcji danych. Reguły automatycznego skalowania, które używają tych metryk, mogą skalować w poziomie lub w liczbie wystąpień maszyn wirtualnych w odpowiedzi na użycie procesora CPU, zapotrzebowanie na pamięć lub dostęp do dysku.

Reguły automatycznego skalowania, które korzystają z metryk opartych na hoście, można utworzyć za pomocą jednego z następujących narzędzi:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](tutorial-autoscale-cli.md)
- [Szablon Azure](tutorial-autoscale-template.md)

Aby utworzyć reguły automatycznego skalowania, które używają bardziej szczegółowych metryk wydajności, można [zainstalować i skonfigurować rozszerzenie usługi Azure Diagnostics](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) na WYSTĄPIENIACH maszyn wirtualnych lub [skonfigurować aplikację przy użyciu usługi App Insights](#application-level-metrics-with-app-insights).

Reguły automatycznego skalowania, które używają metryk opartych na hoście, metryk maszyny wirtualnej gościa przy użyciu rozszerzenia diagnostyki platformy Azure, a usługa App Insights może korzystać z następujących ustawień konfiguracji.

### <a name="metric-sources"></a>Źródła metryk
Reguły skalowania automatycznego mogą używać metryk z jednego z następujących źródeł:

| Źródło metryki        | Przypadek użycia                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Bieżący zestaw skalowania    | W przypadku metryk opartych na hoście, które nie wymagają zainstalowania lub skonfigurowania dodatkowych agentów.                                  |
| Konto magazynu      | Rozszerzenie diagnostyki platformy Azure zapisuje metryki wydajności do usługi Azure Storage, która jest następnie używana do wyzwalania reguł skalowania automatycznego. |
| Kolejka usługi Service Bus    | Aplikacja lub inne składniki mogą przesyłać komunikaty w kolejce Azure Service Bus, aby wyzwolić reguły.                   |
| Application Insights | Pakiet Instrumentacji zainstalowany w aplikacji, który przesyła strumieniowo metryki bezpośrednio z aplikacji.                         |


### <a name="autoscale-rule-criteria"></a>Kryteria reguły automatycznego skalowania
Następujące metryki oparte na hoście są dostępne do użycia podczas tworzenia reguł skalowania automatycznego. Jeśli używasz rozszerzenia diagnostyki platformy Azure lub usługi App Insights, możesz zdefiniować metryki do monitorowania i używania z regułami skalowania automatycznego.

| Nazwa metryki               |
|---------------------------|
| Procentowe użycie procesora CPU            |
| Sieć — wejście                |
| Sieć — wyjście               |
| Bajty odczytu dysku           |
| Bajty zapisu dysku          |
| Operacje odczytu z dysku/s  |
| Operacje zapisu na dysku/s |
| Pozostałe kredyty procesora CPU     |
| Wykorzystane środki CPU      |

Podczas tworzenia reguł skalowania automatycznego w celu monitorowania danej metryki reguły przenoszą jedną z następujących akcji agregacji metryk:

| Typ agregacji |
|------------------|
| Średnia          |
| Minimum          |
| Maksimum          |
| Łącznie            |
| Ostatnie             |
| Liczba            |

Reguły skalowania automatycznego są następnie wyzwalane, gdy metryki są porównywane ze zdefiniowanym progiem z jednym z następujących operatorów:

| Operator                 |
|--------------------------|
| Większe niż             |
| Większe niż lub równe |
| Mniejsze niż                |
| Mniejsze niż lub równe    |
| Równe                 |
| Różne od             |


### <a name="actions-when-rules-trigger"></a>Akcje, gdy wyzwalacz reguł
Podczas wyzwalania reguły automatycznego skalowania zestaw skalowania może być automatycznie skalowany w jeden z następujących sposobów:

| Operacja skalowania     | Przypadek użycia                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zwiększ licznik o   | Stała liczba wystąpień maszyn wirtualnych do utworzenia. Przydatne w zestawach skalowania z mniejszą liczbą maszyn wirtualnych.                                           |
| Zwiększ wartość procentową o | Procentowy wzrost wystąpień maszyn wirtualnych. Dobre dla większych zestawów skalowania, w przypadku których stały wzrost może nie znacznie poprawić wydajności. |
| Zwiększ liczbę do   | Utwórz tyle wystąpień maszyn wirtualnych, aby osiągnąć żądaną maksymalną kwotę.                                                            |
| Zmniejsz licznik o   | Stała liczba wystąpień maszyn wirtualnych do usunięcia. Przydatne w zestawach skalowania z mniejszą liczbą maszyn wirtualnych.                                           |
| Zmniejsz procent według | Zmniejszenie liczby wystąpień maszyn wirtualnych na podstawie procentu. Dobre dla większych zestawów skalowania, w których stały wzrost nie może znacznie zmniejszyć zużycia zasobów i kosztów. |
| Zmniejsz liczbę do   | Usuń tyle wystąpień maszyn wirtualnych, które są wymagane do osiągnięcia żądanej minimalnej ilości.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metryki maszyn wirtualnych gościa z rozszerzeniem usługi Azure Diagnostics
Rozszerzenie diagnostyki Azure to Agent, który działa w wystąpieniu maszyny wirtualnej. Agent monitoruje i zapisuje metryki wydajności do usługi Azure Storage. Te metryki wydajności zawierają bardziej szczegółowe informacje o stanie maszyny wirtualnej, takie jak *AverageReadTime* dla dysków lub *PercentIdleTime* dla procesora CPU. Można tworzyć reguły automatycznego skalowania na podstawie bardziej szczegółowej świadomości wydajności maszyny wirtualnej, a nie tylko procent użycia procesora CPU lub zużycia pamięci.

Aby użyć rozszerzenia diagnostyki Azure, należy utworzyć konta usługi Azure Storage dla wystąpień maszyn wirtualnych, zainstalować agenta usługi Azure Diagnostics, a następnie skonfigurować maszyny wirtualne do przesyłania strumieniowego określonych liczników wydajności do konta magazynu.

Aby uzyskać więcej informacji, zobacz artykuły o tym, jak włączyć rozszerzenie diagnostyki platformy Azure na [maszynie wirtualnej z systemem Linux](../virtual-machines/extensions/diagnostics-linux.md) lub [maszynie wirtualnej z systemem Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metryki na poziomie aplikacji z usługą App Insights
Aby zwiększyć widoczność wydajności aplikacji, można użyć Application Insights. Zainstalujesz mały pakiet Instrumentacji w aplikacji, który monitoruje aplikację i wysyła dane telemetryczne do platformy Azure. Można monitorować metryki, takie jak czasy odpowiedzi aplikacji, wydajność ładowania strony i liczby sesji. Te metryki aplikacji mogą służyć do tworzenia reguł skalowania automatycznego na poziomie szczegółowym i osadzonym w miarę wyzwalania reguł na podstawie szczegółowych informacji, które mogą mieć wpływ na środowisko klienta.

Aby uzyskać więcej informacji o usłudze Application Insights, zobacz [Co to jest usługa Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Zaplanowane Skalowanie automatyczne
Można również tworzyć reguły automatycznego skalowania na podstawie harmonogramów. Te reguły oparte na harmonogramie umożliwiają automatyczne skalowanie liczby wystąpień maszyn wirtualnych o stałym czasie. W przypadku reguł opartych na wydajności może wystąpić wpływ na wydajność aplikacji, zanim wyzwalacz reguł skalowania automatycznego i nowe wystąpienia maszyn wirtualnych zostaną udostępnione. Jeśli możesz zawidzieć takie zapotrzebowanie, dodatkowe wystąpienia maszyn wirtualnych są obsługiwane i gotowe do użycia w dodatkowych klientach i zapotrzebowaniu na aplikacje.

Poniżej przedstawiono scenariusze, które mogą korzystać z reguł automatycznego skalowania opartych na harmonogramie:

- Automatyczne skalowanie liczby wystąpień maszyn wirtualnych na początku dnia roboczego w przypadku wzrostu popytu na żądanie klienta. Na koniec dnia roboczego program automatycznie skaluje liczbę wystąpień maszyn wirtualnych w celu zminimalizowania kosztów zasobów, gdy użycie aplikacji jest niskie.
- Jeśli dział wielokrotnie zużywa aplikację w określonych częściach miesiąca lub cyklu Obrachunkowego, automatycznie skaluje liczbę wystąpień maszyn wirtualnych, aby sprostać ich dodatkowym potrzebom.
- W przypadku wystąpienia wydarzeń marketingowych, promocji lub sprzedaży w dniach wolnych można automatycznie skalować liczbę wystąpień maszyn wirtualnych przed przewidywanym zapotrzebowaniem klienta. 


## <a name="next-steps"></a>Następne kroki
Można tworzyć reguły automatycznego skalowania, które korzystają z metryk opartych na hoście, przy użyciu jednego z następujących narzędzi:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](tutorial-autoscale-cli.md)
- [Szablon Azure](tutorial-autoscale-template.md)

W tym omówieniu szczegółowo opisano sposób używania reguł skalowania automatycznego w celu skalowania w poziomie i zwiększania lub zmniejszania *liczby* wystąpień maszyn wirtualnych w zestawie skalowania. Możesz również skalować w pionie, aby zwiększyć lub zmniejszyć *rozmiar* wystąpienia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [pionowy Skalowanie automatyczne przy użyciu zestawów skalowania maszyn wirtualnych](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Aby uzyskać informacje na temat zarządzania wystąpieniami maszyn wirtualnych, zobacz [Zarządzanie zestawami skalowania maszyn wirtualnych za pomocą Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Aby dowiedzieć się, jak generować alerty podczas wyzwalacza reguł skalowania automatycznego, zobacz [Używanie akcji automatycznego skalowania do wysyłania powiadomień o alertach poczty e-mail i elementów webhook w Azure monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). [Dzienników inspekcji można także używać do wysyłania powiadomień o alertach poczty e-mail i elementów webhook w programie Azure monitor](../azure-monitor/alerts/alerts-log-webhook.md).
