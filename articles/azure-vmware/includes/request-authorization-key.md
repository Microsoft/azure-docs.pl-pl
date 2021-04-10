---
title: Żądaj klucza autoryzacji dla ExpressRoute
description: Procedura żądania klucza autoryzacji dla ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027015"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. W Azure Portal w sekcji **łączność** na karcie **ExpressRoute** wybierz pozycję **+ Zażądaj klucza autoryzacji**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Zrzut ekranu przedstawiający sposób żądania klucza autoryzacji ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Podaj dla niego nazwę i wybierz pozycję **Utwórz**. 
      
   Utworzenie klucza może potrwać około 30 sekund. Po utworzeniu nowy klucz zostanie wyświetlony na liście kluczy autoryzacji dla chmury prywatnej.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Zrzut ekranu przedstawiający klucz autoryzacji ExpressRoute Global Reach.":::
  
1. Skopiuj klucz autoryzacji i identyfikator ExpressRoute. Będziesz ich używać do kończenia komunikacji równorzędnej.  

   > [!NOTE]
   > Klucz autoryzacji znika po pewnym czasie, więc skopiuj go, gdy tylko zostanie wyświetlony.