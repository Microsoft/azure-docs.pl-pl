---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80420956"
---
W tym artykule opisano sposób przekazywania dysku VHD z komputera lokalnego do dysku zarządzanego platformy Azure lub kopiowania dysku zarządzanego do innego regionu przy użyciu AzCopy. Ten proces, przekazywanie bezpośrednie, umożliwia również przekazywanie wirtualnego dysku twardego do 32 TiB w rozmiarze bezpośrednio do dysku zarządzanego. Obecnie bezpośrednie przekazywanie jest obsługiwane dla standardowego dysku twardego, standardowego SSD i dysków zarządzanych w warstwie Premium SSD. Nie jest to jeszcze obsługiwane w przypadku dysków Ultra Disks.

Jeśli tworzysz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy używanie bezpośredniego przekazywania do przywracania kopii zapasowych klientów na dyskach zarządzanych. W przypadku przekazywania wirtualnego dysku twardego ze źródła zewnętrznego na platformę Azure szybkości będą zależne od przepustowości lokalnej. W przypadku przekazywania lub kopiowania z maszyny wirtualnej platformy Azure przepustowość będzie taka sama jak standardowa HDD.