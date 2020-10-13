---
title: Wdróż horyzont w rozwiązaniu VMware platformy Azure
description: Dowiedz się, jak wdrożyć platformę VMware w rozwiązaniu VMware platformy Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: bda4be049e360670cb7038bfbb3070c2a5f262c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729053"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Wdróż horyzont w rozwiązaniu VMware platformy Azure 

>[!NOTE]
>Ten dokument koncentruje się na produkcie VMware Horizon. Jest to dawniej znany jako horyzont 7 przed zmianą nazwy produktu z zakresu od 7 do horyzontu. Horyzonty to inne rozwiązanie niż w chmurze w systemie Azure, chociaż istnieją pewne składniki udostępnione. Najważniejsze zalety rozwiązania VMware platformy Azure obejmują zarówno uproszczenie metody ustalania rozmiarów, jak i zarządzanie programem VMware Cloud Foundation do Azure Portal.

Program [VMware Horizon](https://www.vmware.com/products/horizon.html)® to platforma wirtualna i aplikacje, które działają w centrum danych i udostępniają proste i scentralizowane zarządzanie. Udostępnia ona wirtualne pulpity i aplikacje użytkownikom końcowym na dowolnym urządzeniu, w dowolnym miejscu. Horyzonty umożliwiają tworzenie i brokerowanie połączeń z pulpitami wirtualnymi systemu Windows, pulpitami wirtualnymi z systemem Linux, hostowanymi aplikacjami Pulpit zdalny Server (RDS), komputerami stacjonarnymi i maszynami fizycznymi.

W tym miejscu koncentrujemy się na rozmieszczeniu rozwiązań VMware na platformie Azure. Aby uzyskać ogólne informacje na temat zakresu VMware, zapoznaj się z dokumentacją produkcyjną horyzontu:

* [Co to jest horyzonty VMware?](https://www.vmware.com/products/horizon.html)

* [Dowiedz się więcej o usłudze VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Architektura referencyjna dla zakresu](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Wraz z wprowadzeniem do usługi Azure VMware na platformie Azure istnieją teraz dwa rozwiązania infrastruktury pulpitu wirtualnego (VDI). Poniższy diagram podsumowuje kluczowe różnice na wysokim poziomie.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Różnice między horyzontem na platformie Azure VMware a chmurą horyzontu na platformie Azure" border="false":::

Horyzonty 2006 i nowsze wersje w wierszu wydania w ramach horyzontu 8 obsługują zarówno wdrożenie lokalne, jak i wdrażanie rozwiązań VMware na platformie Azure. Istnieje kilka funkcji, które są obsługiwane lokalnie, ale nie w rozwiązaniu VMware platformy Azure. Obsługiwane są również dodatkowe produkty w ekosystemie horyzontu. Aby uzyskać więcej informacji, zobacz Dostępność [funkcji i współdziałanie](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Wdróż horyzont w chmurze hybrydowej

Możesz wdrożyć horyzont w środowisku chmury hybrydowej, gdy korzystasz z architektury "Architecture w chmurze" (CPA) do łączenia lokalnych centrów danych i centrów dane platformy Azure. CPA jest zwykle używany do skalowania w górę wdrożenia, tworzenia chmury hybrydowej i zapewnienia nadmiarowości dla ciągłości biznesowej i odzyskiwania po awarii. Szczegółowe omówienie wskazówek związanych z ciągłością biznesową oprogramowania VMware — zobacz [rozszerzanie istniejących środowisk z horyzontem 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA nie jest rozciągniętym wdrożeniem; Każdy zakres w zakresie jest różny, a wszystkie serwery połączeń, które należą do każdego z poszczególnych zasobników, muszą znajdować się w jednej lokalizacji i działać w tej samej domenie emisji z perspektywy sieci.

Jak lokalnie lub w prywatnym centrum danych, horyzont można wdrożyć w chmurze prywatnej rozwiązania Azure VMware. W poniższych sekcjach omówiono kluczowe różnice w wdrażaniu horyzontów lokalnych i w rozwiązaniach VMware platformy Azure.

Chmurę prywatną platformy Azure jest koncepcyjnie taka sama jak w przypadku programu VMware SDDC, termin zwykle używany w dokumentacji horyzontu. Pozostała część tego dokumentu będzie korzystać z postanowień w chmurze prywatnej platformy Azure i oprogramowaniu VMware SDDC.

Aby zarządzać licencjami subskrypcji, wymagany jest łącznik usługi w chmurze w usłudze Horizon w ramach rozwiązania Azure VMware. Łącznik chmury można wdrożyć na platformie Azure Virtual Network obok serwerów połączeń w systemie.

>[!IMPORTANT]
>Nie jest jeszcze dostępna obsługa warstwy sterującej horyzontem w rozwiązaniu na platformie Azure VMware. Pamiętaj, aby pobrać wersję wirtualnego dysku twardego z poziomu łącznika usługi w chmurze.

## <a name="vcenter-cloud-admin-role"></a>rola administratora usługi vCenter Cloud

Ponieważ rozwiązanie Azure VMware jest usługą SDDC, a platforma Azure zarządza cyklem życia SDDC na platformie Azure VMware, model uprawnień vCenter w rozwiązaniu VMware platformy Azure jest ograniczony przez projektowanie.

Klienci są zobowiązani do korzystania z roli administratora chmury, która ma ograniczony zestaw uprawnień programu vCenter. Produkt horyzontu został zmodyfikowany, aby współpracował z rolą administratora chmury w rozwiązaniu VMware platformy Azure, w tym:

* Funkcja natychmiastowego klonowania została zmodyfikowana tak, aby była uruchamiana w rozwiązaniu VMware platformy Azure.

* Określone zasady sieci vsanymi (VMware_Horizon) zostały utworzone w rozwiązaniu VMware platformy Azure do pracy z horyzontem, który musi być dostępny i używany w SDDCs wdrożonym dla zakresu.

* vSphere pamięć podręczna odczytu oparta na zawartości (CBRC), znana także jako akcelerator magazynu widoku, jest wyłączona w przypadku uruchamiania w rozwiązaniu VMware platformy Azure.

>[!IMPORTANT]
>CBRC nie może być ponownie włączona.

>[!NOTE]
>Rozwiązanie VMware firmy Azure automatycznie konfiguruje określone ustawienia zakresu, tak długo, jak w przypadku wdrażania horyzontu 2006 (w polu "liczba zakresu 8) i powyżej" w gałęzi horyzontu 8 i wybierania opcji **platformy Azure** w instalatorze serwera połączeń horyzontu.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Architektura wdrażania rozwiązań VMware na platformie Azure

Typowy projekt architektury horyzontu używa strategii pod i bloku. Blok jest pojedynczym vCenter, podczas gdy wiele bloków łączy się pod. Horyzonty od jest jednostką organizacji określoną według ograniczeń skalowalności. Każdy horyzont pod ma oddzielny Portal zarządzania i dlatego standardowa metoda projektowania ma zminimalizować liczbę zasobników.

Każda chmura ma własny schemat łączności sieciowej. W połączeniu z maszyną VMware SDDC Networking/NSX Edge połączenie sieciowe rozwiązań VMware platformy Azure przedstawia unikatowe wymagania dotyczące wdrażania horyzontów, które różnią się od lokalnych.

Każda chmura prywatna platformy Azure/SDDC jest w stanie obsłużyć 4 000 sesji pulpitu lub aplikacji, co zakłada następujące kwestie:

* Ruch obciążeń jest wyrównany do profilu procesu roboczego zadania LoginVSI.

* Tylko ruch sieciowy jest brany pod uwagę, bez danych użytkownika.

* NSX Edge jest skonfigurowany jako duży.

>[!NOTE]
>Twój profil obciążenia i potrzeby mogą być różne, dlatego wyniki mogą się różnić w zależności od przypadku użycia. Woluminy danych użytkownika mogą obniżyć limity skalowania w kontekście obciążenia. Odpowiednio zmień rozmiar i Zaplanuj wdrożenie. Aby uzyskać więcej informacji, zobacz Wskazówki dotyczące ustalania rozmiaru w sekcji [rozmiaru hosty rozwiązań platformy Azure VMware dla wdrożeń](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

W przypadku maksymalnego limitu chmury prywatnej platformy Azure/SDDC zalecamy architekturę wdrażania, w której serwery połączeń i usługi VMware Unified Access Gateway (UAGs) działają w ramach Virtual Network platformy Azure. Dzięki temu każdy chmurę prywatną platformy Azure/SDDC w bloku. To z kolei maksymalizuje skalowalność usługi Horizon działającej w rozwiązaniu VMware platformy Azure.

Połączenie między usługą Azure Virtual Network a chmurami prywatnymi platformy Azure/SDDCs należy skonfigurować przy użyciu ExpressRoute FastPath. Na poniższym diagramie przedstawiono wdrożenie podstawowego horyzontu.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Różnice między horyzontem na platformie Azure VMware a chmurą horyzontu na platformie Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Połączenie sieciowe z funkcją skalowania na platformie Azure VMware

W tej sekcji przedstawiono architekturę sieci na wysokim poziomie na potrzeby skalowania rozwiązania VMware na platformie Azure z Typowymi przykładami wdrożenia. Fokus jest tutaj przeznaczony dla najważniejszych elementów sieciowych.

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pojedynczy zakres na platformie Azure VMware

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Różnice między horyzontem na platformie Azure VMware a chmurą horyzontu na platformie Azure" border="false":::

Pojedynczym horyzontem w zakresie jest najbardziej prosty scenariusz wdrażania do przodu. W tym przykładzie użytkownik zdecyduje się na wdrożenie tylko jednego zakresu w regionie Wschodnie stany USA. Ponieważ każdą chmurę prywatną/SDDCą szacuje się na około obsłudze ruchu komputerów stacjonarnych 4 000, można zaplanować wdrożenie maksymalnie trzech chmur prywatnych/SDDCs.

Dlatego w tym przykładzie, w połączeniu z maszynami wirtualnymi z infrastrukturą procesora (VM) wdrożonymi w usłudze Azure Virtual Network, można dotrzeć do 12 000 sesji na horyzonty na podstawie obciążenia i potrzeb dotyczących danych. Połączenie między poszczególnymi chmurami prywatnymi i SDDC z platformą Azure Virtual Network to ExpressRoute szybka ścieżka, w której nie jest wymagany ruch wschodni-zachodni między chmurami prywatnymi.

Kluczowe założenia dla tego podstawowego przykładu wdrożenia są następujące:

* Nie masz lokalnego horyzontu, z którym chcesz nawiązać połączenie z tym nowym systemem, przy użyciu architektury Cloud pod (CPA).

* Użytkownicy końcowi łączą się z pulpitami wirtualnymi za pośrednictwem Internetu (a łącząc się za pośrednictwem lokalnego centrum danych).

W tym podstawowym przykładzie można podłączyć kontroler domeny usługi AD na platformie Azure Virtual Network z lokalnym Active Directory za pośrednictwem sieci VPN lub obwodu usługi ExpressRoute.

W omawianym przykładowym przykładzie można zapewnić obsługę łączności zasobów lokalnych. Może to oznaczać, że użytkownicy uzyskują dostęp do komputerów stacjonarnych i generują ruch aplikacji klasycznych lub łącząc się z lokalnym zakresem, przy użyciu CPA.

Na poniższym diagramie pokazano, jak to zrobić.Aby połączyć sieć firmową z usługą Azure Virtual Network, musisz mieć ExpressRoute.Należy również połączyć sieć firmową z każdą chmurą prywatną/SDDCs przy użyciu Global Reach, która umożliwia łączność z SDDC z zasobami ExpressRoute i lokalnymi.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Różnice między horyzontem na platformie Azure VMware a chmurą horyzontu na platformie Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Wiele zasobników horyzontów na platformie Azure VMware w wielu regionach

W przypadku innego zakresu pod przykładem Przyjrzyjmy się przykładowi, który pokazuje skalowanie na wiele zasobników.W tym przykładzie wdrażasz dwa horyzonty użycia w dwóch różnych regionach i federowanie je przy użyciu CPA.Konfiguracja sieci jest taka sama jak w poprzednim przykładzie, a niektóre dodatkowe linki międzyregionalne. 

Musisz połączyć Virtual Network platformy Azure w każdym regionie z chmurami prywatnymi/SDDCs w innym regionie, umożliwiając serwerom połączeń horyzontów należących do Federacji CPA łączenie się ze wszystkimi pulpitami w obszarze zarządzania.Dodanie dodatkowych chmur prywatnych/SDDCs do tej konfiguracji umożliwi skalowanie do 24 000 sesji ogółem. 

Chociaż ten przykład pokazuje wiele regionów, ta sama zasada zostałaby zastosowana, jeśli chcesz wdrożyć dwa horyzonty w tym samym regionie. Należy pamiętać, że należy upewnić się, że drugie horyzonty pod jest wdrożone w *osobnym Virtual Network platformy Azure*.Na koniec, podobnie jak w przypadku poprzedniego przykładu, możesz połączyć sieć firmową i lokalne miejsce do tego przykładu wieloskładnikowego/regionu, korzystając z ExpressRoute klienta i Global Reach.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Różnice między horyzontem na platformie Azure VMware a chmurą horyzontu na platformie Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Rozmiar hostów rozwiązań platformy Azure VMware na potrzeby wdrożeń z horyzontem 

Metodologia ustalania wielkości dla hosta działającego na platformie Azure VMware jest łatwiejsza niż w środowisku lokalnym od momentu, gdy wystąpienie hosta rozwiązania VMware platformy Azure jest znormalizowane. Dokładne ustalanie wielkości hosta pomoże określić liczbę hostów potrzebnych do obsługi wymagań infrastruktury VDI i jest podstawą do określenia kosztu poszczególnych komputerów.

### <a name="azure-vmware-solution-host-instance"></a>Wystąpienie hosta rozwiązania VMware platformy Azure

* Serwer PowerEdge R640-DSS z OGRANICZENIAmi

* 36 rdzenie \@ 2,3 GHz

* 576 GB PAMIĘCI RAM

* Kontroler HBA SAS HBA330 12 GB/s (nie RAID)

* 1,92 TB 10512 dysków SSD SATA mix użycie 6 GB/s 512 2,5 w gorącą

* Intel 1,6 TB, NVMe, mieszane użycie Express Flash, 2,5 SFF dysk, U. 2, P4600 z operatorem

* 2 sieci vSAN grupy dysków: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Dane wejściowe zmiany zakresu

Zaplanuj zaplanowane obciążenie:

* Liczba współbieżnych komputerów stacjonarnych

* Wymagane vCPU na pulpit

* Wymagana pamięć vRAM na komputer stacjonarny

* Wymagany magazyn na pulpit

Ogólnie rzecz biorąc, wdrożenia infrastruktury VDI są ograniczone przez procesor lub pamięć RAM, ponieważ te czynniki określają rozmiar hosta. Przyjrzyjmy się następującemu przykładowi do typu procesu roboczego wiedzy LoginVSI, zweryfikowanego z testowaniem wydajności:

* 2 000 współbieżne wdrożenie pulpitu

* 2vCPU na pulpit.

* 4 GB pamięci vRAM na komputer stacjonarny.

* 50 GB przestrzeni dyskowej na pulpit

W tym przykładzie całkowita liczba hostów jest równa 18, co zapewnia gęstość maszyny wirtualnej na hosta 111.

>[!IMPORTANT]
>Obciążenia klientów różnią się w zależności od tego przykładu pracownika LoginVSI Knowledge. W ramach planowania wdrożenia programu należy współpracować z oprogramowaniem VMware EUC SEs, aby określić konkretne wymagania dotyczące rozmiarów i wydajności. Należy pamiętać, aby uruchomić własne testy wydajnościowe przy użyciu rzeczywistego, zaplanowanego obciążenia przed finalizowaniem zmiany rozmiarów hosta i odpowiednio dostosować.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Zakres licencjonowania rozwiązań VMware na platformie Azure 

Istnieją cztery składniki, które łączą się z ogólnymi kosztami uruchamiania w rozwiązaniach VMware platformy Azure. 

### <a name="azure-vmware-solution-capacity-cost"></a>Koszt pojemności rozwiązań VMware na platformie Azure

Aby uzyskać informacje o cenach, zobacz stronę z [cennikiem rozwiązań VMware platformy Azure](https://azure.microsoft.com/pricing/details/azure-vmware/) .

### <a name="horizon-licensing-cost"></a>Koszt licencjonowania wg horyzontu

Istnieją dwie dostępne licencje do użycia z rozwiązaniem VMware platformy Azure, które mogą być jednocześnie użytkownikami (CCU) lub nazwanymi użytkownikami (ni):

* Licencja na subskrypcję usługi Horizon

* Licencja na uniwersalną subskrypcję

Jeśli w przyszłości będzie można wdrożyć tylko horyzont na platformie Azure VMware, użyj licencji subskrypcyjnej na horyzonty, ponieważ jest to niższy koszt.

W przypadku wdrażania obu horyzontów na platformie Azure VMware i lokalnie, jak w przypadku użycia odzyskiwania po awarii, wybierz licencję uniwersalną w ramach architektury. Licencja uniwersalna to wyższy koszt, ponieważ obejmuje licencję vSphere dla wdrożenia lokalnego.

Skontaktuj się z zespołem sprzedaży VMware EUC, aby określić koszt licencjonowania w zależności od potrzeb.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Koszt maszyn wirtualnych infrastruktury horyzontu na platformie Azure Virtual Network

Na podstawie standardowej architektury wdrażania maszyny wirtualne infrastruktury horyzontu składają się z serwerów połączeń, UAGs, menedżerów woluminów aplikacji i są wdrażane w Virtual Network na platformie Azure klienta. Dodatkowe wystąpienia natywne platformy Azure są wymagane do obsługi usług wysokiej dostępności (HA), Microsoft SQL lub Microsoft Active Directory (AD) na platformie Azure. Poniżej znajduje się lista wystąpień platformy Azure opartych na przykładowym wdrożeniu 2 000-Desktop. 

| Składnik infrastruktury horyzontu | Wystąpienie platformy Azure | Wymagana liczba wystąpień (dla 2 000-komputerów stacjonarnych)    | Komentarz  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Serwer połączeń                | D4sv3          | 2       | *Zawiera 1 wystąpienie dla HA*             |    
| UAG                              | F2sv2          | 2       | *Zawiera 1 wystąpienie dla HA*             |
| Menedżer woluminów aplikacji              | D4sv3          | 2       | *Zawiera 1 wystąpienie dla HA*             |
| Łącznik Cloud                  | D4sv3          | 1       |                                          |
| Kontroler usługi AD                    | D4sv3          | 2       | *Opcja używania usługi AD MSFT na platformie Azure* |
| MS-SQL Database                  | D4sv3          | 2       | *Opcja korzystania z usługi SQL na platformie Azure*     |
| Udział plików systemu Windows               | D4sv3          |         | *Opcjonalne*                               |

Koszt maszyny wirtualnej infrastruktury wynosi \$ 0,36 za użytkownika miesięcznie dla wdrożenia 2 000-pulpitu w powyższym przykładzie. Należy zwrócić uwagę, że w tym przykładzie wykorzystano Cennik dla wystąpienia USA platformy Azure od czerwca 2020. Ceny mogą się różnić w zależności od regionu, wybranych opcji i chronometrażu.
