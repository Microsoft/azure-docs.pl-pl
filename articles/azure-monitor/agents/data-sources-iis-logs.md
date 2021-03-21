---
title: Zbierz dzienniki usług IIS z agentem Log Analytics w programie Azure Monitor
description: Internet Information Services (IIS) przechowuje aktywność użytkownika w plikach dziennika, które mogą być zbierane przez Azure Monitor.  W tym artykule opisano sposób konfigurowania kolekcji dzienników usług IIS i szczegółów dotyczących rekordów tworzonych w Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 87cb19daa23c9fcca601771a9fe168b98be02627
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586271"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Zbierz dzienniki usług IIS z agentem Log Analytics w programie Azure Monitor
Internet Information Services (IIS) przechowuje aktywność użytkownika w plikach dziennika, które mogą być zbierane przez agenta Log Analytics i przechowywane w [dziennikach Azure monitor](../data-platform.md).

> [!IMPORTANT]
> W tym artykule opisano zbieranie dzienników usług IIS za pomocą [agenta log Analytics](./log-analytics-agent.md) , który jest jednym z agentów używanych przez Azure monitor. Inni Agenci zbierają różne dane i są skonfigurowani inaczej. Zobacz [Omówienie agentów Azure monitor](../agents/agents-overview.md) , aby uzyskać listę dostępnych agentów oraz dane, które mogą zbierać.

![Dzienniki usług IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurowanie dzienników usług IIS
Azure Monitor zbiera wpisy z plików dziennika utworzonych przez usługi IIS, dlatego należy [skonfigurować usługi IIS do rejestrowania](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

Usługa Azure Monitor obsługuje tylko pliki dzienników usług IIS przechowywane w formacie W3C i nie obsługuje pól niestandardowych ani funkcji Zaawansowane rejestrowanie usług IIS. Nie zbiera dzienników w formacie NCSA ani w formacie macierzystym IIS.

Skonfiguruj dzienniki usług IIS w Azure Monitor z [menu konfiguracji agenta](../agents/agent-data-sources.md#configuring-data-sources) dla agenta log Analytics.  Nie jest wymagana żadna konfiguracja, a nie opcja **zbierania plików dziennika usług IIS w formacie W3C**.


## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor zbiera wpisy dzienników usług IIS z każdego agenta przy każdej zmianie znacznika czasu dziennika. Dziennik jest odczytywany co **5 minut**. Jeśli z jakiegokolwiek powodu usługi IIS nie zaktualizują sygnatury czasowej przed upływem czasu przerzucenia podczas tworzenia nowego pliku, zostaną zebrane wpisy po utworzeniu nowego pliku. Częstotliwość tworzenia nowych plików jest kontrolowana przez ustawienie **harmonogramu przerzucania pliku dziennika** dla witryny IIS, która jest domyślnie raz dziennie. Jeśli ustawienie jest **co godzinę**, Azure monitor zbiera dziennik co godzinę. Jeśli to ustawienie ma wartość **codziennie**, Azure monitor zbiera dziennik co 24 godziny.

> [!IMPORTANT]
> Zaleca się skonfigurowanie **harmonogramu przerzucania pliku dziennika** na **co godzinę**. Jeśli jest ustawiona na **codzienne**, można napotkać w danych, że dane będą zbierane tylko raz dziennie.

## <a name="iis-log-record-properties"></a>Właściwości rekordu dziennika usług IIS
Rekordy dziennika usług IIS mają typ **W3CIISLog** i mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, z którego został zebrany zdarzenie. |
| Opcja |Adres IP klienta. |
| csMethod |Metoda żądania, taka jak GET lub POST. |
| csReferer |Lokacja, z której korzysta użytkownik z linkiem do bieżącej lokacji. |
| csUserAgent |Typ przeglądarki klienta. |
| csUserName |Nazwa uwierzytelnionego użytkownika, który uzyskał dostęp do serwera. Użytkownicy anonimowi są wskazywani przez łącznik. |
| csUriStem |Miejsce docelowe żądania, takie jak strona sieci Web. |
| csUriQuery |Zapytanie (jeśli istnieje), które klient próbował wykonać. |
| ManagementGroupName |Nazwa grupy zarządzania dla agentów Operations Manager.  W przypadku innych agentów jest to AOI-\<workspace ID\> |
| RemoteIPCountry |Kraj/region adresu IP klienta. |
| RemoteIPLatitude |Szerokość geograficzna adresu IP klienta. |
| RemoteIPLongitude |Długość adresu IP klienta. |
| scStatus |Kod stanu HTTP. |
| scSubStatus |Kod błędu podstanu. |
| scWin32Status |Kod stanu systemu Windows. |
| kabl |Adres IP serwera sieci Web. |
| SourceSystem |Programu Operations Manager |
| sPort |Port na serwerze, z którym jest połączony klient. |
| sSiteName |Nazwa witryny usług IIS. |
| TimeGenerated |Data i godzina zarejestrowania wpisu. |
| TimeTaken |Długość czasu przetwarzania żądania w milisekundach. |
| csHost | Nazwa hosta. |
| csBytes | Liczba bajtów odebranych przez serwer. |

## <a name="log-queries-with-iis-logs"></a>Rejestruj zapytania z dziennikami usług IIS
W poniższej tabeli przedstawiono różne przykłady zapytań dzienników, które pobierają rekordy dziennika usług IIS.

| Zapytanie | Opis |
|:--- |:--- |
| W3CIISLog |Wszystkie rekordy dziennika usług IIS. |
| W3CIISLog &#124;, gdzie scStatus = = 500 |Wszystkie rekordy dziennika IIS ze stanem powrotu 500. |
| W3CIISLog &#124; Sumuj liczbę () według przelewu |Liczba wpisów dziennika IIS według adresu IP klienta. |
| W3CIISLog &#124;, gdzie csHost = = "www \. contoso.com" &#124; Sumuj liczbę () przez csUriStem |Liczba wpisów dziennika IIS wg adresu URL dla hosta contoso.com www \. . |
| &#124; W3CIISLog podsumowania sum (csBytes) przez komputer &#124; Take 500000 |Łączna liczba bajtów odebranych przez każdy komputer IIS. |

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj Azure Monitor, aby zbierać inne [źródła danych](../agents/agent-data-sources.md) na potrzeby analizy.
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.
