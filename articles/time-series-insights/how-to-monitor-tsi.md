---
title: Time Series Insights monitorowania | Microsoft Docs
description: Monitoruj Time Series Insights pod kątem dostępności, wydajności i operacji.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: a46ddeddfcefcd4d6e7f87747fe36cfc6ec82e35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737566"
---
# <a name="monitoring-time-series-insights"></a>Time Series Insights monitorowania

Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez Time Series Insights i sposób korzystania z funkcji Azure Monitor do analizowania i generowania alertów dotyczących tych danych.

## <a name="monitor-overview"></a>Przegląd monitora

Strona **Przegląd** w Azure Portal dla każdego środowiska Time Series Insights zawiera krótki widok użycia zasobów, taki jak liczba odebranych komunikatów i liczba przechowywanych bajtów. Te informacje są przydatne, ale w tym okienku jest dostępnych tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu. Dodatkowe typy zbierania danych można włączyć za pomocą jakiejś konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor

Time Series Insights tworzy dane monitorowania przy użyciu [Azure monitor](../azure-monitor/overview.md), który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych.

Zacznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md), który opisuje następujące pojęcia:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje zostały skompilowane w tym artykule, opisując szczegółowe dane zebrane dla Azure Time Series Insights. Te sekcje zawierają również przykłady konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

> [!TIP]
> Aby zrozumieć koszty związane z Azure Monitor, zobacz [użycie i szacowane koszty](../azure-monitor//usage-estimated-costs.md). Aby zrozumieć czas, w jakim dane mają być wyświetlane w Azure Monitor, zobacz czas pozyskiwania [danych dziennika](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Monitorowanie danych z Azure Time Series Insights

Azure Time Series Insights gromadzi te same rodzaje danych monitorowania, jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Aby uzyskać szczegółowe informacje o dziennikach i metrykach, które można zbierać, zobacz [Azure Time Series Insights informacje o danych monitorowania](how-to-monitor-tsi-reference.md) .

## <a name="collection-and-routing"></a>Kolekcja i Routing

Metryki platformy są zbierane i przechowywane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawienia diagnostycznego.

Dzienniki zasobów nie są zbierane i przechowywane do momentu utworzenia ustawienia diagnostycznego i skierowania ich do co najmniej jednej lokalizacji.
Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure](../azure-monitor/essentials/diagnostic-settings.md) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell. Podczas tworzenia ustawienia diagnostycznego należy określić, które kategorie dzienników mają być zbierane.

Dzienniki można zbierać z następujących kategorii dla Azure Time Series Insights:

   | Kategoria | Opis |
   |---|---|
   | Ruch przychodzący  | Kategoria ruchu przychodzącego śledzi błędy występujące w potoku transferu danych przychodzących. Ta kategoria zawiera błędy występujące podczas otrzymywania zdarzeń (takich jak awarie w celu nawiązania połączenia ze źródłem zdarzenia) i przetwarzania zdarzeń (takich jak błędy podczas analizowania ładunku zdarzenia). |

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki dla Azure Time Series Insights, a także metryki z innych usług platformy Azure, można analizować, otwierając metryki z menu Azure Monitor. Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../azure-monitor/essentials/metrics-getting-started.md) .

Aby zapoznać się z listą zebranych metryk platformy, zobacz [informacje dotyczące monitorowania Azure Time Series Insights danych](how-to-monitor-tsi-reference.md#metrics)

W tym przykładzie przedstawiono liczbę bajtów odebranych ze wszystkich źródeł zdarzeń do środowiska Azure Time Series Insights.

**Odebrane bajty odbierane** w [ ![ usłudze Azure Time Series](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox) z transferem danych przychodzących

W przykładzie pokazano, ile bajtów zostało pomyślnie przetworzonych i dostępnych dla zapytania w środowisku Azure Time Series Insights.

**Bajty przechowywane** w [ ![ usłudze Azure Time Series w bajtach przechowywanych](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox) w pamięci podchodzącej

## <a name="analyzing-logs"></a>Analizowanie dzienników
Można uzyskać dostęp do dzienników zasobów jako obiektów BLOB na koncie magazynu, jako dane zdarzenia lub za pomocą zapytań analitycznych dzienników.

Dane w dziennikach Azure Monitor są przechowywane w tabelach, dla których każda tabela ma swój własny zestaw unikatowych właściwości.

Wszystkie dzienniki zasobów w Azure Monitor mają te same pola, a następnie pola specyficzne dla usługi. Wspólny schemat został przedstawiony w [schemacie dziennika zasobów Azure monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Listę typów dzienników zasobów zbieranych dla Azure Time Series Insights można znaleźć w temacie [Azure Time Series Insights monitorowanie danych](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights przechowuje dane w poniższych tabelach.

| Tabela | Opis |
|:---|:---|
| TSIIngress | Tabela, w której są przechowywane dane z kategorii transferów przychodzących. Kategoria ruchu przychodzącego śledzi błędy występujące w potoku transferu danych przychodzących. Ta kategoria zawiera błędy występujące podczas otrzymywania zdarzeń (takich jak awarie w celu nawiązania połączenia ze źródłem zdarzenia) i przetwarzania zdarzeń (takich jak błędy podczas analizowania ładunku zdarzenia).

Aby przekierować dane do dzienników Azure Monitor, należy utworzyć ustawienie diagnostyczne, aby wysyłać dzienniki zasobów lub metryki platformy do Log Analytics obszaru roboczego. Aby dowiedzieć się więcej, zobacz [zbieranie i Routing](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono zapytania, których można użyć do monitorowania środowiska Azure Time Series Insightsowego:

+ Uzyskaj szczegółowe informacje o błędach połączenia ze źródłem zdarzeń w ciągu ostatnich pięciu dni:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Pobierz szczegóły dotyczące nieprawidłowych komunikatów odebranych w ciągu ostatnich pięciu dni:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Alerty

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów w systemie przed ich zapisaniem przez klientów. Można ustawić alerty dotyczące [metryk](../azure-monitor/alerts/alerts-metric-overview.md), [dzienników](../azure-monitor/alerts/alerts-unified-log.md)i [dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md). Różne typy alertów mają zalety i wady.

Podczas tworzenia reguły alertu na podstawie metryk platformy należy pamiętać, że w przypadku metryk Time Series Insights platformy, które są zbierane w jednostkach zliczania, niektóre agregacje mogą nie być dostępne ani użyteczne.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o dziennikach i metrykach utworzonych przez Azure Time Series Insights, zobacz temat [Informacje o danych monitorowania Azure Time Series Insights](how-to-monitor-tsi-reference.md) .
* Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) .