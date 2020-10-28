---
title: Zabezpieczanie zasobów Azure CDN przy użyciu uwierzytelniania tokenów | Microsoft Docs
description: Dowiedz się, jak za pomocą uwierzytelniania tokenów zabezpieczyć dostęp do zasobów Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778007"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpieczanie zasobów Azure CDN przy użyciu uwierzytelniania tokenów

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie tokenu jest mechanizmem, który pozwala zapobiegać wykorzystaniu zasobów przez usługę Azure Content Delivery Network (CDN) dla nieautoryzowanych klientów. Uwierzytelnianie tokenu jest zwykle wykonywane w celu zapobieżenia *hotlinking* zawartości, w której inna witryna sieci Web, taka jak tablica komunikatów, używa swoich zasobów bez uprawnień. Hotlinking może mieć wpływ na koszty dostarczania zawartości. Przez włączenie uwierzytelniania tokenu w sieci CDN, żądania są uwierzytelniane przez serwer brzegowy usługi CDN, zanim Usługa CDN dostarczy zawartość. 

## <a name="how-it-works"></a>Jak to działa

Uwierzytelnianie tokenu weryfikuje, czy żądania są generowane przez zaufaną witrynę przez wymaganie, aby żądania zawierały wartość tokenu, która przechowuje zakodowane informacje o żądającym. Zawartość jest obsługiwana dla żądającego tylko wtedy, gdy zakodowane informacje spełniają wymagania. w przeciwnym razie żądania są odrzucane. Wymagania można skonfigurować przy użyciu co najmniej jednego z następujących parametrów:

