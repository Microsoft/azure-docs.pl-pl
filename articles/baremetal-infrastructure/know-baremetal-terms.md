---
title: Poznasz warunki korzystania z infrastruktury Azure BareMetal
description: Poznaj warunki korzystania z infrastruktury Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725462"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Poznaj warunki dla infrastruktury BareMetal

W tym artykule ominiemy ważne terminy związane z infrastrukturą BareMetal.

- **Poprawka:** istnieją dwie różne wersje sygnatur dla sygnatur infrastruktury bareMetal (HANA Large Instance). Różnią się one architekturą i bliskości hostów maszyn wirtualnych platformy Azure:
    - "Poprawka 3" (Rev 3): oryginalny projekt wdrożony w połowie 2016 r.
    - "Poprawka 4.2" (Rev 4.2): nowy projekt, który zapewnia bliższą bliskość hostów maszyn wirtualnych platformy Azure, z bardzo niskim opóźnieniem sieci między maszynami wirtualnymi platformy Azure i dużymi wystąpieniami HANA. Zasoby w Azure Portal są nazywane "infrastrukturą BareMetal", a klienci mogą uzyskać dostęp do swoich zasobów jako wystąpienia BareMetal z Azure Portal.

- **Sygnatura:** definiuje wewnętrzny rozmiar wdrożenia firmy Microsoft wystąpień programu BareMetal. Przed wdrożeniem wystąpień należy wdrożyć w lokalizacji centrum danych sygnaturę wystąpienia BareMetal składającą się z regałów obliczeniowych, sieciowych i magazynowych. Takie wdrożenie jest nazywane sygnaturą wystąpienia systemu BareMetal.

- **Dzierżawa:** klient wdrażający sygnaturę wystąpienia systemu BareMetal jest izolowany jako *dzierżawca.* Dzierżawa jest izolowana w warstwie sieci, magazynu i zasobów obliczeniowych od innych dzierżaw. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżaw nie widzą siebie nawzajem ani nie komunikują się ze sobą na poziomie sygnatur wystąpienia BareMetal. Klient może zdecydować się na wdrożenie w różnych dzierżawach. Nawet wtedy nie ma komunikacji między dzierżawami na poziomie sygnatury wystąpienia bareMetal.

## <a name="next-steps"></a>Następne kroki

Teraz, po wprowadzeniu do ważnej terminologii dotyczącej infrastruktury BareMetal, możesz dowiedzieć się więcej na temat:
- Więcej szczegółów dotyczących infrastruktury [BareMetal.](concepts-baremetal-infrastructure-overview.md)
- How to Connect BareMetal Infrastructure instances in Azure (Jak połączyć wystąpienia infrastruktury [BareMetal na platformie Azure).](connect-baremetal-infrastructure.md)

