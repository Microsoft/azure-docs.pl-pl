---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421929"
---
Korzystanie z maszyn wirtualnych spot pozwala na wykorzystanie naszej niewykorzystanej pojemności przy znacznych oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure będzie eksmitować maszyny wirtualne spot. W związku z tym maszyny wirtualne spot są idealne dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperów/testów, duże obciążenia obliczeniowe i inne.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, połówka dnia i innych. Podczas wdrażania maszyn wirtualnych spot platforma Azure przydzieli maszyny wirtualne, jeśli dostępna jest pojemność, ale dla tych maszyn wirtualnych nie ma żadnej umowy SLA. Spot VM nie oferuje gwarancji wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure będzie eksmitować maszyny wirtualne spot z powiadomieniem 30 sekund. 


## <a name="eviction-policy"></a>Zasady eksmisji

Maszyny wirtualne mogą być eksmitowane na podstawie pojemności lub maksymalnej ceny, którą ustawisz. W przypadku maszyn wirtualnych zasady eksmisji jest ustawiona na *Deallocate,* który przenosi eksmitowanych maszyn wirtualnych do stanu zatrzymanego-deallokowane, co pozwala na ponowne wdrożenie eksmitowanych maszyn wirtualnych w późniejszym czasie. Jednak ponowne przydzielanie maszyn wirtualnych spot będzie zależeć od dostępnej pojemności punktowej. Zdelegalizowane maszyny wirtualne będą wliczane do limitu podstawowego limitu vCPU i zostanie naliczona opłata za dyski bazowe. 

Użytkownicy mogą wyrazić zgodę na otrzymywanie powiadomień w maszynie wirtualnej za pośrednictwem [usługi Azure Scheduled Events.](../articles/virtual-machines/linux/scheduled-events.md) Spowoduje to powiadomienie, jeśli maszyny wirtualne są eksmitowane i będziesz miał 30 sekund, aby zakończyć wszystkie zadania i wykonać zadania zamknięcia przed eksmisją. 


| Opcja | Wynik |
|--------|---------|
| Cena maksymalna jest ustawiona na >= aktualna cena. | Maszyna wirtualna jest wdrażana, jeśli dostępna jest pojemność i przydział. |
| Cena maksymalna jest ustawiona na < aktualną cenę. | Maszyna wirtualna nie jest wdrażana. Pojawi się komunikat o błędzie, że cena maksymalna musi być >= aktualna cena. |
| Ponowne uruchamianie maszyny Wirtualnej zatrzymania/przydzielenia, jeśli cena maksymalna jest >= aktualna cena | Jeśli istnieje pojemność i przydział, maszyna wirtualna jest wdrażana. |
| Ponowne uruchamianie maszyny Wirtualnej zatrzymania/przydzielenia, jeśli cena maksymalna jest < bieżącej cenie | Pojawi się komunikat o błędzie, że cena maksymalna musi być >= aktualna cena. | 
| Cena maszyny Wirtualnej wzrosła i jest teraz > maksymalnej cenie. | Maszyna wirtualna zostanie eksmitowana. Otrzymasz powiadomienie z 30 s przed rzeczywistą eksmisją. | 
| Po eksmisji cena maszyny Wirtualnej wraca do < maksymalnej cenie. | Maszyna wirtualna nie zostanie automatycznie uruchomiona ponownie. Możesz ponownie uruchomić maszynę wirtualną samodzielnie, a zostanie naliczona po bieżącej cenie. |
| Jeśli cena maksymalna jest ustawiona na`-1` | Maszyna wirtualna nie zostanie eksmitowana ze względu na ceny. Maksymalna cena będzie aktualną ceną, do ceny standardowych maszyn wirtualnych. Nigdy nie zostaniesz obciążony powyżej standardowej ceny.| 
| Zmiana ceny maksymalnej | Musisz zdelocate maszyny Wirtualnej, aby zmienić cenę maksymalną. Zdemiuzuj alokację maszyny Wirtualnej, ustaw nową cenę maksymalną, a następnie zaktualizuj maszynę wirtualną. |

## <a name="limitations"></a>Ograniczenia

Następujące rozmiary maszyn wirtualnych nie są obsługiwane dla maszyn wirtualnych punktowych:
 - Seria B
 - Wersje promocyjne o dowolnym rozmiarze (takie jak Rozmiary promocyjne Dv2, NV, NC, H)

Maszyny wirtualne punktowe nie mogą obecnie używać tymczasowych dysków systemu operacyjnego.

Maszyny wirtualne punktowe można wdrożyć w dowolnym regionie, z wyjątkiem platformy Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Cennik

Ceny maszyn wirtualnych punktowych są zmienne na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz Ceny maszyn wirtualnych dla [systemów Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


W przypadku zmiennych cen możesz ustawić cenę maksymalną w dolarach amerykańskich (USD), używając do 5 miejsc po przecinku. Na przykład wartość `0.98765`będzie maksymalna cena $0.98765 USD za godzinę. Jeśli ustawisz maksymalną `-1`cenę, maszyna wirtualna nie zostanie eksmitowana na podstawie ceny. Cena za maszynę wirtualną będzie bieżącą ceną spot lub ceną standardowej maszyny Wirtualnej, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i przydział.


##  <a name="frequently-asked-questions"></a>Często zadawane pytania

**P.** Po utworzeniu jest spot VM taka sama jak zwykła standardowa maszyna wirtualna?

**Odp.:** Tak, z wyjątkiem nie ma umowy SLA dla maszyn wirtualnych punktowych i mogą one zostać eksmitowane w dowolnym momencie.


**P.** Co zrobić, gdy dostajesz eksmitowany, ale nadal potrzebujesz pojemności?

**Odp.:** Jeśli potrzebujesz pojemności od razu, zaleca się używanie standardowych maszyn wirtualnych zamiast maszyn wirtualnych typu spot.


**P.** Jak zarządza się przydziałem dla maszyn wirtualnych punktowych?

**Odp.:** Maszyny wirtualne punktowe będą miały oddzielną pulę przydziałów. Przydział punktowy będzie współużytkowany między maszynami wirtualnymi i wystąpieniami zestawu skalowania. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**P.** Czy mogę poprosić o dodatkowy przydział na spot?

**Odp.:** Tak, można przesłać wniosek o zwiększenie przydziału dla maszyn wirtualnych punktowych za pośrednictwem [standardowego procesu żądania przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**P.** Jakie kanały obsługują maszyny wirtualne spot?

**Odp.:** Zobacz poniższą tabelę dostępności maszyn wirtualnych w miejscu.

<a name="channel"></a>

| Kanały platformy Azure               | Dostępność maszyn wirtualnych w miejscu platformy Azure       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Tak                               |
| Płatność zgodnie z rzeczywistym użyciem                | Tak                               |
| Dostawca usług w chmurze (CSP) | [Skontaktuj się ze swoim partnerem](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Umowa klienta firmy Microsoft | Tak                               |
| Zalety                     | Niedostępne                     |
| Sponsorowane                    | Niedostępne                     |
| Bezpłatna wersja próbna                   | Niedostępne                     |


**P.** Gdzie mogę zadać pytania?

**Odp.:** Możesz opublikować i oznaczyć swoje pytanie na `azure-spot` Q&[A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



