---
title: Często zadawane pytania dotyczące cen dla systemu Azure Network Performance Monitor | Microsoft Docs
description: Często zadawane pytania — Network Performance Monitor platformy Azure
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: 4b9e8a81095a63c667925f8b51d9f9f2aa081f8d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832882"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Zmiany cen dla Network Performance Monitor platformy Azure

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego w Network Performance Monitor. Można nadal korzystać z testów utworzonych przed 1 lipca 2021. Aby zminimalizować zakłócenia działania usługi dla bieżących obciążeń, należy [migrować testy z Network Performance Monitor do nowego monitora połączeń](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) na platformie Azure Network Watcher przed 29 lutego 2024.

Poznamy Twoją opinię i ostatnio wprowadzono [nowe środowisko cenowe](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) dla różnych usług monitorowania na platformie Azure. W tym artykule zawarto informacje o zmianach cen związanych z usługą Azure [Network Performance Monitor](../../networking/network-monitoring-overview.md) (npm) w łatwym do odczytu formacie pytania i odpowiedzi.

Network Performance Monitor składa się z trzech składników:
* [Monitorowanie wydajności](../../networking/network-monitoring-overview.md#performance-monitor)
* [Monitor punktu końcowego usługi](../../networking/network-monitoring-overview.md)
* [Monitor ExpressRoute](../../networking/network-monitoring-overview.md#expressroute-monitor)

W poniższych sekcjach opisano zmiany cen składników NPM.

## <a name="performance-monitor"></a>Monitorowanie wydajności

**Jak wykorzystanie monitora wydajności jest rozliczane w starym modelu?**

Naliczanie opłat za NPM było oparte na użyciu i zużyciu dwóch składników:
* **Węzły**: wszystkie transakcje syntetyczne pochodzą i kończą się w węzłach. Węzły są również określane jako agenci lub agenci zarządzania firmy Microsoft.
* **Dane**: wyniki różnych testów sieciowych są przechowywane w obszarze roboczym log Analytics.

W ramach starego modelu rachunek został obliczony na podstawie liczby węzłów i ilości wygenerowanych danych. 

**Jak jest naliczana opłata za użycie monitora wydajności w ramach nowego modelu?**

Funkcja monitora wydajności w programie NPM jest teraz rozliczana na podstawie kombinacji: 

* Monitorowane linki podsieci
* Ilość danych

**Co to jest link do podsieci?**

Monitor wydajności monitoruje łączność między dwiema lub większą liczbą lokalizacji w sieci. Połączenie między grupą węzłów lub agentów w jednej podsieci i grupą węzłów w innej podsieci nosi nazwę linku podsieci.

**Mam dwie podsieci (a i B) i mam kilku agentów w każdej podsieci. Monitor wydajności monitoruje łączność ze wszystkich agentów w podsieci A do wszystkich agentów w podsieci B. Czy zostanie naliczona opłata na podstawie liczby połączeń między podsieciami?**

Nie. W przypadku rozliczeń wszystkie połączenia z podsieci A do podsieci B są pogrupowane w jedno łącze podsieci. Opłaty są naliczane za jedno połączenie. Monitor wydajności będzie kontynuował Monitorowanie łączności między różnymi agentami w każdej podsieci.

**Jakie są koszty monitorowania linku podsieci?**

Koszt monitorowania pojedynczej podsieci w całym miesiącu znajduje się w sekcji [polecenie ping](https://azure.microsoft.com/pricing/details/network-watcher/) Grid.

**Jakie są opłaty za dane generowane przez Monitor wydajności?**

Opłata za pozyskiwanie (przekazywanie danych do Log Analytics obszaru roboczego w Azure Monitor, przetwarzania i indeksowania) jest dostępna na [stronie cennika](https://azure.microsoft.com/pricing/details/log-analytics/) dla log Analytics, w sekcji pozyskiwanie danych. Opłata za przechowywanie danych (czyli dane przechowywane w opcji klienta poza pierwszym miesiącem) jest również dostępna na [stronie cennika](https://azure.microsoft.com/pricing/details/log-analytics/)w sekcji przechowywanie danych.


## <a name="expressroute-monitor"></a>Monitor ExpressRoute

**Jakie są opłaty za użycie monitora ExpressRoute?**

Opłaty za monitor ExpressRoute są naliczane na podstawie ilości danych wygenerowanych podczas monitorowania. Aby uzyskać więcej informacji, zobacz "Jakie są opłaty za dane generowane przez Monitor wydajności?".

**Używam monitora ExpressRoute do monitorowania wielu obwodów usługi ExpressRoute. Czy opłaty są naliczane na podstawie liczby monitorowanych obwodów?**

Opłata nie jest naliczana na podstawie liczby obwodów lub typu komunikacji równorzędnej (na przykład prywatnej komunikacji równorzędnej, komunikacji równorzędnej firmy Microsoft). Opłata jest naliczana na podstawie ilości danych, jak wyjaśniono wcześniej.

**Jaka jest ilość danych generowanych podczas ExpressRoute monitorowania pojedynczego obwodu?**

Ilość wygenerowanych danych miesięcznie, gdy ExpressRoute monitoruje połączenie prywatne komunikacji równorzędnej:

|Percentyl      |Dane/miesiąc (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Zgodnie z tą tabelą Klienci z 50. percentyla są obciążani za 192 MB danych. W cenie USD 2.30/GB za pierwszy miesiąc koszt związany z monitorowaniem obwodu to $0,43 USD (= 192 * 2,30/1024).

**Jakie są przyczyny różnic w ilości danych?**

Ilość wygenerowanych danych monitorowania zależy od kilku czynników, takich jak:
* Liczba agentów. Dokładność izolacji błędów zwiększa się wraz ze wzrostem liczby agentów.
* Liczba przeskoków w sieci.
* Liczba ścieżek między źródłem a miejscem docelowym.

Klienci o wyższych percentylach (w powyższej tabeli) zwykle monitorują swoje obwody z kilku punktów Vantage w sieci lokalnej. Wiele agentów jest również umieszczonych w sieci, w dalszej odległości od routera brzegowego dostawcy usług. Agenci są często umieszczani w kilku lokacjach użytkowników, gałęziach i stojakach w centrach danych.

## <a name="service-endpoint-monitor"></a>Monitor punktu końcowego usługi

**Jakie opłaty są naliczane za użycie monitora punktu końcowego usługi?**

Opłaty za użycie monitora punktu końcowego usługi są obliczane na podstawie:
* Liczba połączeń
* Ilość danych

**Co to jest połączenie?**

Połączenie jest testem osiągalności do jednego punktu końcowego (URL lub usługi sieciowej) z jednego agenta przez cały miesiąc. Na przykład monitorowanie połączenia z bing.com od trzech agentów stanowi trzy połączenia.

**Jakie są koszty monitora punktu końcowego usługi?**

Zapoznaj się z sekcją [monitorowanie połączeń](https://azure.microsoft.com/pricing/details/network-watcher/) , aby uzyskać koszt monitorowania punktu końcowego przez cały miesiąc. Opłaty za dane są dostępne na [stronie cennika](https://azure.microsoft.com/pricing/details/log-analytics/) log Analytics w sekcji pozyskiwanie danych.

## <a name="references"></a>Odwołania

[Log Analytics — często zadawane pytania](https://azure.microsoft.com/pricing/details/log-analytics/): sekcja często zadawanych pytań zawiera informacje o cenach za węzeł warstwy Bezpłatna i innych.
