---
title: Media Services monitorowania
description: Zacznij tutaj, aby dowiedzieć się, jak monitorować Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609053"
---
# <a name="monitor-media-services"></a>Monitoruj Media Services

Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez Media Services i sposób korzystania z funkcji Azure Monitor do analizowania i generowania alertów dotyczących tych danych.

## <a name="metrics-are-useful"></a>Metryki są przydatne

Poniżej przedstawiono przykłady sposobu, w jaki monitorowanie Media Services metryki mogą pomóc zrozumieć sposób działania aplikacji. Oto kilka pytań, na które można rozwiązać Media Services metryki:

- Jak mogę monitorować mój standardowy punkt końcowy przesyłania strumieniowego, aby sprawdzić, czy przekroczono limity?
- Jak mogę sprawdzić, czy mam wystarczającą liczbę jednostek skalowania punktów końcowych przesyłania strumieniowego w warstwie Premium?
- Jak ustawić alert, aby dowiedzieć się, kiedy skalować punkty końcowe przesyłania strumieniowego?
- Jak mogę ustawić alert, aby sprawdzić, czy osiągnięto maksymalną liczbę wyjściową skonfigurowaną na koncie?
- Jak można zobaczyć podział żądań zakończonych niepowodzeniem i przyczynę niepowodzenia?
- Jak mogę sprawdzić, ile żądań HLS lub PAUZy jest pobieranych z Pakowarki?
- Jak mogę ustawić alert, aby wiedzieć, kiedy osiągnięto wartość progową nieudanych żądań?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Media Services tworzy dane monitorowania przy użyciu [Azure monitor](../../../azure-monitor/overview.md), który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych.

Zacznij od odczytywania artykułu dotyczącego [monitorowania zasobów platformy Azure za pomocą Azure monitor](../../../azure-monitor/essentials/monitor-azure-resource.md), który opisuje następujące pojęcia:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

## <a name="monitoring-data"></a>Dane monitorowania

Media Services gromadzi te same rodzaje danych monitorowania, jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Wszystkie dane zbierane przez usługę Azure Monitor pasują do jednego z dwóch podstawowych typów: metryk i dzienników. Z tymi dwoma typami można:

- Wizualizuj i analizuj dane metryk przy użyciu Eksploratora metryk.
- Monitoruj Media Services dzienniki diagnostyczne i twórz dla nich alerty i powiadomienia.
- Za pomocą dzienników można:
  - Wyślij do usługi Azure Storage
  - Przesyłanie strumieniowe do usługi Azure Event Hubs
  - Eksportuj je do Log Analytics
  - Korzystanie z usług innych firm

Aby uzyskać szczegółowe informacje na temat metryk i dzienników tworzonych przez Media Services, zobacz artykuł [monitorowanie danych Media Services](monitor-media-services-data-reference.md) .

## <a name="collection-and-routing"></a>Kolekcja i Routing

*Metryki platformy* i *Dziennik aktywności* są zbierane i przechowywane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawień diagnostycznych.  

*Dzienniki zasobów* **nie** są zbierane i przechowywane do momentu utworzenia ustawienia diagnostycznego i skierowania ich do co najmniej jednej lokalizacji.

Zapoznaj się z artykułem [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](../../../azure-monitor/essentials/diagnostic-settings.md) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell.

Podczas tworzenia ustawienia diagnostycznego należy określić, które kategorie dzienników mają być zbierane. Kategorie Media Services są wymienione w temacie [Media Services monitorowanie danych](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki dla Media Services z metrykami z innych usług platformy Azure za pomocą Eksploratora metryk można analizować, otwierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../../azure-monitor/essentials/metrics-getting-started.md) .

Listę metryk zbieranych dla Media Services można znaleźć w temacie [Monitoring Media Services Data Reference](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Analizowanie dzienników

Dane w dziennikach Azure Monitor są przechowywane w tabelach, w których każda tabela ma swój własny zestaw unikatowych właściwości.  

Wszystkie dzienniki zasobów w Azure Monitor mają te same pola, a następnie pola specyficzne dla usługi. Wspólny schemat został przedstawiony w [schemacie dziennika zasobów Azure monitor](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Schemat Media Services dzienników zasobów znajduje się w temacie [monitorowanie Media Services odwołania do danych](monitor-media-services-data-reference.md).

[Dziennik aktywności](../../../azure-monitor/essentials/activity-log.md) to dziennik platformy na platformie Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Można wyświetlić go niezależnie lub skierować do dzienników Azure Monitor, w którym można wykonywać dużo bardziej skomplikowane zapytania przy użyciu Log Analytics.

Listę typów dzienników zasobów zbieranych dla Media Services można znaleźć w temacie [Monitoring Media Services Data Reference](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Dlaczego warto używać dzienników diagnostycznych?

Niektóre elementy, które można przeanalizować za pomocą dzienników diagnostycznych, to:

- Liczba licencji dostarczonych przez typ DRM.
- Liczba licencji dostarczonych przez zasady.
- Błędy według typu DRM lub zasad.
- Liczba nieautoryzowanych żądań licencji od klientów.

## <a name="alerts"></a>Alerty

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów w systemie przed ich zapisaniem przez klientów. Można ustawić alerty dotyczące [metryk](../../../azure-monitor/alerts/alerts-metric-overview.md), [dzienników](../../../azure-monitor/alerts/alerts-unified-log.md)i [dziennika aktywności](../../../azure-monitor/alerts/activity-log-alerts.md).

Metryki Media Services są zbierane w regularnych odstępach czasu, niezależnie od tego, czy wartość jest zmieniana. Są one przydatne w przypadku alertów, ponieważ mogą być próbkowane często. Alert może być uruchamiany szybko z stosunkowo prostą logiką.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
