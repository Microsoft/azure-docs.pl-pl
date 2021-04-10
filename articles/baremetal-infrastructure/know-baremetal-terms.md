---
title: Znajomość warunków infrastruktury Azure BareMetal
description: Poznaj warunki infrastruktury Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 53a601cc4556198479d8ca5d7495942d4dc2762c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580127"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Poznaj warunki dotyczące infrastruktury BareMetal

W tym artykule omówiono kilka ważnych terminów związanych z infrastrukturą BareMetal.

- **Poprawka**: istnieje Oryginalna poprawka sygnatury znana jako Poprawka 3 (rev 3) i dwie dodatkowe poprawki sygnatury dla sygnatur wystąpień BareMetal. Każda sygnatura różni się w architekturze i sąsiedztwie na hostach maszyn wirtualnych platformy Azure:
    - **Poprawka 4** (rev 4): nowszy projekt, który zapewnia bliższe bliskość hostom maszyn wirtualnych platformy Azure i obniża opóźnienie między maszynami wirtualnymi platformy Azure i wystąpieniami SAP HANA. 
    - **Poprawka 4,2** (rev 4,2): Najnowsza BareMetal infrastruktura z użyciem istniejącej architektury rev 4. Rev 4 zapewnia bliższą bliskość hostom maszyn wirtualnych platformy Azure. Ma znaczne ulepszenia opóźnienia sieci między maszynami wirtualnymi platformy Azure i wystąpieniami BareMetal wdrożonymi w sygnaturach lub wierszach w rev 4. Możesz uzyskać dostęp do wystąpień BareMetal i zarządzać nimi za pomocą Azure Portal.    

- **Sygnatura**: określa wewnętrzny rozmiar wdrożenia firmy Microsoft BareMetal Instances. Przed wdrożeniem wystąpień należy wdrożyć sygnaturę wystąpienia BareMetal składającą się z stojaków obliczeniowych, sieciowych i magazynowych w lokalizacji centrum danych. Takie wdrożenie nazywa się sygnaturą wystąpienia BareMetal.

- **Dzierżawca**: klient wdrażający sygnaturę wystąpienia BareMetal zostaje odizolowany jako *dzierżawca.* Dzierżawa jest odizolowana od sieci, magazynu i warstwy obliczeniowej od innych dzierżawców. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżawców nie są ze sobą widoczne ani nie komunikują się ze sobą na poziomie sygnatury wystąpienia BareMetal. Klient może wybrać wdrożenie w różnych dzierżawach. Nawet nie ma żadnej komunikacji między dzierżawcami na poziomie sygnatury wystąpienia BareMetal.

## <a name="next-steps"></a>Następne kroki
Teraz, po wprowadzeniu ważnej terminologii infrastruktury BareMetal, warto zapoznać się z tematem:
- Więcej szczegółów dotyczących [infrastruktury BareMetal](concepts-baremetal-infrastructure-overview.md).
- Jak [połączyć wystąpienia infrastruktury BareMetal na platformie Azure](connect-baremetal-infrastructure.md).

