---
title: Monitoruj kondycję obszaru roboczego Log Analytics w Azure Monitor
description: Opisuje sposób monitorowania kondycji obszaru roboczego Log Analytics przy użyciu danych z tabeli operacji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 09df1c2ecb94089f23a88dd36c5343bb4cf2feea
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710944"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitoruj kondycję obszaru roboczego Log Analytics w Azure Monitor
Aby zachować wydajność i dostępność obszaru roboczego Log Analytics w Azure Monitor, musisz mieć możliwość aktywnego wykrywania wszelkich powstających problemów. W tym artykule opisano sposób monitorowania kondycji obszaru roboczego Log Analytics przy użyciu danych z tabeli [operacje](/azure/azure-monitor/reference/tables/operation) . Ta tabela jest uwzględniona w każdym obszarze roboczym Log Analytics i zawiera błędy i ostrzeżenia, które występują w obszarze roboczym. Należy regularnie przeglądać te dane i tworzyć alerty w celu ich aktywnego powiadamiania o wszelkich ważnych zdarzeniach w obszarze roboczym.

## <a name="_logoperation-function"></a>Funkcja _LogOperation

Dzienniki Azure Monitor wysyłają szczegóły wszelkich problemów do tabeli [operacji](/azure/azure-monitor/reference/tables/operation) w obszarze roboczym, w którym wystąpił problem. Funkcja system **_LogOperation** jest oparta na tabeli **operacji** i zawiera uproszczony zestaw informacji na potrzeby analizy i generowania alertów.

## <a name="columns"></a>Kolumny

Funkcja **_LogOperation** zwraca kolumny w poniższej tabeli.

| Kolumna | Opis |
|:---|:---|
| TimeGenerated | Czas wystąpienia zdarzenia w formacie UTC. |
| Kategoria  | Grupa kategorii operacji. Może służyć do filtrowania typów operacji i pomaga w tworzeniu dokładniejszych inspekcji i alertów systemowych. Zapoznaj się z sekcją poniżej, aby zapoznać się z listą kategorii. |
| Operacja  | Opis typu operacji. Może to wskazywać jeden z Log Analytics limitów, typ operacji lub część procesu. |
| Poziom | Poziom ważności problemu:<br>-Info: nie jest wymagana żadna konkretna Uwaga.<br>-Warning: proces nie został ukończony zgodnie z oczekiwaniami i należy zwrócić uwagę.<br>-Error: proces nie powiódł się i konieczna jest pilna Uwaga. 
| Szczegóły | Szczegółowy opis operacji obejmuje określony komunikat o błędzie, jeśli istnieje. |
| _ResourceId | Identyfikator zasobu zasobu platformy Azure związanego z operacją.  |
| Computer (Komputer) | Nazwa komputera, jeśli operacja jest powiązana z agentem Azure Monitor. |
| CorrelationId | Używane do grupowania kolejnych powiązanych operacji. |


## <a name="categories"></a>Kategorie

W poniższej tabeli opisano kategorie z funkcji _LogOperation. 

| Kategoria | Opis |
|:---|:---|
| Pozyskiwanie           | Operacje, które są częścią procesu pozyskiwania danych. Aby uzyskać więcej informacji, zobacz poniżej. |
| Agent               | Wskazuje problem z instalacją agenta. |
| Zbieranie danych     | Operacje związane z procesami zbierania danych. |
| Określanie celu rozwiązania  | Operacja typu *ConfigurationScope* została przetworzona. |
| Rozwiązanie do oceny | Proces oceny został wykonany. |


