---
title: Połączenie komunikacji równorzędnej dla partnerów programu Exchange z serwerem tras przy użyciu portalu
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange z serwerem tras przy użyciu Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537195"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange z serwerem tras w Azure Portal

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange z serwerem tras przy użyciu Azure Portal. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.


## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj się z tematem [wymagania wstępne](prerequisites.md) i [Przewodnik po komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md) .
* Jeśli masz już komunikację równorzędną programu Exchange z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zobacz [konwertowanie starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i Inicjowanie obsługi komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange z serwerem tras


Jako internetowy dostawca programu Exchange można utworzyć żądanie komunikacji równorzędnej programu Exchange, [tworząc komunikację równorzędną]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **podstawowe** Wypełnij pola, tak jak pokazano poniżej:

    > [!div class="mx-imgBorder"] 
    > ![Rejestrowanie usługi Peering Service](./media/setup-basics-tab.png)

* Wybierz swoją subskrypcję platformy Azure.

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

1. Na stronie Tworzenie komunikacji równorzędnej na karcie Konfiguracja Wypełnij pola, tak jak pokazano poniżej:

    > [!div class="mx-imgBorder"]
    > ![Konfigurowanie serwera tras](./media/setup-exchange-conf-tab-routeserver.png)
 
    * W obszarze Typ komunikacji równorzędnej wybierz pozycję **bezpośrednie**
    * W przypadku sieci firmy Microsoft wybierz pozycję **AS8075 z serwerem tras programu Exchange**. 
    * Wybierz jednostkę SKU jako **bezpłatnie jako podstawową**. Nie wybieraj opcji Bezpłatna wersja Premium, ponieważ jest zarezerwowana dla aplikacji specjalnych.
    * Wybierz lokalizację **, w której chcesz** skonfigurować komunikację równorzędną.

1. W obszarze **połączenia komunikacji równorzędnej**wybierz pozycję **Utwórz nowy** .

1.  W obszarze **bezpośrednie połączenie komunikacji równorzędnej**wypełnij następujące szczegóły sesji BGP:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia okienko bezpośrednie połączenie komunikacji równorzędnej z dodatkowymi informacjami.](./media/setup-exchange-conf-tab-direct-route.png)


     * Funkcja komunikacji równorzędnej, wybierz odpowiednią lokalizację fizyczną dla komunikacji równorzędnej
     * Dostawca adresów sesji, Wybieranie elementu równorzędnego
     * Prefiks IPv4 sesji zostanie dostarczony przez element równorzędny dostawcy programu Exchange
     * Adres IPv4 sesji równorzędnej jest wybierany przez element równorzędny programu Exchange dla serwera tras z zakresu prefiksów adresów IP.
     * Adres IPv4 sesji firmy Microsoft będzie adresem IP routera przydzielonym z zakresu prefiksów adresów IP.
     * Protokół IPv6 sesji jest w tej chwili opcjonalny.
     * Maksymalny anonsowany prefiks IPv4 może należeć do 20000. 
     * Korzystanie z usługi komunikacji równorzędnej jest domyślnie wyłączone. Można ją włączyć, gdy dostawca programu Exchange podpisał umowę usługi Komunikacja równorzędna z firmą Microsoft.

1. Po zakończeniu kliknij przycisk **Zapisz**. 

1. W obszarze Tworzenie komunikacji równorzędnej zobaczysz weryfikację. Po przekazaniu walidacji kliknij pozycję **Utwórz** .

    > [!div class="mx-imgBorder"]
    > ![Sprawdzanie poprawności ustawień](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >W przypadku zwykłych usługodawców internetowych (ISP), którzy są partnerami usługi komunikacji równorzędnej firmy Microsoft, wymagana jest rejestracja prefiksów IP klienta. Jednak w przypadku partnerów programu Exchange z serwerem tras wymagane jest zarejestrowanie WPW klienta i nie prefiksów. Ten sam klucz ASN będzie ważny w przypadku rejestracji prefiksu klienta.

1. Wybierz pozycję **zarejestrowany WPW** w sekcji Ustawienia.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia okienko komunikacji równorzędnej z zarejestrowanym elementem menu S NS o nazwie out.](./media/setup-exchange-registered-asn.png)

1. Wybierz pozycję **Dodaj zarejestrowany numer ASN** , aby utworzyć nowy numer ASN klienta w ramach subskrypcji programu Exchange.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia okienko rejestr A N z polami tekstowym Nazwa i S N.](./media/setup-exchange-register-new-asn.png)

1. W obszarze zarejestruj numer ASN wybierz nazwę i wypełnij klienta, a następnie kliknij przycisk Zapisz.

1. W obszarze zarejestrowane WPW otrzymasz skojarzony klucz prefiksu przypisany do każdego numeru ASN. Jako dostawca usługi Exchange musisz podać ten klucz prefiksu klientowi, aby mógł zarejestrować usługę komunikacji równorzędnej w ramach swojej subskrypcji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia okienko zarejestrowane S NS z kluczami prefiksów.](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Anulowanie aprowizacji komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).
