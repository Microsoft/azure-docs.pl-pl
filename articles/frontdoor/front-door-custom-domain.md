---
title: Samouczek — Dodawanie domeny niestandardowej do konfiguracji drzwi platformy Azure
description: W tym samouczku dowiesz się, jak dodać domenę niestandardową do usługi Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: duau
ms.openlocfilehash: e1540602bae0779d69c0cb4bb59e93b810b52904
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550765"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Samouczek: dodawanie domeny niestandardowej do usługi Front Door

W tym Samouczku opisano, jak dodać domenę niestandardową do usługi Front Door. W przypadku korzystania z usługi Azure Front Drzwiczks do dostarczania aplikacji niestandardowa domena jest potrzebna, jeśli chcesz, aby Twoja nazwa domeny była widoczna na żądanie użytkownika końcowego. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem.

Po utworzeniu drzwi czołowych domyślnym hostem frontonu, który jest poddomeną `azurefd.net` , jest dołączony do adresu URL w celu dostarczania zawartości z tyłu z zaplecza domyślnie (na przykład https: \/ /contoso-frontend.azurefd.NET/activeusers.htm). Dla Twojej wygody usługa Azure Front Door udostępnia opcję kojarzenia domeny niestandardowej z hostem domyślnym. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy domeny należącej do usługi Front Door (np. https:\//www.contoso.com/photo.png). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z usługą Front Door.
> - Weryfikowanie domeny niestandardowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Drzwi z przodu **nie obsługują domen** niestandardowych z [formacie Punycode](https://en.wikipedia.org/wiki/Punycode) znaków. 

## <a name="prerequisites"></a>Wymagania wstępne

* Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz temat [Quickstart: Create a Front Door for a highly available global web application](quickstart-create-front-door.md) (Szybki start: tworzenie usługi Front Door na potrzeby łatwo dostępnej globalnej aplikacji internetowej).

* Jeśli nie masz jeszcze domeny niestandardowej, musisz ją najpierw kupić od dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](../app-service/manage-custom-dns-buy-domain.md).

* Jeśli używasz platformy Azure do hostowania [domen DNS](../dns/dns-overview.md), musisz delegować system nazw domen (DNS) dostawcy domen do usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](../dns/dns-delegate-domain-azure-dns.md). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, przejdź do sekcji [Tworzenie rekordu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Tworzenie rekordu CNAME usługi DNS

Przed rozpoczęciem używania domeny niestandardowej z usługą Front Door należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) z pomocą dostawcy domeny, aby wskazać na domyślny host frontonu usługi Front Door (powiedzmy contoso.azurefd.net). Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej. W przypadku drzwi frontonu platformy Azure nazwa domeny źródłowej to nazwa domeny niestandardowej, a nazwa domeny docelowej to domyślna nazwa hosta drzwi. Po zweryfikowaniu utworzonego przez Ciebie rekordu CNAME ruch skierowany do źródłowej domeny niestandardowej (na przykład www \. contoso.com) jest kierowany do określonego docelowego hosta frontonu (na przykład contoso-frontend.azurefd.NET). 

Domenę niestandardową i jej poddomenę można skojarzyć tylko z jednymi przednimi drzwiami w danym momencie. Można jednak używać różnych poddomen z tej samej domeny niestandardowej dla różnych drzwi przednich przy użyciu wielu rekordów CNAME. Możesz również zmapować domenę niestandardową o różne domeny podrzędne na te same tylne drzwi.


## <a name="map-the-temporary-afdverify-subdomain"></a>Mapowanie tymczasowej domeny podrzędnej afdverify

W przypadku mapowania istniejącej domeny, która znajduje się w środowisku produkcyjnym, należy wziąć pod uwagę zagadnienia specjalne. W przypadku rejestrowania domeny niestandardowej w witrynie Azure Portal może wystąpić krótki przestój domeny. Aby uniknąć przerwania ruchu w sieci Web, najpierw zamapuj domenę niestandardową na domyślnego hosta frontonu z systemem Azure afdverify, aby utworzyć tymczasowe mapowanie CNAME. Przy użyciu tej metody użytkownicy mogą uzyskiwać dostęp do domeny bez zakłóceń w trakcie mapowania DNS.

