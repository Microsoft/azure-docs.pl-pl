---
title: Konserwacja i aktualizacje
description: Przegląd konserwacji i aktualizacji maszyn wirtualnych działających na platformie Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 51df72e31acaadc83f4c094b99fa938377e5f023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500006"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Konserwacja maszyn wirtualnych na platformie Azure

Platforma Azure okresowo aktualizuje swoją platformę, aby zwiększyć niezawodność, wydajność i bezpieczeństwo infrastruktury hosta dla maszyn wirtualnych. Zakres aktualizacji obejmuje tematykę od poprawek składników oprogramowania w środowisku hostingu do uaktualniania składników sieci lub likwidowania sprzętu. 

Aktualizacje rzadko wpływają na hostowane maszyny wirtualne. Gdy aktualizacje mają wpływ, na platformie Azure wybierana jest najmniejsza dostosowana Metoda aktualizacji:

- Jeśli aktualizacja nie wymaga ponownego uruchomienia komputera, maszyna wirtualna jest wstrzymana podczas aktualizowania hosta lub maszyna wirtualna jest migrowana na żywo do już zaktualizowanego hosta. 
- Jeśli konserwacja wymaga ponownego uruchomienia, zostanie powiadomiona o planowanej konserwacji. Platforma Azure udostępnia również przedział czasu, w którym można samodzielnie uruchomić konserwację, w odpowiednim czasie. Okno samoobsługowe jest zwykle 35 dni, chyba że konserwacji są pilne. Platforma Azure jest inwestować w technologie, aby ograniczyć liczbę przypadków, w których konserwacja planowanej platformy wymaga ponownego uruchomienia maszyn wirtualnych. Aby uzyskać instrukcje dotyczące zarządzania zaplanowaną konserwacją, zobacz Obsługa planowanych powiadomień konserwacyjnych za pomocą [interfejsu wiersza polecenia](maintenance-notifications-cli.md)platformy Azure, [programu PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md).

Na tej stronie opisano, jak platforma Azure wykonuje oba typy konserwacji. Aby uzyskać więcej informacji o nieplanowanych zdarzeniach (przestoju), zobacz [Zarządzanie dostępnością maszyn wirtualnych w systemie Windows](./availability.md) lub odpowiednim artykule dla systemu [Linux](./availability.md).

