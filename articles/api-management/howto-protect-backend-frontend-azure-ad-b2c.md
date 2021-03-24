---
title: Ochrona zaplecza SPA na platformie Azure API Management przy użyciu Active Directory B2C
description: Ochrona interfejsu API za pomocą protokołu OAuth 2,0 przy użyciu Azure Active Directory B2C, Azure API Management i łatwej autoryzacji do wywołania przy użyciu SPA w języku JavaScript za pomocą PKCE włączony przepływ uwierzytelniania SPA.
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
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954925"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Ochrona zaplecza SPA przy użyciu protokołu OAuth 2,0 Azure Active Directory B2C i platformy Azure API Management

W tym scenariuszu pokazano, jak skonfigurować wystąpienie usługi Azure API Management, aby chronić interfejs API.
Użyjemy przepływu Azure AD B2C SPA (auth Code + PKCE), aby uzyskać token, obok API Management do zabezpieczenia Azure Functions zaplecza przy użyciu EasyAuth.

## <a name="aims"></a>Osiągnięcia

Będziemy widzieć, jak API Management mogą być używane w uproszczonym scenariuszu z Azure Functions i Azure AD B2C. Utworzysz aplikację JavaScript (JS) wywołującą interfejs API, który umożliwia użytkownikom Azure AD B2C. Następnie do ochrony interfejsu API zaplecza w ramach kluczowych funkcji zasad służy API Management Walidacja — JWT, CORS i Limit szybkości.

Aby zapewnić bardziej obronną ochronę, należy ponownie użyć EasyAuth do zweryfikowania tokenu w interfejsie API zaplecza i upewnienia się, że usługa API Management jest jedyną usługą, która może wywoływać Azure Functions zaplecza.

## <a name="what-will-you-learn"></a>Co znasz

> [!div class="checklist"]
> * Konfiguracja aplikacji jednostronicowej i interfejsu API zaplecza w Azure Active Directory B2C
> * Tworzenie interfejsu API zaplecza Azure Functions
> * Importowanie interfejsu API Azure Functions do platformy Azure API Management
> * Zabezpieczanie interfejsu API w usłudze Azure API Management
> * Wywoływanie punktów końcowych autoryzacji Azure Active Directory B2C za pośrednictwem bibliotek Microsoft Identity platform Library (MSAL.js)
> * Przechowywanie aplikacji jednostronicowej w języku HTML/Wanili JS i obsługa jej w punkcie końcowym usługi Azure Blob Storage

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Konto magazynu platformy Azure (StorageV2 Ogólnego przeznaczenia) w wersji 2 na potrzeby hostowania aplikacji jednostronicowej dla usługi frontonu.
* Wystąpienie usługi Azure API Management (każda warstwa będzie działała, w tym "zużycie"), ale niektóre funkcje mające zastosowanie do pełnego scenariusza nie są dostępne w tej warstwie (Rate-limit-by-Key i dedykowany wirtualny adres IP), te ograniczenia są wywoływane poniżej w odpowiednim artykule.
* Pusta aplikacja funkcji platformy Azure (z uruchomionym środowiskiem uruchomieniowym .NET Core, zgodnie z planem zużycia) do hostowania wywołanego interfejsu API
* Dzierżawca Azure AD B2C połączony z subskrypcją.

Mimo że w przypadku użycia zasobów w tym samym regionie w obciążeniu produkcyjnym, w tym artykule z tego artykułu opisano, że region wdrożenia nie jest ważny.

## <a name="overview"></a>Omówienie

