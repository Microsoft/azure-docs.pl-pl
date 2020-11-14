---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "92479590"
---
1. Otwórz stronę bramy sieci wirtualnej. Możesz przejść do bramy, przechodząc do **nazwy sieci wirtualnej — omówienie >-> podłączone urządzenia — > nazwę bramy** , chociaż istnieje wiele innych sposobów nawigowania.
1. Na stronie bramy wybierz pozycję **połączenia**. W górnej części strony połączenia wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Dodawanie połączenia** .

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Połączenie lokacja-lokacja":::
1. Na stronie **Dodawanie połączenia** skonfiguruj wartości dla swojego połączenia.

   * **Nazwa:** nazwij połączenie.
   * **Typ połączenia:** Wybierz pozycję **lokacja-lokacja (IPSec)**.
   * **Brama sieci wirtualnej:** wartość jest stała, ponieważ połączenie jest nawiązywane z tej bramy.
   * **Brama sieci lokalnej:** Wybierz pozycję **Wybierz bramę sieci lokalnej** i wybierz bramę sieci lokalnej, która ma być używana.
   * **Klucz wspólny:** wartość musi być zgodna z wartością używaną dla lokalnego urządzenia sieci VPN. W przykładzie użyto wartości „abc123”, ale można (i należy) użyć bardziej złożonej wartości. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.
   * Nie zaznaczaj **opcji Użyj prywatnego adresu IP platformy Azure** .
   * Pozostaw zaznaczenie pola wyboru **Włącz protokół BGP** .
   * Wybierz pozycję **IKEv2**.
   * Pozostałe wartości w polach **Subskrypcja** , **Grupa zasobów** i **Lokalizacja** są ustalone.

1. Wybierz **przycisk OK** , aby utworzyć połączenie. Na ekranie zostanie wyświetlony napis *Tworzenie połączenia*.
1. Połączenie będzie widoczne na stronie **Połączenia** bramy sieci wirtualnej. Stan zmieni się z *Nieznany* na *Łączenie* , a następnie na *Powiodło się*.
