---
title: Przegląd klasycznych alertów w Azure Monitor
description: Alerty klasyczne są przestarzałe. Alerty umożliwiają monitorowanie metryk zasobów platformy Azure, zdarzeń lub dzienników oraz powiadamianie o spełnieniu warunku określonego przez użytkownika.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 38b393281692ac14083a0a8bbf5b8908b5758a39
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737311"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>What are classic alerts in Microsoft Azure? (Czym są alerty klasyczne na platformie Microsoft Azure?)

> [!NOTE]
> W tym artykule opisano sposób tworzenia starszych klasycznych alertów dotyczących metryk. Azure Monitor teraz obsługuje [nowsze alerty metryk niemal w czasie rzeczywistym i nowe środowisko alertów](./alerts-overview.md). Alerty klasyczne są [wycofywane](./monitoring-classic-retirement.md) dla użytkowników chmury publicznej, ale nadal są ograniczone do **31 maja 2021**. Alerty klasyczne dla usług Azure Government Cloud i Azure Chiny 21Vianet zostaną wycofane z **29 lutego 2024**.
>

Alerty umożliwiają konfigurowanie warunków dla danych i otrzymywanie powiadomień, gdy warunki są zgodne z najnowszymi danymi monitorowania.

## <a name="old-and-new-alerting-capabilities"></a>Stare i nowe funkcje alertów

W przeszłości Azure Monitor, Application Insights, Log Analytics i Service Health miały osobne funkcje alertów. W nadgodzinach, platforma Azure ulepszona i połączona zarówno z interfejsem użytkownika, jak i różnymi metodami alertów. Konsolidacja jest nadal w toku.

