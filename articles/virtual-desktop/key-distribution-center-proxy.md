---
title: Konfigurowanie serwera proxy protokołu Kerberos centrum dystrybucji kluczy Windows Virtual Desktop — Azure
description: Jak skonfigurować pulę hostów usług pulpitu wirtualnego systemu Windows do korzystania z serwera proxy centrum dystrybucji kluczy Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798420"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Konfigurowanie serwera proxy centrum dystrybucji kluczy protokołu Kerberos

W tym artykule opisano sposób konfigurowania serwera proxy centrum dystrybucji kluczy (KDC) protokołu Kerberos dla puli hostów. Ten serwer proxy umożliwia organizacjom uwierzytelnianie przy użyciu protokołu Kerberos poza granicami przedsiębiorstwa. Na przykład można użyć serwera proxy centrum dystrybucji kluczy do włączenia uwierzytelniania karty inteligentnej dla klientów zewnętrznych.

## <a name="how-to-configure-the-kdc-proxy"></a>Jak skonfigurować serwer proxy centrum dystrybucji kluczy

Aby skonfigurować serwer proxy centrum dystrybucji kluczy:

1. Zaloguj się do Azure Portal jako administrator.

2. Przejdź do strony pulpit wirtualny systemu Windows.

3. Wybierz pulę hostów, dla której chcesz włączyć serwer proxy centrum dystrybucji kluczy, a następnie wybierz pozycję **Właściwości RDP**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę Azure Portal, w której użytkownik wybiera pule hostów, a następnie nazwę przykładowej puli hostów, a następnie właściwości protokołu RDP.](media/rdp-properties.png)

4. Wybierz kartę **Zaawansowane** , a następnie wprowadź wartość w następującym formacie bez spacji:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wybraną kartę Zaawansowane z wartością wprowadzoną w kroku 4.](media/advanced-tab-selected.png)

5. Wybierz pozycję **Zapisz**.

6. Wybrana Pula hostów powinna teraz rozpoczynać pracę z plikami połączeń RDP przy użyciu wprowadzonego pola kdcproxyname.

## <a name="next-steps"></a>Następne kroki

Rola RDGateway w Usługi pulpitu zdalnego obejmuje usługę proxy centrum dystrybucji kluczy. Zobacz [Wdrażanie roli Brama usług pulpitu zdalnego w programie Virtual Desktop systemu Windows](rd-gateway-role.md) , aby określić, że ma być obiektem docelowym dla pulpitu wirtualnego systemu Windows.
