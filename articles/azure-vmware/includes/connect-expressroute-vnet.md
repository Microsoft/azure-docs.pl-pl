---
title: Połącz ExpressRoute z bramą sieci wirtualnej
description: Procedura łączenia ExpressRoute z bramą sieci wirtualnej.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494368"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Przejdź do chmury prywatnej utworzonej w samouczku [wdrażanie klastra vSphere w usłudze Azure](../tutorial-create-private-cloud.md) . Wybierz pozycję **łączność** w obszarze **Zarządzaj**, a następnie wybierz kartę **ExpressRoute** .

1. Skopiuj klucz autoryzacji. Jeśli nie ma klucza autoryzacji, należy go utworzyć, a następnie wybrać pozycję **+ zażądać klucza autoryzacji**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Skopiuj klucz autoryzacji. Jeśli nie ma klucza autoryzacji, należy go utworzyć, a następnie wybrać pozycję + zażądać klucza autoryzacji." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Przejdź do bramy Virtual Network utworzonej w poprzednim kroku, a następnie w obszarze **Ustawienia** wybierz pozycję **połączenia**. Na stronie **połączenia** wybierz pozycję **+ Dodaj**.

1. Na stronie **Dodawanie połączenia** podaj wartości pól, a następnie wybierz **przycisk OK**. 

   | Pole | Wartość |
   | --- | --- |
   | **Nazwa**  | Wprowadź nazwę połączenia.  |
   | **Connection type** (Typ połączenia)  | Wybierz pozycję **ExpressRoute**.  |
   | **Zrealizuj autoryzację**  | Upewnij się, że to pole wyboru jest zaznaczone.  |
   | **Brama sieci wirtualnej** | Utworzona wcześniej Brama Virtual Network.  |
   | **Klucz autoryzacji**  | Skopiuj i Wklej klucz autoryzacji z karty ExpressRoute w grupie zasobów. |
   | **Identyfikator URI obwodu równorzędnego**  | Skopiuj i wklej identyfikator ExpressRoute z karty ExpressRoute dla grupy zasobów.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie połączenia w celu połączenia usługi ExpressRoute z bramą sieci wirtualnej.":::

Połączenie między obwodem usługi ExpressRoute a Virtual Network zostanie utworzone.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Zrzut ekranu z połączeniami bramy sieci wirtualnej.":::