Alerty klasyczne można wyświetlać tylko na ekranie klasycznych alertów użytkownika w Azure Portal. Ten ekran jest wyświetlany na przycisku **Wyświetl klasyczne alerty** na ekranie alertów. 

 ![Opcje alertów w Azure Portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Nowe możliwości użytkownika dotyczące alertów mają następujące zalety w porównaniu z klasycznymi alertami:
- **Lepszy system powiadomień** — wszystkie nowsze alerty używają grup akcji, które są nazwanymi grupami powiadomień i akcji, które mogą być ponownie używane w wielu alertach. Klasyczne alerty metryk i starsze alerty Log Analytics nie używają grup akcji.
- **Ujednolicone środowisko tworzenia** — wszystkie tworzenie alertów dotyczących metryk, dzienników i dzienników aktywności w Azure Monitor, Log Analytics i Application Insights jest w jednym miejscu.
- **Wyświetl wyzwolone alerty log Analytics w Azure Portal** — teraz można zobaczyć wyzwolone alerty log Analytics w subskrypcji. Wcześniej były one w osobnym portalu.
- **Rozdzielenie wywołanych alertów i reguł alertów** — reguły alertów (definicja warunku wyzwalającego Alert), a następnie wyzwolone alerty (wystąpienie reguły alertu jest wyzwalane) są rozróżniane, więc widoki operacyjne i konfiguracyjne są rozdzielone.
- **Lepszy przepływ pracy** — nowe środowisko tworzenia alertów prowadzi użytkownika przez proces konfigurowania reguły alertu, co ułatwia odkrywanie właściwych rzeczy w celu uzyskania alertów.
- **Alerty inteligentne konsolidacja** i **Ustawianie stanu alertu**  — nowsze alerty obejmują funkcję grupowania podwójnego pokazującą podobne alerty, aby zredukować Przeciążenie w interfejsie użytkownika. 

Nowsze alerty dotyczące metryk mają następujące korzyści dotyczące klasycznego alertu dotyczącego metryki:
- **Ulepszone opóźnienie**: nowsze alerty metryczne mogą być uruchamiane w ciągu co najmniej jednej minuty. Starsze alerty metryk są zawsze uruchamiane z częstotliwością wynoszącą 5 minut. Nowsze alerty zwiększają mniejsze opóźnienie od wystąpienia problemu do powiadomienia lub akcji (od 3 do 5 minut). Starsze alerty są od 5 do 15 minut w zależności od typu.  Alerty dzienników zwykle mają od 10 do 15 minut opóźnienia z powodu czasu potrzebnego na pozyskiwanie dzienników, ale nowsze metody przetwarzania skracają ten czas. 
- **Obsługa wielowymiarowych metryk**: można generować alerty dla metryk wymiarowych, co pozwala monitorować interesujący segment metryki.
- **Większa kontrola nad warunkami metryk**: można zdefiniować bogatsze reguły alertów. Nowsze alerty obsługują monitorowanie wartości maksymalnej, minimalnej, średniej i całkowitej metryk.
- **Połączone monitorowanie wielu metryk**: można monitorować wiele metryk (obecnie do dwóch metryk) przy użyciu jednej reguły. Alert jest wyzwalany, jeśli obydwie metryki naruszają odpowiednie progi przez określony czas.
- **Lepszy system powiadomień**: wszystkie nowsze alerty używają [grup akcji](./action-groups.md), które są nazwanymi grupami powiadomień i akcji, które mogą być ponownie używane w wielu alertach.  Klasyczne alerty metryk i starsze alerty Log Analytics nie używają grup akcji. 
- **Metryki z dzienników** (publiczna wersja zapoznawcza): dane dziennika do log Analytics mogą być teraz wyodrębniane i konwertowane na metryki Azure monitor, a następnie otrzymywane z alertami podobnie jak inne metryki. Zobacz [alerty (klasyczne)]() , aby zapoznać się z terminologią specyficzną dla alertów klasycznych. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasyczne alerty dotyczące danych Azure Monitor
Dostępne są dwa typy alertów klasycznych — alerty metryk i alerty dzienników aktywności.

* **Klasyczne alerty metryk** — ten alert jest wyzwalany, gdy wartość określonej metryki przekroczy przypisany próg. Alert generuje powiadomienie w przypadku przekroczenia progu i spełnienia warunku alertu. W tym momencie alert jest uznawany za aktywowany. Generuje inne powiadomienie, gdy jest "rozwiązane" — to znaczy, gdy próg zostanie przekroczony, a warunek nie jest już spełniany.

* **Alerty dziennika aktywności klasycznej** — alert dziennika przesyłania strumieniowego, który jest wyzwalany dla wpisu zdarzenia dziennika aktywności, który odpowiada kryteriom filtrowania. Te alerty mają tylko jeden stan "aktywowany". Aparat alertów po prostu stosuje kryteria filtrowania do każdego nowego zdarzenia. Nie wyszukuje starszych wpisów. Te alerty mogą powiadamiać użytkownika o wystąpieniu nowego zdarzenia Service Health lub gdy użytkownik lub aplikacja wykonuje operację w ramach subskrypcji, na przykład "Usuń maszynę wirtualną".

W przypadku danych dzienników zasobów dostępnych za pomocą Azure Monitor należy skierować dane do Log Analytics i użyć alertu zapytania dziennika. Log Analytics teraz używa [nowej metody alertów](./alerts-overview.md) 

Poniższy diagram podsumowuje źródła danych w Azure Monitor i pojęciowo, w jaki sposób można ostrzec o tych danych.

![Objaśniono alerty](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taksonomia alertów (klasyczny)
Na platformie Azure są używane następujące warunki do opisywania klasycznych alertów i ich funkcji:
* **Alert** — definicja kryteriów (co najmniej jedna reguła lub warunki), które zostaną aktywowane po spełnieniu.
* Stan **aktywny** — gdy spełnione są kryteria zdefiniowane przez alert klasyczny.
* **Rozwiązano** — stan, gdy kryteria zdefiniowane przez alert klasyczny nie są już spełnione po wcześniejszym spełnieniu.
* **Powiadomienie** — Akcja podejmowana na podstawie klasycznego alertu, który staje się aktywny.
* **Akcja** — określone wywołanie wysyłane do odbiorcy powiadomienia (na przykład wysyłanie pocztą e-mail adresu lub ogłaszanie na adres URL elementu webhook). Powiadomienia zazwyczaj mogą wyzwalać wiele akcji.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak mogę odebrać powiadomienie z Azure Monitor klasycznego alertu?
W przeszłości alerty platformy Azure z różnych usług używały własnych wbudowanych metod powiadomień. 

Azure Monitor utworzyć grupowanie powiadomień wielokrotnego użytku o nazwie *grupy akcji*. Grupy akcji określają zestaw odbiorników dla powiadomienia. Za każdym razem, gdy zostanie uaktywniony alert odwołujący się do grupy akcji, wszyscy odbiorcy otrzymają to powiadomienie. Grupy akcji umożliwiają ponowne użycie grupowania odbiorników (na przykład listy inżynierów połączeń) w wielu obiektach alertów. Grupy akcji obsługują powiadomienie, ogłaszając na adres URL elementu webhook, a także adresy e-mail, numery SMS i inne czynności.  Aby uzyskać więcej informacji, zobacz [grupy akcji](./action-groups.md). 

Starsze alerty klasycznego dziennika aktywności korzystają z grup akcji.

Jednak starsze alerty metryk nie korzystają z grup akcji. Zamiast tego można skonfigurować następujące akcje: 
- Wysyłaj powiadomienia e-mail do administratora usługi, współadministratorzy lub adresy e-mail określone przez użytkownika.
- Wywoływanie elementu webhook, który umożliwia uruchamianie dodatkowych akcji automatyzacji.

Elementy webhook umożliwiają automatyzację i korygowanie, na przykład przy użyciu:
- Element Runbook usługi Azure Automation
- Funkcja platformy Azure
- Aplikacja logiki platformy Azure
- usługa innej firmy

## <a name="next-steps"></a>Następne kroki
Uzyskaj informacje na temat reguł alertów i skonfiguruj je przy użyciu:

* Dowiedz się więcej o [metrykach](../data-platform.md)
* Konfigurowanie [klasycznych alertów metryk za pośrednictwem Azure Portal](alerts-classic-portal.md)
* Konfigurowanie [klasycznych alertów metryk programu PowerShell](alerts-classic-portal.md)
* Konfigurowanie [klasycznego alertu metryki interfejsu wiersza polecenia (CLI)](alerts-classic-portal.md)
* Konfigurowanie [klasycznych alertów metryk Azure monitor interfejs API REST](/rest/api/monitor/alertrules)
* Dowiedz się więcej o [dzienniku aktywności](../essentials/platform-logs-overview.md)
* Konfigurowanie [alertów dziennika aktywności za pośrednictwem Azure Portal](./activity-log-alerts.md)
* Konfigurowanie [alertów dziennika aktywności za pośrednictwem Menedżer zasobów](./alerts-activity-log.md)
* Przejrzyj [schemat elementu webhook alertu dziennika aktywności](activity-log-alerts-webhook.md)
* Dowiedz się więcej na temat [grup akcji](./action-groups.md)
* Skonfiguruj [nowsze alerty](alerts-metric.md)