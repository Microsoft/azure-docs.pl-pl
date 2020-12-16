---
title: Ochrona zaplecza SPA z uwierzytelnianiem OAuth 2,0 przy użyciu Azure Active Directory B2C i platformy Azure API Management.
description: Ochrona interfejsu API za pomocą protokołu OAuth 2,0 przy użyciu Azure Active Directory B2C, API Management platformy Azure i łatwej autoryzacji do wywołania na podstawie SPA JavaScript.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new, devx-track-js
ms.openlocfilehash: b157200b03bdc89e00bfa5c8264d78baf24875c1
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609126"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Ochrona zaplecza SPA przy użyciu protokołu OAuth 2,0 Azure Active Directory B2C i platformy Azure API Management

W tym scenariuszu pokazano, jak skonfigurować wystąpienie usługi Azure API Management, aby chronić interfejs API.
Użyjemy protokołu OAuth 2,0 z Azure AD B2C, obok API Management do zabezpieczenia Azure Functions zaplecza przy użyciu EasyAuth.

## <a name="aims"></a>Osiągnięcia
Będziemy widzieć, jak API Management mogą być używane w uproszczonym scenariuszu z Azure Functions i Azure AD B2C. Utworzysz aplikację JavaScript (JS) wywołującą interfejs API, która umożliwia użytkownikom Azure AD B2C. Następnie do ochrony interfejsu API zaplecza używana jest funkcja zasad walidacji API Management.

Aby uzyskać szczegółowe zabezpieczenia, użyjemy EasyAuth do ponownego zweryfikowania tokenu wewnątrz interfejsu API zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musisz mieć:
* Konto magazynu platformy Azure (StorageV2 Ogólnego przeznaczenia) w wersji 2 do hostowania aplikacji jednostronicowej w usłudze fronton JS
* Wystąpienie usługi Azure API Management 
* Pusta aplikacja funkcji platformy Azure (z uruchomionym środowiskiem uruchomieniowym v2 .NET Core, zgodnie z planem zużycia systemu Windows) do hostowania wywołanego interfejsu API
* Dzierżawa Azure AD B2C, powiązana z subskrypcją 

Mimo że w przypadku użycia zasobów w tym samym regionie w obciążeniu produkcyjnym, w tym artykule z tego artykułu opisano, że region wdrożenia nie jest ważny.

