---
title: Znajomość warunków infrastruktury Azure BareMetal
description: Poznaj warunki infrastruktury Azure BareMetal.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829155"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Poznaj warunki dotyczące infrastruktury BareMetal

W tym artykule omówiono niektóre ważne warunki BareMetal.

- **Poprawka**: Istnieją dwie różne wersje sygnatury dla sygnatur wystąpień BareMetal. Każda wersja różni się w architekturze i sąsiedztwie na hostach maszyn wirtualnych platformy Azure:
    - **Poprawka 3** (Rev. 3): jest oryginalnym projektem.
    - **Poprawka 4** (Rev. 4): to nowy projekt, który zapewnia bliższą bliskość hostom maszyn wirtualnych platformy Azure i obniża opóźnienie między maszynami wirtualnymi platformy Azure i jednostkami wystąpienia BareMetal. 
    - **Poprawka 4,2** (rev 4,2): to najnowsza BareMetal infrastruktura, która używa istniejącej architektury rev 4. Możesz uzyskać dostęp do wystąpień BareMetal i zarządzać nimi za pomocą Azure Portal.  

- **Sygnatura**: określa wewnętrzny rozmiar wdrożenia firmy Microsoft BareMetal Instances. Przed wdrożeniem jednostek wystąpień można wdrożyć sygnaturę wystąpienia BareMetal składającą się z stojaków obliczeniowych, sieci i magazynu w lokalizacji centrum danych. Takie wdrożenie nosi nazwę sygnatury wystąpienia BareMetal lub z poprawki 4,2.

- **Dzierżawca**: klient wdrożony w sygnaturze wystąpienia BareMetal zostaje odizolowany do *dzierżawy.* Dzierżawa jest odizolowana od sieci, magazynu i warstwy obliczeniowej od innych dzierżawców. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżawców nie są ze sobą widoczne ani nie komunikują się ze sobą na poziomie sygnatury wystąpienia BareMetal. Klient może wybrać wdrożenie w różnych dzierżawach. Nawet nie ma żadnej komunikacji między dzierżawcami na poziomie sygnatury wystąpienia BareMetal.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak identyfikować jednostki wystąpień BareMetal i korzystać z nich za pomocą [Azure Portal](workloads/sap/baremetal-infrastructure-portal.md).


 