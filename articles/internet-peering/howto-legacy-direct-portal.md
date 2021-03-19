---
title: Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu Azure Portal
titleSuffix: Azure
description: Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 496774f034c861131624b552b1acefca9f399ea8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700267"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu Azure Portal

W tym artykule opisano sposób konwertowania istniejącej starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu Azure Portal.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [programu PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik po komunikacji równorzędnej](walkthrough-direct-all.md) .


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej

Jako usługodawca internetowy można skonwertować starsze bezpośrednie połączenia komunikacji równorzędnej za pomocą [tworzenia komunikacji równorzędnej]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **podstawowe** Wypełnij pola, tak jak pokazano poniżej:

    > [!div class="mx-imgBorder"] 
    > ![Rejestrowanie usługi Peering Service](./media/setup-basics-tab.png)

*    Wybierz swoją subskrypcję platformy Azure.

* W obszarze Grupa zasobów można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, wybierając pozycję Utwórz nową. W tym przykładzie utworzymy nową grupę zasobów.

* Nazwa odpowiada nazwie zasobu i może być dowolna.

* Region jest wybierany w przypadku wybrania istniejącej grupy zasobów. Jeśli wybrano opcję utworzenia nowej grupy zasobów, należy również wybrać region platformy Azure, w którym ma się znajdować zasób.

>[!NOTE]
>Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędną z firmą Microsoft. Najlepszym rozwiązaniem jest zorganizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach świadczenia usługi Azure. Na przykład w przypadku komunikacji równorzędnej w Ashburn można utworzyć grupę zasobów w regionie Wschodnie stany USA lub Wschodnie stany USA 2.

* W polu **PeerASN** wybierz swój numer ASN.

>[!IMPORTANT] 
>Przed przesłaniem żądania komunikacji równorzędnej można wybrać tylko numer ASN z ValidationState jako zatwierdzony. Jeśli żądanie PeerAsn zostało przesłane, poczekaj przez 12 godzin lub aby można było zatwierdzić skojarzenie ASN. Jeśli wybrany numer ASN oczekuje na weryfikację, zobaczysz komunikat o błędzie. Jeśli nie widzisz numeru ASN, który musisz wybrać, sprawdź, czy wybrano prawidłową subskrypcję. Jeśli tak jest, sprawdź, czy utworzono już PeerAsn za pomocą polecenia **[Skojarz równorzędny numer ASN z subskrypcją platformy Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Uruchom zasób i skonfiguruj ustawienia podstawowe
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
