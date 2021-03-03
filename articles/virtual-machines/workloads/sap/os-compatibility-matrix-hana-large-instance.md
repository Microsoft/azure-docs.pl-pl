---
title: Macierz zgodności systemu operacyjnego dla SAP HANA (duże wystąpienia) | Microsoft Docs
description: Macierz zgodności reprezentuje zgodność różnych wersji systemu operacyjnego z różnymi typami sprzętu (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccfdffc4e488de7f3cecb150305596743b3a9e44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675415"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Zgodne systemy operacyjne dla dużych wystąpień HANA

## <a name="hana-large-instance-type-i"></a>Typ dużego wystąpienia HANA I     
  | System operacyjny | Dostępność        | Jednostki SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Nie jest już oferowane | S72, S72m, s96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 Z DODATKIEM SP3      | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 Z DODATKIEM SP5      | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7,6         | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Jednostki SKU pamięci trwałej
  | System operacyjny | Dostępność | Jednostki SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Dostępne    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Typ dużej instancji HANA II     
  |  System operacyjny       | Dostępność        | Jednostki SKU                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | Nie jest już oferowane | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 Z DODATKIEM SP3             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP4             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 Z DODATKIEM SP5             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | RHEL 7,6                | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |

## <a name="related-documents"></a>Powiązane dokumenty

- Aby dowiedzieć się więcej na temat [dostępnych jednostek SKU](hana-available-skus.md)
- Aby dowiedzieć się więcej o [uaktualnianiu systemu operacyjnego](os-upgrade-hana-large-instance.md)
  

  
  
