---
title: Omówienie tworzenia obrazów systemu Linux dla platformy Azure
description: Jak przenieść obrazy maszyn wirtualnych z systemem Linux lub utworzyć nowe obrazy do użycia na platformie Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a77c2bc69418b821933c0b62674500f7a32e40a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565210"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Przełączanie i tworzenie obrazów systemu Linux na platformie Azure

W tym przeglądzie przedstawiono podstawowe koncepcje dotyczące programu Imaging oraz sposób pomyślnego kompilowania i używania obrazów systemu Linux na platformie Azure. Przed przełączeniem obrazu niestandardowego na platformę Azure należy pamiętać o typach i opcjach dostępnych dla użytkownika.

W tym artykule omówiono punkty i wymagania dotyczące podejmowania decyzji dotyczących obrazu, wyjaśniono kluczowe pojęcia, dzięki czemu można wykonać te czynności i utworzyć własne niestandardowe obrazy w specyfikacji.

## <a name="difference-between-managed-disks-and-images"></a>Różnica między dyskami zarządzanymi i obrazami


System Azure umożliwia przeniesienie dysku VHD na platformę, użycie go jako [dysku zarządzanego](../faq-for-disks.md#managed-disks)lub użycie jako źródła obrazu. 

Azure Managed disks to pojedyncze wirtualne dyski twarde. Można albo utworzyć istniejący wirtualny dysk twardy i utworzyć na jego dysku zarządzanym. Maszyny wirtualne można tworzyć na podstawie dysków zarządzanych, dołączając dysk do maszyny wirtualnej, ale można używać dysku VHD tylko z jedną maszyną wirtualną. Nie możesz modyfikować żadnych właściwości systemu operacyjnego, platforma Azure podejmie próbę włączenia maszyny wirtualnej i uruchomienia jej przy użyciu tego dysku. 

Obrazy platformy Azure mogą składać się z wielu dysków systemu operacyjnego i dysków z danymi. W przypadku tworzenia maszyny wirtualnej przy użyciu obrazu zarządzanego platforma tworzy kopię obrazu i używa jej do utworzenia maszyny wirtualnej, dzięki czemu obsługa obrazów zarządzanych jest używana ponownie w przypadku wielu maszyn wirtualnych. System Azure zapewnia także zaawansowane funkcje zarządzania dla obrazów, takich jak replikacja globalna i przechowywanie wersji za pomocą [galerii obrazów udostępnionych](../shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Uogólnione i wyspecjalizowane

Platforma Azure oferuje dwa główne typy obrazów, uogólnione i wyspecjalizowane. Warunki uogólnione i wyspecjalizowane są pierwotnie terminami systemu Windows, które zostały zmigrowane na platformie Azure. Te typy definiują sposób obsługi przez platformę maszyny wirtualnej po jej włączeniu. Oba typy mają zalety i wady i wymagania wstępne. Przed rozpoczęciem musisz wiedzieć, jakiego typu obrazu potrzebujesz. Poniżej przedstawiono podsumowanie scenariuszy i typów, które należy wybrać:

| Scenariusz      | Typ obrazu  | Opcje magazynu |
| ------------- |:-------------:| :-------------:| 
| Utwórz obraz, który można skonfigurować do użytku przez wiele maszyn wirtualnych, i ustawić nazwę hosta, Dodaj administratora i wykonaj inne zadania podczas pierwszego rozruchu. | Uogólniony | Galeria obrazów udostępnionych lub autonomiczne obrazy zarządzane |
| Tworzenie obrazu na podstawie migawki maszyny wirtualnej lub kopii zapasowej | Wyspecjalizowany |Galeria obrazów udostępnionych lub dysk zarządzany |
| Szybkie tworzenie obrazu, który nie wymaga żadnej konfiguracji do tworzenia wielu maszyn wirtualnych |Wyspecjalizowany |Galeria obrazów udostępnionych |


### <a name="generalized-images"></a>Uogólnione obrazy

Obraz uogólniony jest obrazem, który wymaga zakończenia instalacji przy pierwszym rozruchu. Na przykład podczas pierwszego rozruchu ustawiana jest nazwa hosta, użytkownik administracyjny i inne konfiguracje specyficzne dla maszyny wirtualnej. Jest to przydatne, gdy chcesz, aby obraz był wielokrotnie używany, i gdy chcesz przekazać parametry podczas tworzenia. Jeśli obraz uogólniony zawiera agenta platformy Azure, Agent przetworzy parametry i powraca do platformy, że początkowa konfiguracja została ukończona. Ten proces jest nazywany [aprowizacji](./provisioning.md). 

Inicjowanie obsługi wymaga, aby w obrazie znajdował się provisioner. Istnieją dwie aprowizacji:
- [Agent systemu Linux platformy Azure](../extensions/agent-linux.md)
- [Cloud-init](./using-cloud-init.md)

Są to [wymagania wstępne](./create-upload-generic.md) dotyczące tworzenia obrazu.


### <a name="specialized-images"></a>Obrazy specjalne
Są to obrazy, które są całkowicie skonfigurowane i nie wymagają maszyn wirtualnych i parametrów specjalnych, platforma przejdzie po prostu na maszynę wirtualną, musisz obsługiwać unikatowość w ramach maszyny wirtualnej, na przykład ustawienie nazwy hosta, aby uniknąć konfliktów DNS w tej samej sieci wirtualnej. 

Agenci aprowizacji nie są zobowiązani do tych obrazów, ale mogą chcieć mieć możliwość obsługi rozszerzeń. Można zainstalować agenta systemu Linux, ale wyłączyć opcję aprowizacji. Mimo że nie jest potrzebny Agent aprowizacji, obraz musi spełniać [wymagania wstępne](./create-upload-generic.md)  dotyczące obrazów platformy Azure.


## <a name="image-storage-options"></a>Opcje magazynu obrazu
Podczas przełączania obrazu systemu Linux dostępne są dwie opcje:

- Zarządzane obrazy do tworzenia prostych maszyn wirtualnych w środowisku deweloperskim i testowym.
- [Udostępniona Galeria obrazów](../shared-image-galleries.md) na potrzeby tworzenia i udostępniania obrazów na dużą skalę.


### <a name="managed-images"></a>Zarządzane obrazy

Zarządzane obrazy mogą służyć do tworzenia wielu maszyn wirtualnych, ale mają wiele ograniczeń. Obrazy zarządzane można tworzyć tylko na podstawie uogólnionego źródła (maszyny wirtualnej lub wirtualnego dysku twardego). Mogą one być używane tylko do tworzenia maszyn wirtualnych w tym samym regionie i nie mogą być współużytkowane przez subskrypcje i dzierżawców.

Obrazy zarządzane mogą służyć do tworzenia i testowania środowisk, w których potrzebujesz kilku prostych uogólnionych obrazów do użycia w ramach jednego regionu i subskrypcji. 

### <a name="azure-shared-image-gallery-sig"></a>Galeria udostępnionych obrazów systemu Azure (SIG)

[Udostępnione Galerie obrazów](../shared-image-galleries.md) są zalecane do tworzenia i udostępniania obrazów oraz zarządzania nimi na dużą skalę. Udostępnione Galerie obrazów ułatwiają tworzenie struktury i organizacji na obrazach.  

- Obsługa obrazów uogólnionych i wyspecjalizowanych.
- Obsługa obrazów 1 i 2 generacji.
- Replikacja globalna obrazów.
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania.
- Wysoce dostępne obrazy z magazynem strefowo nadmiarowy (ZRS) w regionach, które obsługują Strefy dostępności. ZRS zapewnia lepszą odporność na awarie stref.
- Udostępnianie między subskrypcjami, a nawet między dzierżawami Active Directory (AD) przy użyciu funkcji RBAC platformy Azure.
- Skalowanie wdrożeń przy użyciu replik obrazów w każdym regionie.

Na wysokim poziomie utworzysz SIG i składa się z:
- Definicje obrazów — są to kontenery, które zawierają grupy obrazów.
- Wersje obrazów — są to obrazy rzeczywiste



## <a name="hyper-v-generation"></a>Generowanie funkcji Hyper-V

Platforma Azure obsługuje funkcję Hyper-V Generation 1 (Gen1) i generacja 2 (Gen2), Gen2 to najnowsza generacja i oferuje dodatkowe funkcje w zakresie Gen1. Na przykład: zwiększona pamięć, rozszerzenia programu Intel Software Guard (Intel SGX) i Zwirtualizowana Pamięć trwała (vPMEM). Maszyny wirtualne 2. generacji działające lokalnie mają pewne funkcje, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz sekcję funkcje i możliwości. Aby uzyskać więcej informacji, zobacz ten [artykuł](../generation-2.md). Jeśli potrzebujesz dodatkowych funkcji, Utwórz obrazy Gen2.

Jeśli nadal potrzebujesz utworzyć własny obraz, upewnij się, że spełnia on [wymagania wstępne obrazu](./create-upload-generic.md), i przekaż go do platformy Azure. Wymagania dotyczące dystrybucji:


- [dystrybucje oparte na systemie CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES i openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć [udostępnioną galerię obrazów](tutorial-custom-images.md).