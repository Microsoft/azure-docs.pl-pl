---
title: Połącz ExpressRoute z bramą sieci wirtualnej
description: Procedura łączenia ExpressRoute z bramą sieci wirtualnej.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 241919e3a69b8d1c3c24e6c894bcbf20aea62d5f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578357"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Przejdź do chmury prywatnej utworzonej w poprzednim samouczku i wybierz pozycję **łączność** w obszarze **Zarządzanie**, wybierz kartę **ExpressRoute** .

1. Skopiuj klucz autoryzacji. Jeśli nie ma klucza autoryzacji, należy go utworzyć, a następnie wybrać pozycję **+ zażądać klucza autoryzacji**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Skopiuj klucz autoryzacji. Jeśli nie ma klucza autoryzacji, należy go utworzyć, a następnie wybrać pozycję + zażądać klucza autoryzacji." border="true":::

1. Przejdź do bramy Virtual Network utworzonej w poprzednim kroku, a następnie w obszarze **Ustawienia**wybierz pozycję **połączenia**. Na stronie **połączenia** wybierz pozycję **+ Dodaj**.

1. Na stronie **Dodawanie połączenia** podaj wartości pól, a następnie wybierz **przycisk OK**. 

   | Pole | Wartość |
   | --- | --- |
   | **Nazwa**  | Wprowadź nazwę połączenia.  |
   | **Connection type** (Typ połączenia)  | Wybierz pozycję **ExpressRoute**.  |
   | **Zrealizuj autoryzację**  | Upewnij się, że to pole wyboru jest zaznaczone.  |
   | **Brama sieci wirtualnej** | Utworzona wcześniej Brama Virtual Network.  |
   | **Klucz autoryzacji**  | Skopiuj i Wklej klucz autoryzacji z karty ExpressRoute w grupie zasobów. |
   | **Identyfikator URI obwodu równorzędnego**  | Skopiuj i wklej identyfikator ExpressRoute z karty ExpressRoute dla grupy zasobów.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Skopiuj klucz autoryzacji. Jeśli nie ma klucza autoryzacji, należy go utworzyć, a następnie wybrać pozycję + zażądać klucza autoryzacji." border="true":::

Połączenie między obwodem usługi ExpressRoute a Virtual Network zostanie utworzone.