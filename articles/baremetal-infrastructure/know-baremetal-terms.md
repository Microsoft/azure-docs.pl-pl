---
title: Poznaj warunki korzystania z infrastruktury Azure BareMetal
description: Poznaj warunki korzystania z infrastruktury Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536780"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Poznaj warunki dla infrastruktury BareMetal

W tym artykule ominiemy niektóre ważne terminy związane z infrastrukturą BareMetal.

- **Poprawka:** Istnieje oryginalna poprawka sygnatury znana jako Poprawka 3 (Rev 3) i dwie dodatkowe wersje sygnatur dla sygnatur wystąpienia BareMetal. Każda sygnatura różni się architekturą i bliskości hostów maszyn wirtualnych platformy Azure:
    - **Poprawka 4** (Rev 4): nowsza konstrukcja, która zapewnia bliższą odległość od hostów maszyn wirtualnych platformy Azure i mniejsze opóźnienie między maszynami wirtualnymi platformy Azure a SAP HANA maszynami wirtualnymi. 
    - **Poprawka 4.2** (Rev 4.2): najnowsza rebranded BareMetal Infrastructure korzystająca z istniejącej architektury Rev 4. Wersja 4 zapewnia bliższą odległość od hostów maszyn wirtualnych platformy Azure. Ma znaczne ulepszenia opóźnienia sieci między maszynami wirtualnych platformy Azure i wystąpieniami systemu BareMetal wdrożonych w sygnaturach lub wierszach rev 4. Możesz uzyskać dostęp do wystąpień programu BareMetal i zarządzać nimi za pośrednictwem Azure Portal.    

- **Sygnatura:** definiuje wewnętrzny rozmiar wdrożenia firmy Microsoft wystąpień programu BareMetal. Przed wdrożeniem wystąpień należy wdrożyć w lokalizacji centrum danych sygnaturę wystąpienia systemu BareMetal składającą się z regałów obliczeniowych, sieciowych i magazynowych. Takie wdrożenie jest nazywane sygnaturą wystąpienia programu BareMetal.

- **Dzierżawa:** klient wdrażający sygnaturę wystąpienia systemu BareMetal jest izolowany jako *dzierżawca.* Dzierżawa jest izolowana w warstwie sieci, magazynu i obliczeń od innych dzierżaw. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżaw nie widzą siebie nawzajem ani nie komunikują się ze sobą na poziomie sygnatury wystąpienia BareMetal. Klient może zdecydować się na wdrożenie w różnych dzierżawach. Nawet wtedy nie ma komunikacji między dzierżawami na poziomie sygnatury wystąpienia bareMetal.

## <a name="next-steps"></a>Następne kroki

Teraz, po wprowadzeniu do ważnej terminologii dotyczącej infrastruktury BareMetal, możesz dowiedzieć się więcej na temat:
- Więcej szczegółów dotyczących infrastruktury [BareMetal.](concepts-baremetal-infrastructure-overview.md)
- How to Connect BareMetal Infrastructure instances in Azure (Jak połączyć wystąpienia infrastruktury [BareMetal na platformie Azure).](connect-baremetal-infrastructure.md)

