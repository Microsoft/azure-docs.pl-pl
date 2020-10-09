---
title: Korzystanie z maszyn wirtualnych platformy Azure
description: Dowiedz się, jak oszczędzać koszty przy użyciu maszyn wirtualnych platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 66d62cde9ea17e73f561dfbce94eb3d3e7175b6d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827803"
---
# <a name="use-spot-vms-in-azure"></a>Korzystanie z maszyn wirtualnych na platformie Azure

Korzystanie z maszyn wirtualnych na miejscu pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, pory dnia i innych. Podczas wdrażania maszyn wirtualnych w miejscu platforma Azure przydzieli maszyny wirtualne, jeśli będzie dostępna pojemność, ale nie ma umowy SLA dla tych maszyn wirtualnych. Na maszynie wirtualnej nie są dostępne gwarancje wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy maszyny wirtualne z 30 sekund. 


## <a name="eviction-policy"></a>Zasady eksmisji

Maszyny wirtualne można wykluczyć w oparciu o pojemność lub maksymalną ustawioną cenę. Podczas tworzenia maszyny wirtualnej na miejscu możesz ustawić zasady wykluczania na *Cofnij przydział* (domyślnie) lub *Usuń*. 

Zasada cofania *przydziału* przenosi maszynę wirtualną do stanu zatrzymania bez alokacji, umożliwiając ponowne wdrożenie go później. Nie ma jednak gwarancji, że alokacja powiedzie się. Wycofane maszyny wirtualne będą wliczane do limitu przydziału i będą naliczane opłaty za magazyn dla dysków bazowych. 

Jeśli chcesz usunąć maszynę wirtualną, gdy zostanie ona wykluczona, możesz ustawić zasady wykluczania do *usunięcia*. Wykluczone maszyny wirtualne zostaną usunięte wraz z ich podstawowymi dyskami, więc nie będzie można naliczać opłat za magazyn. 

Możesz zrezygnować z otrzymywania powiadomień w ramach maszyny wirtualnej za pomocą [usługi Azure Scheduled Events](./linux/scheduled-events.md). Spowoduje to powiadomienie użytkownika, jeśli maszyny wirtualne zostaną wykluczone, a użytkownik będzie miał 30 sekund na ukończenie zadań i wykonanie zadań zamknięcia przed wykluczeniem. 


| Opcja | Wynik |
|--------|---------|
| Cena maksymalna jest ustawiona na >= aktualna cena. | Maszyna wirtualna jest wdrażana, jeśli dostępne są zasoby i zasoby. |
| Cena maksymalna jest ustawiona na < bieżącej ceny. | Maszyna wirtualna nie została wdrożona. Zostanie wyświetlony komunikat o błędzie, że maksymalna cena musi być >= aktualna cena. |
| Ponowne uruchamianie maszyny wirtualnej Zatrzymaj/Cofnij przydział, jeśli maksymalna cena jest >= aktualna cena | W przypadku pojemności i przydziału, maszyna wirtualna jest wdrażana. |
| Ponowne uruchamianie maszyny wirtualnej Zatrzymaj/Cofnij przydział, jeśli maksymalna cena jest < bieżącej cenie | Zostanie wyświetlony komunikat o błędzie, że maksymalna cena musi być >= aktualna cena. | 
| Cena maszyny wirtualnej została zakończona i teraz > Cena maksymalna. | Maszyna wirtualna zostanie wykluczona. Przed rzeczywistym wykluczeniem otrzymasz powiadomienie 30 s. | 
| Po wykluczeniu cena maszyny wirtualnej jest powracana do < maksymalnej ceny. | Maszyna wirtualna nie zostanie automatycznie uruchomiona. Możesz ponownie uruchomić maszynę wirtualną, a opłata będzie naliczana według bieżącej ceny. |
| Jeśli maksymalna cena jest ustawiona na `-1` | Ta maszyna wirtualna nie zostanie wykluczona ze względu na ceny. Cena maksymalna to cena bieżąca, do ceny standardowych maszyn wirtualnych. Nigdy nie będzie naliczana opłata powyżej standardowej ceny.| 
| Zmiana maksymalnej ceny | Aby zmienić maksymalną cenę, należy cofnąć przydział maszyny wirtualnej. Cofnij przydział maszyny wirtualnej, ustaw nową cenę maksymalną, a następnie zaktualizuj maszynę wirtualną. |


## <a name="limitations"></a>Ograniczenia

Następujące rozmiary maszyn wirtualnych nie są obsługiwane w przypadku maszyn wirtualnych na miejscu:
 - Seria B
 - Promocja wersji dowolnego rozmiaru (na przykład Dv2, NV, w obszarze rozmiary promocji)

Dodatkowe maszyny wirtualne można wdrożyć w dowolnym regionie, z wyjątkiem Microsoft Azure Chinach 21Vianet.

<a name="channel"></a>

Następujące [typy ofert](https://azure.microsoft.com/support/legal/offer-details/) są obecnie obsługiwane:

-   Enterprise Agreement
-   Płatność zgodnie z rzeczywistym użyciem
-   Sponsorowan
- W przypadku dostawcy usług w chmurze (CSP) skontaktuj się z partnerem


## <a name="pricing"></a>Cennik

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 

Możesz również zbadać informacje o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o cenach dodatkowych. Elementy `meterName` i `skuName` będą zawierać `Spot` .

W przypadku zmiennych cenowych istnieje możliwość ustawienia maksymalnej ceny w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.

## <a name="pricing-and-eviction-history"></a>Historia cen i wykluczeń

Możesz wyświetlić historyczne ceny i stawki wykluczenia według rozmiaru w regionie w portalu. Wybierz pozycję **Wyświetl historię cen i Porównaj ceny w pobliżu regiony** , aby wyświetlić tabelę lub Graf cenowy o określonym rozmiarze.  Stawki cen i wykluczeń w poniższych obrazach są tylko przykłady. 

**Wykres**:

:::image type="content" source="./media/spot-chart.png" alt-text="Zrzut ekranu przedstawiający opcje regionu z różnicą cen i stawek wykluczenia jako wykresu.":::

**Tabela**:

:::image type="content" source="./media/spot-table.png" alt-text="Zrzut ekranu przedstawiający opcje regionu z różnicą cen i stawek wykluczenia jako wykresu.":::



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

## <a name="next-steps"></a>Następne kroki
Wdrażaj maszyny wirtualne za pomocą [interfejsu wiersza polecenia](./linux/spot-cli.md), [portalu](spot-portal.md), [ARM](./linux/spot-template.md)lub [programu PowerShell](./windows/spot-powershell.md) .

Możesz również wdrożyć [zestaw skalowania z wystąpieniami maszyn wirtualnych](../virtual-machine-scale-sets/use-spot.md).

Jeśli wystąpi błąd, zobacz [kody błędów](./error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
