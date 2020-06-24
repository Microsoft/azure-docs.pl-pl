---
title: Pojęcia — chmury prywatne i klastry
description: Dowiedz się więcej na temat kluczowych funkcji centrów danych zdefiniowanych przez oprogramowanie VMware platformy Azure i klastrów vSphere w rozwiązaniu VMware na platformie Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 09e1fd45b1dd873509f942ef8b524783acfed4ce
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906993"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Azure VMware Solution (wersja zapoznawcza) — Podgląd chmury prywatnej i pojęcia dotyczące klastrów

Rozwiązanie Azure VMware (Automatyczna synchronizacja) zapewnia chmurę prywatną opartą na oprogramowaniu VMware na platformie Azure. Chmury prywatne są zbudowane z klastrów dedykowanych hostów bez systemu operacyjnego, które są wdrażane i zarządzane za pośrednictwem Azure Portal. Obsługa klastrów w chmurach prywatnych odbywa się przy użyciu oprogramowania VMware vSphere, vCenter, sieci vSAN i NSX. Automatyczna synchronizacja sprzętu i wdrożeń oprogramowania w chmurze prywatnej jest w pełni zintegrowane i zautomatyzowane na platformie Azure.

Istnieje relacja logiczna między subskrypcjami platformy Azure, automatyczna synchronizacja chmur prywatnych, klastrów sieci vSAN i hostów. Na diagramie są wyświetlane dwie chmury prywatne w ramach jednej subskrypcji platformy Azure. Chmury prywatne reprezentują programowanie i środowisko produkcyjne, z których każdy ma własną chmurę prywatną. W każdej z tych chmur prywatnych istnieją dwa klastry. Aby pokazać mniejsze potencjalne potrzeby środowiska programistycznego, używane są mniejsze klastry z hostami o niższych pojemnościach. Wszystkie te koncepcje opisano w poniższych sekcjach.

![Obraz dwóch chmur prywatnych w ramach subskrypcji klienta](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Chmury prywatne

Chmury prywatne zawierają klastry sieci vSAN, które są tworzone za pomocą dedykowanych hostów platformy Azure z systemem bez systemu operacyjnego. Każda chmura prywatna może mieć wiele klastrów, które są zarządzane przez ten sam serwer vCenter i Menedżera NSX-T. Chmury prywatne można wdrażać w portalu, przy użyciu interfejsu wiersza polecenia lub programu PowerShell. Podobnie jak w przypadku innych zasobów, chmury prywatne są instalowane i zarządzane w ramach subskrypcji platformy Azure.

Liczba chmur prywatnych w ramach subskrypcji jest skalowalna. Początkowo istnieje ograniczenie jednej chmury prywatnej na subskrypcję.

## <a name="clusters"></a>Klastry

W każdej chmurze prywatnej utworzysz co najmniej jeden klaster sieci vSAN. Podczas tworzenia chmury prywatnej jest domyślnie jeden klaster. Możesz dodać dodatkowe klastry do chmury prywatnej za pomocą Azure Portal lub za pośrednictwem interfejsu API. Wszystkie klastry mają domyślny rozmiar trzech hostów i mogą być skalowane z 3 do 16 hostów. Typ hostów użytych w klastrze musi być tego samego typu. Typy hostów są opisane w następnej sekcji.

Klastry próbne są dostępne do oceny i są ograniczone do trzech hostów i jednego klastra próbnego na chmurę prywatną. W trakcie okresu próbnego można skalować klaster w wersji próbnej za pomocą jednego hosta.

Tworzenie, usuwanie i skalowanie klastrów odbywa się za pomocą portalu lub interfejsu API. Nadal używasz Menedżera vSphere i NSX-T do zarządzania większością aspektów konfiguracji klastra lub operacji. Wszystkie magazyny lokalne każdego hosta w klastrze są kontrolowane przez sieci vSAN.

## <a name="hosts"></a>Hosts

Węzły infrastruktury, które są zbieżne z funkcją Hyper-in, są używane w ramach automatycznej synchronizacji klastrów w chmurze prywatnej. Pojemność pamięci RAM, procesora CPU i dysku hosta znajduje się w poniższej tabeli. 

| Typ hosta              |             Procesor CPU             |   Pamięć RAM (GB)   |  Warstwa pamięci podręcznej interfejsu NVMe sieci vSAN (TB, RAW)  |  warstwa pojemności sieci vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| Wysoka końcowa (IT)          |  Dwurdzeniowy procesor Intel 18 2,3 GHz  |     576      |                3.2               |                15,20               |

Hosty, które są używane do kompilowania lub skalowania klastrów, są uzyskiwane z izolowanej puli hostów. Te hosty przekazały testy sprzętu i wszystkie dane zostały bezpiecznie usunięte z dysków flash. Po usunięciu hosta z klastra dyski wewnętrzne są bezpiecznie czyszczone i hosty są umieszczane w izolowanej puli hostów. Po dodaniu hosta do klastra jest używany oczyszczony host z puli izolowanej.

## <a name="vmware-software-versions"></a>Wersje oprogramowania VMware

Bieżące wersje oprogramowania VMware używane w ramach automatycznej synchronizacji klastrów w chmurze prywatnej są następujące:

| Oprogramowanie              |    Wersja   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| Sieci vSAN                  |    6,7 U2    |
| NSX-T                 |      2.5     |

W przypadku każdego nowego klastra w chmurze prywatnej wersja oprogramowania będzie zgodna z bieżącą uruchomioną w chmurze prywatnej. W przypadku każdej nowej chmury prywatnej w ramach subskrypcji klienta jest zainstalowana najnowsza wersja stosu oprogramowania.

Ogólne zasady i procesy uaktualniania oprogramowania platformy do automatycznej synchronizacji są opisane w dokumencie pojęcia dotyczące uaktualniania.

## <a name="host-maintenance-and-lifecycle-management"></a>Zarządzanie konserwacją i cyklem życia hosta

Zarządzanie konserwacją i cyklem życia hosta odbywa się bez wpływu na pojemność ani wydajność klastrów chmury prywatnej. Przykłady automatycznej konserwacji hosta obejmują uaktualnienia oprogramowania układowego i naprawy sprzętu lub wymiany.

Firma Microsoft jest odpowiedzialna za zarządzanie cyklem życia urządzeń NSX-T, takimi jak NSX-T Manager i NSX-T Edge. Firma Microsoft jest odpowiedzialna za uruchamianie konfiguracji sieci, np. Tworzenie bramy warstwy 0 i Włączanie routingu północ-południe. Jako administrator chmury prywatnej do automatycznej synchronizacji użytkownik jest odpowiedzialny za NSXą konfigurację SDN, taką jak segmenty sieci, reguły rozproszonej zapory, bramy warstwy 1 i moduły równoważenia obciążenia.

> [!IMPORTANT]
> Administrator automatycznej synchronizacji nie może modyfikować konfiguracji bramy NSX-T ani warstwy "0". Może to spowodować utratę usługi.

## <a name="backup-and-restoration"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe konfiguracji vCenter i NSX-T chmury prywatnej są tworzone co godzinę. Kopie zapasowe są przechowywane przez trzy dni. Żądanie dotyczące przywracania z kopii zapasowej jest wymagane za pośrednictwem żądania obsługi w Azure Portal.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi sieci i łączności między połączeniami](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

