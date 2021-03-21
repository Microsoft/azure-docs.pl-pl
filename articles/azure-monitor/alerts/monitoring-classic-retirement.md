---
title: Aktualizacja klasycznego alertu & monitorowania w programie Azure Monitor
description: Opis wycofania usług i funkcji monitorowania klasycznego, wcześniej pokazanych w Azure Portal w obszarze alerty (klasyczne).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041097"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Ujednolicone & monitorowania alertów w Azure Monitor zastępują klasyczne alerty & monitorowania

Azure Monitor to Ujednolicony stos monitorowania, który obsługuje "jedną metrykę" i "jeden alert" w ramach zasobów platformy Azure. Więcej informacji można znaleźć w tym [wpisie w blogu](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nowe platformy do monitorowania i zgłaszania alertów platformy Azure zostały skompilowane, aby były szybsze, inteligentniejsze i rozszerzalne — dzięki rozwijającej się expanse w chmurze obliczeniowej i w firmie Microsoft Intelligent Cloud.

Dzięki nowej platformie monitorowania i wysyłania alertów platformy Azure klasyczne alerty w Azure Monitor są wycofywane dla użytkowników chmury publicznej, chociaż nadal są ograniczone do **31 maja 2021**. Alerty klasyczne dla usług Azure Government Cloud i Azure Chiny 21Vianet zostaną wycofane z **29 lutego 2024**.

 ![Alert klasyczny w Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Zachęcamy do rozpoczęcia i ponownego utworzenia alertów na nowej platformie.

> [!IMPORTANT]
> Klasyczne reguły alertów utworzone w dzienniku aktywności nie będą przestarzałe ani zmigrowane. Dostęp do wszystkich klasycznych reguł alertów utworzonych w dzienniku aktywności można uzyskać, korzystając z nowych alertów Azure Monitor. Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure monitor](./alerts-activity-log.md). Podobnie można uzyskać dostęp do alertów dotyczących Service Health i używać ich w przypadku, gdy pochodzi z nowej sekcji Service Health. Aby uzyskać szczegółowe informacje, zobacz [alerty dotyczące powiadomień o kondycji usługi](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Ujednolicone metryki i alerty dla zasobów platformy Azure

W marcu 2018 wydaliśmy następną generację alertów dla zasobów platformy Azure. Nowsza platforma metryk i alerty są szybsze i zapewniają większy stopień szczegółowości przy użyciu wymiarów. Wymiary umożliwiają wycięcie i przefiltrowanie do określonej kombinacji wartości, warunku lub operacji. Nowsze alerty metryk używają grup akcji zezwalających na więcej powiadomień i akcji automatyzacji. Zobacz więcej informacji na temat [zarządzania alertami metryk przy użyciu Azure monitor](./alerts-metric.md).

Nowsze metryki dla zasobów platformy Azure są dostępne jako:

- **Azure monitor metryki standardowej platformy** — oferuje popularne, wstępnie wypełnione metryki z różnych usług i produktów platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym [obsługiwanych metryk w witrynie Azure monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) i [Obsługuj alerty dotyczące metryk w Azure monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure monitor metryki niestandardowych** — zapewniające metryki z źródeł opartych na użytkownikach, w tym agenta Diagnostyka Azure. Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym [niestandardowych metryk w Azure monitor](../essentials/metrics-custom-overview.md). Przy użyciu metryk niestandardowych można także publikować metryki zbierane przez program [Windows Diagnostyka Azure Agent](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) i [agenta telegraf InfluxData](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Wycofanie klasycznej platformy monitorowania i zgłaszania alertów

Jak wspomniano wcześniej, starsze klasyczne monitorowanie i alerty są wycofywane dla użytkowników chmury publicznej. Obejmuje to zamknięcie pokrewnych interfejsów API, interfejsu Azure Portal i usług w nim, chociaż nadal są ograniczone do **31 maja 2021**. Alerty klasyczne dla usług Azure Government Cloud i Azure Chiny 21Vianet zostaną wycofane z **29 lutego 2024**.

W odniesieniu do zakresu emerytury są dostępne wyłącznie w przypadku klasycznych metryk dostępnych obecnie w [sekcji alertów (klasycznych)](./alerts-classic.overview.md) Azure Portal i dostępnych jako [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules) zasoby.

Składają się na to następujące elementy:

- Usługa klasycznego monitorowania i alertów zostanie wycofana i nie będzie już dostępna do tworzenia nowych reguł alertów.
- Wszystkie reguły alertów, które nadal istnieją w alertach (klasyczny), będą nadal wykonywane i wyzwalane.
- Zostaną zmigrowane większość klasycznych reguł alertów. Proces ten będzie bezproblemowe bez przestojów, a klienci nie będą mogli korzystać z monitorowania.
- Wywołane powiadomienia będą zawierać nowe struktury ładunków. Należy dostosować obiekt docelowy do pracy z nową strukturą.
- [Nie można automatycznie migrować niektórych klasycznych reguł alertów, które](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) będą wymagać ręcznej akcji od użytkowników, aby kontynuować pracę.

> [!IMPORTANT]
> Azure Monitor wybrała [Narzędzie do dobrowolnej migracji](alerts-using-migration-tool.md) reguł alertów klasycznych do nowej platformy. Pozostałe reguły zostaną zmigrowane automatycznie po wycofaniu usługi. Klienci będą musieli zapewnić, że Automatyzacja korzystający z klasycznej reguły alertów jest dostosowywana do obsługi nowego ładunku z [ujednoliconych metryk i alertów dla innych zasobów platformy Azure](#unified-metrics-and-alerts-for-azure-resources)po migracji klasycznych reguł alertów. Aby uzyskać więcej informacji, zobacz [Przygotowywanie do migracji klasycznej reguły alertów](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Cennik dla zmigrowanych reguł alertów

Opracowujemy narzędzie do migracji, które ułatwia Migrowanie Azure Monitor [klasycznych alertów](./alerts-classic.overview.md) do nowego środowiska alertów. Zmigrowane reguły alertów i odpowiadające im zmigrowane grupy akcji (poczta e-mail, element webhook lub LogicApp) pozostaną bezpłatne. Funkcja z alertami klasycznymi, w tym możliwość edytowania progu, typu agregacji i stopnia szczegółowości agregacji, będzie nadal dostępna bezpłatnie z zmigrowanymi regułami alertów. Jednak w przypadku edytowania zmigrowanej reguły alertów w celu korzystania z dowolnej z nowych funkcji platformy alertów, powiadomień lub typów akcji zostanie naliczona odpowiednia opłata. Aby uzyskać więcej informacji na temat cen reguł alertów i powiadomień, zobacz [Azure monitor Cennik](https://azure.microsoft.com/pricing/details/monitor/).

Poniżej przedstawiono przykłady sytuacji, w których naliczane są opłaty za regułę alertu:

- Każda nowa (niemigrowana) reguła alertu utworzona poza wolne jednostki na nowej platformie Azure Monitor
- Wszelkie dane pozyskane i zachowane poza bezpłatnymi jednostkami zawartymi w Azure Monitor
- Wszystkie testy sieci Web wielotestowego wykonywane przez Application Insights
- Wszystkie metryki niestandardowe przechowywane poza bezpłatnymi jednostkami zawartymi w Azure Monitor
- Wszystkie zmigrowane reguły alertów, które są edytowane w celu używania nowszych funkcji alertu metryki, takich jak częstotliwość, wiele zasobów/wymiary, [dynamiczne progi](../alerts/alerts-dynamic-thresholds.md), zmiana zasobu/sygnał i tak dalej.
- Wszystkie zmigrowane grupy akcji, które są edytowane w celu korzystania z nowszych powiadomień lub typy akcji, takich jak wiadomości SMS, połączenia głosowe i/lub integracja narzędzia ITSM.

## <a name="next-steps"></a>Następne kroki

* Poznaj [nowe ujednolicone Azure monitor](../overview.md).
* Dowiedz się więcej o nowych [alertach platformy Azure](./alerts-overview.md).