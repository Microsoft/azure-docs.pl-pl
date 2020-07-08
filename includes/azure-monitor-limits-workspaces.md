---
title: dołączanie pliku
description: dołączanie pliku
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 864b37c9e59786546ad2c29faf8457cfc3a21f6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82161164"
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

**Azure Portal**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwracanych przez zapytanie dziennika | 10 000 | Zmniejsz wyniki przy użyciu zakresu zapytania, zakresu czasu i filtrów w zapytaniu. |


**Interfejs API modułu zbierającego dane**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalny rozmiar pojedynczego wpisu | 30 MB | Podziel większe woluminy na wiele wpisów. |
| Maksymalny rozmiar wartości pól  | 32 KB | Pola dłuższe niż 32 KB są obcinane. |

**Wyszukaj interfejs API**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba rekordów zwrócona w pojedynczym zapytaniu | 500 000 | |
| Maksymalny rozmiar zwracanych danych | 64 000 000 bajtów (~ 61 MiB)| |
| Maksymalny czas działania zapytania | 10 minut | Aby uzyskać szczegółowe informacje, zobacz [limity czasu](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Maksymalna liczba żądań | 200 żądań w ciągu 30 sekund na użytkownika usługi AAD lub adres IP klienta | Aby uzyskać szczegółowe informacje, zobacz [limity szybkości](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Ogólne limity obszaru roboczego**

| Kategoria | Limit | Komentarze |
|:---|:---|:---|
| Maksymalna liczba kolumn w tabeli         | 500 | |
| Maksymalna liczba znaków w nazwie kolumny | 500 | |
| Eksportowanie danych | Obecnie niedostępne | Agreguj i Eksportuj dane przy użyciu funkcji platformy Azure lub aplikacji logiki. | 

**Współczynnik ilości woluminu pozyskiwania danych**


Azure Monitor to usługa danych o dużej skali, która umożliwia tysiącom klientów wysyłanie terabajtów danych co miesiąc w coraz większej tempie. Domyślny limit ilości woluminu pozyskiwania danych wysyłanych z zasobów platformy Azure przy użyciu [ustawień diagnostycznych](../articles/azure-monitor/platform/diagnostic-settings.md) to około **6 GB/min** na obszar roboczy. Jest to przybliżona wartość, ponieważ rzeczywisty rozmiar może się różnić między typami danych w zależności od długości dziennika i jego stosunku kompresji. Ten limit nie dotyczy danych wysyłanych z agentów lub [interfejsu API modułu zbierającego dane](../articles/azure-monitor/platform/data-collector-api.md).

W przypadku wysyłania danych o wyższej stawce do jednego obszaru roboczego niektóre dane zostaną usunięte, a zdarzenie jest wysyłane do tabeli *operacji* w obszarze roboczym co 6 godzin, podczas gdy próg zostanie przekroczony. Jeśli wolumin pozyskiwania w dalszym ciągu przekroczy limit szybkości lub oczekujesz, że wkrótce dojdziesz do niego, możesz poprosić o zwiększenie do obszaru roboczego, wysyłając wiadomość e-mail LAIngestionRate@microsoft.com lub otwierając żądanie pomocy technicznej.
 
Aby otrzymywać powiadomienia o takim zdarzeniu w Twoim obszarze roboczym, należy utworzyć [regułę alertu dziennika](../articles/azure-monitor/platform/alerts-log.md) przy użyciu następującego zapytania z podstawą logiki alertu na liczbie wyników, które nie są równe zeru.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>W zależności od tego, jak długo korzystasz z Log Analytics, możesz mieć dostęp do starszych warstw cenowych. Dowiedz się więcej na temat [log Analytics starszych warstw cenowych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
