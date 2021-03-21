---
title: Alert Management rozwiązanie na platformie Azure Log Analytics | Microsoft Docs
description: Alert Management rozwiązanie w Log Analytics pomaga analizować wszystkie alerty w danym środowisku.  Oprócz konsolidacji alertów wygenerowanych w ramach Log Analytics importuje alerty z połączonych grup zarządzania System Center Operations Manager do Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 6f3761b1c933d113bf65c70370c29bcfa7950eea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728658"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Alert Management rozwiązanie na platformie Azure Log Analytics

![Ikona Alert Management](media/alert-management-solution/icon.png)

Alert Management rozwiązanie pomaga analizować wszystkie alerty w repozytorium Log Analytics.  Te alerty mogą pochodzić z różnych źródeł, w tym źródeł [utworzonych przez log Analytics](../alerts/alerts-overview.md) lub [zaimportowanych z Nagios lub Zabbix](../vm/quick-collect-linux-computer.md). Rozwiązanie importuje również alerty z dowolnych [połączonych System Center Operations Manager grup zarządzania](../agents/om-agents.md).

## <a name="prerequisites"></a>Wymagania wstępne
Rozwiązanie działa z dowolnymi rekordami w repozytorium Log Analytics z typem **alertu**, dlatego należy wykonać dowolną konfigurację wymaganą do zebrania tych rekordów.

- W przypadku alertów Log Analytics [Utwórz reguły alertów](../alerts/alerts-overview.md) , aby utworzyć rekordy alertów bezpośrednio w repozytorium.
- W przypadku alertów Nagios i Zabbix [Skonfiguruj te serwery](../vm/quick-collect-linux-computer.md) do wysyłania alertów do log Analytics.
- W przypadku alertów System Center Operations Manager [Połącz grupę zarządzania Operations Manager z obszarem roboczym log Analytics](../agents/om-agents.md).  Wszystkie alerty utworzone w System Center Operations Manager są importowane do Log Analytics.  

## <a name="configuration"></a>Konfigurowanie
Dodaj Alert Management rozwiązanie do obszaru roboczego Log Analytics przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań](../insights/solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.

## <a name="management-packs"></a>Pakiety administracyjne
Jeśli grupa zarządzania System Center Operations Manager jest połączona z obszarem roboczym Log Analytics, następujące pakiety administracyjne są instalowane w System Center Operations Manager po dodaniu tego rozwiązania.  Nie ma potrzeby konfigurowania ani konserwacji wymaganych pakietów administracyjnych.

* Microsoft System Center Advisor Alert Management (Microsoft. IntelligencePacks. AlertManagement)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../agents/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="data-collection"></a>Zbieranie danych
### <a name="agents"></a>Agenci
W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|:--- |:--- |:--- |
| [Agenci dla systemu Windows](../agents/agent-windows.md) | Nie |Bezpośredni agenci systemu Windows nie generują alertów.  Alerty Log Analytics mogą być tworzone na podstawie zdarzeń i danych wydajności zebranych z agentów systemu Windows. |
| [Agenci dla systemu Linux](../vm/quick-collect-linux-computer.md) | Nie |Agenci bezpośredniego systemu Linux nie generują alertów.  Alerty Log Analytics mogą być tworzone na podstawie zdarzeń i danych wydajności zebranych z agentów systemu Linux.  Alerty Nagios i Zabbix są zbierane z tych serwerów, które wymagają agenta systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../agents/om-agents.md) |Tak |Alerty generowane przez agentów Operations Manager są dostarczane do grupy zarządzania, a następnie przekazywane do Log Analytics.<br><br>Bezpośrednie połączenie od agentów Operations Manager do Log Analytics nie jest wymagane. Dane alertu są przekazywane z grupy zarządzania do repozytorium Log Analytics. |


### <a name="collection-frequency"></a>Częstotliwość zbierania
- Rekordy alertów są dostępne dla rozwiązania, gdy tylko są przechowywane w repozytorium.
- Dane alertu są wysyłane z grupy zarządzania Operations Manager do Log Analytics co trzy minuty.  

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu Alert Management rozwiązanie do obszaru roboczego Log Analytics kafelek **alert Management** zostanie dodany do pulpitu nawigacyjnego.  Ten kafelek przedstawia liczbę i graficzną reprezentację liczby aktywnych alertów, które zostały wygenerowane w ciągu ostatnich 24 godzin.  Tego zakresu czasu nie można zmienić.

![Kafelek Alert Management](media/alert-management-solution/tile.png)

Kliknij kafelek **alert Management** , aby otworzyć pulpit nawigacyjny **alert Management** .  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli.  Każda kolumna zawiera 10 najważniejszych alertów według liczby pasujących do kryteriów tej kolumny dla określonego zakresu i zakresu czasu.  Możesz uruchomić wyszukiwanie w dzienniku, które udostępnia całą listę, klikając pozycję **Zobacz wszystko** u dołu kolumny lub klikając nagłówek kolumny.

