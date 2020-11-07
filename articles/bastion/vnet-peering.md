---
title: Wirtualne sieci równorzędne i architektura platformy Azure bastionu
description: W tym artykule dowiesz się, jak łączyć się z maszynami wirtualnymi za pomocą komunikacji równorzędnej i usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: cherylmc
ms.openlocfilehash: ad3cf33dacffc8bcda9376857206784afedf7139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361989"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>Wirtualne sieci równorzędne i Azure bastionu (wersja zapoznawcza)

Usługa Azure bastionu i Komunikacja równorzędna sieci wirtualnej mogą być używane razem. Po skonfigurowaniu komunikacji równorzędnej sieci wirtualnej nie trzeba wdrażać usługi Azure bastionu w każdej równorzędnej sieci wirtualnej. Oznacza to, że jeśli masz hosta usługi Azure bastionu skonfigurowany w jednej sieci wirtualnej (VNet), można go użyć do nawiązania połączenia z maszynami wirtualnymi wdrożonymi w komunikacji równorzędnej bez wdrażania dodatkowego hosta bastionu. Aby uzyskać więcej informacji o wirtualnych sieciach równorzędnych, zobacz [Informacje o komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md).

Usługa Azure bastionu współpracuje z następującymi typami komunikacji równorzędnej:

* **Komunikacja równorzędna sieci wirtualnej:** Połącz sieci wirtualne w tym samym regionie świadczenia usługi Azure.
* **Globalna komunikacja równorzędna sieci wirtualnych:** Łączenie sieci wirtualnych w różnych regionach platformy Azure.

## <a name="architecture"></a>Architektura

W przypadku skonfigurowania komunikacji równorzędnej sieci wirtualnych usługa Azure bastionu może zostać wdrożona w topologiach gwiazdy i szprych. Wdrożenie usługi Azure bastionu odbywa się na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Po zainicjowaniu obsługi administracyjnej usługi Azure bastionu w sieci wirtualnej, środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tym samym sieci wirtualnej, a także za pomocą komunikacji równorzędnej sieci wirtualnych. Oznacza to, że można skonsolidować wdrożenie bastionu do pojedynczej sieci wirtualnej i nadal dotrzeć do maszyn wirtualnych wdrożonych w równorzędnej sieci wirtualnej, co umożliwia scentralizowane wdrażanie.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagram projektowania i architektury":::

Na tym rysunku przedstawiono architekturę wdrożenia usługi Azure bastionu w modelu gwiazdy i gwiazdy. Na tym diagramie można zobaczyć następującą konfigurację:

* Host bastionu jest wdrażany w centralnej sieci wirtualnej centrum.
* Wdrożono centralną grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń).
* Publiczny adres IP nie jest wymagany na maszynie wirtualnej platformy Azure.

**Czynnooci**

1. Połącz się z Azure Portal przy użyciu dowolnej przeglądarki HTML5.
1. Wybierz maszynę wirtualną, z którą chcesz nawiązać połączenie.
1. Usługa Azure bastionu jest bezproblemowo wykrywana w ramach równorzędnej sieci wirtualnej.
1. Po jednym kliknięciu sesja RDP/SSH zostanie otwarta w przeglądarce. W przypadku limitów sesji współbieżnych połączeń RDP i SSH zobacz [sesje protokołu RDP i SSH](bastion-faq.md#limits).

   :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Połącz":::

   Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszyną wirtualną za pośrednictwem usługi Azure bastionu, zobacz:

   * [Nawiąż połączenie z maszyną wirtualną-RDP](bastion-connect-vm-rdp.md).
   * [Nawiązywanie połączenia z maszyną wirtualną — SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).