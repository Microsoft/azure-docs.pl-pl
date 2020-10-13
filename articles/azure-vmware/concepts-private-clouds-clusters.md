---
title: Pojęcia — chmury prywatne i klastry
description: Dowiedz się więcej na temat kluczowych funkcji centrów danych zdefiniowanych przez oprogramowanie VMware platformy Azure i klastrów vSphere w rozwiązaniu VMware na platformie Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 715293d9951876ff0f794f8f6b580093f89571b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316872"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Rozwiązanie Azure VMware Private Cloud i pojęcia dotyczące klastrów

Rozwiązanie Azure VMware zapewnia chmurę prywatną opartą na oprogramowaniu VMware na platformie Azure. Chmury prywatne są zbudowane z klastrów dedykowanych hostów bez systemu operacyjnego, które są wdrażane i zarządzane za pośrednictwem Azure Portal. Obsługa klastrów w chmurach prywatnych odbywa się przy użyciu oprogramowania VMware vSphere, vCenter, sieci vSAN i NSX. Wdrożenie sprzętu i oprogramowania chmury prywatnej na platformie Azure jest w pełni zintegrowane i zautomatyzowane na platformie Azure.

Istnieje relacja logiczna między subskrypcjami platformy Azure, chmurami prywatnymi rozwiązań VMware platformy Azure, klastrami sieci vSAN i hostami. Na diagramie są wyświetlane dwie chmury prywatne w ramach jednej subskrypcji platformy Azure. Chmury prywatne reprezentują programowanie i środowisko produkcyjne, z których każdy ma własną chmurę prywatną. W każdej z tych chmur prywatnych istnieją dwa klastry. Aby pokazać mniejsze potencjalne potrzeby środowiska programistycznego, używane są mniejsze klastry z hostami o niższych pojemnościach. Wszystkie te koncepcje opisano w poniższych sekcjach.

![Obraz dwóch chmur prywatnych w ramach subskrypcji klienta](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Chmury prywatne

Chmury prywatne zawierają klastry sieci vSAN, które są tworzone za pomocą dedykowanych hostów platformy Azure z systemem bez systemu operacyjnego. Każda chmura prywatna może mieć wiele klastrów, które są zarządzane przez ten sam serwer vCenter i Menedżera NSX-T. Chmury prywatne można wdrażać w portalu, przy użyciu interfejsu wiersza polecenia lub programu PowerShell. Podobnie jak w przypadku innych zasobów, chmury prywatne są instalowane i zarządzane w ramach subskrypcji platformy Azure.

Liczba chmur prywatnych w ramach subskrypcji jest skalowalna. Początkowo istnieje ograniczenie jednej chmury prywatnej na subskrypcję.

## <a name="clusters"></a>Klastry

W każdej chmurze prywatnej utworzysz co najmniej jeden klaster sieci vSAN. Podczas tworzenia chmury prywatnej jest domyślnie jeden klaster. Możesz dodać dodatkowe klastry do chmury prywatnej za pomocą Azure Portal lub za pośrednictwem interfejsu API. Wszystkie klastry mają domyślny rozmiar trzech hostów i mogą być skalowane z 3 do 16 hostów. Typ hostów użytych w klastrze musi być tego samego typu. Typy hostów są opisane w następnej sekcji.

Klastry próbne są dostępne do oceny i są ograniczone do trzech hostów i jednego klastra próbnego na chmurę prywatną. W trakcie okresu próbnego można skalować klaster w wersji próbnej za pomocą jednego hosta.

Tworzenie, usuwanie i skalowanie klastrów odbywa się za pomocą portalu lub interfejsu API. Nadal używasz Menedżera vSphere i NSX-T do zarządzania większością aspektów konfiguracji klastra lub operacji. Wszystkie magazyny lokalne każdego hosta w klastrze są kontrolowane przez sieci vSAN.

## <a name="hosts"></a>Hosts

Węzły infrastruktury w chmurze prywatnej rozwiązania Azure VMware są używane w przypadku zbieżnych z funkcją Hyper- Pojemność pamięci RAM, procesora CPU i dysku hosta znajduje się w poniższej tabeli. 

| Typ hosta              |             Procesor CPU             |   Pamięć RAM (GB)   |  Warstwa pamięci podręcznej interfejsu NVMe sieci vSAN (TB, RAW)  |  warstwa pojemności sieci vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End ()          |  Dwurdzeniowy procesor Intel 18 2,3 GHz  |     576      |                3.2               |                15,20               |

Hosty, które są używane do kompilowania lub skalowania klastrów, są uzyskiwane z izolowanej puli hostów. Te hosty przekazały testy sprzętu i wszystkie dane zostały bezpiecznie usunięte z dysków flash. Po usunięciu hosta z klastra dyski wewnętrzne są bezpiecznie czyszczone i hosty są umieszczane w izolowanej puli hostów. Po dodaniu hosta do klastra jest używany oczyszczony host z puli izolowanej.

## <a name="vmware-software-versions"></a>Wersje oprogramowania VMware

Bieżące wersje oprogramowania VMware używane w klastrach chmur prywatnych rozwiązania Azure VMware są następujące:

| Oprogramowanie              |    Wersja   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| Sieci vSAN                  |    6,7 U2    |
| NSX-T                 |      2,5     |

W przypadku każdego nowego klastra w chmurze prywatnej wersja oprogramowania będzie zgodna z bieżącą uruchomioną w chmurze prywatnej. W przypadku każdej nowej chmury prywatnej w ramach subskrypcji klienta jest zainstalowana najnowsza wersja stosu oprogramowania.

Ogólne zasady i procesy uaktualniania dotyczące oprogramowania platformy Azure VMware Solution platform opisano w dokumencie pojęcia dotyczące uaktualniania.

## <a name="host-maintenance-and-lifecycle-management"></a>Zarządzanie konserwacją i cyklem życia hosta

Zarządzanie konserwacją i cyklem życia hosta odbywa się bez wpływu na pojemność ani wydajność klastrów chmury prywatnej. Przykłady automatycznej konserwacji hosta obejmują uaktualnienia oprogramowania układowego i naprawy sprzętu lub wymiany.

Firma Microsoft jest odpowiedzialna za zarządzanie cyklem życia urządzeń NSX-T, takimi jak NSX-T Manager i NSX-T Edge. Firma Microsoft jest odpowiedzialna za uruchamianie konfiguracji sieci, np. Tworzenie bramy warstwy 0 i Włączanie routingu North-Southowego. Jako administrator chmury prywatnej rozwiązania Azure VMware jest odpowiedzialny za NSX-T SDN Configuration. Na przykład segmenty sieci, rozproszone reguły zapory, bramy warstwy 1 i moduły równoważenia obciążenia.

> [!IMPORTANT]
> Administrator rozwiązania VMware platformy Azure nie może modyfikować konfiguracji bramy NSX-T ani warstwy 0. Może to spowodować utratę usługi.

## <a name="backup-and-restoration"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe konfiguracji vCenter i NSX-T chmury prywatnej są tworzone co godzinę. Kopie zapasowe są przechowywane przez trzy dni. Żądanie dotyczące przywracania z kopii zapasowej jest wymagane za pośrednictwem żądania obsługi w Azure Portal.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi sieci i łączności między połączeniami](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

