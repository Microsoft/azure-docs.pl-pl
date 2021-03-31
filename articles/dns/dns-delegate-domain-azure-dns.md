---
title: 'Samouczek: Hostowanie domeny i poddomeny — Azure DNS'
description: W tym samouczku dowiesz się, jak skonfigurować Azure DNS do hostowania stref DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: a8f64ab3141459142def12a1758b0fe0a94ca432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282171"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Samouczek: hostowanie własnej domeny w usłudze Azure DNS

Za pomocą usługi Azure DNS można hostować swoją domenę DNS i zarządzać rekordami DNS. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Załóżmy, że masz zakupioną domenę „contoso.net” od rejestratora nazw domen i tworzysz strefę o nazwie „contoso.net” w usłudze Azure DNS. Ponieważ jesteś właścicielem domeny, rejestrator oferuje Ci opcję skonfigurowania rekordów serwerów nazw (NS) dla domeny. Rejestrator przechowuje rekordy NS w strefie nadrzędnej platformy .NET. Użytkownicy Internetu na całym świecie są kierowani do Twojej domeny w strefie usługi Azure DNS podczas próby rozpoznania rekordów DNS w strefie contoso.net.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie strefy DNS.
> * Pobieranie listy serwerów nazw.
> * Delegowanie domeny.
> * Sprawdzanie, czy delegowanie działa.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć nazwę domeny dostępną do przetestowania, aby można było hostować w Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

W tym przykładzie odwołujemy się do domeny nadrzędnej jako **contoso.NET**

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby utworzyć strefę DNS. Wyszukaj i wybierz **strefy DNS**.

   ![Strefa DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Wybierz pozycję **Utwórz strefę DNS**.
1. Na stronie **Tworzenie strefy DNS** wprowadź następujące wartości, a następnie wybierz pozycję **Utwórz**: na przykład **contoso.NET**
      > [!NOTE] 
      > Jeśli nowo tworzona strefa jest strefą podrzędną (np. Strefa nadrzędna = contoso.net podrzędne strefy = child.contoso.net), zapoznaj się z naszym [samouczkiem Tworzenie nowej podrzędnej strefy DNS](./tutorial-public-dns-zones-child.md)

    | **Ustawienie** | **Wartość** | **Szczegóły** |
    |--|--|--|
    | **Szczegóły projektu:**  |  |  |
    | **Grupa zasobów**    | ContosoRG | Utwórz grupę zasobów. Nazwa grupy zasobów musi być unikatowa w ramach wybranej subskrypcji. Lokalizacja grupy zasobów nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze "globalna" i nie jest wyświetlana. |
    | **Szczegóły wystąpienia:** |  |  |
    | **Element podrzędny strefy**        | pozostaw niezaznaczone | Ponieważ ta strefa **nie** jest [strefą podrzędną](./tutorial-public-dns-zones-child.md) , należy pozostawić zaznaczenie tego pola wyboru |
    | **Nazwa**              | contoso.net | Pole nazwy strefy nadrzędnej      |
    | **Lokalizacja**          | East US | To pole jest oparte na lokalizacji wybranej w ramach tworzenia grupy zasobów  |
    

## <a name="retrieve-name-servers"></a>Pobieranie serwerów nazw

Aby móc delegować swoją strefę DNS do usługi Azure DNS, musisz znać serwery nazw dla swojej strefy. Usługa Azure DNS przydziela serwery nazw z puli za każdym razem, gdy tworzona jest strefa.

1. Gdy utworzysz strefę DNS, w okienku **Ulubione** witryny Azure Portal wybierz pozycję **Wszystkie zasoby**. Na stronie **Wszystkie zasoby** wybierz swoją strefę DNS. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wprowadzić nazwę Twojej domeny w polu **Filtruj według nazwy**, aby łatwo uzyskać dostęp do bramy aplikacji. 

1. Na stronie Strefa DNS pobierz serwery nazw. W tym przykładzie do contoso.net strefy zostały przypisane serwery nazw *ns1-01.Azure-DNS.com*, *NS2-01.Azure-DNS.NET*, *NS3-01.Azure-DNS.org* i *NS4-01.Azure-DNS.info*:

   ![Lista serwerów nazw](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Usługa Azure DNS automatycznie tworzy autorytatywne rekordy NS w strefie dla przypisanych serwerów nazw.

## <a name="delegate-the-domain"></a>Delegowanie domeny

Po utworzeniu strefy DNS i pobraniu serwerów nazw należy zaktualizować domenę nadrzędną przy użyciu informacji o serwerach nazw usługi Azure DNS. Każdy rejestrator ma swoje własne narzędzia do zarządzania systemem DNS służące do zmiany rekordów serwerów nazw dla domeny. 

1. Na stronie zarządzania systemem DNS rejestratora edytuj rekordy NS i zastąp je rekordami serwerów nazw usługi Azure DNS.

1. Podczas delegowania domeny do usługi Azure DNS musisz używać serwerów nazw udostępnionych przez usługę Azure DNS. Używaj wszystkich czterech serwerów nazw, niezależnie od nazwy domeny. Delegowanie domeny nie wymaga, aby serwer nazw korzystał z tej samej domeny najwyższego poziomu, co domena użytkownika.

> [!NOTE]
> Podczas kopiowania każdego z adresów serwera nazw upewnij się, że kopiujesz kropkę na końcu adresu. Końcowa kropka wskazuje koniec w pełni kwalifikowanej nazwy domeny. Niektórzy rejestratorzy dołączają kropkę, jeśli nazwa NS nie ma jej na końcu. Aby zapewnić zgodność ze specyfikacjami RFC dla systemu DNS uwzględnij końcową kropkę.

Delegowanie z wykorzystaniem serwerów nazw we własnej strefie, niekiedy nazywanych *serwerami nazw znaczących*, nie jest obecnie obsługiwane w usłudze Azure DNS.

## <a name="verify-the-delegation"></a>Weryfikowanie delegowania

Po zakończeniu delegowania możesz sprawdzić, czy działa, uruchamiając zapytanie o rekord SOA (Start of Authority) dla swojej strefy za pomocą narzędzia takiego jak *nslookup*. Rekord SOA jest automatycznie tworzony po utworzeniu strefy. Po zakończeniu delegowania może być konieczne odczekanie co najmniej 10 minut, zanim będzie można pomyślnie zweryfikować jego poprawne działanie. Jest to czas potrzebny na propagowanie zmian w systemie DNS.

Określenie serwerów nazw usługi Azure DNS nie jest konieczne. Jeśli delegowanie zostało skonfigurowane prawidłowo, normalny proces rozpoznawania nazw DNS znajdzie serwery nazw automatycznie.

1. W wierszu polecenia wprowadź polecenie nslookup podobne do poniższego przykładu:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Sprawdź, czy odpowiedź wygląda podobnie do następujących danych wyjściowych polecenia nslookup:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wykonanie następnego samouczka, możesz zachować grupę zasobów **contosoRG**. W przeciwnym razie usuń grupę zasobów **contosoRG**, co spowoduje usunięcie zasobów utworzonych w ramach tego samouczka.

- Wybierz grupę zasobów **contosoRG**, a następnie wybierz pozycję **Usuń grupę zasobów**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono strefę DNS dla domeny i delegowano ją do usługi Azure DNS. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