## <a name="overview"></a>Omówienie
Oto ilustracja przedstawiająca używane składniki oraz przepływ między nimi po zakończeniu tego procesu.
![Składniki używane i Flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Składniki używane i Flow")

Poniżej przedstawiono krótkie omówienie kroków:

1. Tworzenie Azure AD B2C wywoływanie (frontonu, API Management) i aplikacji interfejsu API z zakresami i udzielanie dostępu do interfejsu API
1. Utwórz zasady tworzenia konta lub logowania, aby umożliwić użytkownikom logowanie się przy użyciu Azure AD B2C 
1. Skonfiguruj API Management przy użyciu nowych Azure AD B2C identyfikatorów klienta i kluczy, aby włączyć autoryzację użytkownika OAuth2 w konsoli dewelopera
1. Tworzenie interfejsu API funkcji
1. Skonfiguruj interfejs API funkcji, aby włączyć EasyAuth z nowym IDENTYFIKATORem i kluczami klienta Azure AD B2C i zablokować do APIM VIP 
1. Kompilowanie definicji interfejsu API w API Management
1. Skonfiguruj OAuth2 dla konfiguracji interfejsu API API Management
1. Konfigurowanie zasad **CORS** i Dodawanie zasad **walidacji-JWT** do weryfikowania tokenu OAuth dla każdego żądania przychodzącego
1. Kompilowanie aplikacji wywołującej w celu korzystania z interfejsu API
1. Przekaż przykład SPA na stronie JS
1. Konfigurowanie przykładowej aplikacji klienckiej JS przy użyciu nowych identyfikatorów i kluczy klienta Azure AD B2C 
1. Testowanie aplikacji klienckiej

## <a name="configure-azure-ad-b2c"></a>Konfigurowanie Azure AD B2C 
Otwórz blok Azure AD B2C w portalu i wykonaj następujące czynności.
1. Wybieranie karty **aplikacje** 
1. Kliknij przycisk "Dodaj" i Utwórz trzy aplikacje dla następujących celów
   * Klient frontonu.
   * Interfejs API funkcji zaplecza.
   * Obowiązkowe Portal dla deweloperów API Management (o ile nie korzystasz z platformy Azure API Management w warstwie zużycia więcej informacji w tym scenariuszu).
1. Ustaw WebApp/Web API dla wszystkich 3 aplikacji i ustaw opcję "Zezwalaj na niejawny przepływ" na wartość tak tylko dla klienta frontonu.
1. Teraz ustaw identyfikator URI aplikacji, wybierz unikatową i istotną dla tworzonej usługi.
1. Użyj symboli zastępczych dla adresów URL odpowiedzi dla teraz, takich jak https://localhost , zaktualizujemy te adresy URL później.
1. Kliknij przycisk Utwórz, a następnie powtórz kroki 2-5 dla każdej z trzech aplikacji powyżej, rejestrując identyfikator URI AppID, nazwę i identyfikator aplikacji do późniejszego użycia dla wszystkich trzech aplikacji.
1. Otwórz aplikację Portal dla deweloperów API Management z listy aplikacji i wybierz kartę *klucze* (w obszarze Ogólne), a następnie kliknij pozycję Generuj klucz, aby wygenerować klucz uwierzytelniania.
1. Po kliknięciu przycisku Zapisz Zarejestruj klucz w bezpiecznym miejscu do późniejszego użycia — Pamiętaj, że w tym miejscu można wyświetlić i skopiować ten klucz.
1. Teraz wybierz kartę *opublikowane zakresy* (w obszarze dostęp do interfejsu API)
1. Utwórz zakres dla interfejsu API funkcji i nadaj mu nazwę, a następnie zanotuj zakres i wypełnioną pełną wartość zakresu, a następnie kliknij pozycję "Zapisz".
   > [!NOTE]
   > Zakresy Azure AD B2C są skutecznymi uprawnieniami w interfejsie API, z których mogą żądać dostępu inne aplikacje za pośrednictwem bloku dostęp do interfejsu API w aplikacjach, tym wydajniej zostały utworzone uprawnienia aplikacji dla wywoływanego interfejsu API.
1. Otwórz pozostałe dwie aplikacje, a następnie poszukaj w obszarze kartę *dostęp do interfejsu API* .
1. Udziel im dostępu do zakresu interfejsu API zaplecza i domyślnego, który już tam istnieje ("dostęp do profilu użytkownika").
1. Wygeneruj dla nich klucz każdy, wybierając kartę *klucze* w obszarze "ogólne", aby wygenerować klucz uwierzytelniania i zarejestrować te klucze w bezpiecznym miejscu.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Utwórz przepływ użytkownika "Rejestracja lub logowanie"
1. Wróć do katalogu głównego (lub "przegląd") bloku Azure AD B2C 
1. Następnie wybierz pozycję "Przepływy użytkownika (zasady)", a następnie kliknij pozycję "nowy przepływ użytkownika".
1. Wybierz typ przepływu użytkownika "Rejestracja i logowanie"
1. Nadaj zasadom nazwę i Zapisz ją w późniejszym czasie.
1. Następnie w obszarze "dostawcy tożsamości" Sprawdź pozycję "Logowanie do konta użytkownika" (może to dotyczyć "Rejestracja w wiadomości E-mail"), a następnie kliknij przycisk OK. 
1. W obszarze "atrybuty użytkownika i oświadczenia" kliknij pozycję "Pokaż więcej..." następnie wybierz opcje dotyczące roszczeń, które użytkownicy mają wprowadzać i które zwracają w tokenie. Sprawdź co najmniej nazwę wyświetlaną i adres E-mail, aby zebrać i zwrócić, a następnie kliknij przycisk "OK", a następnie kliknij przycisk "Utwórz".
1. Wybierz zasady, które zostały utworzone na liście, a następnie kliknij przycisk Uruchom przepływ użytkownika.
1. Ta akcja spowoduje otwarcie bloku uruchamianie przepływu użytkownika, wybranie aplikacji frontonu, a następnie zapisanie adresu domeny b2clogin.com, która jest wyświetlana na liście rozwijanej dla opcji "Wybierz domenę".
1. Kliknij link u góry, aby otworzyć "dobrze znane OpenID Connect Konfiguracja punktu końcowego" i zapisać wartości authorization_endpoint i token_endpoint, jak również wartość linku jako dobrze znanego punktu końcowego konfiguracji OpenID Connect.

   > [!NOTE]
   > Zasady B2C umożliwiają Uwidacznianie punktów końcowych logowania Azure AD B2C, aby umożliwić Przechwytywanie różnych składników danych i Logowanie użytkowników na różne sposoby. W takim przypadku skonfigurowano punkt końcowy rejestracji lub logowania, który uwidoczni dobrze znany punkt końcowy konfiguracji, a w odniesieniu do wszystkich utworzonych zasad został zidentyfikowany w adresie URL za pomocą parametru p =.
   > 
   > Po wykonaniu tej czynności masz już funkcjonalną platformę do identyfikacji klienta, która będzie podpisywać użytkowników do wielu aplikacji. 
   > Jeśli chcesz, możesz kliknąć przycisk "Uruchom przepływ użytkownika" tutaj (aby przejść przez proces tworzenia konta lub logowania) i uzyskać informacje o tym, co będzie w toku, ale krok przekierowania na końcu zakończy się niepowodzeniem, ponieważ aplikacja nie została jeszcze wdrożona.

## <a name="build-the-function-api"></a>Tworzenie interfejsu API funkcji
1. Przełącz się z powrotem do standardowej dzierżawy usługi Azure AD w Azure Portal, aby umożliwić ponowne skonfigurowanie elementów w ramach subskrypcji 
1. Przejdź do bloku aplikacje funkcji Azure Portal, Otwórz pustą aplikację funkcji, a następnie utwórz nową funkcję In-Portal "webhook + API" za pośrednictwem przewodnika Szybki Start.
1. Wklej przykładowy kod z poniższych do Run. CSX na istniejącym kodzie, który zostanie wyświetlony.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > Właśnie wklejony kod funkcji skryptu c# po prostu rejestruje wiersz do dzienników funkcji i zwraca tekst "Hello world" z niektórymi danymi dynamicznymi (Data i godzina).

3. Wybierz pozycję "Zintegruj" w bloku po lewej stronie, a następnie wybierz pozycję "Edytor zaawansowany" w prawym górnym rogu okienka.
4. Wklej przykładowy kod poniżej w istniejącym formacie JSON.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Przełącz się z powrotem na kartę HttpTrigger1, kliknij przycisk "Pobierz adres URL funkcji", a następnie skopiuj wyświetlony adres URL.

   > [!NOTE]
   > Utworzone powiązania po prostu informują funkcje, które odpowiadają za anonimowe żądania HTTP GET do właśnie skopiowanego adresu URL. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Teraz mamy skalowalny interfejs API protokołu HTTPS bezserwerowy, który może zwrócić bardzo prosty ładunek.
   > Teraz można testować wywoływanie tego interfejsu API z przeglądarki sieci Web przy użyciu adresu URL powyżej, można także rozdzielić część? Code = Secret w adresie URL i udowodnić, że Azure Functions zwróci błąd 401.

## <a name="configure-and-secure-the-function-api"></a>Konfigurowanie i zabezpieczanie interfejsu API funkcji
1. Należy skonfigurować dwa dodatkowe obszary w aplikacji funkcji (ograniczenia uwierzytelniania i sieci).
1. Po pierwsze Skonfiguruj uwierzytelnianie/autoryzację, a następnie kliknij nazwę aplikacji funkcji (obok &lt; &gt; ikony funkcji z), aby wyświetlić stronę przegląd.
1. Następnie wybierz kartę "funkcje platformy" i wybierz pozycję "uwierzytelnianie/autoryzacja".
1. Włącz funkcję uwierzytelniania App Service.
1. W obszarze "dostawcy uwierzytelniania" Wybierz pozycję "Azure Active Directory" i wybierz pozycję "Zaawansowane" z przełącznika trybu zarządzania.
1. Wklej identyfikator aplikacji interfejsu API funkcji zaplecza (z Azure AD B2C do pola "identyfikator klienta")
1. Wklej dobrze znany punkt końcowy konfiguracji Open-ID z zasad rejestracji lub logowania do pola adresu URL wystawcy (Ta konfiguracja została zarejestrowana wcześniej).
1. Wybierz przycisk OK.
1. Ustaw akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione na liście rozwijanej "Zaloguj się za pomocą Azure Active Directory", a następnie kliknij przycisk Zapisz.

   > [!NOTE]
   > Teraz interfejs API funkcji jest wdrożony i powinien zgłosić 401 odpowiedzi, jeśli nie podano poprawnego klucza i powinien zwrócić dane, gdy zostanie wyświetlone prawidłowe żądanie.
   > Dodano dodatkowe zabezpieczenia dotyczące zabezpieczeń w programie EasyAuth przez skonfigurowanie opcji "Logowanie za pomocą usługi Azure AD" w celu obsługi nieuwierzytelnionych żądań. Należy pamiętać, że spowoduje to zmianę zachowania nieautoryzowanego żądania między aplikacja funkcji wewnętrznej bazy danych i prze302 stawieniem SPA, ponieważ EasyAuth nastąpi przekierowanie do usługi AAD zamiast nieautoryzowanej odpowiedzi 401, nastąpi poprawienie przy użyciu API Management później.
   > Nadal nie stosujemy zabezpieczeń IP, jeśli masz prawidłowy klucz i token OAuth2, każdy z nich może wywoływać z dowolnego miejsca — najlepiej będzie wymusić, aby wszystkie żądania były realizowane za pośrednictwem API Management.
   > Jeśli używasz warstwy zużycia API Management, nie będzie możliwe przeprowadzenie tej blokady przez adres VIP, ponieważ dla tej warstwy nie istnieje dedykowany statyczny adres IP, konieczne będzie użycie metody blokowania wywołań interfejsu API za pośrednictwem wspólnego klucza funkcji tajnych, więc kroki 11-13 nie będą możliwe.

1. Zamknij blok uwierzytelnianie/autoryzacja 
1. Wybierz pozycję Sieć, a następnie wybierz pozycję ograniczenia dostępu.
1. Następnie Zablokuj dozwolone adresy IP aplikacji funkcji dla adresu VIP API Management wystąpienia. Ten adres VIP znajduje się w sekcji Omówienie usługi API Management w portalu.
1. Jeśli chcesz kontynuować pracę z portalem funkcji i wykonać opcjonalne czynności opisane poniżej, należy również dodać własny publiczny adres IP lub zakres CIDR.
1. Gdy na liście znajduje się wpis Zezwalaj, platforma Azure dodaje niejawną regułę odmowy, aby zablokować wszystkie inne adresy. 

Musisz dodać bloki z formatowaniem CIDR do panelu ograniczenia adresów IP. Gdy musisz dodać pojedynczy adres, taki jak adres VIP API Management, musisz go dodać w formacie XX. XX. XX. XX.

   > [!NOTE]
   > Teraz interfejs API funkcji nie powinien być wywoływany z innych lokalizacji niż za pośrednictwem usługi API Management ani Twojego adresu.
   
## <a name="import-the-function-app-definition"></a>Importowanie definicji aplikacji funkcji
1. Otwórz *blok API Management*, a następnie otwórz *wystąpienie*.
1. Wybierz blok interfejsy API z sekcji API Management tego wystąpienia.
1. W okienku "Dodawanie nowego interfejsu API" Wybierz pozycję "aplikacja funkcji", a następnie wybierz pozycję "pełne" w górnej części okna podręcznego.
1. Kliknij przycisk Przeglądaj, wybierz aplikację funkcji, w której znajduje się interfejs API, a następnie kliknij przycisk Wybierz.
1. Podaj nazwę i opis interfejsu API do użytku wewnętrznego API Management i Dodaj je do produktu "nieograniczony".
1. Upewnij się, że adres URL podstawowy został zarejestrowany do późniejszego użycia, a następnie kliknij przycisk Utwórz.

## <a name="configure-oauth2-for-api-management"></a>Skonfiguruj OAuth2 dla API Management

1. Następnie wybierz blok OAuth 2,0 na karcie Zabezpieczenia, a następnie kliknij pozycję "Dodaj".
1. Podaj wartości w polu *Nazwa wyświetlana* i *Opis* dla dodanego punktu końcowego OAuth (te wartości będą wyświetlane w następnym kroku jako punkt końcowy OAuth2).
1. Możesz wprowadzić dowolną wartość w adresie URL strony rejestracji klienta, ponieważ ta wartość nie będzie używana.
1. Sprawdź *niejawny* typ przydziału uwierzytelniania i pozostaw zaznaczone pole wyboru Przydziel kod autoryzacji.
1. Przejdź do pól *autoryzacja* i punkt końcowy *tokenu* , a następnie wprowadź wartości przechwycone ze znanego wcześniej dokumentu XML konfiguracji.
1. Przewiń w dół i wypełnij *dodatkowy parametr treści* o nazwie "Resource" z identyfikatorem klienta interfejsu API funkcji zaplecza z Azure AD B2C rejestracji aplikacji
1. Wybierz pozycję "poświadczenia klienta", ustaw identyfikator klienta na identyfikator aplikacji konsoli dewelopera — Pomiń ten krok, jeśli używany jest model API Management użycia.
1. Ustaw klucz tajny klienta na nagrany wcześniej — Pomiń ten krok, jeśli używasz modelu API Management użycia.
1. Teraz Zapisz redirect_uri kodu uwierzytelniania przyznany z API Management do późniejszego użycia.

## <a name="set-up-oauth2-for-your-api"></a>Skonfiguruj OAuth2 dla interfejsu API
1. Interfejs API zostanie wyświetlony po lewej stronie portalu w sekcji "wszystkie interfejsy API", Otwórz interfejs API, klikając go.
1. Wybierz kartę Ustawienia.
1. Zaktualizuj ustawienia, wybierając pozycję "OAuth 2,0" z przycisku radiowego Autoryzacja użytkownika.
1. Wybierz zdefiniowany wcześniej serwer uwierzytelniania OAuth.
1. Zaznacz pole wyboru "Zastąp zakres" i wprowadź zakres zarejestrowany dla wywołania interfejsu API zaplecza wcześniej na.

   > [!NOTE]
   > Teraz mamy API Management wystąpienie, które wie, jak uzyskać tokeny dostępu z Azure AD B2C, aby autoryzować żądania i zrozumieć nasze OAuth2 konfigurację Azure Active Directory B2C.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Konfigurowanie zasad **CORS** i **Walidacja-JWT**

> Należy przestrzegać następujących sekcji, niezależnie od używanej warstwy APIM. 

1. Przełącz się z powrotem na kartę projektowanie i wybierz opcję "wszystkie interfejsy API", a następnie kliknij przycisk Widok kodu, aby wyświetlić Edytor zasad.
1. Edytuj sekcję dotyczącą ruchu przychodzącego i wklej poniższy kod XML, tak aby miał postać podobną do następującej.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Edytuj adres URL OpenID Connect-config, aby dopasować dobrze znany Azure AD B2C punkt końcowy dla zasad rejestracji lub logowania.
1. Edytuj wartość usługi Claim w celu dopasowania do prawidłowego identyfikatora aplikacji, znanego również jako identyfikator klienta dla aplikacji interfejsu API zaplecza i Zapisz.

   > [!NOTE]
   > Teraz funkcja zarządzania interfejsami API umożliwia reagowanie na żądania między źródłami na aplikacje typu JS SPA i przekroczenie limitu szybkości i wstępnego sprawdzania poprawności tokenu uwierzytelniania JWT przesyłanego przed przekazaniem żądania do interfejsu API funkcji.

   > [!NOTE]
   > Poniższa sekcja jest opcjonalna i nie dotyczy warstwy **zużycia** , która nie obsługuje portalu dla deweloperów.
   > Jeśli nie zamierzasz korzystać z portalu dla deweloperów lub nie możesz go użyć od czasu korzystania z warstwy zużycia, Pomiń ten krok i Przeskocz bezpośrednio do ["Kompilowanie Spa JavaScript w celu korzystania z interfejsu API"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>Obowiązkowe Konfigurowanie portalu dla deweloperów

1. Otwórz blok Azure AD B2C i przejdź do rejestracji aplikacji w portalu dla deweloperów
1. Ustaw wpis "adres URL" odpowiedzi na taki, który został zanotowany podczas konfigurowania redirect_uri przyznawania kodu uwierzytelniania w API Management wcześniej.

   Teraz, gdy autoryzacja użytkownika OAuth 2,0 jest włączona w programie `Echo API` , Konsola dewelopera uzyskuje token dostępu dla użytkownika przed wywołaniem interfejsu API.

1. Przejdź do dowolnej operacji `Echo API` w portalu dla deweloperów, a następnie wybierz pozycję **Wypróbuj** , aby przejść do konsoli dewelopera.
1. Zanotuj nowy element w sekcji **autoryzacja** odpowiadający serwerowi autoryzacji, który właśnie został dodany.
1. Z listy rozwijanej autoryzacja wybierz pozycję **kod autoryzacji** , a zostanie wyświetlony monit o zalogowanie się do dzierżawy usługi Azure AD. Jeśli użytkownik jest już zalogowany przy użyciu konta, może nie zostać wyświetlony monit.
1. Po pomyślnym zalogowaniu `Authorization: Bearer` do żądania zostanie dodany nagłówek z tokenem dostępu Azure AD B2C zakodowanym w formacie base64. 
1. Wybierz pozycję **Wyślij** , aby pomyślnie wywołać interfejs API.

   > [!NOTE]
   > Teraz funkcja zarządzania interfejsami API umożliwia uzyskanie tokenów portalu dla deweloperów w celu przetestowania interfejsu API i zrozumienie jego definicji oraz renderowanie odpowiedniej strony testowej w portalu deweloperów.

1. W bloku przegląd portalu API Management kliknij pozycję "Portal deweloperów", aby zalogować się jako administrator interfejsu API.
1. W tym miejscu ty i inni użytkownicy interfejsu API mogą testować i wywoływać je z poziomu konsoli programu.
1. Wybierz pozycję "produkty", a następnie wybierz pozycję "nieograniczone", a następnie wybierz utworzony wcześniej interfejs API i kliknij przycisk "Wypróbuj"
1. Odkryj klucz subskrypcji interfejsu API i skopiuj go w bezpiecznym miejscu przy użyciu adresu URL żądania, który będzie później potrzebny.
1. Wybierz również niejawne, z listy rozwijanej uwierzytelniania OAuth i może być konieczne uwierzytelnienie w tym miejscu przy użyciu okna podręcznego.
1. Kliknij przycisk "Wyślij", a jeśli wszystko jest prawidłowo, aplikacja funkcji powinno odpowiedzieć na wiadomość powitalną za pośrednictwem usługi API Management z komunikatem 200 OK i z nieprawidłowym kodem JSON.

   > [!NOTE]
   > Gratulacje, masz teraz Azure AD B2C, API Management i Azure Functions współdziałania w celu publikowania, zabezpieczania i korzystania z interfejsu API. Być może zauważono, że interfejs API jest w rzeczywistości zabezpieczony dwukrotnie przy użyciu tej metody, raz z nagłówkiem API Management OCP-Subscription-Key i raz z autoryzacją: JWT okaziciela.
   > Poprawność, jak to jest aplikacja jednostronicowa języka JavaScript, używamy klucza API Management tylko dla wywołań ograniczających szybkość i rozliczeń.
   > Rzeczywista autoryzacja i uwierzytelnianie jest obsługiwane przez Azure AD B2C i są hermetyzowane w tokenie JWT, które są sprawdzane dwukrotnie, raz przez API Management, a następnie przez Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Kompilowanie SPA JavaScript w celu korzystania z interfejsu API
1. Otwórz blok konta magazynu w Azure Portal 
1. Wybierz utworzone konto i wybierz blok "statyczna witryna sieci Web" z sekcji Ustawienia (jeśli nie widzisz opcji "statyczna witryna sieci Web", sprawdź, czy utworzono konto w wersji 2).
1. Ustaw statyczną funkcję hostingu sieci Web na wartość "Enabled" i Ustaw nazwę dokumentu indeksu na "index.html", a następnie kliknij pozycję "Zapisz".
1. Zanotuj zawartość podstawowego punktu końcowego, ponieważ jest to lokalizacja, w której będzie hostowana witryna frontonu. 

   > [!NOTE]
   > Możesz użyć usługi Azure Blob Storage i ponownego zapisu w usłudze CDN albo Azure App Service, ale Blob Storage funkcja hostingu statycznej witryny sieci Web zapewnia nam kontener domyślny do obsługi statycznej zawartości internetowej/HTML/JS/CSS z magazynu Azure

## <a name="upload-the-js-spa-sample"></a>Przekaż przykład SPA na stronie JS
1. W bloku konto magazynu wybierz blok "obiekty blob" z sekcji usługa BLOB, a następnie kliknij kontener $web, który pojawia się w okienku po prawej stronie.
1. Zapisz Poniższy kod do pliku lokalnego na komputerze jako index.html, a następnie Przekaż plik index.html do kontenera $web.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},
                    url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Przejdź do podstawowego punktu końcowego witryny sieci Web, który został zapisany wcześniej w ostatniej sekcji.

   > [!NOTE]
   > Gratulacje, po prostu wdrożono aplikację jednostronicową JavaScript do usługi Azure Storage, ponieważ nie skonfigurowano aplikacji JS z kluczami dla interfejsu API lub aplikacja JS została jeszcze skonfigurowana za pomocą Azure AD B2C szczegóły — strona nie będzie jeszcze działać, jeśli zostanie otwarta.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Skonfiguruj uwierzytelnianie SPA w programie JS dla Azure AD B2C
1. Teraz wiemy, gdzie wszystko jest: można skonfigurować SPA przy użyciu odpowiedniego adresu interfejsu API API Management i poprawnych Azure AD B2C identyfikatorów aplikacji/klientów
1. Wróć do bloku Azure Portal Storage, kliknij pozycję index.html, a następnie wybierz pozycję "Edytuj obiekt BLOB". 
1. Zaktualizuj szczegóły uwierzytelniania, aby odpowiadały aplikacji frontonu, która została wcześniej zarejestrowana w usłudze B2C, zwracając uwagę, że wartości "b2cScopes" są przeznaczone dla zaplecza interfejsu API.
1. Klucz webApi i adres URL interfejsu API można znaleźć w okienku test API Management dla operacji interfejsu API.
1. Utwórz klucz subskrypcji APIM po nagłówku w API Management z powrotem do bloku API Management, wybierając pozycję "subskrypcje" i klikając pozycję "Dodaj subskrypcję", a następnie zapisując rekord. Kliknięcie wielokropka (...) obok utworzonego wiersza umożliwi wyświetlenie kluczy, aby można było skopiować klucz podstawowy.
1. Powinien wyglądać podobnie do poniższego kodu:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Klikanie pozycji Zapisz.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Ustawianie identyfikatorów URI przekierowania dla aplikacji frontonu Azure AD B2C
1. Otwórz blok Azure AD B2C i przejdź do rejestracji aplikacji dla aplikacji frontonu języka JavaScript
1. Ustaw adres URL przekierowania na taki, który został zanotowany, gdy wcześniej skonfigurowano statyczny podstawowy punkt końcowy witryny sieci Web

   > [!NOTE] 
   > Ta konfiguracja spowoduje, że klient aplikacji frontonu otrzyma token dostępu z odpowiednimi oświadczeniami z Azure AD B2C.
   > SPA będzie mógł dodać ten element jako token okaziciela w nagłówku https w wywołaniu interfejsu API zaplecza.
   > API Management przed przekazaniem żądania do interfejsu API usługi Azure Functions sprawdzi przed upływem limitu czasu wywołania do punktu końcowego przez klucz subskrybenta.
   > SPA będzie renderować odpowiedź w przeglądarce.

   > *Gratulacje, skonfigurowano Azure AD B2C, API Management platformy Azure, Azure Functions, Azure App Service autoryzację do pracy w idealnym zasobie!*

   > [!NOTE]
   > Teraz mamy prostą aplikację z prostym zabezpieczonym interfejsem API, aby ją przetestować.

## <a name="test-the-client-application"></a>Testowanie aplikacji klienckiej
1. Otwórz adres URL przykładowej aplikacji, który został zanotowany w utworzonym wcześniej koncie magazynu
1. Kliknij pozycję "Zaloguj się" w prawym górnym rogu, a kliknięcie przycisku spowoduje wyświetlenie Azure AD B2C profilu rejestracji lub logowania.
1. Wpis w sekcji "zalogowany jako" na ekranie zostanie wypełniony z tokenu JWT.
1. Teraz kliknij przycisk "Wywołaj internetowy interfejs API", a strona powinna zostać zaktualizowana wartościami wysłanymi z bezpiecznego interfejsu API.

## <a name="and-were-done"></a>Wszystko gotowe
Powyższe kroki można dostosować i edytować, aby umożliwić korzystanie z wielu różnych Azure AD B2C z API Management.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [Azure Active Directory i OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Poznaj więcej [filmów wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) dotyczących API Management.
* Aby poznać inne sposoby zabezpieczenia usługi zaplecza, zobacz [uwierzytelnianie wzajemne certyfikatów](api-management-howto-mutual-certificates.md).
* [Utwórz wystąpienie usługi API Management](get-started-create-service-instance.md).
* [Zarządzanie pierwszym interfejsem API](import-and-publish.md).
