---
title: 'Samouczek: hostować domenę i poddomenę — Azure DNS'
description: Z tego samouczka dowiesz się, jak skonfigurować Azure DNS hostowania stref DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c9c0568eb4d8a7403fc29f34a4c4e9f6e0fadecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738868"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Samouczek: hostowanie własnej domeny w usłudze Azure DNS

Za pomocą usługi Azure DNS można hostować swoją domenę DNS i zarządzać rekordami DNS. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Załóżmy, że kupujesz domenę u rejestratora nazw domen, a następnie tworzysz strefę o `contoso.net` nazwie w `contoso.net` Azure DNS. Ponieważ jesteś właścicielem domeny, rejestrator oferuje Ci opcję skonfigurowania rekordów serwera nazw (NS) dla domeny. Rejestrator przechowuje rekordy NS w strefie nadrzędnej .NET. Użytkownicy Internetu na całym świecie są kierowani do Twojej domeny w strefie usługi Azure DNS podczas próby rozpoznania rekordów DNS w strefie contoso.net.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie strefy DNS.
> * Pobieranie listy serwerów nazw.
> * Delegowanie domeny.
> * Sprawdzanie, czy delegowanie działa.


Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć dostępną nazwę domeny do testowania, która może być hostem w Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

W tym przykładzie będziemy odwoływać się do domeny nadrzędnej a `contoso.net` .

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Przejdź do [Azure Portal,](https://portal.azure.com/) aby utworzyć strefę DNS. Wyszukaj i wybierz **strefy DNS.**

   ![Strefa DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Wybierz **pozycję Utwórz strefę DNS.**

1. Na stronie **Tworzenie strefy DNS** wprowadź następujące wartości, a następnie wybierz pozycję **Utwórz**. Na przykład `contoso.net`.

   > [!NOTE] 
   > Jeśli nowo tworzymy strefę podrzędną (np. Strefa nadrzędna = Strefa podrzędna = ), zapoznaj się z samouczkiem Tworzenie nowej podrzędnej `contoso.net` `child.contoso.net` strefy [DNS](./tutorial-public-dns-zones-child.md)

    | **Ustawienie** | **Wartość** | **Szczegóły** |
    |--|--|--|
    | **Grupa zasobów**    | ContosoRG | Utwórz grupę zasobów. Nazwa grupy zasobów musi być unikatowa w ramach wybranej subskrypcji. Lokalizacja grupy zasobów nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana. |
    | **Podrzędny obszar strefy**        | pozostaw niezaznaczone | Ponieważ ta strefa nie **jest strefą** [podrzędną,](./tutorial-public-dns-zones-child.md) pozostaw to pole niezaznaczone |
    | **Nazwa**              | `contoso.net` | Pole nazwy strefy nadrzędnej      |
    | **Lokalizacja**          | East US | To pole jest oparte na lokalizacji wybranej w ramach tworzenia grupy zasobów  |
    

## <a name="retrieve-name-servers"></a>Pobieranie serwerów nazw

Aby móc delegować swoją strefę DNS do usługi Azure DNS, musisz znać serwery nazw dla swojej strefy. Azure DNS serwer nazw z puli za każdym razem, gdy jest tworzona strefa.

1. Gdy utworzysz strefę DNS, w okienku **Ulubione** witryny Azure Portal wybierz pozycję **Wszystkie zasoby**. Na stronie **Wszystkie zasoby** wybierz swoją strefę DNS. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wprowadzić nazwę domeny w polu Filtruj według **nazwy,** aby łatwo uzyskać dostęp do bramy aplikacji. 

1. Na stronie Strefa DNS pobierz serwery nazw. W tym przykładzie `contoso.net` strefie przypisano serwery nazw , , * i `ns1-01.azure-dns.com` `ns2-01.azure-dns.net` `ns3-01.azure-dns.org` `ns4-01.azure-dns.info` :

   ![Lista serwerów nazw](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Usługa Azure DNS automatycznie tworzy autorytatywne rekordy NS w strefie dla przypisanych serwerów nazw.

## <a name="delegate-the-domain"></a>Delegowanie domeny

Po utworzeniu strefy DNS i utworzeniu serwerów nazw należy zaktualizować domenę nadrzędną przy użyciu Azure DNS nazw. Każdy rejestrator ma swoje własne narzędzia do zarządzania systemem DNS służące do zmiany rekordów serwerów nazw dla domeny. 

1. Na stronie zarządzania systemem DNS rejestratora edytuj rekordy NS i zastąp je rekordami serwerów nazw usługi Azure DNS.

1. Podczas delegowania domeny do usługi Azure DNS musisz używać serwerów nazw udostępnionych przez usługę Azure DNS. Używaj wszystkich czterech serwerów nazw, niezależnie od nazwy domeny. Delegowanie domeny nie wymaga, aby serwer nazw korzystał z tej samej domeny najwyższego poziomu, co domena użytkownika.

> [!NOTE]
> Podczas kopiowania każdego z adresów serwera nazw upewnij się, że kopiujesz kropkę na końcu adresu. Końcowa kropka wskazuje koniec w pełni kwalifikowanej nazwy domeny. Niektórzy rejestratorzy dołączają kropkę, jeśli nazwa NS nie ma jej na końcu. Aby zapewnić zgodność ze specyfikacjami RFC dla systemu DNS uwzględnij końcową kropkę.

Delegowanie z wykorzystaniem serwerów nazw we własnej strefie, niekiedy nazywanych *serwerami nazw znaczących*, nie jest obecnie obsługiwane w usłudze Azure DNS.

## <a name="verify-the-delegation"></a>Weryfikowanie delegowania

Po zakończeniu delegowania możesz sprawdzić, czy działa, uruchamiając zapytanie o rekord SOA (Start of Authority) dla swojej strefy za pomocą narzędzia takiego jak *nslookup*. Rekord SOA jest automatycznie tworzony po utworzeniu strefy. Po ukończeniu delegowania może być konieczne odczekanie co najmniej 10 minut, zanim będzie można pomyślnie zweryfikować, czy działa. Jest to czas potrzebny na propagowanie zmian w systemie DNS.

Określenie serwerów nazw usługi Azure DNS nie jest konieczne. Jeśli delegowanie zostało skonfigurowane prawidłowo, normalny proces rozpoznawania nazw DNS znajdzie serwery nazw automatycznie.

1. W wierszu polecenia wprowadź polecenie nslookup podobne do następującego przykładu:

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

Wybierz grupę zasobów **contosoRG**, a następnie wybierz pozycję **Usuń grupę zasobów**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono strefę DNS dla domeny i delegowano ją do usługi Azure DNS. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
