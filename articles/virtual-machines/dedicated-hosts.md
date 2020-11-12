---
title: Omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych
description: Dowiedz się więcej o tym, jak można używać dedykowanych hostów platformy Azure do wdrażania maszyn wirtualnych.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 0b0e198075455f697c87ad48741a770e6f78b5a5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542154"
---
# <a name="azure-dedicated-hosts"></a>Dedykowane hosty platformy Azure

Dedykowany Host platformy Azure to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych, udostępniane jako zasób. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.


## <a name="benefits"></a>Korzyści 

Rezerwowanie całego hosta zapewnia następujące korzyści:

-   Izolacja sprzętowa na poziomie serwera fizycznego. Żadne inne maszyny wirtualne nie zostaną umieszczone na hostach. Dedykowane hosty są wdrażane w tych samych centrach danych i korzystają z tej samej sieci i podstawowej infrastruktury magazynu, co inne, nieizolowane hosty.
-   Kontrola nad zdarzeniami konserwacji zainicjowanymi przez platformę Azure. Chociaż większość zdarzeń konserwacji nie ma wpływu na maszyny wirtualne, istnieją pewne wrażliwe obciążenia, w przypadku których każda sekunda wstrzymania może mieć wpływ. Za pomocą dedykowanych hostów możesz wybrać okno obsługi, aby zmniejszyć wpływ na usługę.
-   Korzyść używania hybrydowego platformy Azure umożliwia korzystanie z własnych licencji dla systemów Windows i SQL na platformie Azure. Korzystanie z zalet hybrydowych zapewnia dodatkowe korzyści. Aby uzyskać więcej informacji, zobacz [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupy, hosty i maszyny wirtualne  

![Widok nowych zasobów dla dedykowanych hostów.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Grupa hostów** jest zasobem, który reprezentuje kolekcję dedykowanych hostów. Można utworzyć grupę hostów w regionie i strefie dostępności, a następnie dodać do niej hosty.

**Host** jest zasobem zamapowanym na serwer fizyczny w centrum danych platformy Azure. Serwer fizyczny jest przypisywany podczas tworzenia hosta. Host jest tworzony w obrębie grupy hostów. Host ma jednostkę SKU opisującą, które rozmiary maszyn wirtualnych można utworzyć. Każdy host może obsługiwać wiele maszyn wirtualnych o różnych rozmiarach, o ile są one z tej samej serii rozmiarów.


## <a name="high-availability-considerations"></a>Zagadnienia dotyczące wysokiej dostępności 

Aby zapewnić wysoką dostępność, należy wdrożyć wiele maszyn wirtualnych, rozmieścić je na wielu hostach (co najmniej 2). Dedykowane hosty platformy Azure udostępniają kilka opcji aprowizacji infrastruktury do kształtowania granic izolacji błędów.

### <a name="use-availability-zones-for-fault-isolation"></a>Użyj Strefy dostępności na potrzeby izolacji błędów

Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Grupa hostów jest tworzona w jednej strefie dostępności. Po utworzeniu wszystkie hosty zostaną umieszczone w tej strefie. Aby zapewnić wysoką dostępność w różnych strefach, należy utworzyć wiele grup hostów (jednej na strefę) i odpowiednio rozmieścić hosty.

Jeśli grupa hostów zostanie przypisana do strefy dostępności, wszystkie maszyny wirtualne utworzone na tym hoście muszą zostać utworzone w tej samej strefie.

### <a name="use-fault-domains-for-fault-isolation"></a>Użyj domen błędów na potrzeby izolacji błędów

Hosta można utworzyć w określonej domenie błędów. Podobnie jak w przypadku maszyn wirtualnych w zestawie skalowania lub zestawie dostępności, hosty w różnych domenach błędów będą umieszczane w różnych stojakach fizycznych w centrum danych. Podczas tworzenia grupy hostów należy określić liczbę domen błędów. Podczas tworzenia hostów w grupie hostów należy przypisać domenę błędów dla każdego hosta. Maszyny wirtualne nie wymagają przypisywania domeny błędów.

Domeny błędów nie są takie same jak w przypadku wspólnej lokalizacji. Posiadanie tej samej domeny błędów dla dwóch hostów nie oznacza, że są one blisko siebie.

Domeny błędów są objęte zakresem grupy hostów. Nie należy wprowadzać żadnych założeń między dwiema grupami hostów (chyba że znajdują się w różnych strefach dostępności).

Maszyny wirtualne wdrożone na hostach z różnymi domenami błędów będą mieć swoje podstawowe usługi dysków zarządzanych w wielu sygnaturach magazynu, aby zwiększyć ochronę izolacji błędów.

### <a name="using-availability-zones-and-fault-domains"></a>Używanie Strefy dostępności i domen błędów

Można używać obu funkcji jednocześnie, aby osiągnąć jeszcze większą izolację błędów. W takim przypadku należy określić strefę dostępności i liczbę domen błędów w dla każdej grupy hostów, przypisać domenę błędów do każdego hosta w grupie i przypisać strefę dostępności do każdej z maszyn wirtualnych

Przykładowy szablon Menedżer zasobów znaleziony w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) używa stref i domen błędów do rozmieszczenia hostów w celu uzyskania maksymalnej odporności w regionie.


## <a name="manual-vs-automatic-placement"></a>Ręczne a automatyczne umieszczanie 

> [!IMPORTANT]
> Automatyczne umieszczanie jest obecnie w publicznej wersji zapoznawczej.
> Aby wziąć udział w wersji zapoznawczej, wypełnij ankietę dołączania w wersji zapoznawczej pod adresem [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podczas tworzenia maszyny wirtualnej na platformie Azure można wybrać dedykowanego hosta do użycia. Można również użyć opcji, aby automatycznie umieścić maszyny wirtualne na istniejących hostach w grupie hostów. 

Podczas tworzenia nowej grupy hostów upewnij się, że jest zaznaczona opcja automatycznego umieszczania maszyn wirtualnych. Podczas tworzenia maszyny wirtualnej wybierz grupę hostów i zezwól platformie Azure na wybranie najlepszego hosta dla maszyny wirtualnej. 

Grupy hostów, które są włączone do automatycznego umieszczania, nie wymagają automatycznego umieszczania wszystkich maszyn wirtualnych. Nadal będzie można jawnie wybrać hosta, nawet jeśli wybrano opcję automatycznego umieszczania dla grupy hostów. 

### <a name="limitations"></a>Ograniczenia

Znane problemy i ograniczenia dotyczące automatycznego umieszczania maszyn wirtualnych:

- Nie będzie można stosować korzyści z używania hybrydowej platformy Azure na dedykowanych hostach.
- Ponowne wdrożenie maszyny wirtualnej nie będzie możliwe. 
- Nie będzie można używać maszyn wirtualnych z serii Lsv2, NVasv4, NVsv3, Msv2 lub M z dedykowanymi hostami 


## <a name="virtual-machine-scale-set-support"></a>Obsługa zestawu skalowania maszyn wirtualnych

Zestawy skalowania maszyn wirtualnych umożliwiają traktowanie grup maszyn wirtualnych jako jednego zasobu i stosowanie zasad dostępności, zarządzania, skalowania i aranżacji jako grupy. Istniejące dedykowane hosty mogą być również używane dla zestawów skalowania maszyn wirtualnych. 

> [!IMPORTANT]
> Virtual Machine Scale Sets na dedykowanych hostach jest obecnie w publicznej wersji zapoznawczej.
> Aby wziąć udział w wersji zapoznawczej, wypełnij ankietę dołączania w wersji zapoznawczej pod adresem [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) .
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podczas tworzenia zestawu skalowania maszyn wirtualnych można określić istniejącą grupę hostów, aby wszystkie wystąpienia maszyn wirtualnych były tworzone na dedykowanych hostach.

Podczas tworzenia zestawu skalowania maszyn wirtualnych w dedykowanej grupie hostów obowiązują następujące wymagania:

- Automatyczne umieszczanie maszyny wirtualnej musi być włączone.
- Ustawienie dostępności grupy hostów powinno być zgodne z zestawem skalowania. 
    - Regionalna Grupa hostów (utworzona bez określenia strefy dostępności) powinna być używana na potrzeby regionalnych zestawów skalowania.
    - Grupa hostów i zestaw skalowania muszą używać tej samej strefy dostępności. 
    - Liczba domen błędów dla poziomu grupy hostów powinna być zgodna z liczbą domen błędów dla zestawu skalowania. Azure Portal umożliwia określenie *maksymalnego rozproszenia* dla zestawu skalowania, który ustawia liczbę domen błędów wynoszącą 1.
- Należy najpierw utworzyć dedykowane hosty z wystarczającą pojemnością oraz te same ustawienia dla stref zestawów skalowania i domen błędów.
- Obsługiwane rozmiary maszyn wirtualnych dla dedykowanych hostów powinny być zgodne z tymi, które są używane dla zestawu skalowania.

Nie wszystkie ustawienia aranżacji i optymalizacji zestawu skalowania są obsługiwane przez dedykowane hosty. Zastosuj następujące ustawienia do zestawu skalowania: 
- Wyłącz nadmierne Inicjowanie obsługi administracyjnej.
- Korzystanie z trybu aranżacji ScaleSetVM 
- Nie używaj grup umieszczania bliskości dla wspólnej lokalizacji



## <a name="maintenance-control"></a>Sterowanie konserwacją

Infrastruktura obsługująca maszyny wirtualne może być czasami aktualizowana w celu poprawy niezawodności, wydajności, zabezpieczeń i uruchamiania nowych funkcji. Platforma Azure próbuje zminimalizować wpływ konserwacji na platformę, jeśli jest to możliwe, ale klienci, którzy mają *wrażliwe* obciążenia, nie mogą tolerować nawet kilka sekund, aby maszyna wirtualna mogła zostać zamrożona lub odłączona do konserwacji.

**Kontrola konserwacji** udostępnia klientom opcję pomijania regularnych aktualizacji platformy zaplanowanych na ich dedykowanych hostach, a następnie zastosuje ją w wybranym momencie w 35 dziennym oknie. W oknie obsługi można zastosować konserwację bezpośrednio na poziomie hosta w dowolnej kolejności. Gdy okno obsługi zostanie przełączone, firma Microsoft przejdzie do przodu i zastosuje oczekującą konserwację do hostów w kolejności, która może nie być zgodna z domenami błędów zdefiniowanymi przez użytkownika.

Aby uzyskać więcej informacji, zobacz [Zarządzanie aktualizacjami platform przy użyciu sterowania konserwacją](./maintenance-control.md).

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

Po aprowizacji dedykowanego hosta platforma Azure przypisze ją do serwera fizycznego. Gwarantuje to dostępność pojemności, gdy trzeba zainicjować obsługę administracyjną maszyny wirtualnej. Platforma Azure używa całej pojemności w regionie (lub strefie), aby wybrać serwer fizyczny dla hosta. Oznacza to również, że klienci mogą być w stanie rozwijać dedykowane hosty bez obaw o wykorzystaniu miejsca w klastrze.

## <a name="quotas"></a>Przydziały

Istnieją dwa typy przydziałów, które są używane podczas wdrażania dedykowanego hosta.

1. Dedykowany limit przydziału vCPU hosta. Domyślny limit przydziału to 3000 procesorów wirtualnych vCPU, na region.
1. Przydział rodziny rozmiarów maszyn wirtualnych. Na przykład subskrypcja z **opcją płatność zgodnie z rzeczywistym** użyciem może mieć przydział 10 procesorów wirtualnych vCPU dostępny dla serii rozmiaru Dsv3 w regionie Wschodnie stany USA. Aby wdrożyć dedykowanego hosta Dsv3, należy poprosić o zwiększenie limitu przydziału na co najmniej 64 procesorów wirtualnych vCPU, zanim będzie można wdrożyć dedykowanego hosta. 

Aby zażądać zwiększenia limitu przydziału, Utwórz żądanie obsługi w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Udostępnienie dedykowanego hosta będzie wymagało użycia dedykowanego vCPU hosta i przydziału rodziny maszyn wirtualnych vCPU, ale nie będzie on korzystał z regionu vCPU.


![Zrzut ekranu strony użycie i przydziały w portalu](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Aby uzyskać więcej informacji, zobacz [przydziały maszyn wirtualnych vCPU](./windows/quotas.md).

Bezpłatna wersja próbna i subskrypcje MSDN nie mają przydziału dla dedykowanych hostów platformy Azure.

## <a name="pricing"></a>Cennik

Opłaty za użytkowników są naliczone za dedykowanego hosta, niezależnie od liczby wdrożonych maszyn wirtualnych. W comiesięcznym zestawie danych zostanie wyświetlony nowy typ zasobów do rozliczania. Maszyny wirtualne na dedykowanym hoście nadal będą widoczne w twoim zestawie, ale cena będzie równa 0.

Cena hosta jest ustawiana na podstawie rodziny maszyn wirtualnych, typu (rozmiaru sprzętu) i regionu. Cena hosta jest określana względem największego rozmiaru maszyny wirtualnej obsługiwanego na hoście.

Opłaty za korzystanie z licencjonowania oprogramowania, magazynu i sieci są rozliczane oddzielnie od hosta i maszyn wirtualnych. Nie wprowadzono zmian w tych elementach rozliczanych.

Aby uzyskać więcej informacji, zobacz [Cennik dedykowanego hosta platformy Azure](https://aka.ms/ADHPricing).

Można także zaoszczędzić na kosztach przy użyciu [zarezerwowanego wystąpienia dedykowanych hostów platformy Azure](prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Rozmiary i generacja sprzętu

Jednostka SKU jest definiowana dla hosta i reprezentuje serię i typ rozmiaru maszyny wirtualnej. Można mieszać wiele maszyn wirtualnych o różnych rozmiarach w ramach jednego hosta, o ile mają one taką samą serię rozmiarów. 

*Typ* to generacja sprzętowa. Różne typy sprzętu dla tej samej serii maszyn wirtualnych będą należeć od różnych dostawców procesora CPU i mają różne generacji procesora CPU oraz liczbę rdzeni. 

Rozmiary i typy sprzętu różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie cennika](https://aka.ms/ADHPricing) hosta.


## <a name="host-life-cycle"></a>Cykl życia hosta


Platforma Azure monitoruje stan kondycji hostów i zarządza nim. Podczas wykonywania zapytania dotyczącego hosta zostaną zwrócone następujące stany:

| Stan kondycji   | Opis       |
|----------|----------------|
| Dostępne hosty     | Nie ma żadnych znanych problemów z hostem.   |
| Host objęty badaniem  | Mamy problemy z hostem, do którego chcemy. Jest to stan przejściowy wymagany przez platformę Azure do wypróbowania i zidentyfikowania zakresu oraz głównej przyczyny zidentyfikowanego problemu. Może to mieć wpływ na maszyny wirtualne działające na hoście. |
| Host oczekujący na cofnięcie alokacji   | Platforma Azure nie może przywrócić kondycji hosta z powrotem do stanu prawidłowego i poprosił o ponowne wdrożenie maszyn wirtualnych poza tym hostem. Jeśli `autoReplaceOnFailure` Ta funkcja jest włączona, Twoje maszyny *service healed* wirtualne są w dobrej kondycji. W przeciwnym razie maszyna wirtualna może działać na hoście, który kończy się niepowodzeniem.|
| Cofnięto przydział hosta  | Wszystkie maszyny wirtualne zostały usunięte z hosta. Nie są już naliczane opłaty za tego hosta, ponieważ sprzęt nie został przetworzony.   |


## <a name="next-steps"></a>Następne kroki

- Dedykowany Host można wdrożyć przy użyciu [Azure PowerShell](./windows/dedicated-hosts-powershell.md), [portalu](./dedicated-hosts-portal.md)i [interfejsu wiersza polecenia platformy Azure](./linux/dedicated-hosts-cli.md).

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.

- Można także zaoszczędzić na kosztach przy użyciu [zarezerwowanego wystąpienia dedykowanych hostów platformy Azure](prepay-dedicated-hosts-reserved-instances.md).