W przeciwnym razie, jeśli używasz domeny niestandardowej po raz pierwszy i nie ma w niej żadnego ruchu związanego ze środowiskiem produkcyjnym, domenę niestandardową można mapować bezpośrednio do usługi Front Door. Przejdź do sekcji [Mapowanie trwałej domeny niestandardowej](#map-the-permanent-custom-domain).

Aby utworzyć rekord CNAME z poziomu domeny podrzędnej afdverify:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy                    | Typ  | Element docelowy                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Źródło: Wprowadź nazwę domeny niestandardowej, w tym poddomenę afdverify, w następującym formacie: afdverify. _&lt; niestandardowa &gt; nazwa domeny_. Na przykład afdverify.www.contoso.com. W przypadku mapowania domeny symboli wieloznacznych, takich jak \* contoso.com, wartość źródłowa jest taka sama jak w przypadku symboli wieloznacznych: afdverify.contoso.com.

    - Typ: wprowadź wartość *CNAME*.

    - Miejsce docelowe: Wprowadź swój domyślny Host frontonu frontonu, w tym poddomenę afdverify, w następującym formacie: afdverify. _&lt; nazwa &gt; punktu końcowego_. azurefd.NET. Na przykład afdverify.contoso-frontend.azurefd.net.

4. Zapisz zmiany.

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij następujące pola wpisu CNAME:

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia, z uwzględnieniem nazwy domeny podrzędnej afdverify. Na przykład afdverify.www.

    - Wskazuje na: wprowadź nazwę hosta domyślnego hosta frontonu usługi Front Door, z uwzględnieniem nazwy domeny podrzędnej afdverify. Na przykład afdverify.contoso-frontend.azurefd.net. 

    - Czas wygaśnięcia: Wybierz *jedną godzinę* .

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Kojarzenie domeny niestandardowej z usługą Front Door

Po zarejestrowaniu domeny niestandardowej można dodać ją do usługi Front Door.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i przejdź do usługi Front Door zawierającej hosta frontonu, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **projektanta usługi Front Door** kliknij „+”, aby dodać domenę niestandardową.
    
3. Wybierz opcję **Domena niestandardowa**. 

4. Dla opcji **Host frontonu** wstępnie wypełniony jest host frontonu do użycia jako domena docelowa rekordu CNAME, utworzony na podstawie usługi Front Door: *&lt;domyślna nazwa hosta&gt;*.azurefd.net. Nie można zmienić tej nazwy.

5. W obszarze **Niestandardowa nazwa hosta** wprowadź domenę niestandardową (razem z poddomeną), która ma być używana jako domena źródłowa rekordu CNAME. Na przykład www \. contoso.com lub CDN.contoso.com. Nie używaj nazwy domeny podrzędnej afdverify.

6. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana.

>[!WARNING]
> **Musisz** upewnić się, że każdy z hostów frontonu (z uwzględnieniem domen niestandardowych) w usłudze Front Door ma regułę rozsyłania ze skojarzoną z nim domyślną ścieżką („/\*”). Oznacza to, że wśród wszystkich reguł rozsyłania musi być co najmniej jedna reguła rozsyłania dla każdego z hostów frontonu zdefiniowanych w ścieżce domyślnej („/\*”). Jeśli tak nie będzie, ruch użytkowników końcowych może nie być kierowany prawidłowo.

## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zakończeniu rejestracji domeny niestandardowej sprawdź, czy odwołuje się ona do domyślnego hosta frontonu usługi Front Door.
 
W przeglądarce przejdź do adresu pliku przy użyciu domeny niestandardowej. Jeśli na przykład domena niestandardowa to robotics.contoso.com, adres URL buforowanego pliku powinien być podobny do następującego: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Sprawdź, czy wynik jest taki sam jak w przypadku uzyskiwania dostępu do przednich drzwi bezpośrednio z *&lt; przodu &gt;*. azurefd.NET.


## <a name="map-the-permanent-custom-domain"></a>Mapowanie trwałej domeny niestandardowej

Jeśli sprawdzono, że domena podrzędna afdverify została pomyślnie mapowana do usługi Front Door (lub jeśli używasz nowej domeny niestandardowej, która nie znajduje się w środowisku produkcyjnym), można mapować domenę niestandardową bezpośrednio do domyślnego hosta frontonu usługi Front Door.

Aby utworzyć rekord CNAME dla domeny niestandardowej:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny sieci Web z etykietą **nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy          | Typ  | Element docelowy           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Źródło: wprowadź niestandardową nazwę domeny (na przykład www \. contoso.com).

   - Typ: wprowadź wartość *CNAME*.

   - Element docelowy: wprowadź domyślny host frontonu usługi Front Door. Musi mieć następujący format:_&lt; hostname &gt;_. azurefd.NET. Na przykład contoso-frontend.azurefd.net.

4. Zapisz zmiany.

5. Jeśli wcześniej utworzono rekord CNAME tymczasowej domeny podrzędnej afdverify, usuń go. 

6. Jeśli używasz tej domeny niestandardowej w środowisku produkcyjnym po raz pierwszy, wykonaj kroki z sekcji [Kojarzenie domeny niestandardowej z usługą Front Door](#associate-the-custom-domain-with-your-front-door) i [Weryfikowanie domeny niestandardowej](#verify-the-custom-domain).

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij pola wpisu CNAME:

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia. Na przykład www lub profil.

    - Wskazuje na: wprowadź domyślną nazwę hosta usługi Front Door. Na przykład contoso.azurefd.net. 

    - Czas wygaśnięcia: Wybierz *jedną godzinę* .

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

7. Jeśli masz rekord CNAME domeny afdverify, wybierz ikonę ołówka obok niego, a następnie wybierz ikonę kosza.

8. Wybierz pozycję **Usuń**, aby usunąć rekord CNAME.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach dodano domenę niestandardową do usługi Front Door. Jeśli nie chcesz skojarzyć usługi Front Door z domeną niestandardową, możesz usunąć domenę niestandardową, wykonując następujące kroki:
 
1. W projektancie usługi Front Door wybierz domenę niestandardową, która ma zostać usunięta.

2. Kliknij przycisk Usuń z menu kontekstowego dla domeny niestandardowej.  

   Skojarzenie domeny niestandardowej z punktem końcowym zostanie usunięte.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie rekordu DNS CNAME.
* Kojarzenie domeny niestandardowej z usługą Front Door.
* Weryfikowanie domeny niestandardowej.

Aby dowiedzieć się, jak włączyć protokół HTTPS dla domeny niestandardowej, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie protokołu HTTPS dla domeny niestandardowej](front-door-custom-domain-https.md)
