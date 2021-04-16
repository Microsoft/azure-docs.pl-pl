---
title: Monitorowanie kondycji obszaru roboczego usługi Log Analytics w Azure Monitor
description: Opisuje sposób monitorowania kondycji obszaru roboczego usługi Log Analytics przy użyciu danych w tabeli Operacja.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538854"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorowanie kondycji obszaru roboczego usługi Log Analytics w Azure Monitor
Aby zachować wydajność i dostępność obszaru roboczego usługi Log Analytics na Azure Monitor, musisz mieć możliwość proaktywnego wykrywania wszelkich problemów, które się pojawią. W tym artykule opisano sposób monitorowania kondycji obszaru roboczego usługi Log Analytics przy użyciu danych z [tabeli Operacja.](/azure/azure-monitor/reference/tables/operation) Ta tabela jest zawarta w każdym obszarze roboczym usługi Log Analytics i zawiera błędy i ostrzeżenia występujące w obszarze roboczym. Należy regularnie przeglądać te dane i tworzyć alerty, aby proaktywnie powiadamiać o wszelkich ważnych zdarzeniach w obszarze roboczym.

## <a name="_logoperation-function"></a>_LogOperation, funkcja

Azure Monitor dzienniki wysyłają szczegółowe informacje o wszelkich problemach do tabeli [Operacja](/azure/azure-monitor/reference/tables/operation) w obszarze roboczym, w którym wystąpił problem. Funkcja **_LogOperation** jest oparta na tabeli **Operation** i udostępnia uproszczony zestaw informacji na temat analizy i alertów.

## <a name="columns"></a>Kolumny

Funkcja **_LogOperation** zwraca kolumny z poniższej tabeli.

| Kolumna | Opis |
|:---|:---|
| TimeGenerated | Czas zdarzenia w czasie UTC. |
| Kategoria  | Grupa kategorii operacji. Może służyć do filtrowania typów operacji i tworzenia bardziej precyzyjnej inspekcji systemu i alertów. Lista kategorii znajduje się w poniższej sekcji. |
| Operacja  | Opis typu operacji. Może to oznaczać jeden z limitów usługi Log Analytics, typ operacji lub część procesu. |
| Poziom | Poziom ważności problemu:<br>- Informacje: Nie jest potrzebna żadna konkcyjna uwaga.<br>— Ostrzeżenie: Proces nie został ukończony zgodnie z oczekiwaniami i potrzebna jest uwaga.<br>— Błąd: Proces nie powiódł się i potrzebna jest pilna uwaga. 
| Szczegóły | Szczegółowy opis operacji zawiera określony komunikat o błędzie, jeśli istnieje. |
| _ResourceId | Identyfikator zasobu platformy Azure powiązany z operacją.  |
| Computer (Komputer) | Nazwa komputera, jeśli operacja jest powiązana z Azure Monitor agenta. |
| CorrelationId | Służy do grupowania kolejnych powiązanych operacji. |


## <a name="categories"></a>Kategorie

W poniższej tabeli opisano kategorie z _LogOperation funkcji. 

| Kategoria | Opis |
|:---|:---|
| Pozyskiwanie           | Operacje, które są częścią procesu pozyskiwania danych. Aby uzyskać więcej informacji, zobacz poniżej. |
| Agent               | Wskazuje problem z instalacją agenta. |
| Zbieranie danych     | Operacje związane z procesami zbierania danych. |
| Kierowanie rozwiązań  | Operacja typu *ConfigurationScope* została przetworzona. |
| Rozwiązanie do oceny | Wykonano proces oceny. |