| Kolumna | Opis |
|:--- |:--- |
| Alerty krytyczne |Wszystkie alerty o ważności krytyczne pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić przeszukiwanie dziennika zwracające wszystkie rekordy dla tego alertu. |
| Alerty ostrzegawcze |Wszystkie alerty o ważności ostrzeżenie pogrupowane według nazwy alertu.  Kliknij nazwę alertu, aby uruchomić przeszukiwanie dziennika zwracające wszystkie rekordy dla tego alertu. |
| Alerty aktywnych System Center Operations Manager |Wszystkie alerty zebrane z Operations Manager z dowolnym stanem innym niż *zamknięte* pogrupowane według źródła, które wygenerowały alert. |
| Wszystkie aktywne alerty |Wszystkie alerty o ważności pogrupowane według nazwy alertu. Zawiera tylko alerty Operations Manager z dowolnym stanem innym niż *zamknięty*. |

W przypadku przewijania po prawej stronie Pulpit nawigacyjny zawiera kilka typowych zapytań, które można kliknąć, aby wykonać [Wyszukiwanie w dzienniku](../logs/log-query-overview.md) dla danych alertów.

![Pulpit nawigacyjny Alert Management](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Rozwiązanie Alert Management analizuje każdy rekord z typem **alertu**.  Alerty utworzone przez Log Analytics lub zebrane z Nagios lub Zabbix nie są bezpośrednio zbierane przez rozwiązanie.

Rozwiązanie wykonuje Importowanie alertów z System Center Operations Manager i tworzy odpowiadające im rekordy dla każdego typu **alertu** i SourceSystem **OpsManager**.  Te rekordy mają właściwości w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*Alert* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Szczegóły elementu danych, który spowodował wygenerowanie alertu w formacie XML. |
| `AlertDescription` |Szczegółowy opis alertu. |
| `AlertId` |Identyfikator GUID alertu. |
| `AlertName` |Nazwa alertu. |
| `AlertPriority` |Poziom priorytetu alertu. |
| `AlertSeverity` |Poziom ważności alertu. |
| `AlertState` |Najnowszy stan rozwiązywania alertu. |
| `LastModifiedBy` |Nazwa użytkownika, który ostatnio zmodyfikował alert. |
| `ManagementGroupName` |Nazwa grupy zarządzania, w której został wygenerowany alert. |
| `RepeatCount` |Liczba przypadków wygenerowania tego samego monitorowanego obiektu od momentu jego rozpoznania. |
| `ResolvedBy` |Nazwa użytkownika, który rozwiązał alert. Puste, jeśli alert nie został jeszcze rozwiązany. |
| `SourceDisplayName` |Nazwa wyświetlana obiektu monitorowania, który wygenerował alert. |
| `SourceFullName` |Pełna nazwa obiektu monitorowania, który wygenerował alert. |
| `TicketId` |Identyfikator biletu dla alertu, jeśli środowisko System Center Operations Manager jest zintegrowane z procesem przypisywania biletów dla alertów.  Pusty identyfikator biletu nie jest przypisany. |
| `TimeGenerated` |Data i godzina utworzenia alertu. |
| `TimeLastModified` |Data i godzina ostatniej zmiany alertu. |
| `TimeRaised` |Data i godzina wygenerowania alertu. |
| `TimeResolved` |Data i godzina, o której alert został rozwiązany. Puste, jeśli alert nie został jeszcze rozwiązany. |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów alertów zebranych przez to rozwiązanie: 

| Zapytanie | Opis |
|:---|:---|
| Alert &#124;, gdzie SourceSystem = = "OpsManager" i AlertSeverity = = "Error" i TimeRaised > temu (24 godziny) |Alerty krytyczne zgłoszone w ciągu ostatnich 24 godzin |
| &#124; alertu, gdzie AlertSeverity = = "Warning" i TimeRaised > temu (24 godziny) |Alerty ostrzegawcze zgłoszone w ciągu ostatnich 24 godzin |
| &#124; alertu, gdzie SourceSystem = = "OpsManager" i stan alertu! = "Closed" oraz TimeRaised > temu (24 godziny) &#124; podsumowania Count = Count () przez SourceDisplayName |Źródła z aktywnymi alertami zgłoszonymi w ciągu ostatnich 24 godzin |
| Alert &#124;, gdzie SourceSystem = = "OpsManager" i AlertSeverity = = "Error" oraz TimeRaised > temu (24 godziny) i stan alertu! = "Closed" |Alerty krytyczne zgłoszone w ciągu ostatnich 24 godzin, które są nadal aktywne |
| Alert &#124;, gdzie SourceSystem = = "OpsManager" i TimeRaised > temu (24 godziny) i stan alertu = = "zamknięte" |Alerty zgłoszone w ciągu ostatnich 24 godzin, które są teraz zamknięte |
| Alert &#124;, gdzie SourceSystem = = "OpsManager" i TimeRaised > temu (1D) &#124; podsumowania Count = Count () przez AlertSeverity |Alerty zgłoszone w ciągu ostatnich 1 dnia pogrupowane według ich ważności |
| Alert &#124;, gdzie SourceSystem = = "OpsManager" i TimeRaised > temu (1D) &#124; sortować według opisu RepeatCount |Alerty zgłoszone w ciągu ostatniej doby posortowane według wartości liczby powtórzeń |



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [alertów w usłudze Log Analytics](../alerts/alerts-overview.md), aby poznać szczegóły generowania alertów z usługi Log Analytics.