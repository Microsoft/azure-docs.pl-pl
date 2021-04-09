---
title: Pojęcia — chmury prywatne i klastry
description: Dowiedz się więcej na temat kluczowych możliwości rozwiązań VMware platformy Azure zdefiniowanych przez oprogramowanie oraz klastrów vSphere.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: aff66e01ae4b056eb082d2000611718b1a5cf66a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773928"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Rozwiązanie Azure VMware Private Cloud i pojęcia dotyczące klastrów

Rozwiązanie Azure VMware zapewnia chmurę prywatną opartą na oprogramowaniu VMware na platformie Azure. Chmury prywatne zawierają klastry z dedykowanymi hostami systemu Azure bez systemu operacyjnego. Są one wdrażane i zarządzane za pomocą Azure Portal, interfejsu wiersza polecenia lub programu PowerShell.  Klastry obsługiwane w chmurach prywatnych obejmują oprogramowanie VMware vSphere, vCenter, sieci vSAN i NSX. Wdrożenie sprzętu i oprogramowania chmury prywatnej na platformie Azure jest w pełni zintegrowane i zautomatyzowane na platformie Azure.

Istnieje relacja logiczna między subskrypcjami platformy Azure, chmurami prywatnymi rozwiązań VMware platformy Azure, klastrami sieci vSAN i hostami. Diagram przedstawia pojedynczą subskrypcję platformy Azure z dwiema chmurami prywatnymi, które reprezentują środowisko programistyczne i produkcyjne.  W każdej z tych chmur prywatnych są dwa klastry. 

W tym artykule opisano wszystkie te pojęcia.

![Obraz dwóch chmur prywatnych w ramach subskrypcji klienta](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>Chmury prywatne

Chmury prywatne zawierają klastry sieci vSAN zbudowane z dedykowanymi hostami systemu Azure bez systemu operacyjnego. Każda chmura prywatna może mieć wiele klastrów zarządzanych przez ten sam serwer vCenter i Menedżera NSX-T. Chmury prywatne można wdrażać w portalu, w interfejsie wiersza polecenia lub w programie PowerShell i zarządzać nimi. 

Podobnie jak w przypadku innych zasobów, chmury prywatne są instalowane i zarządzane w ramach subskrypcji platformy Azure. Liczba chmur prywatnych w ramach subskrypcji jest skalowalna. Początkowo istnieje ograniczenie jednej chmury prywatnej na subskrypcję.

## <a name="clusters"></a>Klastry
Dla każdej utworzonej chmury prywatnej jest domyślnie jeden klaster sieci vSAN. Możesz dodawać, usuwać i skalować klastry za pomocą Azure Portal lub za pośrednictwem interfejsu API.  Wszystkie klastry mają domyślny rozmiar trzech hostów i można skalować do 16 hostów. Na chmurę prywatną mogą znajdować się maksymalnie cztery klastry.

Klastry próbne są dostępne do oceny i są ograniczone do trzech hostów. Istnieje pojedynczy klaster próbny na chmurę prywatną. W trakcie okresu próbnego można skalować klaster w wersji próbnej za pomocą jednego hosta.

Menedżer vSphere i NSX-T służy do zarządzania większością aspektów konfiguracji klastra lub operacji. Wszystkie magazyny lokalne każdego hosta w klastrze są pod kontrolą sieci vSAN.

## <a name="hosts"></a>Hosts

Klastry rozwiązań VMware platformy Azure są oparte na infrastrukturze z technologią Hyper-i bez systemu operacyjnego. W poniższej tabeli przedstawiono pojemności pamięci RAM, procesora CPU i dysku hosta.

| Typ hosta              |             Procesor CPU             |   Pamięć RAM (GB)   |  Warstwa pamięci podręcznej interfejsu NVMe sieci vSAN (TB, RAW)  |  warstwa pojemności sieci vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  Dwurdzeniowy procesor Intel 18 2,3 GHz  |     576      |                3.2               |                15,20               |

Hosty używane do kompilowania lub skalowania klastrów pochodzą z izolowanej puli hostów. Te hosty przekazały testy sprzętu i wszystkie dane zostały bezpiecznie usunięte. 

## <a name="vmware-software-versions"></a>Wersje oprogramowania VMware

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="update-frequency"></a>Częstotliwość aktualizacji

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>Zarządzanie konserwacją i cyklem życia hosta

Zarządzanie konserwacją i cyklem życia hosta nie ma wpływu na pojemność lub wydajność klastrów w chmurze prywatnej.  Przykłady automatycznej konserwacji hosta obejmują uaktualnienia oprogramowania układowego i naprawy sprzętu lub wymiany.

Firma Microsoft jest odpowiedzialna za zarządzanie cyklem życia urządzeń NSX-T, takimi jak NSX-T Manager i NSX-T Edge. Firma Microsoft jest odpowiedzialna za uruchamianie konfiguracji sieci, np. Tworzenie bramy warstwy 0 i Włączanie routingu North-Southowego. Użytkownik jest odpowiedzialny za NSX-T SDN Configuration. Na przykład segmenty sieci, rozproszone reguły zapory, bramy warstwy 1 i moduły równoważenia obciążenia.

## <a name="backup-and-restoration"></a>Tworzenie kopii zapasowej i przywracanie

Konfiguracje usługi vCenter i NSX w chmurze prywatnej są oparte na godzinowym harmonogramie tworzenia kopii zapasowych.  Kopie zapasowe są przechowywane przez trzy dni. Jeśli musisz przywrócić kopię zapasową, Otwórz [żądanie obsługi](https://rc.portal.azure.com/#create/Microsoft.Support) w Azure Portal, aby zażądać przywrócenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały omówione koncepcje chmur prywatnych rozwiązania Azure VMware, możesz zapoznać się z tematem: 

- [Azure VMware rozwiązanie do obsługi sieci i międzyłączności](concepts-networking.md).
- [Pojęcia dotyczące magazynu rozwiązań VMware platformy Azure](concepts-storage.md).
- [Jak włączyć zasób rozwiązania VMware dla platformy Azure](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