- Kraj/region: Zezwalanie lub odrzucanie żądań pochodzących z krajów/regionów określonych w [kodzie kraju/regionu](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Zezwalaj tylko na żądania zgodne z określonym zasobem lub ścieżką.
- Host: Zezwalanie lub odmawianie żądań, które używają określonych hostów w nagłówku żądania.
- Odwołujący: Zezwalaj lub Odmów żądania od określonego odwołującego.
- Adres IP: Zezwalaj tylko na żądania pochodzące z określonego adresu IP lub podsieci IP.
- Protokół: Zezwalanie lub odmawianie żądań na podstawie protokołu używanego do żądania zawartości.
- Czas wygaśnięcia: Przypisz datę i okres, aby upewnić się, że link jest prawidłowy tylko dla ograniczonego okresu.

Aby uzyskać więcej informacji, Zobacz szczegółowe przykłady konfiguracji dla każdego parametru podczas [konfigurowania uwierzytelniania tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Jeśli autoryzacja tokenu jest włączona dla dowolnej ścieżki na tym koncie, tryb pamięci podręcznej jest jedynym trybem, który może być używany do buforowania ciągu zapytania. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="reference-architecture"></a>Architektura referencyjna

Poniższy diagram przepływu pracy opisuje sposób, w jaki usługa CDN używa uwierzytelniania tokenu do pracy z aplikacją sieci Web.

![Przepływ pracy uwierzytelniania tokenu sieci CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logika walidacji tokenu w punkcie końcowym usługi CDN
    
Poniższy schemat blokowy opisuje, jak Azure CDN sprawdza poprawność żądania klienta, gdy uwierzytelnianie tokenu jest skonfigurowane w punkcie końcowym usługi CDN.

![Logika walidacji tokenu sieci CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurowanie uwierzytelniania tokenu

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego profilu CDN, a następnie wybierz pozycję **Zarządzaj** , aby uruchomić dodatkowy Portal.

    ![Przycisk zarządzania profilem CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Umieść kursor na **dużym formacie http** , a następnie wybierz pozycję **uwierzytelnianie tokenu** w menu wysuwanym. Następnie można skonfigurować klucz szyfrowania i parametry szyfrowania w następujący sposób:

   1. Utwórz jeden lub więcej kluczy szyfrowania. Klucz szyfrowania uwzględnia wielkość liter i może zawierać dowolną kombinację znaków alfanumerycznych. Wszystkie inne typy znaków, w tym spacje, są niedozwolone. Maksymalna długość to 250 znaków. Aby upewnić się, że klucze szyfrowania są losowo, zaleca się ich utworzenie za pomocą [Narzędzia OpenSSL](https://www.openssl.org/). 

      Narzędzie OpenSSL ma następującą składnię:

      ```rand -hex <key length>```

      Na przykład:

      ```OpenSSL> rand -hex 32``` 

      Aby uniknąć przestoju, należy utworzyć klucz podstawowy i zapasowy. Klucz kopii zapasowej zapewnia nieprzerwany dostęp do zawartości w przypadku aktualizowania klucza podstawowego.
    
   2. Wprowadź unikatowy klucz szyfrowania w polu **klucz podstawowy** i opcjonalnie wprowadź klucz kopii zapasowej w polu **klucz kopii zapasowej** .

   3. Wybierz minimalną wersję szyfrowania dla każdego klucza z listy **minimalna wersja szyfrowania** , a następnie wybierz pozycję **Aktualizuj** :
      - **V2** : wskazuje, że klucz może być używany do generowania tokenów w wersji 2,0 i 3,0. Tej opcji należy użyć tylko w przypadku przejścia ze starszego klucza szyfrowania 2,0 do wersji 3,0.
      - **V3** : (zalecane) wskazuje, że klucz może być używany tylko do generowania tokenów w wersji 3,0.

      ![Klucz instalacji uwierzytelniania tokenu usługi CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Użyj narzędzia Szyfruj, aby skonfigurować parametry szyfrowania i wygenerować token. Za pomocą narzędzia do szyfrowania można zezwalać na żądania lub odmawiać żądań na podstawie czasu wygaśnięcia, kraju/regionu, odwołującego się, protokołu i adresu IP klienta (w dowolnej kombinacji). Chociaż nie ma żadnego limitu liczby i kombinacji parametrów, które można łączyć w celu utworzenia tokenu, łączna długość tokenu jest ograniczona do 512 znaków. 

      ![Narzędzie szyfrowania CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Wprowadź wartości dla co najmniej jednego z następujących parametrów szyfrowania w sekcji **Narzędzia do szyfrowania** : 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nazwa parametru</th> 
      >   <th>Opis</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Przypisuje do tokenu czas wygaśnięcia, po upływie którego token wygasa. Żądania przesłane po upłynięciu czasu wygaśnięcia zostaną odrzucone. Ten parametr używa sygnatury czasowej systemu UNIX, która jest oparta na liczbie sekund od czasu standardowej epoki systemu UNIX `1/1/1970 00:00:00 GMT` . (Narzędzia online można użyć do konwersji między czasem standardowym i czasem systemu UNIX).> 
      >    Na przykład jeśli chcesz, aby token wygaśnie `12/31/2016 12:00:00 GMT` , wprowadź wartość w polu sygnatura czasowa systemu UNIX `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Umożliwia dostosowanie tokenów do określonego zasobu lub ścieżki. Ogranicza dostęp do żądań, których adres URL zaczyna się od określonej ścieżki względnej. Adresy URL są rozróżniane wielkości liter. Wprowadź wiele ścieżek, oddzielając poszczególne ścieżki przecinkami; Nie dodawaj spacji. W zależności od wymagań można skonfigurować różne wartości w celu zapewnienia różnego poziomu dostępu.> 
      >    Na przykład dla adresu URL `http://www.mydomain.com/pictures/city/strasbourg.png` te żądania są dozwolone dla następujących wartości wejściowych: 
      >    <ul>
      >       <li>Wartość wejściowa `/` : dozwolone są wszystkie żądania.</li>
      >       <li>Wartość wejściowa `/pictures` są dozwolone następujące żądania: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Wartość wejściowa `/pictures/` : dozwolone są tylko żądania zawierające `/pictures/` ścieżkę. Na przykład `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Wartość wejściowa `/pictures/city/strasbourg.png` : dozwolone są tylko żądania dla tej konkretnej ścieżki i elementu zawartości.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Zezwala tylko na żądania pochodzące z co najmniej jednego określonego kraju/regionu. Żądania pochodzące ze wszystkich innych krajów/regionów są odrzucane. W przypadku każdego kraju/regionu należy użyć dwuliterowego [kodu ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) , a każdy z nich należy oddzielić przecinkami; Nie dodawaj odstępu. Na przykład jeśli chcesz zezwolić na dostęp tylko z Stany Zjednoczone i Francji, wprowadź wartość `US,FR` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Odrzuca żądania pochodzące z co najmniej jednego określonego kraju/regionu. Żądania pochodzące ze wszystkich innych krajów/regionów są dozwolone. Implementacja jest taka sama jak parametr <b>ec_country_allow</b> . Jeśli w parametrach <b>ec_country_allow</b> i <b>ec_country_deny</b> jest obecny kod kraju/regionu, parametr <b>ec_country_allow</b> ma pierwszeństwo.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Zezwala tylko na żądania od określonego odwołującego. Odwołujący identyfikuje adres URL strony sieci Web, która jest połączona z żądanym zasobem. Nie dołączaj protokołu do wartości parametru.> 
      >    Dozwolone są następujące typy danych wejściowych:
      >    <ul>
      >       <li>Nazwa hosta lub nazwa hosta i ścieżka.</li>
      >       <li>Wielu odwołujących się. Aby dodać wielu odwołujących, oddziel każdy odwołujący przecinkami; Nie dodawaj odstępu. W przypadku określenia wartości odwołującej, ale informacje o odmowie nie są wysyłane w żądaniu z powodu konfiguracji przeglądarki, żądanie jest domyślnie odrzucane.</li> 
      >       <li>Żądania z brakującymi lub pustymi informacjami o odwołującym się. Domyślnie parametr <b>ec_ref_allow</b> blokuje te typy żądań. Aby zezwolić na te żądania, wprowadź tekst "Brak" lub wprowadź wartość pustą (przy użyciu końcowego przecinka).</li> 
      >       <li>Poddomen. Aby zezwolić na poddomeny, Wprowadź gwiazdkę ( \* ). Na przykład, aby zezwolić na wszystkie poddomeny `contoso.com` , wprowadź `*.contoso.com` .</li>
      >    </ul> 
      >    Na przykład, aby zezwolić na dostęp do żądań z `www.contoso.com` , wszystkie poddomeny w obszarze `contoso2.com` i żądania z pustymi lub brakującymi odwołującymi, wprowadź `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Odrzuca żądania od określonego odwołującego. Implementacja jest taka sama jak parametr <b>ec_ref_allow</b> . Jeśli odwołujący występuje zarówno w <b>ec_ref_allow</b> , jak i <b>ec_ref_deny</b> , parametr <b>ec_ref_allow</b> ma pierwszeństwo.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Zezwala tylko na żądania z określonego protokołu. Prawidłowe wartości to `http` , `https` , lub `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Odrzuca żądania od określonego protokołu. Implementacja jest taka sama jak parametr <b>ec_proto_allow</b> . Jeśli protokół jest obecny zarówno w <b>ec_proto_allow</b> , jak i <b>ec_proto_deny</b> , parametr <b>ec_proto_allow</b> ma pierwszeństwo.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Ogranicza dostęp do określonego adresu IP osoby żądającej. Obsługiwane są zarówno adresy IPV4, jak i IPV6. Można określić pojedynczy adres IP lub adresy IP żądania skojarzone z określoną podsiecią. Na przykład `11.22.33.0/22` zezwala na żądania z adresów IP 11.22.32.1 do 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Po zakończeniu wprowadzania wartości parametrów szyfrowania wybierz klucz do zaszyfrowania (Jeśli utworzono podstawowy i klucz kopii zapasowej) z **klucza do szyfrowania** listy.
    
   6. Wybierz wersję szyfrowania z listy **wersja szyfrowania** : **v2** dla wersji 2 lub **v3** dla wersji 3 (zalecane). 

   7. Wybierz pozycję **Szyfruj** , aby wygenerować token.

      Po wygenerowaniu tokenu zostanie on wyświetlony w polu **wygenerowany token** . Aby użyć tokenu, dołącz go jako ciąg zapytania do końca pliku w ścieżce adresu URL. Na przykład `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcjonalnie Przetestuj token za pomocą narzędzia deszyfrującego, aby można było wyświetlić parametry tokenu. Wklej wartość tokenu w polu **token do odszyfrowania** . Wybierz klucz szyfrowania do użycia z listy **klucz do odszyfrowania** , a następnie wybierz opcję **Odszyfruj** .

      Gdy token zostanie odszyfrowany, jego parametry są wyświetlane w polu **pierwotne parametry** .

   9. Opcjonalnie można dostosować typ kodu odpowiedzi, który jest zwracany w przypadku odrzucenia żądania. Wybierz opcję **włączone** , a następnie wybierz kod odpowiedzi z listy **kod odpowiedzi** . **Nazwa nagłówka** jest automatycznie ustawiana na **lokalizację** . Wybierz pozycję **Zapisz** , aby zaimplementować nowy kod odpowiedzi. W przypadku niektórych kodów odpowiedzi należy również wprowadzić adres URL strony błędu w polu **wartość nagłówka** . Kod odpowiedzi **403** (zabroniony) jest domyślnie wybrany. 

3. W obszarze **http Large** wybierz pozycję **aparat reguł** . Aparat reguł służy do definiowania ścieżek do zastosowania funkcji, włączania funkcji uwierzytelniania tokenu i włączania dodatkowych funkcji związanych z uwierzytelnianiem tokenu. Aby uzyskać więcej informacji, zobacz temat [Informacje o aparacie reguł](./cdn-verizon-premium-rules-engine-reference.md).

   1. Wybierz istniejącą regułę lub Utwórz nową regułę, aby zdefiniować element zawartości lub ścieżkę, dla której chcesz zastosować uwierzytelnianie tokenu. 
   2. Aby włączyć uwierzytelnianie tokenu dla reguły, wybierz pozycję **[uwierzytelnianie tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** z listy **funkcje** , a następnie wybierz pozycję **włączone** . Wybierz pozycję **Aktualizuj** w przypadku aktualizowania reguły lub **Dodaj** , jeśli tworzysz regułę.
        
      ![Przykład włączenia uwierzytelniania tokenu aparatu reguł sieci CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. W aparacie reguł można także włączyć dodatkowe funkcje związane z uwierzytelnianiem tokenu. Aby włączyć dowolne z następujących funkcji, wybierz je z listy **funkcje** , a następnie wybierz pozycję **włączone** .
    
   - **[Kod odmowy uwierzytelniania tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)** : określa typ odpowiedzi zwracanej do użytkownika, gdy żądanie zostanie odrzucone. Reguły ustawione w tym miejscu zastępują kod odpowiedzi ustawiony w sekcji **Niestandardowa obsługa odmowy** na stronie uwierzytelniania opartego na tokenach.

   - **[Wielkość liter w adresie URL ignorowania uwierzytelniania tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)** : określa, czy adres URL używany do sprawdzania poprawności tokenu jest uwzględniana wielkość liter.

   - **[Parametr uwierzytelniania tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)** : zmienia nazwę parametru ciągu zapytania uwierzytelniania tokenu, który pojawia się w ŻĄDANYm adresie URL. 
        
     ![Przykład ustawień uwierzytelniania tokenu aparatu reguł sieci CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Token można dostosować, uzyskując dostęp do kodu źródłowego w usłudze [GitHub](https://github.com/VerizonDigital/ectoken).
   Dostępne języki to:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funkcje i Cennik dostawcy

Aby uzyskać informacje o funkcjach, zobacz [Azure CDN funkcje produktu](cdn-features.md). Aby uzyskać informacje o cenach, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).