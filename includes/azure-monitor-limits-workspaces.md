---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: e6b64b5a1a60ba3bbf93e607536eeb0379669c73
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91644708"
---
**Wolumin zbierania danych i ich przechowywanie** 

| Warstwa | Limit dziennie | Przechowywanie danych | Komentarz |
|:---|:---|:---|:---|
| Bieżąca warstwa cenowa za GB<br>(wprowadzono 2018 kwietnia) | Bez ograniczeń | 30-730 dni | Przechowywanie danych przez dłużej niż 31 dni jest dostępne w przypadku dodatkowych opłat. Dowiedz się więcej o cenach Azure Monitor. |
| Starsze warstwy bezpłatne<br>(wprowadzono 2016 kwietnia) | 500 MB | 7 dni | Gdy obszar roboczy osiągnie limit 500 MB dziennie, pozyskiwanie danych zostaje zatrzymane i wznowione na początku następnego dnia. Dzień jest oparty na czasie UTC. Należy pamiętać, że dane zbierane przez Azure Security Center nie są uwzględniane w tym limicie 500 MB na dzień i będą nadal zbierane powyżej tego limitu.  |
| Starsza wersja autonomiczna na warstwę GB<br>(wprowadzono 2016 kwietnia) | Bez ograniczeń | od 30 do 730 dni | Przechowywanie danych przez dłużej niż 31 dni jest dostępne w przypadku dodatkowych opłat. Dowiedz się więcej o cenach Azure Monitor. |
| Starsza wersja na węzeł (OMS)<br>(wprowadzono 2016 kwietnia) | Bez ograniczeń | od 30 do 730 dni | Przechowywanie danych przez dłużej niż 31 dni jest dostępne w przypadku dodatkowych opłat. Dowiedz się więcej o cenach Azure Monitor. |
| Starsza warstwa standardowa | Bez ograniczeń | 30 dni  | Nie można dostosować przechowywania |
| Starsza warstwa Premium | Bez ograniczeń | 365 dni  | Nie można dostosować przechowywania |

**Liczba obszarów roboczych na subskrypcję.**

| Warstwa cenowa    | Limit obszaru roboczego | Komentarze
|:---|:---|:---|
| Warstwa Bezpłatna  | 10 | Nie można zwiększyć tego limitu. |
| Wszystkie inne warstwy | Bez ograniczeń | Użytkownik ma ograniczoną liczbę zasobów w grupie zasobów oraz liczbę grup zasobów na subskrypcję. |

**Witryna Azure Portal**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwracanych przez zapytanie dziennika | 10 000 | Zmniejsz wyniki przy użyciu zakresu zapytania, zakresu czasu i filtrów w zapytaniu. |


**Interfejs API modułu zbierającego dane**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalny rozmiar pojedynczego wpisu | 30 MB | Podziel większe woluminy na wiele wpisów. |
| Maksymalny rozmiar wartości pól  | 32 KB | Pola dłuższe niż 32 KB są obcinane. |

**Interfejs API wyszukiwania**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwrócona w pojedynczym zapytaniu | 500 000 | |
| Maksymalny rozmiar zwracanych danych | 64 000 000 bajtów (~ 61 MiB)| |
| Maksymalny czas działania zapytania | 10 minut | Aby uzyskać szczegółowe informacje, zobacz [limity czasu](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Maksymalna liczba żądań | 200 żądań w ciągu 30 sekund na użytkownika lub adres IP klienta usługi Azure AD | Aby uzyskać szczegółowe informacje, zobacz [limity szybkości](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Ogólne limity obszaru roboczego**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba kolumn w tabeli         | 500 | |
| Maksymalna liczba znaków w nazwie kolumny | 500 | |
| Eksportowanie danych | Obecnie niedostępne | Agreguj i Eksportuj dane przy użyciu funkcji platformy Azure lub aplikacji logiki. | 

**<a name="data-ingestion-volume-rate">Współczynnik ilości woluminu pozyskiwania danych</a>**

Azure Monitor to usługa danych o dużej skali, która umożliwia tysiącom klientów wysyłanie terabajtów danych co miesiąc w coraz większej tempie. Limit liczby woluminów umożliwia odizolowanie Azure Monitor klientów od nagłych wzrostów pozyskiwania w środowisku wielodostępnym. Domyślny próg współczynnika wolumenu pozyskiwania wynoszący 500 MB (skompresowany) jest zdefiniowany w obszarze roboczym, co jest tłumaczone na około **6 GB/min** nieskompresowane — rzeczywisty rozmiar może się różnić między typami danych, zależnie od długości dziennika i jego stosunku kompresji. Limit liczby woluminów dotyczy wszystkich danych pobieranych, niezależnie od tego, czy są wysyłane z zasobów platformy Azure przy użyciu [ustawień diagnostycznych](../articles/azure-monitor/platform/diagnostic-settings.md), [interfejsu API modułu zbierającego dane](../articles/azure-monitor/platform/data-collector-api.md) czy agentów.

W przypadku wysyłania danych do obszaru roboczego o współczynniku ilościowym wyższym niż 80% wartości progowej skonfigurowanej w obszarze roboczym, zdarzenie jest wysyłane do tabeli *operacji* w obszarze roboczym co 6 godzin, podczas gdy próg nadal zostanie przekroczony. Gdy ilość pozyskiwanych woluminów jest wyższa niż wartość progowa, niektóre dane są porzucane, a zdarzenie jest wysyłane do tabeli *operacji* w obszarze roboczym co 6 godzin, podczas gdy próg nadal zostanie przekroczony. W przypadku przekroczenia progu przez okres pozyskiwania lub oczekujesz, że zostanie on wkrótce osiągnięty, możesz poprosić o zwiększenie go, otwierając żądanie pomocy technicznej. 

Aby otrzymywać powiadomienia o zbliżaniu się lub osiągnięciu limitu ilości woluminu pozyskiwania w obszarze roboczym, należy utworzyć [regułę alertu dziennika](../articles/azure-monitor/platform/alerts-log.md) przy użyciu następującego zapytania z podstawą logiki alertu na liczbie wyników większym niż zero, okres próbny wynoszący 5 minut i częstotliwość 5 minut.

Współczynnik wolumenu pozyskiwania przekroczył próg
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Error"
```

Współczynnik objętości pozyskiwania przekraczający 80% wartości progowej
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Warning"
```

Współczynnik objętości pozyskiwania przekraczający 70% wartości progowej
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Info"
```

>[!NOTE]
>W zależności od tego, jak długo korzystasz z Log Analytics, możesz mieć dostęp do starszych warstw cenowych. Dowiedz się więcej na temat [log Analytics starszych warstw cenowych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