### <a name="ingestion"></a>Pozyskiwanie
Operacje pozyskiwania to problemy, które wystąpiły podczas przyjmowania danych, w tym powiadomienia o osiągnięciu limitów obszaru roboczego Log Analytics platformy Azure. Warunki błędów w tej kategorii mogą sugerować utratę danych, więc są one szczególnie ważne do monitorowania. Poniższa tabela zawiera szczegółowe informacje dotyczące tych operacji. Zobacz [limity usługi Azure monitor](../service-limits.md#log-analytics-workspaces) dla limitów usługi dla log Analytics obszarów roboczych.


| Operacja | Poziom | Szczegóły | Powiązany artykuł |
|:---|:---|:---|:---|
| Dziennik niestandardowy | Błąd   | Osiągnięto limit kolumn pól niestandardowych. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Dziennik niestandardowy | Błąd   | Pozyskiwanie dzienników niestandardowych nie powiodło się. | |
| Metadane. | Błąd | Wykryto błąd konfiguracji. | |
| Zbieranie danych | Błąd   | Dane zostały usunięte, ponieważ żądanie zostało utworzone wcześniej niż określona liczba dni. | [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Zbieranie danych | Info    | Wykryto konfigurację maszyny kolekcji.| |
| Zbieranie danych | Info    | Zbieranie danych zostało uruchomione z powodu nowego dnia. | [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Zbieranie danych | Ostrzeżenie | Zbieranie danych zostało zatrzymane z powodu osiągnięcia dziennego limitu.| [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Przetwarzanie danych | Błąd   | Nieprawidłowy format JSON. | [Wysyłanie danych dziennika do Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)](../logs/data-collector-api.md#request-body) | 
| Przetwarzanie danych | Ostrzeżenie | Wartość została przycięta do maksymalnego dozwolonego rozmiaru. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Przetwarzanie danych | Ostrzeżenie | Osiągnięto limit rozmiaru wartości pola. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Szybkość pozyskiwania | Info | Limit szybkości pozyskiwania zbliżający się do 70%. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Szybkość pozyskiwania | Ostrzeżenie | Limit szybkości pozyskiwania zbliżający się do limitu. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Szybkość pozyskiwania | Błąd   | Osiągnięto limit szybkości. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Storage | Błąd   | Nie można uzyskać dostępu do konta magazynu, ponieważ użyte poświadczenia są nieprawidłowe.  |



   

## <a name="alert-rules"></a>Zasady alertów
[Alerty zapytań dzienników](../alerts/alerts-log-query.md) w programie Azure monitor być proaktywnie powiadamiane o wykryciu problemu w obszarze roboczym log Analytics. Należy używać strategii, która pozwala na czasowo reagować na problemy, jednocześnie minimalizując koszty. Opłata jest naliczana za każdą regułę alertu z kosztem w zależności od częstotliwości, która jest szacowana.

Zalecaną strategią jest rozpoczęcie od dwóch reguł alertów na podstawie poziomu problemu. Aby uzyskać ostrzeżenia, należy użyć krótkiej częstotliwości, takiej jak co 5 minut. Ponieważ błędy wskazują na potencjalną utratę danych, chcesz szybko odpowiedzieć na nie, aby zminimalizować wszelkie straty. Ostrzeżenia zwykle wskazują na problem, który nie wymaga natychmiastowej uwagi, aby można było przejrzeć je codziennie.

Użyj procesu w temacie [Tworzenie, wyświetlanie i zarządzanie alertami dzienników przy użyciu Azure monitor](../alerts/alerts-log.md) , aby utworzyć reguły alertów dziennika. W poniższych sekcjach opisano szczegóły dla każdej reguły.


| Zapytanie | Wartość progu | Okres | Częstotliwość |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Te reguły alertów będą odpowiadać na wszystkie operacje z błędem lub ostrzeżeniem. W miarę jak lepiej poznać operacje generujące alerty, możesz chcieć inaczej reagować na konkretne operacje. Na przykład możesz chcieć wysyłać powiadomienia do różnych osób w celu wykonywania określonych operacji. 

Aby utworzyć regułę alertu dla określonej operacji, należy użyć zapytania, które zawiera kolumny **Kategoria** i **operacja** . 

Poniższy przykład tworzy Alert ostrzegawczy, gdy współczynnik wolumenu pozyskiwania osiągnął 80% limitu.

- Obiekt docelowy: Wybierz obszar roboczy Log Analytics
- Określonych
  - Nazwa sygnału: niestandardowe wyszukiwanie w dzienniku
  - Zapytanie wyszukiwania: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Na podstawie: liczba wyników
  - Warunek: większe niż
  - Próg: 0
  - Okres: 5 (minuty)
  - Częstotliwość: 5 (minuty)
- Nazwa reguły alertu: osiągnięto dzienny limit danych
- Ważność: ostrzeżenie (ważność 1)


Poniższy przykład powoduje utworzenie alertu ostrzegawczego, gdy zbieranie danych osiągnie limit dzienny. 

- Obiekt docelowy: Wybierz obszar roboczy Log Analytics
- Określonych
  - Nazwa sygnału: niestandardowe wyszukiwanie w dzienniku
  - Zapytanie wyszukiwania: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - Na podstawie: liczba wyników
  - Warunek: większe niż
  - Próg: 0
  - Okres: 5 (minuty)
  - Częstotliwość: 5 (minuty)
- Nazwa reguły alertu: osiągnięto dzienny limit danych
- Ważność: ostrzeżenie (ważność 1)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach dzienników](../alerts/alerts-log.md).
- [Zbieraj dane inspekcji zapytania](./query-audit.md) dla obszaru roboczego.