Oto ilustracja przedstawiająca używane składniki oraz przepływ między nimi po zakończeniu tego procesu.
![Składniki używane i Flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Składniki używane i Flow")

Oto krótkie omówienie kroków:

1. Tworzenie Azure AD B2C wywoływanie (frontonu, API Management) i aplikacji interfejsu API z zakresami i udzielanie dostępu do interfejsu API
1. Utwórz zasady rejestracji i logowania, aby umożliwić użytkownikom logowanie się przy użyciu Azure AD B2C 
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

   > [!TIP]
   > Będziemy przechwycić dość kilka informacji i kluczy, tak jak w przypadku tego dokumentu, może się okazać, że warto otworzyć Edytor tekstu do tymczasowego przechowywania następujących elementów konfiguracji.  
   >
   > IDENTYFIKATOR KLIENTA ZAPLECZA B2C:  
   > KLUCZ TAJNY KLIENTA ZAPLECZA B2C:  
   > IDENTYFIKATOR URI ZAKRESU INTERFEJSU API ZAPLECZA B2C:  
   > IDENTYFIKATOR KLIENTA FRONTONU B2C:  
   > IDENTYFIKATOR URI PUNKTU KOŃCOWEGO PRZEPŁYWU UŻYTKOWNIKA B2C:  
   > B2C DOBRZE ZNANY PUNKT KOŃCOWY OPENID CONNECT:   
   > Nazwa zasad B2C: adres URL funkcji Frontendapp_signupandsignin:  
   > PODSTAWOWY ADRES URL INTERFEJSU API APIM: ADRES URL PODSTAWOWEGO PUNKTU KOŃCOWEGO MAGAZYNU:  

## <a name="configure-the-backend-application"></a>Konfigurowanie aplikacji zaplecza

Otwórz blok Azure AD B2C w portalu i wykonaj następujące czynności.

1. Wybierz kartę **rejestracje aplikacji**
1. Kliknij przycisk "Nowa rejestracja".
1. Wybierz opcję "Web" z pola wyboru URI przekierowania.
1. Teraz Ustaw nazwę wyświetlaną, wybierz coś unikatowego i istotnego dla tworzonej usługi. W tym przykładzie zostanie użyta nazwa "aplikacja zaplecza".
1. Użyj symboli zastępczych dla adresów URL odpowiedzi, takich jak " https://jwt.ms " (witryna dekodowania tokenów należących do firmy Microsoft), zaktualizujemy te adresy URL później.
1. Upewnij się, że wybrano opcję "konta w dowolnym dostawcy tożsamości lub katalogu organizacji (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)"
1. Na potrzeby tego przykładu usuń zaznaczenie pola "Udziel zgody administratora", ponieważ obecnie nie będziemy wymagać uprawnień offline_access.
1. Kliknij pozycję „Zarejestruj”.
1. Zapisz identyfikator klienta aplikacji zaplecza do późniejszego użycia (pokazany w obszarze "Identyfikator aplikacji (klienta)").
1. Wybierz kartę *Certyfikaty i wpisy tajne* (w obszarze Zarządzaj), a następnie kliknij pozycję Nowy wpis tajny klienta, aby wygenerować klucz uwierzytelniania (zaakceptuj ustawienia domyślne i kliknij przycisk "Dodaj").
1. Po kliknięciu pozycji "Dodaj" skopiuj klucz (w obszarze "wartość") w celu późniejszego użycia jako "klucz tajny klienta zaplecza" — należy pamiętać, że to okno dialogowe jest jedyną okazją, aby móc skopiować ten klucz.
1. Teraz wybierz kartę *Uwidacznianie interfejsu API* (w obszarze Zarządzanie).
1. Zostanie wyświetlony monit o ustawienie identyfikatora URI AppID, wybranie i zapisanie wartości domyślnej.
1. Utwórz i Nazwij zakres "Hello" dla interfejsu API funkcji, możesz użyć frazy "Hello" dla wszystkich opcji, które można wprowadzić, rejestrując wypełniony pełny identyfikator URI wartości zakresu, a następnie kliknij pozycję "Dodaj zakres".
1. Wróć do katalogu głównego bloku Azure AD B2C, wybierając łącza "Azure AD B2C" w lewym górnym rogu portalu.

   > [!NOTE]
   > Zakresy Azure AD B2C są skutecznymi uprawnieniami w interfejsie API, z których mogą żądać dostępu inne aplikacje za pośrednictwem bloku dostęp do interfejsu API w aplikacjach, tym wydajniej zostały utworzone uprawnienia aplikacji dla wywoływanego interfejsu API.

## <a name="configure-the-frontend-application"></a>Konfigurowanie aplikacji frontonu

1. Wybierz kartę **rejestracje aplikacji**
1. Kliknij przycisk "Nowa rejestracja".
1. Wybierz pozycję "aplikacja jednostronicowa (SPA)" w polu wyboru URI przekierowania.
1. Teraz Ustaw nazwę wyświetlaną i identyfikator URI identyfikatora aplikacji, wybierz unikatową i istotną dla aplikacja frontonu, która będzie używać tej AAD B2C rejestracji aplikacji. W tym przykładzie można użyć "aplikacja frontonu"
1. Zgodnie z pierwszą rejestracją aplikacji pozostaw wybrane typy kont jako domyślne (uwierzytelnianie użytkowników przy użyciu przepływów użytkowników).
1. Użyj symboli zastępczych dla adresów URL odpowiedzi, takich jak " https://jwt.ms " (witryna dekodowania tokenów należących do firmy Microsoft), zaktualizujemy te adresy URL później.
1. Pozostaw pole zgody na zgodę administratora
1. Kliknij pozycję „Zarejestruj”.
1. Zapisz identyfikator klienta aplikacji frontonu do późniejszego użycia (pokazany w sekcji "Identyfikator aplikacji klienta").
1. Przejdź do karty *uprawnienia interfejsu API* .
1. Aby udzielić dostępu do aplikacji zaplecza, kliknij pozycję "Dodaj uprawnienie", następnie "Moje interfejsy API", wybierz pozycję "aplikacja zaplecza", wybierz pozycję "uprawnienia", wybierz zakres utworzony w poprzedniej sekcji, a następnie kliknij pozycję "Dodaj uprawnienia".
1. Kliknij pozycję "Udziel zgody administratora {dzierżawca}" i kliknij pozycję "tak" w oknie dialogowym podręcznym. To menu podręczne przyniesie "aplikację frontonu", aby użyć uprawnienia "Hello" zdefiniowanego wcześniej w "aplikacji zaplecza".
1. Wszystkie uprawnienia powinny teraz być widoczne dla aplikacji jako zielony znacznik w kolumnie Stan.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Utwórz przepływ użytkownika "Rejestracja i logowanie"

1. Wróć do katalogu głównego bloku B2C, wybierając pozycję Azure AD B2C reścieżką.
1. Przejdź do karty "Przepływy użytkownika" (w obszarze zasady).
1. Kliknij pozycję "nowy przepływ użytkownika".
1. Wybierz typ przepływu użytkownika "Rejestracja i logowanie", a następnie wybierz pozycję "zalecane", a następnie opcję "Utwórz".
1. Nadaj zasadom nazwę i Zapisz ją w późniejszym czasie. Na potrzeby tego przykładu można użyć "Frontendapp_signupandsignin", pamiętając, że zostanie on poprzedzony "B2C_1_", aby utworzyć "B2C_1_Frontendapp_signupandsignin"
1. W obszarze "dostawcy tożsamości" i "konta lokalne" Sprawdź pozycję "Rejestracja w usłudze Emailing" (lub "Rejestracja identyfikatora użytkownika" w zależności od konfiguracji dzierżawy B2C) i kliknij przycisk OK. Ta konfiguracja wynika z faktu, że będziemy rejestrować lokalne konta B2C, nie odwołując się do innego dostawcy tożsamości (takiego jak dostawca tożsamości społecznościowej) do korzystania z istniejącego konta mediów społecznościowych użytkownika.
1. Pozostaw domyślne ustawienia MFA i dostępu warunkowego.
1. W obszarze "atrybuty użytkownika i oświadczenia" kliknij pozycję "Pokaż więcej..." następnie wybierz opcje dotyczące roszczeń, które użytkownicy mają wprowadzać i które zwracają w tokenie. Zaznacz pole wyboru "Nazwa wyświetlana" i "adres E-mail" do zebrania, a następnie wybierz opcję "Nazwa wyświetlana" i "adresy E-mail", które mają być zwracane (należy zwrócić szczególną uwagę na fakt, że zbierasz EmailAddress, dane pojedyncze i z prośbą o zwrócenie adresów e-mail, wiele), a następnie kliknij pozycję "Utwórz".
1. Kliknij przepływ użytkownika utworzony na liście, a następnie kliknij przycisk Uruchom przepływ użytkownika.
1. Ta akcja spowoduje otwarcie bloku uruchamianie przepływu użytkownika, wybranie aplikacji frontonu, skopiowanie punktu końcowego przepływu użytkownika i zapisanie go w późniejszym czasie.
1. Skopiuj i Zapisz link na górze, rejestrując jako "dobrze znany punkt końcowy konfiguracji OpenID Connect" do późniejszego użycia.

   > [!NOTE]
   > Zasady B2C umożliwiają Uwidacznianie punktów końcowych logowania Azure AD B2C, aby umożliwić Przechwytywanie różnych składników danych i Logowanie użytkowników na różne sposoby.
   > 
   > W takim przypadku skonfigurowano przepływ rejestracji lub logowania (zasady). W ten sposób jest również narażony dobrze znany punkt końcowy konfiguracji, w obu przypadkach nasze utworzone zasady zostały zidentyfikowane w adresie URL przez parametr ciągu zapytania "p =".  
   >
   > Po wykonaniu tej czynności będziesz mieć funkcjonalną platformę tożsamości klienta, która będzie podpisywać użytkowników do wielu aplikacji.

## <a name="build-the-function-api"></a>Tworzenie interfejsu API funkcji

1. Przełącz się z powrotem do standardowej dzierżawy usługi Azure AD w Azure Portal, aby umożliwić ponowne skonfigurowanie elementów w ramach subskrypcji.
1. Przejdź do bloku aplikacje funkcji Azure Portal, Otwórz pustą aplikację funkcji, a następnie kliknij pozycję "Functions", a następnie kliknij pozycję "Dodaj".
1. W wyświetlonym oknie wysuwanym wybierz pozycję "Programowanie w portalu" w obszarze "Wybierz szablon", a następnie wybierz pozycję "wyzwalacz HTTP", w obszarze Szczegóły szablonu nadaj mu wartość "Hello" z poziomem autoryzacji "Function", a następnie wybierz pozycję Dodaj.
1. Przejdź do bloku Code + test i skopiuj go, a następnie wklej kod przykładowy poniżej *nad istniejącym kodem* , który zostanie wyświetlony.
1. Wybierz pozycję Zapisz.

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

   > [!TIP]
   > Właśnie wklejony kod funkcji skryptu c# po prostu rejestruje wiersz do dzienników funkcji i zwraca tekst "Hello world" z niektórymi danymi dynamicznymi (Data i godzina).

1. Wybierz pozycję "Integracja" w bloku po lewej stronie, a następnie kliknij link http (REQ) w polu "wyzwalacz".
1. Z listy rozwijanej "wybrane metody HTTP" Usuń zaznaczenie metody HTTP POST, pozostawiając tylko opcję Pobierz zaznaczone, a następnie kliknij przycisk Zapisz.
1. Przełącz się z powrotem na kartę kod + test, kliknij przycisk "Pobierz adres URL funkcji", a następnie skopiuj wyświetlony adres URL i Zapisz go później.

   > [!NOTE]
   > Utworzone powiązania po prostu informują funkcje, które odpowiadają za anonimowe żądania HTTP GET do właśnie skopiowanego adresu URL ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ). Teraz korzystamy z skalowalnego interfejsu API https bezserwerowego, który jest w stanie zwrócić bardzo prosty ładunek.
   >
   > Teraz można testować wywoływanie tego interfejsu API z przeglądarki sieci Web przy użyciu używanej wersji adresu URL, który został właśnie skopiowany i zapisany. Można również usunąć parametry ciągu zapytania "? Code = SecretKey" w adresie URL i ponownie wykonać test, aby potwierdzić, że Azure Functions zwróci błąd 401.

