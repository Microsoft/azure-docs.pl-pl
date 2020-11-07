---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356656"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Czy można nadal wdrażać wiele hostów bastionu w ramach równorzędnych sieci wirtualnych?

Tak. Domyślnie użytkownik widzi hosta bastionu, który jest wdrożony w tej samej sieci wirtualnej, w której znajduje się maszyna wirtualna. Jednak w menu **Połącz** użytkownik może zobaczyć wiele hostów bastionu wykrytych w sieciach równorzędnych. Mogą wybrać hosta bastionu, którego woli używać do nawiązywania połączenia z MASZYNą wirtualną wdrożoną w sieci wirtualnej.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Jeśli moje sieci wirtualnych równorzędne są wdrożone w różnych subskrypcjach, połączenie odbywa się za pośrednictwem Bastionuu?

Tak. łączność za pośrednictwem bastionu będzie nadal działała w przypadku komunikacji równorzędnej sieci wirtualnych między różnymi subskrypcjami dla jednej dzierżawy. Subskrypcje między dwoma różnymi dzierżawcami nie są obsługiwane. Aby wyświetlić bastionu w menu rozwijanym **Połącz** , użytkownik musi wybrać subskrypcję, do której ma dostęp w **subskrypcji subskrypcja > globalna**.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Filtr subskrypcji globalnych" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Mam dostęp do równorzędnej sieci wirtualnej, ale nie widzę tam wdrożonej maszyny wirtualnej.

Upewnij się, że użytkownik ma dostęp do **odczytu** zarówno do maszyny wirtualnej, jak i do równorzędnej sieci wirtualnej. Ponadto w obszarze IAM Sprawdź, czy użytkownik ma dostęp do **odczytu** następujących zasobów:

* Rola czytelnika na maszynie wirtualnej.
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej.
* Rola czytelnika w zasobie Azure bastionu.
* Rola czytnika na Virtual Network (niezbędna, jeśli nie istnieje równorzędna Sieć wirtualna).

|Uprawnienia|Opis|Typ uprawnienia|
|---|---| ---|
|Microsoft. Network/bastionHosts/odczyt |Pobiera hosta bastionu|Akcja|
|Microsoft. Network/virtualNetworks/BastionHosts/akcja |Pobiera odwołania do hostów bastionu w Virtual Network.|Akcja|
|Microsoft. Network/virtualNetworks/bastionHosts/default/Action|Pobiera odwołania do hostów bastionu w Virtual Network.|Akcja|
|Microsoft. Network/networkInterfaces/odczyt|Pobiera definicję interfejsu sieciowego.|Akcja|
|Microsoft. Network/networkInterfaces/ipconfiguration/odczyt|Pobiera definicję konfiguracji protokołu IP interfejsu sieciowego.|Akcja|
|Microsoft. Network/virtualNetworks/odczyt|Pobierz definicję sieci wirtualnej|Akcja|
|Microsoft. Network/virtualNetworks/Subnets/virtualMachines/Read|Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej|Akcja|
|Microsoft. Network/virtualNetworks/virtualMachines/odczyt|Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej|Akcja|