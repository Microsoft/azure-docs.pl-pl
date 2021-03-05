---
title: Wdróż horyzont w rozwiązaniu VMware platformy Azure
description: Dowiedz się, jak wdrożyć platformę VMware w rozwiązaniu VMware platformy Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: c34d0ac7806f8284e893cf3ad4f3c82dd404ff41
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181401"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Wdróż horyzont w rozwiązaniu VMware platformy Azure 

>[!NOTE]
>Ten dokument koncentruje się na produkcie VMware Horizon, wcześniej znanym jako horyzont 7. Horyzonty to inne rozwiązanie niż w chmurze w systemie Azure, chociaż istnieją pewne składniki udostępnione. Najważniejsze zalety rozwiązania VMware platformy Azure obejmują zarówno bardziej prostą metodę ustalania rozmiarów, jak i integrację zarządzania VMware Cloud Foundation z Azure Portal.

Program [VMware Horizon](https://www.vmware.com/products/horizon.html)®, platforma pulpitu wirtualnego i aplikacje, działa w centrum danych i zapewnia proste i scentralizowane zarządzanie. Udostępnia ona wirtualne pulpity i aplikacje na dowolnym urządzeniu, w dowolnym miejscu. Horyzont umożliwia tworzenie i Broker połączeń z pulpitami wirtualnymi z systemami Windows i Linux, hostowanych aplikacji Pulpit zdalny Server (RDS), komputerów stacjonarnych i maszyn fizycznych.

W tym miejscu koncentrujemy się na rozmieszczeniu rozwiązań VMware na platformie Azure. Aby uzyskać ogólne informacje na temat zakresu VMware, zapoznaj się z dokumentacją produkcyjną horyzontu:

* [Co to jest horyzonty VMware?](https://www.vmware.com/products/horizon.html)

* [Dowiedz się więcej o usłudze VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Architektura referencyjna dla zakresu](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Wraz z wprowadzeniem do usługi Azure VMware na platformie Azure istnieją teraz dwa rozwiązania infrastruktury pulpitu wirtualnego (VDI). Poniższy diagram podsumowuje kluczowe różnice na wysokim poziomie.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horyzont na platformie Azure VMware i w chmurze w systemie Azure" border="false":::

Horyzonty 2006 i nowsze wersje w wierszu wydania w ramach horyzontu 8 obsługują zarówno wdrożenie lokalne, jak i wdrażanie rozwiązań VMware na platformie Azure. Istnieje kilka funkcji, które są obsługiwane lokalnie, ale nie w rozwiązaniu VMware platformy Azure. Obsługiwane są również dodatkowe produkty w ekosystemie horyzontu. Aby uzyskać więcej informacji, zobacz Dostępność [funkcji i współdziałanie](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Wdróż horyzont w chmurze hybrydowej

Możesz wdrożyć horyzont w środowisku chmury hybrydowej, gdy korzystasz z architektury w chmurze (CPA) do łączenia się z lokalnymi i centrami danych platformy Azure. CPA skaluje wdrożenie, kompiluje chmurę hybrydową i zapewnia nadmiarowość dla ciągłości działania i odzyskiwania po awarii.  Aby uzyskać więcej informacji, zobacz [rozszerzanie istniejących środowisk z horyzontem 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA nie jest rozciągniętym wdrożeniem; Każdy zakres w zakresie jest różny, a wszystkie serwery połączeń, które należą do każdego z poszczególnych zasobników, muszą znajdować się w jednej lokalizacji i działać w tej samej domenie emisji z perspektywy sieci.

Jak lokalnie lub w prywatnym centrum danych, horyzont można wdrożyć w chmurze prywatnej rozwiązania Azure VMware. W poniższych sekcjach omówiono kluczowe różnice w wdrażaniu horyzontów lokalnych i w rozwiązaniach VMware platformy Azure.

Chmurę prywatną platformy Azure jest koncepcyjnie taka sama jak w przypadku programu VMware SDDC, termin zwykle używany w dokumentacji horyzontu. W pozostałej części tego dokumentu używane są terminy chmury prywatnej platformy Azure i VMware SDDC.

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

Każda chmura prywatna platformy Azure i usługa SDDC mogą obsługiwać 4 000 sesji pulpitu lub aplikacji, przy założeniu, że:

* Ruch obciążeń jest wyrównany do profilu procesu roboczego zadania LoginVSI.

* Tylko ruch sieciowy jest brany pod uwagę, bez danych użytkownika.

* NSX Edge jest skonfigurowany jako duży.

>[!NOTE]
>Twój profil obciążenia i potrzeby mogą być różne, dlatego wyniki mogą się różnić w zależności od przypadku użycia. Woluminy danych użytkownika mogą obniżyć limity skalowania w kontekście obciążenia. Odpowiednio zmień rozmiar i Zaplanuj wdrożenie. Aby uzyskać więcej informacji, zobacz Wskazówki dotyczące ustalania rozmiaru w sekcji [rozmiaru hosty rozwiązań platformy Azure VMware dla wdrożeń](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Mając na celu użycie usługi Azure Private Cloud i SDDC Max, zalecamy architekturę wdrażania, w której serwery połączeń i usługi VMware Unified Access Gateway (UAGs) działają w ramach Virtual Network platformy Azure. Efektywnie włączamy każdą chmurę prywatną platformy Azure i SDDC do bloku. Z kolei maksymalizowanie skalowalności zakresu działającego na platformie Azure VMware.

Połączenie między usługą Azure Virtual Network a chmurami prywatnymi platformy Azure/SDDCs należy skonfigurować przy użyciu ExpressRoute FastPath. Na poniższym diagramie przedstawiono wdrożenie podstawowego horyzontu.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Typowe wdrożenie usługi Horizon pod przy użyciu szybkiej ścieżki ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Połączenie sieciowe z funkcją skalowania na platformie Azure VMware

W tej sekcji przedstawiono architekturę sieci na wysokim poziomie z Typowymi przykładami wdrażania, które ułatwiają skalowanie w usłudze Azure VMware. Fokus jest szczególnie dotyczący najważniejszych elementów sieciowych. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pojedynczy zakres na platformie Azure VMware

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Pojedynczy zakres na platformie Azure VMware" border="false":::

Pojedynczym horyzontem wdrożenia jest najbardziej prosty scenariusz wdrażania do przodu, ponieważ wdrażasz tylko jeden zakres w regionie Wschodnie stany USA.  Ponieważ każdą chmurę prywatną i SDDC są szacowane do obsługi sesji komputerów stacjonarnych 4 000, należy wdrożyć maksymalny rozmiar horyzontu pod.  Można zaplanować wdrożenie maksymalnie trzech chmur prywatnych/SDDCs.

W przypadku maszyn wirtualnych z infrastrukturą (VM) wdrożonych na platformie Azure Virtual Network można dotrzeć do 12 000 sesji na zakres. Połączenie między poszczególnymi chmurami prywatnymi i SDDC na platformie Azure Virtual Network jest ExpressRoute szybka ścieżka.  Nie jest wymagany ruch wschodni-zachodni między chmurami prywatnymi. 

Kluczowe założenia dla tego podstawowego przykładu wdrożenia są następujące:

* Nie masz lokalnego horyzontu, z którym chcesz nawiązać połączenie z tym nowym systemem, przy użyciu architektury Cloud pod (CPA).

* Użytkownicy końcowi łączą się z pulpitami wirtualnymi za pośrednictwem Internetu (a łącząc się za pośrednictwem lokalnego centrum danych).

Kontroler domeny usługi AD można połączyć na platformie Azure Virtual Network z lokalną usługą AD za pośrednictwem sieci VPN lub obwodu usługi ExpressRoute.

Odmianą przykładu podstawowego może być obsługa łączności zasobów lokalnych. Na przykład użytkownicy uzyskują dostęp do komputerów stacjonarnych i generują ruch aplikacji klasycznych lub łączą się z lokalnym horyzontem, przy użyciu CPA.

Na diagramie przedstawiono sposób obsługi łączności zasobów lokalnych. Aby nawiązać połączenie z siecią firmową do Virtual Network platformy Azure, potrzebny będzie obwód usługi ExpressRoute.  Należy również połączyć sieć firmową z każdą chmurą prywatną i SDDCs przy użyciu usługi ExpressRoute Global Reach.  Umożliwia połączenie z usługi SDDC z obwodem ExpressRoute i zasobami lokalnymi. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Podłączanie sieci firmowej do Virtual Network platformy Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Wiele zasobników horyzontów na platformie Azure VMware w wielu regionach

Innym scenariuszem jest skalowanie horyzontu na wiele zasobników.  W tym scenariuszu wdrażasz dwa horyzonty użycia w dwóch różnych regionach i sfederować je przy użyciu CPA. Jest podobna do konfiguracji sieci w poprzednim przykładzie, ale z dodatkowymi łączami międzyregionalnymi. 

Możesz połączyć Virtual Network platformy Azure w każdym regionie z chmurami prywatnymi/SDDCs w innym regionie. Umożliwia ona serwerom połączeń Horizon część Federacji CPA do łączenia się ze wszystkimi pulpitami w obszarze zarządzania. Dodanie dodatkowych chmur prywatnych/SDDCs do tej konfiguracji umożliwi skalowanie do 24 000 sesji ogółem. 

Te same zasady mają zastosowanie w przypadku wdrożenia dwóch zasobników w tym samym regionie.  Upewnij się, że w *osobnym Virtual Network platformy Azure* zostanie wdrożony drugi horyzont, a. Podobnie jak w przypadku pojedynczego przykładu, możesz połączyć sieć firmową i lokalne miejsce na tym przykładzie wieloskładnikowym/regionu przy użyciu ExpressRoute i Global Reach. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Wiele zasobników horyzontów na platformie Azure VMware w wielu regionach" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Rozmiar hostów rozwiązań platformy Azure VMware na potrzeby wdrożeń z horyzontem 

Metodologia ustalania wielkości dla hosta działającego na platformie Azure VMware jest prostsza niż w środowisku lokalnym.  Wynika to z faktu, że host rozwiązań VMware platformy Azure jest ustandaryzowany.  Dokładne ustalanie wielkości hosta pozwala określić liczbę hostów potrzebnych do obsługi wymagań infrastruktury VDI.  Jest to centralne określenie kosztu poszczególnych komputerów.

### <a name="sizing-tables"></a>Ustalanie wielkości tabel

Określone wymagania dotyczące vCPU/vRAM dla pulpitów wirtualnych w ramach horyzontu zależą od profilu obciążenia określonego przez klienta.   Współpracuj z zespołem sprzedaży MSFT i VMware, aby pomóc w ustaleniu wymagań vCPU/vRAM dla pulpitów wirtualnych. 

| vCPU na maszynę wirtualną | Pamięć vRAM na maszynę wirtualną (GB) | Wystąpienie | Maszyny wirtualne 100 | Maszyny wirtualne 200 | Maszyny wirtualne 300 | Maszyny wirtualne 400 | Maszyny wirtualne 500 | Maszyny wirtualne 600 | Maszyny wirtualne 700 | Maszyny wirtualne 800 | Maszyny wirtualne 900 | Maszyny wirtualne 1000 | Maszyny wirtualne 2000 | Maszyny wirtualne 3000 | Maszyny wirtualne 4000 | Maszyny wirtualne 5000 | Maszyny wirtualne 6000 | Maszyny wirtualne 6400 |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Dane wejściowe zmiany zakresu

Oto co należy zebrać w przypadku planowanego obciążenia:

* Liczba współbieżnych komputerów stacjonarnych

* Wymagane vCPU na pulpit

* Wymagana pamięć vRAM na komputer stacjonarny

* Wymagany magazyn na pulpit

Ogólnie rzecz biorąc, wdrożenia infrastruktury VDI są ograniczone przez procesor lub pamięć RAM, co określa rozmiar hosta. Przyjrzyjmy się następującemu przykładowi do typu procesu roboczego wiedzy LoginVSI, zweryfikowanego z testowaniem wydajności:

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

W przypadku wdrożenia na platformie Azure VMware i lokalnie, podobnie jak w przypadku użycia odzyskiwania po awarii, wybierz licencję uniwersalną subskrypcji. Obejmuje ona licencję vSphere na wdrożenie lokalne, więc ma wyższy koszt.

Skontaktuj się z zespołem sprzedaży VMware EUC, aby określić koszt licencjonowania w zależności od potrzeb.

### <a name="azure-instance-types"></a>Typy wystąpień platformy Azure

Aby zrozumieć rozmiary maszyn wirtualnych platformy Azure, które będą wymagane dla infrastruktury horyzontów, zapoznaj się z wytycznymi firmy VMware, które można znaleźć [tutaj](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="references"></a>Odwołania
[Wymagania systemowe dla agenta usługi Horizon dla systemu Linux](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat rozwiązania VMware Horizon na platformie Azure VMware, przeczytaj temat [często zadawane pytania dotyczące programu VMware](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).
