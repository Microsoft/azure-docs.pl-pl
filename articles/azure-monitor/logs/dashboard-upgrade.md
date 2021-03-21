---
title: Uaktualnianie wizualizacji pulpitu nawigacyjnego Log Analytics
description: Dowiedz się, jak uaktualnić wizualizacje pulpitu nawigacyjnego Log Analytics przy użyciu zapytań, które mogą zapewniać zaawansowane informacje.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030940"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Uaktualnianie wizualizacji pulpitu nawigacyjnego Log Analytics

W lutym 2020 wprowadziliśmy ulepszoną technologię wizualizacji. Poprawianie i ulepszanie możliwości wizualizacji wyników zapytania oraz szybkiego uzyskiwania szczegółowych informacji. 

Więcej informacji na temat tego uaktualnienia można znaleźć w tej [aktualizacji platformy Azure](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Ta nowa technologia wizualizacji koncentruje się na sposobie tworzenia nowych i ulepszonych środowisk, w których znajduje się zestaw wyników zapytania. 

## <a name="dashboards-in-azure"></a>Pulpity nawigacyjne na platformie Azure

Pulpity nawigacyjne platformy Azure umożliwiają wizualizowanie stanu całego obszaru powierzchni platformy Azure. Są one przeznaczone do zapewnienia, że jedno z okienek szklanych jest ze statusem platformy Azure i ma wiele skrótów do typowych działań. 

Aby uzyskać więcej informacji, zobacz [pulpity nawigacyjne platformy Azure](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>Uaktualnianie części pulpitu nawigacyjnego Log Analytics

Nowa technologia wizualizacji rozwiązuje niektóre typowe problemy ze starą implementacją i wprowadza nowe funkcje przypięte Log Analytics: 

- **Te same dostępne typy** — wszystkie typy wizualizacji dostępne w log Analytics są dostępne jako przypięte części na pulpitach nawigacyjnych.

- **Spójny wygląd i działanie** — Wizualizacja i działania dla przypiętych części jest teraz niemal identyczna z tymi w log Analytics. Różnice są spowodowane optymalizacji, które wymagają delikatnych różnic w zawartości danych wizualizacji. Zobacz część uwagi tego dokumentu, aby uzyskać więcej informacji na temat tych różnic.

- Etykietki **narzędzi i etykiety** — nowe przypięte wizualizacje log Analytics obsługują etykietki narzędzi. Wykresy kołowe i pierścieniowe obsługują teraz etykiety.

- **Interaktywne legendy** — kliknięcie legendy wizualizacji pozwala dodawać i usuwać wymiary z przypiętej wizualizacji jak w log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Etap 1 — monit dotyczący uaktualnienia

Po uaktualnieniu Log Analyticsej przypiętej części do Log Analytics przypiętych części na pulpitach nawigacyjnych są *wyświetlane nowe powiadomienia o* zaakceptowaniu. Jeśli chcesz, aby nowe wizualizacje zostały uaktualnione w celu uaktualnienia wybranych wizualizacji na pulpicie nawigacyjnym.

 
![—](media/dashboard-upgrade/update-message-1.png)
 
![Zrzut ekranu pokazujący sposób aktualizowania wizualizacji kafelka.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Po opublikowaniu pulpitu nawigacyjnego uaktualnienie jest nieodwracalne. Jednak zmiany są odrzucane, jeśli przejdziesz do pulpitu nawigacyjnego bez ponownego publikowania.  

Po kliknięciu Wizualizacja zostanie zaktualizowana do nowej technologii. Delikatne różnice w wizualizacji mogą wystąpić, aby dostosować się do ich wyglądu i działania w Log Analytics.

Po uaktualnieniu wizualizacji należy ponownie opublikować pulpit nawigacyjny, aby zmiany zaczęły obowiązywać.

![Zrzut ekranu przedstawiający uaktualnione wizualizacje.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Etap 2 — Migracja wszystkich pulpitów nawigacyjnych

Gdy początkowy okres obowiązywania jest dłuższy, zespół Log Analytics uaktualni wszystkie pulpity nawigacyjne w systemie. Wyrównanie wszystkich pulpitów nawigacyjnych platformy Azure umożliwia zespołowi wprowadzanie większej liczby wizualizacji i ulepszeń w całej tablicy.

## <a name="considerations"></a>Zagadnienia do rozważenia

Wizualizacje Log Analytics przypięte do pulpitu nawigacyjnego mają konkretne zachowanie, które zostały zaprojektowane w celu zapewnienia optymalnego środowiska. Po przypięciu wizualizacji do pulpitu nawigacyjnego Przejrzyj następujące zagadnienia dotyczące projektowania.

### <a name="query-time-scope---30-day-limit"></a>Zakres czasu zapytania — 30-dniowy limit

Pulpity nawigacyjne mogą zawierać wiele wizualizacji z wielu zapytań, dlatego zakres czasu dla pojedynczego przypiętego zapytania jest ograniczony do 30 dni. Pojedyncze zapytanie może być uruchamiane tylko w przedziale czasu mniejszym lub równym 30 dni. To ograniczenie ma na celu zapewnienie rozsądnego czasu ładowania pulpitu nawigacyjnego.

### <a name="query-data-values---25-values-and-other-grouping"></a>Zapytanie wartości danych — 25 wartości i inne grupowanie

Pulpity nawigacyjne mogą być wizualnie gęste i złożone. Aby zmniejszyć obciążenie poznawcze podczas wyświetlania pulpitu nawigacyjnego, optymalizujemy wizualizacje poprzez ograniczenie wyświetlania do 25 różnych typów danych. Jeśli jest więcej niż 25, Log Analytics optymalizuje dane. Każdy z nich osobno pokazuje 25 typów z większością danych, a następnie grupuje pozostałe wartości do wartości "inne". Na poniższym wykresie przedstawiono takie przypadki.  

![Zrzut ekranu przedstawiający pulpit nawigacyjny z 25 różnymi typami danych.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Odświeżanie pulpitu nawigacyjnego przy ładowaniu

Pulpity nawigacyjne są odświeżane po załadowaniu. Wszystkie zapytania związane z pulpitem nawigacyjnym, które są przypięte Log Analytics wizualizacje są wykonywane, a pulpit nawigacyjny jest odświeżany po załadowaniu. Jeśli strona pulpitu nawigacyjnego pozostanie otwarta, dane na pulpicie nawigacyjnym są odświeżane co 60 minut.

## <a name="next-steps"></a>Następne kroki

[Tworzenie i udostępnianie pulpitów nawigacyjnych w Log Analytics](../visualize/tutorial-logs-dashboards.md)