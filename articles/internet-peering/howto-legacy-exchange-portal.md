---
title: Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu Azure Portal
titleSuffix: Azure
description: Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: ca26189709405ca6dc0d2954bd98f0d933963bf4
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800825"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu Azure Portal

W tym artykule opisano sposób konwertowania istniejącej starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu Azure Portal.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [programu PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj się z tematem [wymagania wstępne](prerequisites.md) i [Przewodnik po komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md) .

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure

Jako internetowy dostawca programu Exchange można utworzyć bezpośrednie żądanie komunikacji równorzędnej, [tworząc komunikację równorzędną]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **podstawowe** Wypełnij pola, tak jak pokazano poniżej:

>    [!div class="mx-imgBorder"]
>   ![Rejestrowanie usługi Peering Service](./media/setup-basics-tab.png)

* Wybierz swoją subskrypcję platformy Azure.

* W obszarze Grupa zasobów można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, wybierając pozycję Utwórz nową. W tym przykładzie utworzymy nową grupę zasobów.

* Nazwa odpowiada nazwie zasobu i może być dowolna.

* Region jest wybierany w przypadku wybrania istniejącej grupy zasobów. Jeśli wybrano opcję utworzenia nowej grupy zasobów, należy również wybrać region platformy Azure, w którym ma się znajdować zasób.

>[!NOTE]
    Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędną z firmą Microsoft. Najlepszym rozwiązaniem jest zorganizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach świadczenia usługi Azure. Na przykład w przypadku komunikacji równorzędnej w Ashburn można utworzyć grupę zasobów w regionie Wschodnie stany USA lub Wschodnie stany USA 2.

* W polu **PeerASN** wybierz swój numer ASN.

>[!IMPORTANT]  
    Przed przesłaniem żądania komunikacji równorzędnej można wybrać tylko numer ASN z ValidationState jako zatwierdzony. Jeśli żądanie PeerAsn zostało przesłane, poczekaj przez 12 godzin lub aby można było zatwierdzić skojarzenie ASN. Jeśli wybrany numer ASN oczekuje na weryfikację, zobaczysz komunikat o błędzie. Jeśli nie widzisz numeru ASN, który musisz wybrać, sprawdź, czy wybrano prawidłową subskrypcję. Jeśli tak jest, sprawdź, czy utworzono już PeerAsn za pomocą polecenia **[Skojarz równorzędny numer ASN z subskrypcją platformy Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

* Wybierz pozycję **Dalej: Konfiguracja** , aby kontynuować.

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange

Starsze połączenia komunikacji równorzędnej można skonwertować przy użyciu zasobu **komunikacji równorzędnej** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Uruchom zasób i skonfiguruj ustawienia podstawowe
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
