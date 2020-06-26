---
title: dołączanie pliku
description: dołączanie pliku
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 76af1084626944d8399edd2c7ec5dec92e25f3ed
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378568"
---
Korzystanie z maszyn wirtualnych na miejscu pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, pory dnia i innych. Podczas wdrażania maszyn wirtualnych w miejscu platforma Azure przydzieli maszyny wirtualne, jeśli będzie dostępna pojemność, ale nie ma umowy SLA dla tych maszyn wirtualnych. Na maszynie wirtualnej nie są dostępne gwarancje wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy maszyny wirtualne z 30 sekund. 


## <a name="eviction-policy"></a>Zasady wykluczania

Maszyny wirtualne można wykluczyć w oparciu o pojemność lub maksymalną ustawioną cenę. W przypadku maszyn wirtualnych zasady wykluczenia są ustawiane na wartość *unallocate* , która przenosi wykluczone maszyny wirtualne do stanu zatrzymania bez alokacji, umożliwiając ponowne wdrożenie wykluczonych maszyn wirtualnych w późniejszym czasie. Ponowne przydzielanie dodatkowych maszyn wirtualnych będzie jednak zależne od dostępnej pojemności. Cofnięte alokacje maszyn wirtualnych będą wliczane do limitu przydziału usługi vCPUe i zostanie naliczona opłata za dyski bazowe. 

Użytkownicy mogą zrezygnować z otrzymywania powiadomień w ramach maszyny wirtualnej za pomocą [usługi Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). Spowoduje to powiadomienie użytkownika, jeśli maszyny wirtualne zostaną wykluczone, a użytkownik będzie miał 30 sekund na ukończenie zadań i wykonanie zadań zamknięcia przed wykluczeniem. 


| Opcja | Wynik |
|--------|---------|
| Cena maksymalna jest ustawiona na >= aktualna cena. | Maszyna wirtualna jest wdrażana, jeśli dostępne są zasoby i zasoby. |
| Cena maksymalna jest ustawiona na < bieżącej ceny. | Maszyna wirtualna nie została wdrożona. Zostanie wyświetlony komunikat o błędzie, że maksymalna cena musi być >= aktualna cena. |
| Ponowne uruchamianie maszyny wirtualnej Zatrzymaj/Cofnij przydział, jeśli maksymalna cena jest >= aktualna cena | W przypadku pojemności i przydziału, maszyna wirtualna jest wdrażana. |
| Ponowne uruchamianie maszyny wirtualnej Zatrzymaj/Cofnij przydział, jeśli maksymalna cena jest < bieżącej cenie | Zostanie wyświetlony komunikat o błędzie, że maksymalna cena musi być >= aktualna cena. | 
| Cena maszyny wirtualnej została zakończona i teraz > Cena maksymalna. | Maszyna wirtualna zostanie wykluczona. Przed rzeczywistym wykluczeniem otrzymasz powiadomienie 30 s. | 
| Po wykluczeniu cena maszyny wirtualnej jest powracana do < maksymalnej ceny. | Maszyna wirtualna nie zostanie automatycznie uruchomiona. Możesz ponownie uruchomić maszynę wirtualną, a opłata będzie naliczana według bieżącej ceny. |
| Jeśli maksymalna cena jest ustawiona na`-1` | Ta maszyna wirtualna nie zostanie wykluczona ze względu na ceny. Cena maksymalna to cena bieżąca, do ceny standardowych maszyn wirtualnych. Nigdy nie będzie naliczana opłata powyżej standardowej ceny.| 
| Zmiana maksymalnej ceny | Aby zmienić maksymalną cenę, należy cofnąć przydział maszyny wirtualnej. Cofnij przydział maszyny wirtualnej, ustaw nową cenę maksymalną, a następnie zaktualizuj maszynę wirtualną. |

## <a name="limitations"></a>Ograniczenia

Następujące rozmiary maszyn wirtualnych nie są obsługiwane w przypadku maszyn wirtualnych na miejscu:
 - Seria B
 - Promocja wersji dowolnego rozmiaru (na przykład Dv2, NV, w obszarze rozmiary promocji)

Dodatkowe maszyny wirtualne można wdrożyć w dowolnym regionie, z wyjątkiem Microsoft Azure Chinach 21Vianet.

Niektóre kanały subskrypcji nie są obsługiwane:

<a name="channel"></a>

| Kanały platformy Azure               | Dostępność maszyn wirtualnych na platformie Azure       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Tak                               |
| Płatność zgodnie z rzeczywistym użyciem                | Tak                               |
| Dostawca usług w chmurze (CSP) | [Skontaktuj się z partnerem](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Zalety                     | Niedostępne                     |
| Sponsorowan                    | Tak                               |
| Bezpłatna wersja próbna                   | Niedostępne                     |



## <a name="pricing"></a>Cennik

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 


W przypadku zmiennych cenowych istnieje możliwość ustawienia maksymalnej ceny w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.


##  <a name="frequently-asked-questions"></a>Często zadawane pytania

**P:** Po utworzeniu, czy na maszynie wirtualnej jest taka sama jak zwykła Standardowa maszyna wirtualna?

Odp **.:** Tak, z tą różnicą, że nie ma umowy SLA dla maszyn wirtualnych i można je wykluczyć w dowolnym momencie.


**P:** Co należy zrobić po wykluczeniu, ale nadal potrzebujesz pojemności?

Odp **.:** Zalecamy używanie standardowych maszyn wirtualnych zamiast maszyn wirtualnych na miejscu, jeśli potrzebujesz pojemności od razu.


**P:** Jak są zarządzane limity przydziału dla maszyn wirtualnych na miejscu?

Odp **.:** Maszyny wirtualne na miejscu będą mieć oddzielną pulę przydziałów. Przydział punktowy będzie współużytkowany między maszynami wirtualnymi i wystąpieniami zestawów skalowania. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**P:** Czy mogę zażądać dodatkowego przydziału na miejscu?

Odp **.:** Tak, będzie można przesłać żądanie w celu zwiększenia limitu przydziału dla maszyn wirtualnych na miejscu za pośrednictwem [standardowego procesu żądania limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**P:** Gdzie mogę publikować pytania?

Odp **.:** Możesz ogłosić pytanie i oznaczyć je za pomocą `azure-spot` [elementu Q&a](https://docs.microsoft.com/answers/topics/azure-spot.html). 



