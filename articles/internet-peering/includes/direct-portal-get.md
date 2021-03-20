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
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356248"
---
1. Przejdź do pozycji **grupy zasobów** i wybierz grupę zasobów wybraną podczas tworzenia zasobu **komunikacji równorzędnej** . Użyj pola **Filtr** , jeśli masz zbyt wiele grup zasobów.

    > [!div class="mx-imgBorder"]
    > ![Grupy zasobów](../media/setup-direct-get-resourcegroup.png)

1. Wybierz utworzony zasób **komunikacji równorzędnej** .

    > [!div class="mx-imgBorder"]
    > ![Strona przegląd została wybrana w lewym okienku. Zawiera informacje o PeeringResourceGroup. Na liście komunikacji równorzędnej jest wyróżniony AshburnPeering.](../media/setup-direct-get-open.png)

1. Na stronie **Przegląd** są wyświetlane informacje wysokiego poziomu, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Okienko przegląd zasobów komunikacji równorzędnej](../media/setup-direct-get-overview.png)

1. Po lewej stronie wybierz pozycję **informacje ASN** , aby wyświetlić informacje przesłane podczas tworzenia PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informacje o WYSYŁCE zasobu komunikacji równorzędnej](../media/setup-direct-get-asninfo.png)

1. Po lewej stronie wybierz pozycję **połączenia**. W górnej części ekranu zobaczysz podsumowanie połączeń komunikacji równorzędnej między numerem ASN i firmą Microsoft w różnych obiektach w linii metra. Możesz również uzyskać dostęp do podsumowania połączeń na stronie **Przegląd** , wybierając pozycję **połączenia** w środkowym okienku, jak pokazano.

    > [!div class="mx-imgBorder"]
    > ![Połączenia zasobów komunikacji równorzędnej](../media/setup-direct-get-connectionssummary.png)

    * **Stan połączenia** odpowiada stanowi konfiguracji połączenia komunikacji równorzędnej. Stany wyświetlane w tym polu są zgodne ze schematem stanu pokazanym w [przewodniku bezpośredniego komunikacji równorzędnej](../walkthrough-direct-all.md).
    * Stan **sesji IPv4** i **stan sesji IPv6** odpowiadają odpowiednio stanom sesji IPv4 i IPv6. 
    * Po zaznaczeniu wiersza w górnej części ekranu sekcja **połączenie** na dole pokazuje szczegóły dla każdego połączenia. Wybierz strzałki, aby rozwinąć **konfigurację**, **adres IPv4** i **adres IPv6**.
