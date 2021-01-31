---
title: Konfigurowanie serwera proxy protokołu Kerberos centrum dystrybucji kluczy Windows Virtual Desktop — Azure
description: Jak skonfigurować pulę hostów usług pulpitu wirtualnego systemu Windows do korzystania z serwera proxy centrum dystrybucji kluczy Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219659"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurowanie serwera proxy protokołu Kerberos centrum dystrybucji kluczy (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano sposób konfigurowania serwera proxy usługi Kerberos centrum dystrybucji kluczy (KDC) (wersja zapoznawcza) dla puli hostów. Ten serwer proxy umożliwia organizacjom uwierzytelnianie przy użyciu protokołu Kerberos poza granicami przedsiębiorstwa. Na przykład można użyć serwera proxy centrum dystrybucji kluczy do włączenia uwierzytelniania karty inteligentnej dla klientów zewnętrznych.

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
