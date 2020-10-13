---
title: plik dołączany
description: plik dołączany
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978136"
---
1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie wybierz pozycję **Połącz**. Wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Wybierz bastionu" border="false":::

1. Po wybraniu bastionu z listy rozwijanej zostanie wyświetlony pasek boczny z trzema kartami: RDP, SSH i bastionu. Ponieważ bastionu został zainicjowany dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Wybierz pozycję **Użyj bastionu**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Wybierz bastionu" border="false":::

1. Na stronie **nawiązywanie połączenia przy użyciu usługi Azure bastionu** wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej, a następnie wybierz pozycję **Połącz**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Wybierz bastionu" border="false":::

1. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Wybierz bastionu" border="false":::
