---
title: Samouczek — dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dodać domenę niestandardową do punktu końcowego usługi Azure Content Delivery Network, aby nazwa domeny była widoczna w adresie URL.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 08bbe1000d457cc4f4d6b655051ec640d4dcecf4
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362099"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN
W tym samouczku przedstawiono sposób dodawania domeny niestandardowej do punktu końcowego usługi Azure Content Delivery Network (CDN). W przypadku dostarczania zawartości przy użyciu punktu końcowego usługi CDN domena niestandardowa jest niezbędna, jeśli chcesz, aby nazwa Twojej domeny była widoczna w adresie URL usługi CDN. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem. 

Po utworzeniu punktu końcowego usługi CDN w profilu nazwa punktu końcowego, czyli domena podrzędna domeny azureedge.net, jest uwzględniana w adresie URL w celu domyślnego dostarczania zawartości usługi CDN (np. https:\//contoso.azureedge.net/photo.png). Dla Twojej wygody usługa Azure CDN udostępnia opcję kojarzenia domeny niestandardowej z punktem końcowym usługi CDN. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy punktu końcowego (np. https:\//www.contoso.com/photo.png). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN.
> - Weryfikowanie domeny niestandardowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć profil usługi CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

Jeśli nie masz jeszcze domeny niestandardowej, musisz ją najpierw kupić od dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Jeśli używasz platformy Azure do hostowania [domen DNS](https://docs.microsoft.com/azure/dns/dns-overview), musisz delegować system nazw domen (DNS) dostawcy domen do usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, przejdź do sekcji [Tworzenie rekordu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Tworzenie rekordu CNAME usługi DNS

Przed rozpoczęciem używania domeny niestandardowej z punktem końcowym usługi Azure CDN należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny, aby wskazać na punkt końcowy usługi CDN. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej. W przypadku usługi Azure CDN nazwa domeny źródłowej to nazwa domeny niestandardowej, a nazwa domeny docelowej to nazwa hosta punktu końcowego usługi CDN. Po Azure CDN weryfikuje utworzony rekord CNAME, ruch skierowany do źródłowej domeny niestandardowej (na przykład www \. contoso.com) jest kierowany do określonej docelowej nazwy hosta punktu końcowego usługi CDN (np. contoso.azureedge.NET). 

Domenę niestandardową i jej domenę podrzędną można skojarzyć tylko z jednym punktem końcowym równocześnie. Można jednak używać różnych domen podrzędnych z tej samej domeny niestandardowej dla różnych punktów końcowych platformy Azure, korzystając z wielu rekordów CNAME. Można również mapować domenę niestandardową z różnymi domenami podrzędnymi do tego samego punktu końcowego usługi CDN.

> [!NOTE]
> Dowolnego typu rekordu aliasu można użyć w przypadku domen niestandardowych, jeśli używasz Azure DNS jako dostawcy domeny. W tym instruktażu jest stosowany typ rekordu CNAME. Jeśli używasz typów rekordów AAAA lub, wykonaj te same kroki i Zastąp rekord CNAME wybranym typem. Jeśli używasz rekordu aliasu, aby dodać domenę główną jako domenę niestandardową, i chcesz włączyć protokół TLS, musisz użyć ręcznej walidacji, zgodnie z opisem w [tym artykule](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint). Aby uzyskać więcej informacji, zobacz [punkty końcowe strefy wskazywania na Azure CDN punktów końcowych](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints).

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapowanie tymczasowej domeny podrzędnej cdnverify

W przypadku mapowania istniejącej domeny, która znajduje się w środowisku produkcyjnym, należy wziąć pod uwagę zagadnienia specjalne. W przypadku rejestrowania domeny niestandardowej w witrynie Azure Portal może wystąpić krótki przestój domeny. Aby uniknąć zakłóceń ruchu internetowego, należy najpierw przeprowadzić mapowanie domeny niestandardowej do nazwy hosta punktu końcowego usługi CDN przy użyciu domeny podrzędnej cdnverify platformy Azure w celu utworzenia tymczasowego mapowania CNAME. Przy użyciu tej metody użytkownicy mogą uzyskiwać dostęp do domeny bez zakłóceń w trakcie mapowania DNS. 

W przeciwnym razie, jeśli używasz domeny niestandardowej po raz pierwszy i nie ma w niej żadnego ruchu związanego ze środowiskiem produkcyjnym, domenę niestandardową można mapować bezpośrednio do punktu końcowego usługi CDN. Przejdź do sekcji [Mapowanie trwałej domeny niestandardowej](#map-the-permanent-custom-domain).

Aby utworzyć rekord CNAME z poziomu poddomeny cdnverify:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy                    | Typ  | Element docelowy                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Źródło: Wprowadź nazwę domeny niestandardowej, w tym poddomenę cdnverify, w następującym formacie: cdnverify. &lt; niestandardowa nazwa domeny &gt; . Na przykład: cdnverify.www.contoso.com.

    - Typ: wprowadź wartość *CNAME*.

    - Miejsce docelowe: Wprowadź nazwę hosta punktu końcowego usługi CDN, w tym poddomenę cdnverify, w następującym formacie: cdnverify. _ &lt; nazwa &gt; punktu końcowego_. azureedge.NET. Na przykład: cdnverify.contoso.azureedge.net.

4. Zapisz zmiany.

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij następujące pola wpisu CNAME:

    ![Zrzut ekranu przedstawia wpis nazwy C z typem, hostem, punktami do i T T L wartości dla tymczasowej domeny podrzędnej cdnverify.](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia, z uwzględnieniem nazwy domeny podrzędnej cdnverify. Na przykład: cdnverify.www.

    - Wskazuje na: wprowadź nazwę hosta punktu końcowego usługi CDN, z uwzględnieniem nazwy domeny podrzędnej cdnverify. Na przykład: cdnverify.contoso.azureedge.net. 

    - Czas wygaśnięcia: pozostaw wybraną wartość *1 godzina*.

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

    ![Zrzut ekranu pokazuje, że wpis nazwy języka C został dodany do tabeli rekordów w sieci D dla tymczasowej domeny podrzędnej cdnverify.](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Tworzenie skojarzenia domeny niestandardowej z punktem końcowym usługi CDN

Po zarejestrowaniu domeny niestandardowej można dodać ją do punktu końcowego usługi CDN. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i przejdź do profilu usługi CDN zawierającego punkt końcowy, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **Profil CDN** wybierz punkt końcowy usługi CDN do skojarzenia z domeną niestandardową.

   Zostanie otwarta strona **Punkt końcowy**.
    
3. Wybierz pozycję **Domena niestandardowa**. 

   ![Przycisk domeny niestandardowej usługi CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Zostanie otwarta strona **Dodawanie domeny niestandardowej**.

4. W obszarze **Nazwa hosta punktu końcowego** wstępnie wypełniono nazwę hosta punktu końcowego używanego jako domena docelowa rekordu CNAME. Ta nazwa pochodzi z adresu URL punktu końcowego usługi CDN: *&lt;nazwa hosta punktu końcowego&gt;*.azureedge.net. Nie można zmienić tej nazwy.

5. W obszarze **Niestandardowa nazwa hosta** wprowadź domenę niestandardową (razem z poddomeną), która ma być używana jako domena źródłowa rekordu CNAME. Na przykład www \. contoso.com lub CDN.contoso.com. Nie używaj nazwy domeny podrzędnej cdnverify.

   ![Okno dialogowe domeny niestandardowej usługi CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana. 

   Propagacja ustawień nowej domeny niestandardowej do wszystkich węzłów brzegowych usługi CDN może zająć trochę czasu: 
    - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
    - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
    - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut.   


## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zakończeniu rejestracji domeny niestandardowej sprawdź, czy odwołuje się ona do punktu końcowego usługi CDN.
 
1. Upewnij się, że masz publiczną zawartość buforowaną w punkcie końcowym. Jeśli na przykład punkt końcowy usługi CDN został skojarzony z kontem magazynu, usługa Azure CDN będzie buforować zawartość w kontenerze publicznym. Aby przetestować domenę niestandardową, sprawdź, czy kontener został ustawiony tak, aby zezwalać na publiczny dostęp, i czy zawiera co najmniej jeden plik.

2. W przeglądarce przejdź do adresu pliku przy użyciu domeny niestandardowej. Na przykład jeśli domena niestandardowa to `www.contoso.com` , adres URL pliku w pamięci podręcznej powinien wyglądać podobnie do następującego adresu URL: `http://www.contoso.com/my-public-container/my-file.jpg` . Sprawdź, czy wynik jest taki sam jak w przypadku uzyskiwania dostępu do punktu końcowego usługi CDN bezpośrednio w * &lt; punkcie nazw hostname &gt; *. azureedge.NET.


## <a name="map-the-permanent-custom-domain"></a>Mapowanie trwałej domeny niestandardowej

Jeśli sprawdzono, czy domena podrzędna cdnverify została pomyślnie mapowana do punktu końcowego (lub jeśli używasz nowej domeny niestandardowej, która nie znajduje się w środowisku produkcyjnym), można mapować domenę niestandardową bezpośrednio do nazwy hosta punktu końcowego usługi CDN.

Aby utworzyć rekord CNAME dla domeny niestandardowej:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny sieci Web z etykietą **nazwa domeny**, **DNS**lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy          | Typ  | Element docelowy           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Źródło: wprowadź niestandardową nazwę domeny (na przykład www \. contoso.com).

   - Typ: wprowadź wartość *CNAME*.

   - Miejsce docelowe: wprowadź nazwę hosta punktu końcowego usługi CDN. Musi mieć następujący format:_ &lt; nazwa &gt; punktu końcowego_. azureedge.NET. Na przykład: contoso.azureedge.net.

4. Zapisz zmiany.

5. Jeśli wcześniej utworzono rekord CNAME tymczasowej domeny podrzędnej cdnverify, usuń go. 

6. Jeśli używasz tej domeny niestandardowej w środowisku produkcyjnym po raz pierwszy, wykonaj kroki z sekcji [Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN](#associate-the-custom-domain-with-your-cdn-endpoint) i [Weryfikowanie domeny niestandardowej](#verify-the-custom-domain).

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij pola wpisu CNAME:

    ![Zrzut ekranu przedstawia wpis nazwy C z typem, hostem, punktami do i T T L wartości dla stałej domeny niestandardowej.](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia. Na przykład: www lub cdn.

    - Wskazuje na: wprowadź nazwę hosta punktu końcowego usługi CDN. Na przykład: contoso.azureedge.net. 

    - Czas wygaśnięcia: pozostaw wybraną wartość *1 godzina*.

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

    ![Zrzut ekranu pokazuje, że wpis nazwy języka C został dodany do tabeli rekordów w D/S dla stałej domeny niestandardowej.](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Jeśli masz rekord CNAME cdnverify, wybierz ikonę ołówka obok niej, a następnie wybierz ikonę kosza.

8. Wybierz pozycję **Usuń**, aby usunąć rekord CNAME.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach dodano domenę niestandardową do punktu końcowego usługi CDN. Jeśli nie chcesz skojarzyć punktu końcowego z domeną niestandardową, możesz usunąć domenę niestandardową, wykonując następujące kroki:
 
1. W profilu usługi CDN wybierz punkt końcowy z domeną niestandardową, która ma zostać usunięta.

2. Na stronie **Punkt końcowy** w obszarze domen niestandardowych kliknij prawym przyciskiem myszy domenę niestandardową, którą chcesz usunąć, a następnie wybierz polecenie **Usuń** z menu kontekstowego.  

   Skojarzenie domeny niestandardowej z punktem końcowym zostanie usunięte.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN.
> - Weryfikowanie domeny niestandardowej.

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować protokół HTTPS w domenie niestandardowej usługi Azure CDN.

> [!div class="nextstepaction"]
> [Samouczek: konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md)


