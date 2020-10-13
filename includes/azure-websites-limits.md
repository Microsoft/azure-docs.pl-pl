---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 557e20dcafabb2db0bc66c9dfda4977aca50de50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91745710"
---
| Zasób | Bezpłatna | Shared | Podstawowa | Standardowa (Standard) | Premium (V3) | Izolowana </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplikacje internetowe, mobilne lub API](https://azure.microsoft.com/services/app-service/) na [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Bez ograniczeń<sup>2</sup> |Bez ograniczeń<sup>2</sup> |Bez ograniczeń<sup>2</sup> |Bez ograniczeń<sup>2</sup>|
| [Plan App Service](../articles/app-service/overview-hosting-plans.md) |10 na region |10 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów|
| Typ wystąpienia obliczeniowego |Shared |Shared |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup></p> |Dedykowane<sup>3</sup>|
| [Skalowanie w poziomie](../articles/app-service/manage-scale-up.md) (maksymalna liczba wystąpień) |1 udostępniony |1 udostępniony |3 dedykowane<sup>3</sup> |10 dedykowanych<sup>3</sup> |30 dedykowanych<sup>3</sup>|100 dedykowany<sup>4</sup>|
| Magazyn<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Przez ponad 250 GB przesłanie żądania pomocy technicznej. |1 TB<sup>5</sup> <br/><br/> Przydział dostępnego magazynu to 999 GB. |
| Czas procesora CPU (5 minut)<sup>6</sup> |3 minuty |3 minuty |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a>|
| Czas procesora CPU (dzień)<sup>6</sup> |60 minut |240 minut |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Nieograniczone, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |
| Pamięć (1 godzina) |1 024 MB na plan App Service |1 024 MB na aplikację |NIE DOTYCZY |NIE DOTYCZY |NIE DOTYCZY |NIE DOTYCZY |
| Przepustowość |165 MB |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |
| Architektura aplikacji |32-bitowa |32-bitowa |32-bitowa/64-bitowa |32-bitowa/64-bitowa |32-bitowa/64-bitowa |32-bitowa/64-bitowa |
| Gniazda sieci Web na wystąpienie<sup>7</sup> |5 |35 |350 |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
| Połączenia IP | 600 | 600 | Zależy od rozmiaru wystąpienia<sup>8</sup> | Zależy od rozmiaru wystąpienia<sup>8</sup> | Zależy od rozmiaru wystąpienia<sup>8</sup> | 16 000 |
| Współbieżne [połączenia debugera](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikację |1 |1 |1 |5 |5 |5 |
| App Service certyfikatów na subskrypcję<sup>9</sup>| Nieobsługiwane | Nieobsługiwane |10 |10 |10 |10 |
| Domeny niestandardowe na aplikację</a> |0 (tylko poddomena azurewebsites.net)|500 |500 |500 |500 |500 |
| [Obsługa protokołu SSL](../articles/app-service/configure-ssl-certificate.md) domeny niestandardowej |Nieobsługiwane, certyfikat wieloznaczny dla \* . azurewebsites.NET jest domyślnie dostępny|Nieobsługiwane, certyfikat wieloznaczny dla \* . azurewebsites.NET jest domyślnie dostępny|Nieograniczone połączenia SNI SSL |Uwzględniono nieograniczone SNI SSL i 1 Połączenie SSL z adresu IP połączeń |Uwzględniono nieograniczone SNI SSL i 1 Połączenie SSL z adresu IP połączeń | Uwzględniono nieograniczone SNI SSL i 1 Połączenie SSL z adresu IP połączeń|
| Połączenia hybrydowe | | | 5 na plan | 25 na plan | 200 na aplikację | 200 na aplikację |
| [Integracja Virtual Network](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| Zintegrowana usługa równoważenia obciążenia | |X |X |X |X |X<sup>10</sup> |
| [Zawsze włączone](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Zaplanowane kopie zapasowe](../articles/app-service/manage-backup.md) | | | | Zaplanowane kopie zapasowe co 2 godziny, maksymalnie 12 kopii zapasowych dziennie (ręczna + zaplanowana) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręczna + zaplanowana) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręczna + zaplanowana) |
| [Automatyczne skalowanie](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Zadania WebJob](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitorowanie punktu końcowego](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| Miejsca [przejściowe](../articles/app-service/deploy-staging-slots.md) na aplikację| | | |5 |20 |20 |
| [Testowanie w środowisku produkcyjnym](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Dzienniki diagnostyczne](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Uwierzytelnianie i autoryzacja](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service certyfikaty zarządzane (publiczna wersja zapoznawcza)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| Umowa SLA | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup> Aplikacje i przydziały magazynu są na App Service plan, chyba że zaznaczono inaczej.  
<sup>2</sup> Rzeczywista liczba aplikacji, które można hostować na tych komputerach, zależy od aktywności aplikacji, rozmiaru wystąpień maszyn i odpowiedniego wykorzystania zasobów.  
<sup>3</sup> Wystąpienia dedykowane mogą mieć różne rozmiary. Aby uzyskać więcej informacji, zobacz [Cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Więcej informacji można uzyskać na żądanie.  
<sup>5</sup> Limit magazynowania to łączny rozmiar zawartości dla wszystkich aplikacji w ramach tego samego planu usługi App Service. Łączny rozmiar zawartości wszystkich aplikacji we wszystkich planach usługi App Service w ramach jednej grupy zasobów i regionu nie może przekroczyć 500 GB.  
<sup>6</sup> Te zasoby są ograniczone przez zasoby fizyczne w dedykowanych wystąpieniach (rozmiar wystąpienia i liczba wystąpień).  
<sup>7</sup> W przypadku skalowania aplikacji w warstwie Podstawowa do dwóch wystąpień dla każdego z tych dwóch wystąpień masz 350 połączeń współbieżnych. W przypadku warstwy Standardowa i wyższych nie ma teoretycznych limitów dla gniazd sieci Web, ale inne czynniki mogą ograniczyć liczbę gniazd sieci Web. Na przykład maksymalna dozwolona liczba równoczesnych żądań (zdefiniowanych przez `maxConcurrentRequestsPerCpu` ) to: 7 500 na małą maszynę wirtualną, 15 000 na średnią maszynę wirtualną (7 500 x 2 rdzenie) i 75 000 na dużą maszynę wirtualną 18 750 (rdzenie x 4).  
<sup>8</sup> Maksymalna liczba połączeń IP jest zależna od wystąpienia i zależy od rozmiaru wystąpienia: 1 920 na wystąpienie B1/S1/P1V3, 3 968 per B2/S2/P2V3, 8 064 dla wystąpienia B3/S3/P3V3.  
<sup>9</sup> Limit przydziału Certyfikat usługi App Service na subskrypcję można zwiększyć przez żądanie obsługi do limitu maksymalnego 200.  
<sup>10</sup> Jednostki SKU izolowana usługa App Service mogą być funkcją wewnętrznego równoważenia obciążenia (ILB) z Azure Load Balancer, dzięki czemu nie ma połączenia publicznego z Internetu. W związku z tym niektóre funkcje ILB App Service izolowanych muszą być używane z maszyn, które mają bezpośredni dostęp do punktu końcowego sieci ILB.  
<sup>11</sup> Uruchamiaj niestandardowe pliki wykonywalne i/lub skrypty na żądanie, zgodnie z harmonogramem lub w sposób ciągły jako zadanie w tle w ramach wystąpienia App Service. Na potrzeby ciągłego wykonywania zadań WebJob jest wymagana zawsze włączona. Brak wstępnie zdefiniowanego limitu liczby zadań WebJob, które mogą być uruchamiane w wystąpieniu App Service. Istnieją praktyczne ograniczenia, które zależą od tego, co próbuje wykonać kod aplikacji.

<sup>12</sup> Wykorzystane domeny nie są obsługiwane. Tylko wystawianie certyfikatów standardowych (Certyfikaty wieloznaczne są niedostępne). Ograniczone tylko do jednego bezpłatnego certyfikatu na domenę niestandardową.
