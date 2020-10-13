---
title: Kojarzenie równorzędnego numeru ASN z subskrypcją platformy Azure przy użyciu portalu
titleSuffix: Azure
description: Kojarzenie równorzędnego numeru ASN z subskrypcją platformy Azure przy użyciu portalu
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540272"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Kojarzenie równorzędnego numeru ASN z subskrypcją platformy Azure przy użyciu portalu

Przed przesłaniem żądania komunikacji równorzędnej jako usługodawca internetowy lub internetowy Dostawca usługi Exchange należy najpierw skojarzyć numer ASN z subskrypcją platformy Azure, wykonując poniższe kroki.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Utwórz PeerAsn, aby skojarzyć swój numer ASN z subskrypcją platformy Azure

### <a name="sign-in-to-the-portal"></a>Zaloguj się do portalu
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Zarejestruj dla dostawcy zasobów komunikacji równorzędnej
Zarejestruj się w celu uzyskania dostawcy zasobów komunikacji równorzędnej w ramach subskrypcji, wykonując poniższe kroki. Jeśli to nie zrobisz, zasoby platformy Azure wymagane do skonfigurowania komunikacji równorzędnej są niedostępne.

1. Kliknij pozycję **subskrypcje** w lewym górnym rogu portalu. Jeśli go nie widzisz, kliknij pozycję **więcej usług** i wyszukaj go.

    > [!div class="mx-imgBorder"]
    > ![Otwarte subskrypcje](./media/rp-subscriptions-open.png)

1. Kliknij subskrypcję, której chcesz użyć do komunikacji równorzędnej.

    > [!div class="mx-imgBorder"]
    > ![Uruchom subskrypcję](./media/rp-subscriptions-launch.png)

1. Po otwarciu subskrypcji po lewej stronie kliknij pozycję **dostawcy zasobów**. Następnie w okienku po prawej stronie Wyszukaj *komunikację równorzędną* w oknie wyszukiwania lub Użyj paska przewijania, aby znaleźć pozycję **Microsoft. peering** i sprawdzić **stan**. Jeśli stan jest ***zarejestrowany***, Pomiń poniższe kroki i przejdź do sekcji **Tworzenie PeerAsn**. Jeśli stan to ***NotRegistered***, wybierz pozycję **Microsoft. Komunikacja równorzędna** i kliknij pozycję **zarejestruj**.

    > [!div class="mx-imgBorder"]
    > ![Rozpoczęcie rejestracji](./media/rp-register-start.png)

1. Zwróć uwagę, że stan zmieni się na ***zarejestrowanie***.

    > [!div class="mx-imgBorder"]
    > ![Rejestracja w toku](./media/rp-register-progress.png)

1. Poczekaj chwilę lub na ukończenie rejestracji. Następnie kliknij przycisk **Odśwież** i sprawdź, czy stan jest ***zarejestrowany***.

    > [!div class="mx-imgBorder"]
    > ![Zakończono rejestrację](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Utwórz PeerAsn
Jako usługodawca internetowy lub internetowy dostawca programu Exchange można utworzyć nowy zasób PeerAsn na potrzeby kojarzenia numeru systemu autonomicznego (ASN) z subskrypcją platformy Azure na [stronie kojarzenie równorzędnej strony ASN](https://go.microsoft.com/fwlink/?linkid=2129592) . Można skojarzyć wiele numerów WPW z subskrypcją, tworząc **PeerAsn** dla każdego numeru ASN, który należy skojarzyć.

1. Na stronie **Skojarz element równorzędny ASN** , w obszarze **podstawowe** karty, wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Karta podstawy PeerAsn](./media/peerasn-basics-tab.png)

    * **Nazwa** odpowiada nazwie zasobu i może być dowolna.  
    * Wybierz **subskrypcję** , która ma być skojarzona z numerem ASN.
    * **Nazwa elementu równorzędnego** odpowiada nazwie firmy i musi być możliwie jak najbliżej profilu PeeringDB. Należy zauważyć, że wartość obsługuje tylko znaki a-z, A-Z i spację
    * Wprowadź numer ASN w polu **peer ASN** .
    * Kliknij pozycję **Utwórz nowy** i wprowadź **adres E-mail** i **numer telefonu** dla centrum operacji sieciowych (noc).
1. Następnie kliknij pozycję **Przegląd + Utwórz** i obserwuj, że w portalu jest uruchamiana podstawowa weryfikacja wprowadzonych informacji. Ten element jest wyświetlany na Wstążce u góry, jak *trwa sprawdzanie poprawności końcowej...*

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia kartę skojarzenie równorzędne A S N.](./media/peerasn-review-tab-validation.png)

1. Gdy komunikat na Wstążce *przejdzie do walidacji*, sprawdź informacje i prześlij żądanie, klikając pozycję **Utwórz**. Jeśli sprawdzanie poprawności nie zostanie zakończone, kliknij przycisk **Wstecz** i powtórz powyższe kroki w celu zmodyfikowania żądania i upewnienia się, że wprowadzone wartości nie mają żadnych błędów.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia kartę skojarzenie równorzędne A S N z zakończono walidacją.](./media/peerasn-review-tab.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone poniżej.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn sukces](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Wyświetl stan elementu PeerAsn
Po pomyślnym wdrożeniu zasobu PeerAsn należy poczekać, aż firma Microsoft zatwierdzi żądanie skojarzenia. Zatwierdzenie może potrwać do 12 godzin. Po zatwierdzeniu otrzymasz powiadomienie o adresie e-mail wprowadzonym w powyższej sekcji.

> [!IMPORTANT]
> Poczekaj, aż ValidationState przełączy "zatwierdzone" przed przesłaniem żądania komunikacji równorzędnej. Zatwierdzenie może potrwać do 12 godzin.

## <a name="modify-peerasn"></a>Modyfikuj PeerAsn
Modyfikowanie PeerAsn nie jest obecnie obsługiwane. Jeśli musisz zmodyfikować, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Usuń PeerAsn
Usuwanie elementu PeerAsn nie jest obecnie obsługiwane. Jeśli musisz usunąć PeerAsn, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)
