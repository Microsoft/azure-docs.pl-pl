---
title: Użyj Virtual Machines na platformie Azure
description: Dowiedz się, jak korzystać z Virtual Machines usługi Azure Spot, aby zaoszczędzić na kosztach.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: 460529ab6e3227a998ac04c4819171274307ff9e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557828"
---
# <a name="use-azure-spot-virtual-machines"></a>Użyj Virtual Machines na platformie Azure 

Korzystanie z usługi Azure Spot Virtual Machines umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy Virtual Machines na platformie Azure. Z tego względu Virtual Machines na platformie Azure są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, pory dnia i innych. Podczas wdrażania usługi Azure Spot Virtual Machines platforma Azure przydzieli maszyny wirtualne, jeśli będzie dostępna pojemność, ale nie ma umowy SLA dla tych maszyn wirtualnych. Maszyna wirtualna w miejscu na platformie Azure nie oferuje gwarancji o wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy Virtual Machines platformy Azure z powiadomieniem o 30 sekundach. 


## <a name="eviction-policy"></a>Zasady eksmisji

Maszyny wirtualne można wykluczyć w oparciu o pojemność lub maksymalną ustawioną cenę. Podczas tworzenia maszyny wirtualnej platformy Azure w miejscu możesz ustawić zasady wykluczania na *Alokacje* (ustawienie domyślne) lub *delete*. 

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

Następujące rozmiary maszyn wirtualnych nie są obsługiwane w przypadku Virtual Machines na platformie Azure:
 - Seria B
 - Promocja wersji dowolnego rozmiaru (na przykład Dv2, NV, w obszarze rozmiary promocji)

Virtual Machines usługi Azure Spot można wdrożyć w dowolnym regionie, z wyjątkiem Microsoft Azure Chiny 21Vianet.

<a name="channel"></a>

Następujące [typy ofert](https://azure.microsoft.com/support/legal/offer-details/) są obecnie obsługiwane:

-   Enterprise Agreement
-   Kod oferty z opcją płatność zgodnie z rzeczywistym użyciem 003P
-   Sponsorowan
- W przypadku dostawcy usług w chmurze (CSP) skontaktuj się z partnerem


## <a name="pricing"></a>Cennik

Cennik usługi Azure Virtual Machines w miejscu to zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 

Możesz również zbadać informacje o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o cenach dodatkowych. Elementy `meterName` i `skuName` będą zawierać `Spot` .

W przypadku zmiennych cenowych istnieje możliwość ustawienia maksymalnej ceny w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.

## <a name="pricing-and-eviction-history"></a>Historia cen i wykluczeń

Możesz wyświetlić historyczne ceny i stawki wykluczenia według rozmiaru w regionie w portalu. Wybierz pozycję **Wyświetl historię cen i Porównaj ceny w pobliżu regiony** , aby wyświetlić tabelę lub Graf cenowy o określonym rozmiarze.  Stawki cen i wykluczeń w poniższych obrazach są tylko przykłady. 

**Wykres**:

:::image type="content" source="./media/spot-chart.png" alt-text="Zrzut ekranu przedstawiający opcje regionu z różnicą cen i stawek wykluczenia jako wykresu.":::

**Tabela**:

:::image type="content" source="./media/spot-table.png" alt-text="Zrzut ekranu przedstawiający opcje regionu z różnicą cen i stawek wykluczania w postaci tabeli.":::



##  <a name="frequently-asked-questions"></a>Często zadawane pytania

**P:** Po utworzeniu usługa jest maszyną wirtualną platformy Azure w tej samej postaci jak zwykła Standardowa maszyna wirtualna?

Odp **.:** Tak, z tą różnicą, że nie ma umowy SLA dla usługi Azure Virtual Machines i można je wykluczyć w dowolnym momencie.


**P:** Co należy zrobić po wykluczeniu, ale nadal potrzebujesz pojemności?

Odp **.:** Zalecamy używanie standardowych maszyn wirtualnych zamiast Virtual Machines platformy Azure, jeśli potrzebujesz pojemności od razu.


**P:** Jak są zarządzane limity przydziału dla usługi Azure Spot Virtual Machines?

Odp **.:** Virtual Machines w miejscu na platformie Azure będzie mieć oddzielną pulę przydziałów. Przydział punktowy będzie współużytkowany między maszynami wirtualnymi i wystąpieniami zestawów skalowania. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).


**P:** Czy mogę zażądać dodatkowego przydziału dla Virtual Machines w miejscu na platformie Azure?

Odp **.:** Tak, będzie można przesłać żądanie, aby zwiększyć limit przydziału dla Virtual Machines w miejscu na platformie Azure przez [standardowy proces żądania limitu przydziału](../azure-portal/supportability/per-vm-quota-requests.md).


**P:** Gdzie mogę publikować pytania?

Odp **.:** Możesz ogłosić pytanie i oznaczyć je za pomocą `azure-spot` [elementu Q&a](/answers/topics/azure-spot.html). 


**P:** Jak mogę zmienić maksymalną cenę maszyny wirtualnej na miejscu?

Odp **.:** Aby zmienić cenę maksymalną, należy cofnąć przydział maszyny wirtualnej. Następnie można zmienić maksymalną cenę w portalu, z sekcji **konfiguracji** maszyny wirtualnej. 

## <a name="next-steps"></a>Następne kroki
Użyj [interfejsu wiersza polecenia](./linux/spot-cli.md), [portalu](spot-portal.md), usługi [ARM](./linux/spot-template.md)lub [programu PowerShell](./windows/spot-powershell.md) , aby wdrożyć Virtual Machines na platformie Azure.

[Zestaw skalowania można również wdrożyć za pomocą wystąpień maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/use-spot.md).

Jeśli wystąpi błąd, zobacz [kody błędów](./error-codes-spot.md).