W ramach maszyny wirtualnej można otrzymywać powiadomienia o nadchodzącej konserwacji przy [użyciu Scheduled Events dla systemu Windows](./windows/scheduled-events.md) lub [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Konserwacja, która nie wymaga ponownego uruchomienia

Większość aktualizacji platformy nie ma wpływu na maszyny wirtualne klientów. Jeśli aktualizacja nie jest możliwa, platforma Azure wybiera mechanizm aktualizacji, który jest najmniej wpływem na maszyny wirtualne klientów. 

Większość konserwacji niezerowego wpływu wstrzymuje maszynę wirtualną przez mniej niż 10 sekund. W niektórych przypadkach platforma Azure używa mechanizmów konserwacji z zachowaniem pamięci. Te mechanizmy wstrzymują maszynę wirtualną przez maksymalnie 30 sekund i zachowują pamięć w pamięci RAM. Następnie maszyna wirtualna jest wznowiona i zegar jest automatycznie synchronizowany. 

Konserwacja pamięci jest zachowywana przez ponad 90 procent maszyn wirtualnych platformy Azure. Nie działa dla serii G, M, N i H. Platforma Azure w coraz większym stopniu używa technologii migracji na żywo i zwiększa możliwości konserwacji pamięci, aby skrócić czas trwania przerwy.  

Te operacje konserwacji, które nie wymagają ponownego uruchomienia, są stosowane w jednej domenie błędów jednocześnie. Są one zatrzymane, jeśli otrzymają ostrzeżenia dotyczące kondycji z narzędzi do monitorowania platformy. 

Te typy aktualizacji mogą mieć wpływ na niektóre aplikacje. Gdy maszyna wirtualna jest migrowana na żywo na inny host, niektóre wrażliwe obciążenia mogą pokazać niewielki spadek wydajności w ciągu kilku minut, co prowadzi do wstrzymania maszyny wirtualnej. Aby przygotować się do obsługi maszyny wirtualnej i ograniczyć wpływ na konserwację platformy Azure, spróbuj [użyć Scheduled Events dla systemu Windows](./windows/scheduled-events.md) lub [Linux](./linux/scheduled-events.md) dla takich aplikacji. 

Aby uzyskać większą kontrolę nad wszystkimi działaniami konserwacyjnymi, takimi jak negatywny wpływ na aktualizacje i bezrozruchowe, można użyć funkcji kontroli konserwacji. Musisz korzystać z [dedykowanych hostów platformy Azure](./dedicated-hosts.md) lub [izolowanej maszyny wirtualnej](../security/fundamentals/isolation-choices.md). Kontrola konserwacji pozwala pominąć wszystkie aktualizacje platformy i zastosować aktualizacje w wybranym czasie w ciągu dnia 35. Aby uzyskać więcej informacji, zobacz [Kontrolowanie aktualizacji przy użyciu sterowania konserwacją i interfejsu wiersza polecenia platformy Azure](maintenance-control.md).


### <a name="live-migration"></a>Migracja na żywo

Migracja na żywo to operacja, która nie wymaga ponownego uruchomienia i zachowuje pamięć dla maszyny wirtualnej. Powoduje to wstrzymanie lub zablokowanie, zazwyczaj trwające nie więcej niż 5 sekund. Z wyjątkiem serii G, M, N i H, wszystkie maszyny wirtualne IaaS (Infrastructure as a Service) są uprawnione do migracji na żywo. Kwalifikujące się maszyny wirtualne reprezentują ponad 90 procent maszyn wirtualnych IaaS wdrożonych w ramach floty platformy Azure. 

Platforma Azure uruchamia migrację na żywo w następujących scenariuszach:
- Planowana konserwacja
- Awaria sprzętu
- Optymalizacje alokacji

Niektóre scenariusze planowanej konserwacji korzystają z migracji na żywo, a w przypadku uruchamiania operacji migracji na żywo można użyć Scheduled Events.

Migracja na żywo może być również używana do przenoszenia maszyn wirtualnych, gdy algorytmy Azure Machine Learning oczekują nieoczekiwanego błędu sprzętowego lub gdy chcesz zoptymalizować alokacje maszyn wirtualnych. Aby uzyskać więcej informacji na temat modelowania predykcyjnego, które wykrywa wystąpienia sprzętu o obniżonej wydajności, zobacz [ulepszanie odporności maszyny wirtualnej platformy Azure dzięki predykcyjnemu uczenia maszynowego i migracji na żywo](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). W przypadku korzystania z tych usług powiadomienia o migracji na żywo są wyświetlane w Azure Portal na monitorze i w Service Health dziennikach, a także w Scheduled Events.

## <a name="maintenance-that-requires-a-reboot"></a>Konserwacja wymagająca ponownego uruchomienia

W rzadkich przypadkach, gdy maszyny wirtualne wymagają ponownego uruchomienia na potrzeby planowanej konserwacji, zostanie wyświetlone powiadomienie z wyprzedzeniem. Planowana konserwacja ma dwie fazy: fazę samoobsługowa i planowana faza konserwacji.

W *fazie samoobsługowej*, która zazwyczaj trwa cztery tygodnie, rozpoczynasz konserwację na maszynach wirtualnych. W ramach samoobsługowego wysyłania zapytań do każdej maszyny wirtualnej w celu wyświetlenia jej stanu i wyniku ostatniego żądania konserwacji.

Po uruchomieniu konserwacji samoobsługowej maszyna wirtualna zostanie ponownie wdrożona w węźle, który został już zaktualizowany. Ponieważ maszyna wirtualna jest ponownie uruchamiana, dysk tymczasowy zostaje utracony, a dynamiczne adresy IP skojarzone z interfejsem sieci wirtualnej są aktualizowane.

Jeśli wystąpi błąd podczas konserwacji samoobsługowej, operacja zostanie zatrzymana, maszyna wirtualna nie zostanie zaktualizowana i zostanie pobrana opcja ponowienia konserwacji samoobsługowej. 

Po zakończeniu fazy samoobsługowej rozpocznie się *faza zaplanowanej konserwacji* . W tej fazie można nadal wykonywać zapytania dotyczące fazy konserwacji, ale nie można samodzielnie uruchomić konserwacji.

Aby uzyskać więcej informacji na temat zarządzania konserwacją, która wymaga ponownego uruchomienia, zobacz **Obsługa planowanych powiadomień konserwacyjnych** za pomocą [interfejsu wiersza polecenia](maintenance-notifications-cli.md)platformy Azure, [programu PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Zagadnienia dotyczące dostępności podczas zaplanowanej konserwacji 

Jeśli zdecydujesz się na zakończenie fazy zaplanowanej konserwacji, musisz wziąć pod uwagę kilka rzeczy, aby zachować najwyższą dostępność maszyn wirtualnych. 

#### <a name="paired-regions"></a>Sparowane regiony

Każdy region platformy Azure jest sparowany z innym regionem w obrębie tego samego sąsiedztwa geograficznego. Razem tworzą one parę regionów. W fazie zaplanowanej konserwacji platforma Azure aktualizuje tylko maszyny wirtualne w jednym regionie pary regionów. Na przykład podczas aktualizacji maszyny wirtualnej w Północno-środkowe stany USA platforma Azure nie aktualizuje żadnej maszyny wirtualnej w regionie Południowo-środkowe stany USA w tym samym czasie. Inne regiony, takie jak Europa Północna, mogą być jednak w trakcie konserwacji w tym samym czasie, co region Wschodnie stany USA. Zrozumienie, jak działa para regionów, może pomóc w lepszym rozproszeniu maszyn wirtualnych między regionami. Aby uzyskać więcej informacji, zobacz [pary regionów platformy Azure](../best-practices-availability-paired-regions.md).

#### <a name="availability-sets-and-scale-sets"></a>Zestawy dostępności i zestawy skalowania

Podczas wdrażania obciążenia na maszynach wirtualnych platformy Azure można utworzyć maszyny wirtualne w ramach *zestawu dostępności* , aby zapewnić wysoką dostępność aplikacji. Korzystając z zestawów dostępności, można upewnić się, że podczas zdarzenia przestoju lub konserwacji, które wymagają ponownego uruchomienia, dostępna jest co najmniej jedna maszyna wirtualna.

W ramach zestawu dostępności poszczególne maszyny wirtualne są rozłożone w maksymalnie 20 domenach aktualizacji. Podczas zaplanowanej konserwacji w danym momencie aktualizowana jest tylko jedna domena aktualizacji. Domeny aktualizacji nie są zawsze aktualizowane sekwencyjnie. 

*Zestawy skalowania* maszyn wirtualnych to zasób obliczeniowy platformy Azure służący do wdrażania zestawu identycznych maszyn wirtualnych jako jednego zasobu i zarządzania nimi. Zestaw skalowania jest automatycznie wdrażany w ramach tej funkcji, np. maszyn wirtualnych w zestawie dostępności. Podobnie jak w przypadku zestawów dostępności, w przypadku korzystania z zestawów skalowania w danym momencie w trakcie zaplanowanej konserwacji jest aktualizowana tylko jedna UD.

Aby uzyskać więcej informacji o konfigurowaniu maszyn wirtualnych pod kątem wysokiej dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych w systemie Windows](./availability.md) lub odpowiednim artykule dla systemu [Linux](./availability.md).

#### <a name="availability-zones"></a>Strefy dostępności

Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. 

Strefa dostępności jest kombinacją domeny błędów i domeny aktualizacji. Jeśli tworzysz trzy lub więcej maszyn wirtualnych w ramach trzech stref w regionie świadczenia usługi Azure, Twoje maszyny wirtualne są efektywnie dystrybuowane w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

Każda aktualizacja infrastruktury powoduje przeprowadzenie strefy stref w jednym regionie. Można jednak wdrożyć wdrożenie w Strefa 1 i różne wdrożenie, które odbywa się w Strefa 2 w tym samym czasie. Wdrożenia nie są wszystkie serializowane. Jednak pojedyncze wdrożenie powoduje tylko jedną strefę w tym samym czasie w celu zmniejszenia ryzyka.

## <a name="next-steps"></a>Następne kroki 

Możesz użyć [interfejsu wiersza polecenia platformy Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md) , aby zarządzać planowaną konserwacją.