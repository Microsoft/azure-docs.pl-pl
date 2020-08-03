---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 691293d0f7ecf5bb2ad83a3f292ad2c9b873e31e
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386536"
---
## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyn wirtualnych nie są obecnie obsługiwane na dedykowanych hostach.

## <a name="benefits"></a>Korzyści 

Rezerwowanie całego hosta zapewnia następujące korzyści:

-   Izolacja sprzętowa na poziomie serwera fizycznego. Żadne inne maszyny wirtualne nie zostaną umieszczone na hostach. Dedykowane hosty są wdrażane w tych samych centrach danych i korzystają z tej samej sieci i podstawowej infrastruktury magazynu, co inne, nieizolowane hosty.
-   Kontrola nad zdarzeniami konserwacji zainicjowanymi przez platformę Azure. Chociaż większość zdarzeń konserwacji nie ma wpływu na maszyny wirtualne, istnieją pewne wrażliwe obciążenia, w przypadku których każda sekunda wstrzymania może mieć wpływ. Za pomocą dedykowanych hostów możesz wybrać okno obsługi, aby zmniejszyć wpływ na usługę.
-   Korzyść używania hybrydowego platformy Azure umożliwia korzystanie z własnych licencji dla systemów Windows i SQL na platformie Azure. Korzystanie z zalet hybrydowych zapewnia dodatkowe korzyści. Aby uzyskać więcej informacji, zobacz [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupy, hosty i maszyny wirtualne  

![Widok nowych zasobów dla dedykowanych hostów.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Grupa hostów** jest zasobem, który reprezentuje kolekcję dedykowanych hostów. Można utworzyć grupę hostów w regionie i strefie dostępności, a następnie dodać do niej hosty.

**Host** jest zasobem zamapowanym na serwer fizyczny w centrum danych platformy Azure. Serwer fizyczny jest przypisywany podczas tworzenia hosta. Host jest tworzony w obrębie grupy hostów. Host ma jednostkę SKU opisującą, które rozmiary maszyn wirtualnych można utworzyć. Każdy host może obsługiwać wiele maszyn wirtualnych o różnych rozmiarach, o ile są one z tej samej serii rozmiarów.

Podczas tworzenia maszyny wirtualnej na platformie Azure można wybrać dedykowanego hosta do użycia dla maszyny wirtualnej. Masz pełną kontrolę nad tym, które maszyny wirtualne są umieszczane na hostach.


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

## <a name="maintenance-control"></a>Sterowanie konserwacją

Infrastruktura obsługująca maszyny wirtualne może być czasami aktualizowana w celu poprawy niezawodności, wydajności, zabezpieczeń i uruchamiania nowych funkcji. Platforma Azure próbuje zminimalizować wpływ konserwacji na platformę, jeśli jest to możliwe, ale klienci, którzy mają *wrażliwe* obciążenia, nie mogą tolerować nawet kilka sekund, aby maszyna wirtualna mogła zostać zamrożona lub odłączona do konserwacji.

**Kontrola konserwacji** udostępnia klientom opcję pomijania regularnych aktualizacji platformy zaplanowanych na ich dedykowanych hostach, a następnie zastosuje ją w wybranym momencie w 35 dziennym oknie.

Aby uzyskać więcej informacji, zobacz [Zarządzanie aktualizacjami platform przy użyciu sterowania konserwacją](https://docs.microsoft.com/azure/virtual-machines/maintenance-control).

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

Po aprowizacji dedykowanego hosta platforma Azure przypisze ją do serwera fizycznego. Gwarantuje to dostępność pojemności, gdy trzeba zainicjować obsługę administracyjną maszyny wirtualnej. Platforma Azure używa całej pojemności w regionie (lub strefie), aby wybrać serwer fizyczny dla hosta. Oznacza to również, że klienci mogą być w stanie rozwijać dedykowane hosty bez obaw o wykorzystaniu miejsca w klastrze.

## <a name="quotas"></a>Przydziały

Istnieją dwa typy przydziałów, które są używane podczas wdrażania dedykowanego hosta.

1. Dedykowany limit przydziału vCPU hosta. Domyślny limit przydziału to 3000 procesorów wirtualnych vCPU, na region.
1. Przydział rodziny rozmiarów maszyn wirtualnych. Na przykład subskrypcja z **opcją płatność zgodnie z rzeczywistym** użyciem może mieć przydział 10 procesorów wirtualnych vCPU dostępny dla serii rozmiaru Dsv3 w regionie Wschodnie stany USA. Aby wdrożyć dedykowanego hosta Dsv3, należy poprosić o zwiększenie limitu przydziału na co najmniej 64 procesorów wirtualnych vCPU, zanim będzie można wdrożyć dedykowanego hosta. 

Aby zażądać zwiększenia limitu przydziału, Utwórz żądanie obsługi w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Udostępnienie dedykowanego hosta będzie wymagało użycia dedykowanego vCPU hosta i przydziału rodziny maszyn wirtualnych vCPU, ale nie będzie on korzystał z regionu vCPU.


![Zrzut ekranu strony użycie i przydziały w portalu](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Aby uzyskać więcej informacji, zobacz [przydziały maszyn wirtualnych vCPU](/azure/virtual-machines/windows/quotas).

Bezpłatna wersja próbna i subskrypcje MSDN nie mają przydziału dla dedykowanych hostów platformy Azure.

## <a name="pricing"></a>Cennik

Opłaty za użytkowników są naliczone za dedykowanego hosta, niezależnie od liczby wdrożonych maszyn wirtualnych. W comiesięcznym zestawie danych zostanie wyświetlony nowy typ zasobów do rozliczania. Maszyny wirtualne na dedykowanym hoście nadal będą widoczne w twoim zestawie, ale cena będzie równa 0.

Cena hosta jest ustawiana na podstawie rodziny maszyn wirtualnych, typu (rozmiaru sprzętu) i regionu. Cena hosta jest określana względem największego rozmiaru maszyny wirtualnej obsługiwanego na hoście.

Opłaty za korzystanie z licencjonowania oprogramowania, magazynu i sieci są rozliczane oddzielnie od hosta i maszyn wirtualnych. Nie wprowadzono zmian w tych elementach rozliczanych.

Aby uzyskać więcej informacji, zobacz [Cennik dedykowanego hosta platformy Azure](https://aka.ms/ADHPricing).

Można także zaoszczędzić na kosztach przy użyciu [zarezerwowanego wystąpienia dedykowanych hostów platformy Azure](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
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

