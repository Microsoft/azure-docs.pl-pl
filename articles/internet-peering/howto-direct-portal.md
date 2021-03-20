---
title: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal
titleSuffix: Azure
description: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537212"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia bezpośredniej komunikacji równorzędnej firmy Microsoft dla usługodawcy internetowego lub internetowego dostawcy usług Exchange przy użyciu Azure Portal. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i wyłączyć obsługę administracyjną.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik po komunikacji równorzędnej](walkthrough-direct-all.md) .
* Jeśli masz już bezpośrednie połączenia komunikacji równorzędnej z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i obsługa bezpośredniej komunikacji równorzędnej

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie bezpośredniej komunikacji równorzędnej

Jako usługodawca internetowy lub internetowy Dostawca usługi Exchange można utworzyć nowe żądanie bezpośredniego komunikacji równorzędnej, [tworząc komunikację równorzędną]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **podstawowe** Wypełnij pola, tak jak pokazano poniżej:


    ![Zrzut ekranu przedstawia kartę Tworzenie strony równorzędnej z podstawą tworzenia nowego linku.](./media/setup-basics-tab.png)

2. Wybierz swoją subskrypcję platformy Azure.

3. W obszarze Grupa zasobów można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, wybierając pozycję Utwórz nową. W tym przykładzie utworzymy nową grupę zasobów.

4. Nazwa odpowiada nazwie zasobu i może być dowolna.

5. Region jest wybierany w przypadku wybrania istniejącej grupy zasobów. Jeśli wybrano opcję utworzenia nowej grupy zasobów, należy również wybrać region platformy Azure, w którym ma się znajdować zasób.

    >[!NOTE]
    > Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędną z firmą Microsoft. Najlepszym rozwiązaniem jest zorganizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach świadczenia usługi Azure. Na przykład w przypadku komunikacji równorzędnej w Ashburn można utworzyć grupę zasobów w regionie Wschodnie stany USA lub Wschodnie stany USA 2.

6. W polu **PeerASN** wybierz swój numer ASN.

    >[!IMPORTANT]
    >Przed przesłaniem żądania komunikacji równorzędnej można wybrać tylko numer ASN z ValidationState jako zatwierdzony. Jeśli żądanie PeerAsn zostało przesłane, poczekaj przez 12 godzin lub aby można było zatwierdzić skojarzenie ASN. Jeśli wybrany numer ASN oczekuje na weryfikację, zobaczysz komunikat o błędzie. Jeśli nie widzisz numeru ASN, który musisz wybrać, sprawdź, czy wybrano prawidłową subskrypcję. Jeśli tak jest, sprawdź, czy utworzono już PeerAsn za pomocą polecenia **[Skojarz równorzędny numer ASN z subskrypcją platformy Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

7. Wybierz pozycję **Dalej: Konfiguracja** , aby kontynuować.



    ![Zrzut ekranu przedstawia kartę tworzenie podstawy komunikacji równorzędnej ze wszystkimi wprowadzonymi wartościami.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie aprowizacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).
