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
ms.date: 05/19/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fbc6c7b8811f3cf46b4f31387c2181c8d085e39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83684884"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Zgodne systemy operacyjne dla dużych wystąpień HANA

## <a name="hana-large-instance-type-i"></a>Typ dużego wystąpienia HANA I     
  | System operacyjny | Dostępność        | Jednostki SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Nie jest już oferowane | S72, S72m, s96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 Z DODATKIEM SP3      | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Dostępne           | S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Jednostki SKU pamięci trwałej
  | System operacyjny | Dostępność | Jednostki SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Dostępne    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Typ dużej instancji HANA II     
  |  System operacyjny       | Dostępność        | Jednostki SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Nie jest już oferowane | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 Z DODATKIEM SP3             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP4             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 Z DODATKIEM SP5             | Dostępne           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Powiązane dokumenty

- Aby dowiedzieć się więcej na temat [dostępnych jednostek SKU](hana-available-skus.md)
- Aby dowiedzieć się więcej o [uaktualnianiu systemu operacyjnego](os-upgrade-hana-large-instance.md)
  

  
  
