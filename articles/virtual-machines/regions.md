---
title: Regiony świadczenia usługi Azure
description: Dowiedz się więcej o regionach do uruchamiania maszyn wirtualnych na platformie Azure.
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthnn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.openlocfilehash: e874a76cc447158127da8482f23bf03d56961818
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504613"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Regiony maszyn wirtualnych na platformie Azure

Ważne jest, aby zrozumieć, jak i gdzie maszyny wirtualne działają na platformie Azure, a także poznać opcje maksymalizowania wydajności, dostępności i nadmiarowości. Ten artykuł zawiera omówienie funkcji dostępności i nadmiarowości platformy Azure.


## <a name="what-are-azure-regions"></a>Co to są regiony platformy Azure?
Platforma Azure działa w wielu centrach danych na całym świecie. Te centra danych są grupowane w regiony geograficzne, dzięki czemu można elastycznie wybierać miejsca do kompilowania aplikacji. 

Zasoby platformy Azure można tworzyć w zdefiniowanych regionach geograficznych, takich jak "zachodnie stany USA", "Europa Północna" lub "Azja Południowo-Wschodnia". Możesz zapoznać się z [listą regionów i ich lokalizacji](https://azure.microsoft.com/regions/). W każdym regionie istnieje wiele centrów danych, co zapewnia nadmiarowość i dostępność. Takie podejście zapewnia elastyczność podczas projektowania aplikacji w celu tworzenia maszyn wirtualnych znajdujących się najbliżej użytkowników i spełniających warunki prawne, zgodność i podatek.

## <a name="special-azure-regions"></a>Specjalne regiony platformy Azure
Platforma Azure zawiera specjalne regiony, których można użyć podczas kompilowania aplikacji w celu zapewnienia zgodności lub w celach prawnych. Te regiony specjalne to:

* **US Gov Wirginia** i **US Gov Iowa**
  * Wystąpienie platformy Azure odizolowane fizycznie i na poziomie sieci logicznej dla instytucji rządowych oraz obsługiwane przez sprawdzony pod kątem bezpieczeństwa personel z obywatelstwem Stanów Zjednoczonych. Uwzględnia dodatkowe certyfikaty zgodności, takie jak [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) i [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Dowiedz się więcej o [platformie Azure Government](https://azure.microsoft.com/features/gov/).
* **Chiny Północne** i **Chiny Wschodnie**
  * Te regiony są dostępne dzięki unikatowemu partnerstwu firm Microsoft i 21Vianet, w ramach którego firma Microsoft nie zarządza bezpośrednio centrami danych. Zobacz więcej informacji na temat [platformy Azure w Chinach](https://www.windowsazure.cn/).
* **Niemcy Środkowe** i **Niemcy Północno-Wschodnie**
  * Regiony te są dostępne za pośrednictwem modelu zarządca danych, zgodnie z którym dane klienta pozostają w Niemczech pod kontrolą systemu T-Systems, niemieckiej firmy Telekom, działającej jako niemiecki zarządca danych.

## <a name="region-pairs"></a>Pary regionów
Każdy region platformy Azure jest powiązany z innym regionem w obrębie tego samego obszaru geograficznego (takiego jak Stany Zjednoczone, Europa i Azja). To podejście umożliwia replikację zasobów, takich jak maszyny wirtualne, między obszarami geograficznymi, co powinno zmniejszyć prawdopodobieństwo tego, że klęski żywiołowe, niepokoje społeczne, przerwy w dostawie prądu lub awarie sieci fizycznych będą wpływać na obydwa regiony na raz. Dodatkowe korzyści wynikające z tworzenia par regionów:

* W przypadku awarii platformy Azure o większym zasięgu jeden region z każdej pary ma przydzielany wyższy priorytet, co pomaga zredukować czas wymagany do przywrócenia aplikacji. 
* Zaplanowane aktualizacje platformy Azure są wdrażane w powiązanych regionach pojedynczo, aby zminimalizować przestoje i ryzyko awarii aplikacji.
* Dla celów związanych z podatkami i egzekwowaniem prawa dane przez cały czas znajdują się w tym samym obszarze geograficznym co para (z wyjątkiem regionu Brazylia Południowa).

Przykłady par regionów:

| Podstawowe | Pomocniczy |
|:--- |:--- |
| Zachodnie stany USA |East US |
| Europa Północna |West Europe |
| Southeast Asia |Azja Wschodnia |

Zobacz pełną [listę par regionów tutaj](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Dostępność funkcji
Niektóre usługi lub funkcje maszyn wirtualnych, takie jak określone rozmiary maszyn wirtualnych lub typy magazynu, są dostępne tylko w określonych regionach. Niektóre globalne usługi platformy Azure, takie jak [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../traffic-manager/traffic-manager-overview.md) lub [Azure DNS](../dns/dns-overview.md), nie wymagają wybrania określonego regionu. Aby ułatwić sobie projektowanie środowiska aplikacji, sprawdź [dostępność usług Azure w poszczególnych regionach](https://azure.microsoft.com/regions/#services). Można również [programowo zbadać obsługiwane rozmiary maszyn wirtualnych i ograniczenia w poszczególnych regionach](../azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Dostępność magazynu
Zrozumienie sposobu działania regionów i obszarów geograficznych platformy Azure staje się ważne, jeśli chcesz skorzystać z dostępnych opcji replikacji magazynu. W zależności od typu magazynu masz do wyboru różne opcje replikacji.

**Dyski zarządzane platformy Azure**
* Magazyn lokalnie nadmiarowy (LRS)
  * Umożliwia trzykrotne replikowanie danych w regionie, w którym utworzono konto magazynu.

**Dyski oparte na koncie magazynu**
* Magazyn lokalnie nadmiarowy (LRS)
  * Umożliwia trzykrotne replikowanie danych w regionie, w którym utworzono konto magazynu.
* Magazyn strefowo nadmiarowy (ZRS)
  * Umożliwia trzykrotne replikowanie danych w ramach dwóch lub trzech obiektów, w jednym lub dwóch regionach.
* Magazyn geograficznie nadmiarowy (GRS)
  * Umożliwia replikowanie danych do regionu pomocniczego, który jest oddalony od regionu podstawowego o setki kilometrów.
* Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)
  * Umożliwia replikowanie danych do regionu pomocniczego, podobnie jak w przypadku magazynu GRS, ale oferuje także dostęp tylko do odczytu do danych w lokalizacji pomocniczej.

W poniższej tabeli przedstawiono krótkie podsumowanie różnic między typami replikacji magazynu:

| Strategia replikacji | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Dane są replikowane między wieloma obiektami. |Nie |Tak |Tak |Tak |
| Dane mogą być odczytywane z lokalizacji pomocniczej i z lokalizacji podstawowej. |Nie |Nie |Nie |Tak |
| Liczba kopii danych obsługiwanych w osobnych węzłach. |3 |3 |6 |6 |

Aby uzyskać więcej informacji, zobacz [opcje replikacji magazynu Azure Storage tutaj](../storage/common/storage-redundancy.md). Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](./managed-disks-overview.md).

### <a name="storage-costs"></a>Koszty magazynowania
Ceny różnią się w zależności od wybranego typu magazynu i dostępności.

**Dyski zarządzane platformy Azure**
* Managed Disks w warstwie Premium są obsługiwane przez dyski Solid-State (dysków SSD) i standardowe Managed Disks są obsługiwane przez regularne obracające się dyski. Opłaty za dyski funkcji Dyski zarządzane w warstwach Premium i Standardowa są naliczane zgodnie z aprowizowaną pojemnością dysku.

**Dyski niezarządzane**
* Usługa Premium Storage jest obsługiwana przez Solid-State Drives (dysków SSD) i jest naliczana na podstawie pojemności dysku.
* Magazyn w warstwie Standardowa opiera się na zwykłych dyskach obrotowych, a opłaty są naliczane na podstawie używanej pojemności i żądanej dostępności magazynu.
  * W przypadku magazynów RA-GRS istnieje dodatkowa opłata za transfer danych replikacji geograficznej związana z przepustowością wykorzystywaną do replikowania tych danych do innego regionu platformy Azure.

Zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/), aby uzyskać informacje na temat różnych typów magazynów i opcji dostępności.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).