---
title: Macierz zgodności systemu operacyjnego dla SAP HANA (duże wystąpienia) | Microsoft Docs
description: Macierz zgodności reprezentuje zgodność różnych wersji systemu operacyjnego z różnymi typami sprzętu (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 14cfd1aef3a49baa11050293fc216256f33cee7a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376667"
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
  

  
  