## <a name="configure-and-secure-the-function-api"></a>Konfigurowanie i zabezpieczanie interfejsu API funkcji

1. Należy skonfigurować dwa dodatkowe obszary w aplikacji funkcji (ograniczenia autoryzacji i sieci).
1. Po pierwsze Skonfiguruj uwierzytelnianie/autoryzację, więc przejdź z powrotem do głównego bloku aplikacji funkcji za pośrednictwem stron nadrzędnych.
1. Następnie wybierz pozycję "uwierzytelnianie/autoryzacja" (w obszarze "Ustawienia").
1. Włącz funkcję uwierzytelniania App Service.
1. Ustaw akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione na liście rozwijanej do "Zaloguj się za pomocą Azure Active Directory".
1. W obszarze "dostawcy uwierzytelniania" Wybierz pozycję "Azure Active Directory".
1. Wybierz opcję "Zaawansowane" z przełącznika trybu zarządzania.
1. Wklej identyfikator klienta [Application] aplikacji zaplecza (z Azure AD B2C) w polu "identyfikator klienta"
1. Wklej dobrze znany punkt końcowy konfiguracji Open-ID z pola Rejestracja i logowanie w polu adres URL wystawcy (Ta konfiguracja została wpisana wcześniej).
1. Kliknij pozycję "Pokaż wpis tajny" i Wklej klucz tajny klienta aplikacji zaplecza do odpowiedniego pola.
1. Wybierz przycisk OK, co spowoduje powrót do bloku/ekranu wyboru dostawcy tożsamości.
1. Pozostaw [Magazyn tokenów](../app-service/overview-authentication-authorization.md#token-store) włączony w ustawieniach zaawansowanych (domyślnie).
1. Kliknij przycisk "Zapisz" (w lewym górnym rogu bloku).

   > [!IMPORTANT]
   > Teraz interfejs API funkcji jest wdrożony i powinien zgłosić 401 odpowiedzi, jeśli poprawność tokenu JWT nie zostanie podana jako autoryzacja: nagłówek okaziciela i powinna zwracać dane po wyświetleniu prawidłowego żądania.  
   > Dodano dodatkowe zabezpieczenia dotyczące zabezpieczeń w programie EasyAuth przez skonfigurowanie opcji "Logowanie za pomocą usługi Azure AD" w celu obsługi nieuwierzytelnionych żądań. Należy pamiętać, że spowoduje to zmianę zachowania nieautoryzowanego żądania między aplikacja funkcji wewnętrznej bazy danych i prze302 stawieniem SPA, ponieważ EasyAuth nastąpi przekierowanie do usługi AAD zamiast nieautoryzowanej odpowiedzi 401, nastąpi poprawienie przy użyciu API Management później.  
   >
   > Nadal nie stosujemy zabezpieczeń IP, jeśli masz prawidłowy klucz i token OAuth2, każdy z nich może wywoływać z dowolnego miejsca — najlepiej będzie wymusić, aby wszystkie żądania były realizowane za pośrednictwem API Management.  
   > 
   > Jeśli używasz warstwy zużycia APIM, [nie istnieje dedykowany wirtualny adres IP platformy Azure API Management](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) , aby zezwolić na listę z ograniczeniami dostępu do funkcji. W przypadku jednostki SKU usługi Azure API Management Standard i powyżej [adresu VIP jest pojedynczy dzierżawca i okres istnienia zasobu](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). W przypadku warstwy użycia usługi Azure API Management można zablokować wywołania interfejsu API za pomocą klucza współużytkowanego funkcji tajnej w części identyfikatora URI, który został skopiowany. Ponadto w przypadku warstwy zużycia nie ma zastosowania Krok 12-17 poniżej.

1. Zamknij blok uwierzytelnianie/autoryzacja 
1. Otwórz *blok API Management portalu*, a następnie otwórz *jego wystąpienie*.
1. Zapisz prywatne wirtualne adresy IP widoczne na karcie Przegląd.
1. Wróć do *bloku Azure Functions portalu,* a następnie ponownie Otwórz *wystąpienie* .
1. Wybierz pozycję Sieć, a następnie wybierz pozycję "Konfiguruj ograniczenia dostępu".
1. Kliknij pozycję "Dodaj regułę" i wprowadź adres VIP skopiowany w kroku 3 powyżej w formacie XX. XX. XX. XX/32.
1. Jeśli chcesz kontynuować pracę z portalem funkcji i wykonać opcjonalne czynności opisane poniżej, należy również dodać własny publiczny adres IP lub zakres CIDR.
1. Gdy na liście znajduje się wpis Zezwalaj, platforma Azure dodaje niejawną regułę odmowy, aby zablokować wszystkie inne adresy.

Musisz dodać bloki z formatowaniem CIDR do panelu ograniczenia adresów IP. Gdy musisz dodać pojedynczy adres, taki jak adres VIP API Management, musisz go dodać w formacie XX. XX. XX. XX/32.

   > [!NOTE]
   > Teraz interfejs API funkcji nie powinien być wywoływany z innych lokalizacji niż za pośrednictwem usługi API Management ani Twojego adresu.

1. Otwórz *blok API Management*, a następnie otwórz *wystąpienie*.
1. Wybierz blok interfejsy API (w obszarze interfejsy API).
1. W okienku "Dodawanie nowego interfejsu API" Wybierz pozycję "aplikacja funkcji", a następnie wybierz pozycję "pełne" w górnej części okna podręcznego.
1. Kliknij przycisk Przeglądaj, wybierz aplikację funkcji, w której znajduje się interfejs API, a następnie kliknij przycisk Wybierz. Następnie kliknij pozycję Wybierz ponownie.
1. Podaj nazwę i opis interfejsu API do użytku wewnętrznego API Management i Dodaj je do produktu "nieograniczony".
1. Skopiuj i Zapisz podstawowy adres URL interfejsu API i kliknij przycisk Utwórz.
1. Kliknij kartę "Ustawienia", a następnie w obszarze subskrypcja-Wyłącz pole wyboru "wymagana subskrypcja", ponieważ w tym przypadku będziemy używać tokenu JWT uwierzytelniania OAuth. Należy pamiętać, że w przypadku korzystania z warstwy zużycia będzie ona nadal wymagana w środowisku produkcyjnym. 

   > [!TIP]
   > W przypadku korzystania z warstwy zużycia APIM nieograniczony produkt nie będzie dostępny jako gotowy do użycia. Zamiast tego przejdź do pozycji "produkty" w obszarze "interfejsy API" i kliknij pozycję "Dodaj".  
   > Wpisz "nieograniczone" jako nazwę produktu i opis, a następnie wybierz interfejs API, który właśnie został dodany z objaśnienia interfejsów API "+" w lewym dolnym rogu ekranu. Wybierz pole wyboru "opublikowany". Pozostaw resztę jako domyślną. Na koniec kliknij przycisk "Utwórz". Ten element został utworzony jako "nieograniczony" i przypisany do interfejsu API. Nowy produkt można później dostosować.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Skonfiguruj i Przechwyć poprawne ustawienia punktu końcowego magazynu

1. Otwórz blok konta magazynu w Azure Portal 
1. Wybierz utworzone konto i wybierz blok "statyczna witryna sieci Web" z sekcji Ustawienia (jeśli nie widzisz opcji "statyczna witryna sieci Web", sprawdź, czy utworzono konto w wersji 2).
1. Ustaw statyczną funkcję hostingu sieci Web na wartość "Enabled" i Ustaw nazwę dokumentu indeksu na "index.html", a następnie kliknij pozycję "Zapisz".
1. Zanotuj zawartość "podstawowego punktu końcowego" w przyszłości, ponieważ ta lokalizacja wskazuje, że witryna frontonu będzie hostowana.

   > [!TIP]
   > Możesz użyć usługi Azure Blob Storage i ponownego zapisania w usłudze CDN lub Azure App Service do hostowania funkcji SPA, ale Blob Storage funkcja hostingu statycznej witryny sieci Web zapewnia nam domyślny kontener do obsługi statycznej zawartości internetowej/HTML/JS/CSS z usługi Azure Storage

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Konfigurowanie zasad **CORS** i **Walidacja-JWT**

> Należy przestrzegać następujących sekcji, niezależnie od używanej warstwy APIM. Adres URL konta magazynu pochodzi z konta magazynu, które zostało udostępnione w ramach wymagań wstępnych w górnej części tego artykułu.
1. Przejdź do bloku usługi API Management portalu i Otwórz wystąpienie.
1. Wybierz pozycję Interfejsy API, a następnie wybierz pozycję "wszystkie interfejsy API".
1. W obszarze "przetwarzanie przychodzące" kliknij przycisk Widok kodu "</>", aby wyświetlić Edytor zasad.
1. Edytuj sekcję dotyczącą ruchu przychodzącego i wklej poniższy kod XML, tak aby miał postać podobną do następującej.
1. Zastąp następujące parametry w zasadach
1. {PrimaryStorageEndpoint} (Podstawowy punkt końcowy magazynu skopiowany w poprzedniej sekcji), {zasadami b2cpolicy-OpenID Connect} ("dobrze znanego punktu końcowego konfiguracji OpenID Connect" skopiowanego wcześniej) i {zaplecza-API-Application-Client-ID} (Identyfikator aplikacji/klienta dla **interfejsu API zaplecza**) z prawidłowymi zapisanymi wcześniej wartościami.
1. Jeśli używasz warstwy zużycia API Management, należy usunąć zasady częstotliwość i limit-według klucza, ponieważ te zasady nie są dostępne w przypadku korzystania z warstwy użycia usługi Azure API Management.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Teraz usługa Azure API Management umożliwia reagowanie na żądania krzyżowego pochodzenia z aplikacji SPA w języku JavaScript. spowoduje to przeprowadzenie ograniczenia przepustowości, ograniczenia szybkości i wstępnego sprawdzania poprawności tokenu uwierzytelniania JWT przed przekazaniem żądania do interfejsu API funkcji.
   > 
   > Gratulacje, masz teraz Azure AD B2C, API Management i Azure Functions współdziałania w celu publikowania, zabezpieczania i korzystania z interfejsu API.

   > [!TIP]
   > Jeśli korzystasz z warstwy użycia API Management, zamiast szybkości ograniczania przez podmiot JWT lub przychodzący adres IP (ograniczanie szybkości wywołań przez zasady kluczy nie jest obecnie obsługiwane dla warstwy "zużycie"), możesz ograniczyć limit przydziału liczby wywołań, patrz [tutaj](./api-management-access-restriction-policies.md#LimitCallRate).  
   > W tym przykładzie jest to aplikacja jednostronicowa języka JavaScript, która używa klucza API Management tylko dla wywołań ograniczających szybkość i rozliczeń. Rzeczywista autoryzacja i uwierzytelnianie jest obsługiwane przez Azure AD B2C i są hermetyzowane w tokenie JWT, które są sprawdzane dwukrotnie, raz przez API Management, a następnie przez funkcję zaplecza platformy Azure.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Przekaż próbkę SPA JavaScript do magazynu statycznego

1. W bloku konto magazynu wybierz blok "kontenery" z sekcji usługa BLOB Service i kliknij kontener $web, który pojawia się w okienku po prawej stronie.
1. Zapisz Poniższy kod do pliku lokalnego na komputerze jako index.html, a następnie Przekaż plik index.html do kontenera $web.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Przejdź do podstawowego punktu końcowego witryny sieci Web, który został zapisany wcześniej w ostatniej sekcji.

   > [!NOTE]
   > Gratulacje, po prostu wdrożono aplikację jednostronicową JavaScript do hostingu zawartości statycznej usługi Azure Storage.  
   > Ze względu na to, że nie skonfigurowano jeszcze aplikacji JS z informacjami dotyczącymi Azure AD B2C — Strona nie będzie jeszcze działała, jeśli ją otworzysz.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Skonfiguruj skrypt SPA JavaScript dla Azure AD B2C

1. Teraz wiemy, gdzie wszystko jest: można skonfigurować SPA przy użyciu odpowiedniego adresu interfejsu API API Management i poprawnych Azure AD B2C identyfikatorów aplikacji/klientów.
1. Wróć do bloku Azure Portal Storage 
1. Wybierz pozycję "Containers" (w obszarze "Ustawienia") 
1. Wybierz z listy kontener "$web"
1. Wybierz obiekt BLOB index.html z listy 
1. Kliknij pozycję "Edytuj" 
1. Zaktualizuj wartości uwierzytelniania w sekcji msal config, aby odpowiadały aplikacji *frontonu* zarejestrowanej w usłudze B2C wcześniej. Skorzystaj z komentarzy do kodu, aby uzyskać wskazówki dotyczące sposobu, w jaki powinny wyglądać wartości konfiguracyjne.
Wartość *urzędu* musi mieć format:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsigninpolicyname}, jeśli użyto naszych przykładowych nazw, a dzierżawa B2C jest nazywana "contoso", oczekiwano, że urząd będzie " https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin ".
1. Ustaw wartości interfejsu API tak, aby odpowiadały adresowi zaplecza (podstawowy adres URL interfejsu API, który został zarejestrowany wcześniej, a wartości "b2cScopes" zostały zarejestrowane wcześniej dla *aplikacji zaplecza*).
1. Klikanie pozycji Zapisz.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Ustawianie identyfikatorów URI przekierowania dla aplikacji frontonu Azure AD B2C

1. Otwórz blok Azure AD B2C i przejdź do rejestracji aplikacji dla aplikacji frontonu języka JavaScript.
1. Kliknij pozycję "Przekieruj identyfikatory URI" i Usuń symbol zastępczy " https://jwt.ms " wprowadzony wcześniej.
1. Dodaj nowy identyfikator URI podstawowego (magazynu) punktu końcowego (minus końcowy ukośnika).

   > [!NOTE]
   > Ta konfiguracja spowoduje, że klient aplikacji frontonu otrzyma token dostępu z odpowiednimi oświadczeniami z Azure AD B2C.  
   > SPA będzie mógł dodać ten element jako token okaziciela w nagłówku https w wywołaniu interfejsu API zaplecza.  
   > 
   > API Management sprawdzi przed przekazaniem tokenów, nawiązanie połączenia z punktem końcowym za pomocą identyfikatora JWT wystawionego przez identyfikator platformy Azure (użytkownik) i adres IP obiektu wywołującego (w zależności od warstwy usługi API Management, przed przeprowadzeniem żądania do interfejsu API funkcji platformy Azure, dodając klucz zabezpieczeń Functions.  
   > SPA będzie renderować odpowiedź w przeglądarce.
   >
   > *Gratulacje, skonfigurowano Azure AD B2C, API Management platformy Azure, Azure Functions, Azure App Service autoryzację do pracy w idealnym zasobie!*

Teraz mamy prostą aplikację z prostym zabezpieczonym interfejsem API, aby ją przetestować.

## <a name="test-the-client-application"></a>Testowanie aplikacji klienckiej

1. Otwórz adres URL przykładowej aplikacji, który został zanotowany w utworzonym wcześniej koncie magazynu.
1. Kliknij pozycję "Zaloguj się" w prawym górnym rogu, a kliknięcie przycisku spowoduje wyświetlenie Azure AD B2C profilu rejestracji lub logowania.
1. Aplikacja powinna powitać użytkownika według nazwy profilu B2C.
1. Teraz kliknij przycisk "Call API", a strona powinna zaktualizować wartości wysyłane z zabezpieczonego interfejsu API.
1. Jeśli klikniesz *wielokrotnie* przycisk interfejsu API wywołania i korzystasz z warstwy dewelopera lub nowszej wersji API Management, należy zauważyć, że rozwiązanie zacznie ograniczać interfejs API i ta funkcja powinna być raportowana w aplikacji przy użyciu odpowiedniej wiadomości.

## <a name="and-were-done"></a>Wszystko gotowe

Powyższe kroki można dostosować i edytować, aby umożliwić korzystanie z wielu różnych Azure AD B2C z API Management.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Active Directory i OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Poznaj więcej [filmów wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) dotyczących API Management.
* Aby poznać inne sposoby zabezpieczenia usługi zaplecza, zobacz [uwierzytelnianie wzajemne certyfikatów](api-management-howto-mutual-certificates.md).
* [Utwórz wystąpienie usługi API Management](get-started-create-service-instance.md).
* [Zarządzanie pierwszym interfejsem API](import-and-publish.md).