### <a name="ingestion"></a>Pozyskiwanie
Operacje pozyskiwania to problemy, które wystąpiły podczas pozyskiwania danych, w tym powiadomienia o osiągnięciu limitów obszaru roboczego usługi Azure Log Analytics. Warunki błędów w tej kategorii mogą sugerować utratę danych, dlatego są szczególnie ważne do monitorowania. W poniższej tabeli przedstawiono szczegółowe informacje na temat tych operacji. Zobacz [Azure Monitor limity usług dla](../service-limits.md#log-analytics-workspaces) obszarów roboczych usługi Log Analytics.


| Operacja | Poziom | Szczegóły | Powiązany artykuł |
|:---|:---|:---|:---|
| Dziennik niestandardowy | Błąd   | Osiągnięto limit kolumn pól niestandardowych. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Dziennik niestandardowy | Błąd   | Pozyskiwanie dzienników niestandardowych nie powiodło się. | |
| Metadanych. | Błąd | Wykryto błąd konfiguracji. | |
| Zbieranie danych | Błąd   | Dane zostały porzucone, ponieważ żądanie zostało utworzone wcześniej niż liczba ustawionych dni. | [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Zbieranie danych | Info    | Wykryto konfigurację maszyny kolekcji.| |
| Zbieranie danych | Info    | Zbieranie danych rozpoczęło się od nowego dnia. | [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Zbieranie danych | Ostrzeżenie | Zbieranie danych zostało zatrzymane z powodu osiągnięto dzienny limit.| [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Przetwarzanie danych | Błąd   | Nieprawidłowy format JSON. | [Wysyłanie danych dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)](../logs/data-collector-api.md#request-body) | 
| Przetwarzanie danych | Ostrzeżenie | Wartość została przycięty do maksymalnego dozwolonego rozmiaru. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Przetwarzanie danych | Ostrzeżenie | Wartość pola przycinana po osiągnięciu limitu rozmiaru. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Szybkość pozyskiwania | Info | Limit szybkości pozyskiwania zbliża się do 70%. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Szybkość pozyskiwania | Ostrzeżenie | Limit szybkości pozyskiwania zbliża się do tego limitu. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Szybkość pozyskiwania | Błąd   | Osiągnięto limit szybkości. | [Limity usługi Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Storage | Błąd   | Nie można uzyskać dostępu do konta magazynu, ponieważ użyte poświadczenia są nieprawidłowe.  |



   

## <a name="alert-rules"></a>Zasady alertów
Alerty [zapytań dzienników w](../alerts/alerts-log-query.md) Azure Monitor, aby proaktywnie powiadamiać o wykryciu problemu w obszarze roboczym usługi Log Analytics. Należy użyć strategii, która pozwala na terminowe reagowanie na problemy przy jednoczesnym zminimalizowaniu kosztów. Opłata za subskrypcję jest naliczana za każdą regułę alertu, a koszt zależy od częstotliwości jej oceny.

Zalecaną strategią jest rozpoczęcie od dwóch reguł alertów w zależności od poziomu problemu. Użyj krótkiej częstotliwości, takiej jak co 5 minut, w przypadku błędów i dłuższej częstotliwości, takiej jak 24 godziny, w przypadku ostrzeżeń. Ponieważ błędy wskazują na potencjalną utratę danych, chcesz szybko zareagować na nie, aby zminimalizować wszelkie straty. Ostrzeżenia zwykle wskazują na problem, który nie wymaga natychmiastowej uwagi, dlatego można je przeglądać codziennie.

Użyj procesu z procedury [Tworzenie i wyświetlanie alertów dzienników](../alerts/alerts-log.md) oraz zarządzanie nimi przy użyciu Azure Monitor, aby utworzyć reguły alertów dzienników. W poniższych sekcjach opisano szczegóły każdej reguły.


| Zapytanie | Wartość progu | Okres | Częstotliwość |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Te reguły alertów będą odpowiadać tak samo dla wszystkich operacji z błędem lub ostrzeżeniem. W przypadku coraz większej liczby operacji generujących alerty możesz chcieć reagować inaczej na konkretne operacje. Na przykład możesz chcieć wysłać powiadomienia do różnych osób dotyczące konkretnych operacji. 

Aby utworzyć regułę alertu dla określonej operacji, użyj zapytania, które zawiera **kolumny Category** i **Operation.** 

Poniższy przykład tworzy alert ostrzegawczy, gdy szybkość pozyskiwania woluminu osiągnie 80% limitu.

- Cel: wybierz obszar roboczy usługi Log Analytics
- Kryteria:
  - Nazwa sygnału: Niestandardowe przeszukiwanie dzienników
  - Zapytanie wyszukiwania: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Na podstawie: Liczba wyników
  - Warunek: Większe niż
  - Próg: 0
  - Okres: 5 (min)
  - Częstotliwość: 5 (min)
- Nazwa reguły alertu: Osiągnięto dzienny limit danych
- Ważność: Ostrzeżenie (ważność 1)


Poniższy przykład tworzy alert ostrzegawczy, gdy zbieranie danych osiągnie dzienny limit. 

- Cel: wybierz obszar roboczy usługi Log Analytics
- Kryteria:
  - Nazwa sygnału: Niestandardowe przeszukiwanie dzienników
  - Zapytanie wyszukiwania: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Na podstawie: Liczba wyników
  - Warunek: Większe niż
  - Próg: 0
  - Okres: 5 (min)
  - Częstotliwość: 5 (min)
- Nazwa reguły alertu: Osiągnięto dzienny limit danych
- Ważność: Ostrzeżenie (ważność 1)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach dzienników.](../alerts/alerts-log.md)
- [Zbieranie danych inspekcji zapytań](./query-audit.md) dla obszaru roboczego.
