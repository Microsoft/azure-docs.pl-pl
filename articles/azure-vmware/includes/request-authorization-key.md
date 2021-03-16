---
title: Żądaj klucza autoryzacji dla ExpressRoute
description: Procedura żądania klucza autoryzacji dla ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491852"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. W Azure Portal w sekcji **łączność** na karcie **ExpressRoute** wybierz pozycję **+ Zażądaj klucza autoryzacji**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Zrzut ekranu przedstawiający sposób żądania klucza autoryzacji ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Podaj dla niego nazwę i wybierz pozycję **Utwórz**. 
      
   Utworzenie klucza może potrwać około 30 sekund. Po utworzeniu nowy klucz zostanie wyświetlony na liście kluczy autoryzacji dla chmury prywatnej.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Zrzut ekranu przedstawiający klucz autoryzacji ExpressRoute Global Reach.":::
  
1. Zanotuj klucz autoryzacji i identyfikator ExpressRoute. Będziesz ich używać do kończenia komunikacji równorzędnej.  

   > [!NOTE]
   > Klucz autoryzacji znika po pewnym czasie, więc skopiuj go, gdy tylko zostanie wyświetlony.