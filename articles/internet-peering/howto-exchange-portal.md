---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu Azure Portal
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84700484"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu Azure Portal. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [programu PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj się z tematem [wymagania wstępne](prerequisites.md) i [Przewodnik po komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md) .
* Jeśli masz już komunikację równorzędną programu Exchange z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zobacz [konwertowanie starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i Inicjowanie obsługi komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange


Jako internetowy dostawca programu Exchange można utworzyć żądanie komunikacji równorzędnej programu Exchange, [tworząc komunikację równorzędną]( https://go.microsoft.com/fwlink/?linkid=2129593).

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

* Wybierz pozycję **Dalej: Konfiguracja** , aby kontynuować.

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Anulowanie aprowizacji komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).
