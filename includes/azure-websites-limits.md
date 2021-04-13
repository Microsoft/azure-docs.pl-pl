---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: dad7799cb5a7579b28847e3968b6b38f1f98298a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327384"
---
| Zasób | Bezpłatna | Udostępniona | Podstawowa | Standardowa (Standard) | Premium (wersja 1–3) | Izolowana </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplikacje internetowe, mobilne lub interfejsu API](https://azure.microsoft.com/services/app-service/) na [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Bez<sup>ograniczeń 2</sup> |Bez<sup>ograniczeń 2</sup> |Bez<sup>ograniczeń 2</sup> |Bez<sup>ograniczeń 2</sup>|
| [Plan usługi App Service](../articles/app-service/overview-hosting-plans.md) |10 na region |10 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów|
| Typ wystąpienia obliczeniowego |Udostępniona |Udostępniona |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup></p> |Dedykowane<sup>3</sup>|
| [Skalowanie w](../articles/app-service/manage-scale-up.md) zewnątrz (maksymalna liczba wystąpień) |1 udostępnione |1 udostępnione |3 dedykowane<sup>3</sup> |10 dedykowanych<sup>3</sup> | 20 dedykowane dla wersji 1 i 2; 30 dedykowane dla wersji 3. <sup>3.</sup>|100 dedykowanych<sup>4</sup>|
| Magazyn<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> |1 TB<sup>5</sup> <br/><br/> Dostępny limit przydziału magazynu wynosi 999 GB. |
| Czas procesora CPU (5 minut)<sup>6</sup> |3 minuty |3 minuty |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Czas procesora CPU (dzień)<sup>6</sup> |60 minut |240 minut |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |Bez ograniczeń, płatność przy standardowych [stawkach](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Pamięć (1 godzina) |1024 MB na App Service planu |1024 MB na aplikację |NIE DOTYCZY |NIE DOTYCZY |NIE DOTYCZY |NIE DOTYCZY |
| Przepustowość |165 MB |Bez ograniczeń, [mają zastosowanie stawki transferu](https://azure.microsoft.com/pricing/details/data-transfers/) danych |Bez ograniczeń, [mają zastosowanie stawki transferu](https://azure.microsoft.com/pricing/details/data-transfers/) danych |Bez ograniczeń, [mają zastosowanie stawki transferu](https://azure.microsoft.com/pricing/details/data-transfers/) danych |Bez ograniczeń, [mają zastosowanie stawki transferu](https://azure.microsoft.com/pricing/details/data-transfers/) danych |Bez ograniczeń, [mają zastosowanie stawki transferu](https://azure.microsoft.com/pricing/details/data-transfers/) danych |
| Architektura aplikacji |32-bitowa |32-bitowa |32-bitowe/64-bitowe |32-bitowe/64-bitowe |32-bitowe/64-bitowe |32-bitowe/64-bitowe |
| Gniazda internetowe na wystąpienie<sup>7</sup> |5 |35 |350 |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
| Wychodzące połączenia IP na wystąpienie | 600 | 600 | Zależy od rozmiaru wystąpienia<sup>8</sup> | Zależy od rozmiaru wystąpienia<sup>8</sup> | Zależy od rozmiaru wystąpienia<sup>8</sup> | 16 000 |
| Równoczesne [połączenia debugera](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikację |1 |1 |1 |5 |5 |5 |
| App Service certyfikatów na subskrypcję<sup>9</sup>| Nieobsługiwane | Nieobsługiwane |10 |10 |10 |10 |
| Domeny niestandardowe na aplikację</a> |0 (azurewebsites.net tylko poddomena)|500 |500 |500 |500 |500 |
| Obsługa protokołu [SSL w domenie niestandardowej](../articles/app-service/configure-ssl-certificate.md) |Nie jest obsługiwane, certyfikat z symbolami wieloznacznymi \* dla azurewebsites.net jest domyślnie dostępny|Nie jest obsługiwane, certyfikat z symbolami wieloznacznymi \* dla azurewebsites.net jest domyślnie dostępny|Nieograniczona liczba SNI SSL sieciowych |Obejmuje SNI SSL i 1 Połączenie SSL z adresu IP połączeń |Obejmuje SNI SSL połączeń Połączenie SSL z adresu IP bez ograniczeń i 1 Połączenie SSL z adresu IP połączeń | Obejmuje SNI SSL i 1 Połączenie SSL z adresu IP połączeń|
| Połączenia hybrydowe | | | 5 na plan | 25 na plan | 200 na aplikację | 200 na aplikację |
| [Integracja sieci wirtualnej](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Prywatne punkty końcowe](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 na aplikację  |    |
| Zintegrowany moduł równoważenia obciążenia | |X |X |X |X |X<sup>10</sup> |
| [Ograniczenia dostępu](../articles/app-service/networking-features.md#access-restrictions) | 512 reguł na aplikację | 512 reguł na aplikację | 512 reguł na aplikację | 512 reguł na aplikację | 512 reguł na aplikację | 512 reguł na aplikację |
| [Zawsze wł.](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Zaplanowane kopie zapasowe](../articles/app-service/manage-backup.md) | | | | Zaplanowane kopie zapasowe co 2 godziny, maksymalnie 12 kopii zapasowych dziennie (ręcznie + zaplanowane) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręcznie i według harmonogramu) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręcznie i według harmonogramu) |
| [Automatyczne skalowanie](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitorowanie punktu końcowego](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Miejsca przejściowe na](../articles/app-service/deploy-staging-slots.md) aplikację| | | |5 |20 |20 |
| [Testowanie w środowisku produkcyjnym](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Dzienniki diagnostyczne](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Uwierzytelnianie i autoryzacja](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service zarządzane (publiczna wersja zapoznawcza)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| Umowa SLA | |  |99,95%|99,95%|99,95%|99,95%|

<sup>1 Aplikacje</sup> i limity przydziału magazynu są App Service planu, chyba że określono inaczej.

<sup>2 Rzeczywista</sup> liczba aplikacji, które można hostowania na tych maszynach, zależy od aktywności aplikacji, rozmiaru wystąpień maszyn i odpowiedniego wykorzystania zasobów.

<sup>3 Dedykowane</sup> wystąpienia mogą mieć różne rozmiary. Aby uzyskać więcej informacji, [zobacz App Service cennika.](https://azure.microsoft.com/pricing/details/app-service/)

<sup>4</sup> Kolejne są dozwolone na żądanie.

<sup>5</sup> Limit magazynu to łączny rozmiar zawartości we wszystkich aplikacjach w tym samym planie usługi App Service. Łączny rozmiar zawartości wszystkich aplikacji we wszystkich planach usługi App Service w jednej grupie zasobów i regionie nie może przekraczać 500 GB. Limit przydziału systemu plików dla App Service hostowanych aplikacji jest określany przez agregację planów App Service utworzonych w regionie i grupie zasobów.

<sup>6</sup> Te zasoby są ograniczone przez zasoby fizyczne w dedykowanych wystąpieniach (rozmiar wystąpienia i liczbę wystąpień).

<sup>7</sup> W przypadku skalowania aplikacji w warstwie Podstawowa do dwóch wystąpień masz 350 połączeń współbieżnych dla każdego z dwóch wystąpień. W przypadku gniazd sieci Web w warstwie Standardowa i wyższych nie ma żadnych teoretycznych limitów, ale inne czynniki mogą ograniczać liczbę gniazd internetowych. Na przykład maksymalna dozwolona liczba żądań współbieżnych (zdefiniowanych przez ) to: 7500 na małą maszynę wirtualną, 15 000 na średnią maszynę wirtualną `maxConcurrentRequestsPerCpu` (7500 x 2 rdzenie) i 75 000 na dużą maszynę wirtualną (18 750 x 4 rdzenie).

<sup>8</sup> Maksymalna liczba połączeń IP na wystąpienie zależy od rozmiaru wystąpienia: 1920 na wystąpienie B1/S1/P1V3, 3968 na wystąpienie B2/S2/P2V3, 8064 na wystąpienie B3/S3/P3V3.

<sup>9</sup> Limit przydziału Certyfikat usługi App Service na subskrypcję można zwiększyć za pośrednictwem żądania obsługi do maksymalnego limitu 200.

<sup>10</sup> izolowana usługa App Service SKU może być wewnętrznie z równoważeniem obciążenia (ILB) przy użyciu Azure Load Balancer, więc nie ma publicznej łączności z Internetem. W związku z tym niektóre funkcje izolowanego równoważenia obciążenia App Service muszą być używane z maszyn, które mają bezpośredni dostęp do punktu końcowego sieci ILB.

<sup>11.</sup> Uruchamiaj niestandardowe pliki wykonywalne i/lub skrypty na żądanie, zgodnie z harmonogramem lub w sposób ciągły jako zadanie w tle w App Service wystąpienia. Zawsze wł. jest wymagany do ciągłego wykonywania zadań WebJob. Nie ma wstępnie zdefiniowanego limitu liczby usług WebJob, które można uruchomić w App Service wystąpienia. Istnieją praktyczne limity, które zależą od tego, co kod aplikacji próbuje zrobić.

<sup>12</sup> Domeny Naked nie są obsługiwane. Wystawianie tylko certyfikatów standardowych (certyfikaty wieloznaczne nie są dostępne). Ograniczenie do tylko jednego bezpłatnego certyfikatu na domenę niestandardową.
