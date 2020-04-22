---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678852"
---
1. Przejdź do **grupy zasobów**i wybierz grupę zasobów wybraną podczas tworzenia zasobu **komunikacji równorzędnej.** Użyj pola **Filtr,** jeśli masz zbyt wiele grup zasobów.

    > [!div class="mx-imgBorder"]
    > ![Grupy zasobów](../media/setup-direct-get-resourcegroup.png)

1. Wybierz utworzony zasób **komunikacji równorzędnej.**

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-get-open.png)

1. Na stronie **Przegląd** są wyświetlane informacje wysokiego poziomu, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Okienko Przegląd zasobów komunikacji równorzędnej](../media/setup-direct-get-overview.png)

1. Po lewej stronie wybierz **pozycję Asn information** to view the information submitted when you created PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informacje o asn zasobu komunikacji równorzędnej](../media/setup-direct-get-asninfo.png)

1. Po lewej stronie wybierz pozycję **Połączenia**. W górnej części ekranu zobaczysz podsumowanie połączeń komunikacji równorzędnej między siecią ASN i Microsoft w różnych obiektach w metrze. Podsumowanie połączeń można również uzyskać na stronie **Przegląd,** wybierając pozycję **Połączenia** na środku okienka, jak pokazano na rysunku.

    > [!div class="mx-imgBorder"]
    > ![Połączenia zasobów komunikacji równorzędnej](../media/setup-direct-get-connectionssummary.png)

    * **Stan połączenia** odpowiada stanowi konfiguracji połączenia równorzędnego. Stany wyświetlane w tym polu są zgodne z diagramem stanu pokazanym w [instruktażu Direct peering](../walkthrough-direct-all.md).
    * **Stan sesji IPv4** i **stan sesji IPv6** odpowiadają odpowiednio stanom sesji Protokołu IPv4 i IPv6. 
    * Po wybraniu wiersza u góry ekranu w sekcji **Połączenie** na dole są wyświetlane szczegóły dla każdego połączenia. Wybierz strzałki, aby rozwinąć **konfigurację,** **adres IPv4**i **adres IPv6**.
