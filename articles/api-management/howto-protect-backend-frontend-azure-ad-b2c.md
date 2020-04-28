---
title: Chroń zaplecze SPA za pomocą OAuth 2.0 przy użyciu usługi Azure Active Directory B2C i usługi Azure API Management.
description: Chroń interfejs API za pomocą interfejsu API 2.0 przy użyciu usługi Azure Active Directory B2C, usługi Azure API Management i Easy Auth, które mają być wywoływane z javascript SPA.
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
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475480"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Ochrona zaplecza SPA za pomocą funkcji OAuth 2.0, usługi Azure Active Directory B2C i usługi Azure API Management

W tym scenariuszu pokazano, jak skonfigurować wystąpienie usługi Azure API Management w celu ochrony interfejsu API.
Użyjemy protokołu OAuth 2.0 z usługą Azure AD B2C, wraz z usługą API Management, aby zabezpieczyć zaplecze usługi Azure Functions przy użyciu EasyAuth.

## <a name="aims"></a>Ma
Zobaczymy, jak usługa Zarządzanie interfejsami API może być używana w uproszczonym scenariuszu za pomocą funkcji platformy Azure i usługi Azure AD B2C. Utworzysz aplikację JavaScript (JS) wywołującą interfejs API, która loguje się do użytkowników za pomocą usługi Azure AD B2C. Następnie użyjesz funkcji zasad sprawdzania poprawności-jwt usługi api Management, aby chronić interfejs API wewnętrznej bazy danych.

Aby obrony w głębi, następnie używamy EasyAuth ponownie sprawdzić poprawność tokenu wewnątrz interfejsu API zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, musisz mieć:
* Konto magazynu ogólnego ogólnego przeznaczenia usługi Azure (StorageV2) platformy Azure (StorageV2) do obsługi aplikacji jednostronicowej js frontendu JS
* Wystąpienie usługi Azure API Management 
* Pusta aplikacja funkcji platformy Azure (z uruchomionym czasem wykonywania V2 .NET Core w planie zużycia systemu Windows) do obsługi wywoływanego interfejsu API
* Dzierżawa usługi Azure AD B2C połączona z subskrypcją 

Chociaż w praktyce można użyć zasobów w tym samym regionie w obciążeniach produkcyjnych, w tym artykule in how-to region wdrożenia nie jest ważne.

## <a name="overview"></a>Omówienie
Oto ilustracja używanych składników i przepływu między nimi po zakończeniu tego procesu.
![Komponenty w użyciu i przepływie](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Komponenty w użyciu i przepływie")

Oto krótki przegląd kroków:

1. Tworzenie wywołania usługi Azure AD B2C (frontend, zarządzanie interfejsami API) i aplikacji interfejsu API z zakresami i udzielanie dostępu do interfejsu API
1. Tworzenie zasad rejestracji lub logowania się, aby umożliwić użytkownikom logowanie się za pomocą usługi Azure AD B2C 
1. Konfigurowanie zarządzania interfejsami API przy pomocą nowych identyfikatorów klienta usługi Azure AD B2C i kluczy w celu włączenia autoryzacji użytkownika OAuth2 w konsoli dewelopera
1. Tworzenie interfejsu API funkcji
1. Skonfiguruj interfejs API funkcji, aby włączyć funkcję EasyAuth przy użyciu nowego identyfikatora klienta i kluczy usługi Azure AD B2C i zablokować do interfejsu APIM VIP 
1. Tworzenie definicji interfejsu API w zarządzaniu interfejsami API
1. Konfigurowanie usługi Oauth2 dla konfiguracji interfejsu API zarządzania interfejsem API
1. Konfigurowanie zasad **CORS** i dodawanie zasad **validate-jwt** w celu sprawdzania poprawności tokenu OAuth dla każdego żądania przychodzącego
1. Tworzenie aplikacji wywołującej do korzystania z interfejsu API
1. Prześlij przykład JS SPA
1. Konfigurowanie przykładowej aplikacji klienckiej JS przy obliczu nowych identyfikatorów i kluczy klienta usługi Azure AD B2C 
1. Testowanie aplikacji klienckiej

## <a name="configure-azure-ad-b2c"></a>Konfigurowanie usługi Azure AD B2C 
Otwórz blok usługi Azure AD B2C w portalu i wykonaj następujące kroki.
1. Wybierz kartę **Aplikacje** 
1. Kliknij przycisk "Dodaj" i utwórz trzy aplikacje do następujących celów
   * Klienta frontu.
   * Interfejs API funkcji wewnętrznej bazy danych.
   * [Opcjonalnie] Portal deweloperów zarządzania interfejsami API (chyba że korzystasz z usługi Azure API Management w warstwie zużycie, więcej informacji na temat tego scenariusza później).
1. Ustaw WebApp / Web API dla wszystkich 3 aplikacji i ustaw "Zezwalaj na przepływ niejawny" na tak tylko dla klienta frontu.
1. Teraz ustaw identyfikator URI identyfikatora aplikacji, wybierz coś unikatowego i odpowiedniego dla tworzonej usługi.
1. Użyj symboli zastępczych dla adresów https://localhostURL odpowiedzi na razie, takich jak , będziemy aktualizować te adresy url później.
1. Kliknij przycisk "Utwórz", a następnie powtórz kroki 2-5 dla każdej z trzech aplikacji powyżej, rejestrując identyfikator URI identyfikatora AppID, nazwę i identyfikator aplikacji do późniejszego użycia dla wszystkich trzech aplikacji.
1. Otwórz aplikację portalu dla deweloperów zarządzania interfejsami API z listy aplikacji i wybierz kartę *Klucze* (w obszarze Ogólne), a następnie kliknij przycisk "Generuj klucz", aby wygenerować klucz ystości
1. Po kliknięciu zapisz, nagraj klucz w bezpiecznym miejscu do późniejszego użycia - zwróć uwagę, że to miejsce jest JEDYNĄ szansą, że dostaniesz, aby zobaczyć i skopiować ten klucz.
1. Teraz wybierz kartę *Opublikowane zakresy* (w obszarze Dostęp do interfejsu API)
1. Utwórz i nazwij zakres interfejsu API funkcji i zarejestruj zakres i zapełnioną wartość pełnego zakresu, a następnie kliknij przycisk "Zapisz".
   > [!NOTE]
   > Zakresy usługi Azure AD B2C są skutecznie uprawnienia w interfejsie API, że inne aplikacje mogą żądać dostępu do za pośrednictwem bloku dostępu interfejsu API z ich aplikacji, skutecznie właśnie utworzone uprawnienia aplikacji dla wywoływanych interfejsu API.
1. Otwórz pozostałe dwie aplikacje, a następnie poszukaj w zakładce *Dostęp interfejsu API.*
1. Udziel im dostępu do zakresu interfejsu API wewnętrznej bazy danych i domyślnego, który już tam był ("Dostęp do profilu użytkownika").
1. Wygeneruj im klucz, wybierając kartę *Klawisze* w obszarze "Ogólne", aby wygenerować klucz yjski i nagrać te klucze w bezpiecznym miejscu na później.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Tworzenie przepływu użytkownika "Zarejestruj się lub zaloguj się"
1. Powrót do katalogu głównego (lub "Przegląd") bloku usługi Azure AD B2C 
1. Następnie wybierz "Przepływy użytkownika (zasady)" i kliknij "Nowy przepływ użytkownika"
1. Wybierz typ przepływu użytkownika "Zarejestruj się i zaloguj się"
1. Nadaj zasadom nazwę i zapisz ją na później.
1. Następnie w obszarze "Dostawcy tożsamości" zaznacz opcję "Zarejestruj się identyfikator użytkownika" (może to oznaczać "Zarejestruj się e-mailem") i kliknij przycisk OK. 
1. W obszarze "Atrybuty i oświadczenia użytkownika" kliknij "Pokaż więcej...". następnie wybierz opcje oświadczeń, które mają być wprowadzane przez użytkowników i powróciły w tokenie. Zaznacz co najmniej "Wyświetlana nazwa" i "Adres e-mail", aby zebrać i zwrócić, a następnie kliknij "OK", a następnie kliknij "Utwórz".
1. Wybierz zasady utworzone na liście, a następnie kliknij przycisk "Uruchom przepływ użytkownika".
1. Ta akcja otworzy blok przepływu użytkownika, wybierz aplikację frontend, a następnie zarejestruje adres domeny b2clogin.com, która jest wyświetlana w obszarze listy rozwijanej dla "Wybierz domenę".
1. Kliknij łącze u góry, aby otworzyć "dobrze znany punkt końcowy konfiguracji openid" i zapisz authorization_endpoint i token_endpoint wartości, a także wartość samego łącza jako dobrze znanego punktu końcowego konfiguracji openid.

   > [!NOTE]
   > Zasady B2C umożliwiają udostępnianie punktów końcowych logowania usługi Azure AD B2C, aby móc przechwytywać różne składniki danych i logować się do użytkowników na różne sposoby. W takim przypadku skonfigurowaliśmy zarejestruj się lub zaloguj się w punkcie końcowym, który ujawnił dobrze znany punkt końcowy konfiguracji, w szczególności nasze utworzone zasady zostały zidentyfikowane w adresie URL przez parametr p=.
   > 
   > Gdy to zrobisz — masz teraz funkcjonalną platformę tożsamości firmy do konsumenta, która będzie logować użytkowników do wielu aplikacji. 
   > Jeśli chcesz, możesz kliknąć przycisk "Uruchom przepływ użytkownika" tutaj (aby przejść przez proces rejestracji lub logowania) i poczuć, co zrobi w praktyce, ale krok przekierowania na końcu zakończy się niepowodzeniem, ponieważ aplikacja nie została jeszcze wdrożona.

## <a name="build-the-function-api"></a>Tworzenie interfejsu API funkcji
1. Przełącz się z powrotem do standardowej dzierżawy usługi Azure AD w portalu Azure, abyśmy mogli ponownie skonfigurować elementy w ramach subskrypcji 
1. Przejdź do bloku aplikacje funkcji witryny Azure portal, otwórz pustą aplikację funkcji, a następnie utwórz nową funkcję "Webhook + API" w portalu za pośrednictwem przewodnika Szybki start.
1. Wklej przykładowy kod z dołu do Run.csx nad istniejącym kodem, który się pojawi.

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
   > Kod funkcji skryptu c#, który właśnie wklejony po prostu rejestruje wiersz do dzienników funkcji i zwraca tekst "Hello World" z pewnymi danymi dynamicznymi (datą i godziną).

3. Wybierz "Integruj" z lewego bloku, a następnie wybierz "Zaawansowany edytor" w prawym górnym rogu okienka.
4. Wklej poniższy przykładowy kod na istniejącym json.

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

5. Przełącz się z powrotem do karty HttpTrigger1, kliknij przycisk "Pobierz adres URL funkcji", a następnie skopiuj wyświetlony adres URL.

   > [!NOTE]
   > Powiązania, które właśnie utworzono, po prostu powiedz funkcji, aby odpowiadać na anonimowe żądania http GET do adresu URL, który właśnie skopiowałeś. (Terazhttps://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) mamy skalowalny interfejs API https bez serwera, który jest w stanie zwrócić bardzo prosty ładunek.
   > Teraz można przetestować wywołanie tego interfejsu API z przeglądarki sieci web przy użyciu adresu URL powyżej, można również rozbierać ?code =tajną część adresu URL i udowodnić, że usługa Azure Functions zwróci błąd 401.

## <a name="configure-and-secure-the-function-api"></a>Konfigurowanie i zabezpieczanie interfejsu API funkcji
1. Należy skonfigurować dwa dodatkowe obszary w aplikacji funkcyjnej (ograniczenia auth i sieci).
1. Po pierwsze Skonfigurujmy uwierzytelnianie / autoryzację, więc kliknij &lt;na&gt; nazwę aplikacji funkcyjnej (obok ikony funkcji Z), aby wyświetlić stronę przeglądu.
1. Następnie wybierz zakładkę "Funkcje platformy" i wybierz "Uwierzytelnianie / autoryzacja".
1. Włącz funkcję uwierzytelniania usługi aplikacji.
1. W obszarze "Dostawcy uwierzytelniania" wybierz "Azure Active Directory" i wybierz opcję "Zaawansowane" z przełącznika trybu zarządzania.
1. Wklej identyfikator aplikacji interfejsu API funkcji wewnętrznej bazy danych (z usługi Azure AD B2C do pola "Identyfikator klienta")
1. Wklej dobrze znany punkt końcowy konfiguracji open-id z zasady rejestracji lub zaloguj się do pola URL wystawcy (wcześniej zarejestrowaliśmy tę konfigurację).
1. Wybierz przycisk OK.
1. Ustaw akcję do podjęcia, gdy żądanie nie jest uwierzytelnione rozwijane na "Zaloguj się za pomocą usługi Azure Active Directory", a następnie kliknij przycisk Zapisz.

   > [!NOTE]
   > Teraz interfejs API funkcji jest wdrożony i należy zgłosić 401 odpowiedzi, jeśli nie podano poprawny klucz i należy zwrócić dane, gdy zostanie wyświetlone prawidłowe żądanie.
   > Dodano dodatkowe zabezpieczenia obrony w EasyAuth, konfigurując opcję "Zaloguj się za pomocą usługi Azure AD" do obsługi nieuwierzytywanych żądań. Należy pamiętać, że spowoduje to zmianę zachowania nieautoryzowanego żądania między aplikacją funkcji wewnętrznej bazy danych a funkcją Frontend SPA, ponieważ EasyAuth wyda przekierowanie 302 do usługi AAD zamiast odpowiedzi nie autoryzowanej 401, poprawimy to przy użyciu zarządzania interfejsami API później.
   > Nadal nie stosujemy zabezpieczeń IP, jeśli masz prawidłowy klucz i token OAuth2, każdy może to wywołać z dowolnego miejsca - najlepiej, jeśli chcemy wymusić wszystkie żądania przyjść za pośrednictwem zarządzania interfejsami API.
   > Jeśli używasz warstwy zużycia usługi API Management, nie będzie można wykonać tej blokady przez adres VIP, ponieważ nie ma dedykowanego statycznego adresu IP dla tej warstwy, musisz polegać na metodzie blokowania wywołań interfejsu API za pośrednictwem udostępnionego klucza funkcji tajnej, więc kroki 11-13 nie będą możliwe.

1. Zamykanie bloku "Uwierzytelnianie/ autoryzacja" 
1. Wybierz opcję "Sieć", a następnie wybierz "Ograniczenia dostępu"
1. Następnie należy zablokować dozwolone adresy IP aplikacji funkcji do adresów VIP wystąpienia usługi API Management. Ten adres VIP jest wyświetlany w sekcji zarządzanie interfejsami API — omówienie portalu.
1. Jeśli chcesz nadal wchodzić w interakcje z portalem funkcji i wykonać poniższe opcjonalne kroki, należy również dodać własny publiczny adres IP lub zakres CIDR.
1. Gdy na liście pojawi się wpis zezwalania, platforma Azure dodaje regułę niejawnego odrzucania, aby zablokować wszystkie inne adresy. 

Do panelu Ograniczenia IP należy dodać sformatowane w formacie CIDR bloki adresów. Jeśli trzeba dodać jeden adres, taki jak ADRES VIP zarządzania interfejsem API, należy dodać go w formacie xx.xx.xx.xx.

   > [!NOTE]
   > Teraz interfejs API funkcji nie powinny być wywoływane z dowolnego miejsca innego niż za pośrednictwem zarządzania interfejsem API lub adres.
   
## <a name="import-the-function-app-definition"></a>Importowanie definicji aplikacji funkcji
1. Otwórz *blok Zarządzania interfejsami API*, a następnie otwórz *wystąpienie*.
1. Wybierz blok interfejsów API z sekcji Zarządzanie interfejsami API wystąpienia.
1. W okienku "Dodaj nowy interfejs API" wybierz pozycję "Aplikacja funkcji", a następnie wybierz opcję "Pełna" z górnej części okna podręcznego.
1. Kliknij przycisk Przeglądaj, wybierz aplikację funkcyjną, w której hostujesz interfejs API, a następnie kliknij pozycję Wybierz.
1. Nadaj interfejsowi API nazwę i opis do użytku wewnętrznego usługi API Management i dodaj go do "nieograniczonego" produktu.
1. Upewnij się, że rejestrujesz podstawowy adres URL do późniejszego użycia, a następnie kliknij przycisk Utwórz.

## <a name="configure-oauth2-for-api-management"></a>Konfigurowanie Oauth2 do zarządzania interfejsami API

1. Następnie wybierz ostrze Oauth 2.0 z karty Zabezpieczenia i kliknij przycisk "Dodaj"
1. Podaj wartości *nazwy wyświetlanej* i *opisu* dla dodanego punktu końcowego Oauth (te wartości będą wyświetlane w następnym kroku jako punkt końcowy Oauth2).
1. Możesz wprowadzić dowolną wartość w adresie URL strony rejestracji klienta, ponieważ ta wartość nie będzie używana.
1. Sprawdź *niejawne Auth* Grant typu i pozostawić kod autoryzacji udzielić typu zaznaczone.
1. Przejdź do pól *Autoryzacja* i *Token* punktu końcowego i wprowadź wartości przechwycone z dokumentu xml o znanej konfiguracji wcześniej.
1. Przewiń w dół i wypełnij *parametr Dodatkowe treści* o nazwie "zasób" identyfikatorem klienta interfejsu API funkcji wewnętrznej bazy danych z rejestracji aplikacji usługi Azure AD B2C
1. Wybierz opcję "Poświadczenia klienta", ustaw identyfikator klienta na identyfikator aplikacji konsoli dewelopera — pomiń ten krok, jeśli używasz modelu zarządzania interfejsem API zużycia.
1. Ustaw klucz tajny klienta na klucz nagrany wcześniej — pomiń ten krok, jeśli używasz modelu zarządzania interfejsem API zużycia.
1. Na koniec teraz nagrać redirect_uri grantu kodu eru z zarządzania interfejsami API do późniejszego użycia.

## <a name="set-up-oauth2-for-your-api"></a>Konfigurowanie interfejsu Oauth2 dla interfejsu API
1. Interfejs API pojawi się po lewej stronie portalu w sekcji "Wszystkie interfejsy API", otwórz interfejs API, klikając go.
1. Wybierz kartę "Ustawienia".
1. Zaktualizuj ustawienia, wybierając "Oauth 2.0" z przycisku opcji autoryzacji użytkownika.
1. Wybierz serwer Oauth, który został zdefiniowany wcześniej.
1. Zaznacz pole wyboru "Zastąrzyj zakres" i wprowadź zakres zarejestrowany dla wywołania interfejsu API wewnętrznej bazy danych wcześniej.

   > [!NOTE]
   > Teraz mamy wystąpienie zarządzania interfejsami API, które wie, jak uzyskać tokeny dostępu z usługi Azure AD B2C do autoryzowania żądań i rozumie naszą konfigurację B2C usługi Azure Active Directory Oauth2 Azure.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Konfigurowanie **zasad CORS** i **sprawdzania poprawności zasad jwt**

> Następujące sekcje powinny być przestrzegane niezależnie od warstwy APIM używane. 

1. Przełącz się z powrotem do karty projekt i wybierz "Wszystkie interfejsy API", a następnie kliknij przycisk widoku kodu, aby wyświetlić edytor zasad.
1. Edytuj sekcję przychodzącą i wklej poniższy xml, aby odczytywana była następująca.

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
1. Edytuj adres URL openid-config, aby dopasować swój dobrze znany punkt końcowy usługi Azure AD B2C dla zasad rejestracji lub logowania.
1. Edytuj wartość oświadczenia, aby dopasować prawidłowy identyfikator aplikacji, znany również jako identyfikator klienta dla aplikacji interfejsu API wewnętrznej bazy danych i zapisz.

   > [!NOTE]
   > Teraz zarządzanie interfejsem API jest w stanie odpowiedzieć na żądania cross origin do aplikacji JS SPA i będzie wykonywać ograniczanie przepustowości, ograniczanie szybkości i wstępnej weryfikacji tokenu Auth JWT przekazywane przed przekazaniem żądania do interfejsu API funkcji.

   > [!NOTE]
   > Poniższa sekcja jest opcjonalna i nie ma zastosowania do **warstwy Zużycie,** która nie obsługuje portalu dewelopera.
   > Jeśli nie zamierzasz korzystać z portalu dla deweloperów lub nie możesz go używać, ponieważ używasz warstwy Zużycie, pomiń ten krok i przejdź bezpośrednio do ["Zbuduj JavaScript SPA, aby korzystać z interfejsu API".](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[Opcjonalnie] Konfigurowanie portalu dewelopera

1. Otwórz blok usługi Azure AD B2C i przejdź do rejestracji aplikacji dla portalu dla deweloperów
1. Ustaw wpis "Adres URL odpowiedzi" na ten, który został odnotowany podczas konfigurowania redirect_uri dotacji kodu eru w usłudze API Management wcześniej.

   Teraz, gdy autoryzacja użytkownika OAuth 2.0 jest włączona w `Echo API`konsoli dewelopera uzyskuje token dostępu dla użytkownika, przed wywołaniem interfejsu API.

1. Przejdź do dowolnej operacji `Echo API` w portalu dla deweloperów i wybierz pozycję **Wypróbuj,** aby przenieść Cię do Konsoli dewelopera.
1. Zanotuj nowy element w sekcji **Autoryzacja,** odpowiadający właśnie dodanemu serwerowi autoryzacji.
1. Wybierz **kod autoryzacji** z listy rozwijanej autoryzacji i zostanie wyświetlony monit o zalogowanie się do dzierżawy usługi Azure AD. Jeśli jesteś już zalogowany za pomocą konta, może nie zostać wyświetlony monit.
1. Po pomyślnym zalogowaniu `Authorization: Bearer` nagłówka jest dodawany do żądania, z tokenem dostępu z usługi Azure AD B2C zakodowane w base64. 
1. Wybierz **pozycję Wyślij** i możesz pomyślnie wywołać interfejs API.

   > [!NOTE]
   > Teraz zarządzanie interfejsem API jest w stanie uzyskać tokeny dla portalu dewelopera, aby przetestować interfejs API i jest w stanie zrozumieć jego definicję i renderować odpowiednią stronę testową w portalu deweloperskim.

1. W bloku przeglądu portalu zarządzania interfejsami API kliknij przycisk "Portal deweloperów", aby zalogować się jako administrator interfejsu API.
1. W tym miejscu ty i inni wybrani konsumenci interfejsu API możecie testować i wywoływać je z konsoli.
1. Wybierz "Produkty", a następnie wybierz "Nieograniczony", a następnie wybierz API utworzyliśmy wcześniej i kliknij "WYPRÓBUJ IT"
1. Odkryj klucz subskrypcji interfejsu API i skopiuj go w bezpiecznym miejscu wraz z adresem URL żądania, który będzie potrzebny później.
1. Również wybrać niejawne, z listy rozwijanej oauth auth i może być trzeba uwierzytelnić tutaj z wyskakującym okienkiem.
1. Kliknij przycisk "Wyślij", a jeśli wszystko jest dobrze, aplikacja funkcji powinna odpowiedzieć z powrotem z hello message za pośrednictwem zarządzania interfejsem API z 200 OK komunikat i niektóre JSON.

   > [!NOTE]
   > Gratulacje, masz teraz usługi Azure AD B2C, zarządzanie interfejsami API i usługi Azure Functions współpracujące w celu publikowania, zabezpieczania i korzystania z interfejsu API. Można zauważyć, że interfejs API jest w rzeczywistości zabezpieczone dwa razy przy użyciu tej metody, raz z api Management Ocp-Subscription-Key Nagłówka i raz z Authorization: Bearer JWT.
   > Można by poprawić, ponieważ w tym przykładzie jest javascript aplikacji jednostronicowej, używamy klucza zarządzania interfejsami API tylko do ograniczania stawek i rozliczeń połączeń.
   > Rzeczywista autoryzacja i uwierzytelnianie jest obsługiwana przez usługę Azure AD B2C i jest hermetyzowana w JWT, który jest sprawdzany dwukrotnie, raz przez zarządzanie interfejsem API, a następnie przez usługi Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Tworzenie JavaScript SPA do korzystania z interfejsu API
1. Otwieranie bloku kont magazynu w witrynie Azure portal 
1. Wybierz utworzone konto i wybierz ostrze "Statyczna witryna" w sekcji Ustawienia (jeśli nie widzisz opcji "Statyczna witryna", sprawdź, czy utworzyłeś konto V2).
1. Ustaw statyczną funkcję hostingu na "włączoną", a następnie ustaw nazwę dokumentu indeksu na "index.html", a następnie kliknij przycisk "zapisz".
1. Zanotuj zawartość podstawowego punktu końcowego, ponieważ ta lokalizacja jest miejscem, w którym będzie hostowana lokacja frontona. 

   > [!NOTE]
   > Można użyć albo Azure Blob Storage + CDN przepisać, lub Usługi Azure App Service — ale funkcja hostingu statycznej witryny usługi Blob Storage daje nam domyślny kontener do obsługi statycznej zawartości sieci web / html / js / css z usługi Azure Storage i wywnioskować domyślną stronę dla nas do zerowej pracy.

## <a name="upload-the-js-spa-sample"></a>Prześlij przykład JS SPA
1. Nadal w bloku konta magazynu wybierz bloku "Obiekty blob" z sekcji Usługa obiektów blob i kliknij na $web kontener, który pojawia się w okienku po prawej stronie.
1. Zapisz poniższy kod w pliku lokalnie na komputerze jako index.html, a następnie przekaż plik index.html do kontenera $web.

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
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Przejdź do podstawowego punktu końcowego statycznej witryny sieci Web, który został zapisany wcześniej w ostatniej sekcji.

   > [!NOTE]
   > Gratulacje, właśnie wdrożyłeś aplikację jednostronicową JavaScript w usłudze Azure Storage Ponieważ nie skonfigurowaliśmy aplikacji JS z kluczami interfejsu API ani nie skonfigurowano aplikacji JS ze szczegółami usługi Azure AD B2C — strona nie będzie jeszcze działać, jeśli ją otworzysz.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Konfigurowanie usługi JS SPA dla usługi Azure AD B2C
1. Teraz wiemy, gdzie wszystko jest: możemy skonfigurować SPA z odpowiednim adresem interfejsu API zarządzania interfejsami API i poprawnymi identyfikatorami aplikacji / klienta usługi Azure AD B2C
1. Wróć do bloku magazynu portalu Azure i kliknij index.html, a następnie wybierz "Edytuj obiekt blob" 
1. Zaktualizuj szczegóły eru, aby dopasować aplikację front-end zarejestrowaną wcześniej w Usłudze B2C, zwracając się do niej, że wartości "b2cScopes" są dla wewnętrznej bazy danych interfejsu API.
1. Klucz webApi i adres URL interfejsu API można znaleźć w okienku testowym zarządzanie interfejsami API dla operacji interfejsu API.
1. Utwórz klucz subskrypcji APIM, kierując się do zarządzania interfejsami API z powrotem do bloku zarządzania interfejsami API, wybierając opcję "Subskrypcje", a następnie klikając "Dodaj subskrypcję", a następnie zapisując rekord. Kliknięcie wielokropek (...) obok utworzonego wiersza umożliwia wyświetlenie klawiszy, dzięki czemu można skopiować klucz podstawowy.
1. Powinien wyglądać mniej więcej jak poniższy kod:-  

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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Ustawianie identyfikatorów URI przekierowania dla aplikacji frontendowej usługi Azure AD B2C
1. Otwórz blok usługi Azure AD B2C i przejdź do rejestracji aplikacji dla aplikacji frontonu JavaScript
1. Ustaw adres URL przekierowania na ten, który został odnotowany podczas wcześniejszego konfigurowania statycznego punktu końcowego podstawowej witryny sieci Web powyżej

   > [!NOTE] 
   > Ta konfiguracja spowoduje, że klient aplikacji frontonetu otrzyma token dostępu z odpowiednimi oświadczeniami z usługi Azure AD B2C.
   > Spa będzie można dodać to jako token na okaziciela w nagłówku https w wywołaniu interfejsu API wewnętrznej bazy danych.
   > Usługa API Management wstępnie sprawdzi token, ograniczyć szybkość wywołań do punktu końcowego przez klucz subskrybenta, przed przekazaniem za pośrednictwem żądania do odbierającego interfejsu API funkcji platformy Azure.
   > Spa będzie renderować odpowiedź w przeglądarce.

   > *Gratulacje, skonfigurowano usługę Azure AD B2C, usługę Azure API Management, usługi Azure Functions, autoryzację usługi Azure App Service, aby działała w doskonałej harmonii!*

   > [!NOTE]
   > Teraz mamy prostą aplikację z prostym zabezpieczonym API, przetestujmy go.

## <a name="test-the-client-application"></a>Testowanie aplikacji klienckiej
1. Otwórz przykładowy adres URL aplikacji, który został odnotowany z konta magazynu utworzonego wcześniej
1. Kliknij "Zaloguj się" w prawym górnym rogu, to kliknięcie spowoduje wyświetlenie profilu rejestracji usługi Azure AD B2C lub zalogowania się.
1. Post Zaloguj się w sekcji "Zalogowany jako" na ekranie zostaną wypełnione z JWT.
1. Teraz kliknij przycisk "Call Web Api", a strona powinna zostać zaktualizowana o wartości wysyłane z bezpiecznego interfejsu API.

## <a name="and-were-done"></a>I gotowe
Powyższe kroki można dostosować i edytować, aby umożliwić wiele różnych zastosowań usługi Azure AD B2C z usługą API Management.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Azure Active Directory i OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Obejrzyj więcej [filmów](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o zarządzaniu interfejsami API.
* Aby uzyskać inne sposoby zabezpieczenia usługi zaplecza, zobacz [Uwierzytelnianie wzajemnego certyfikatu](api-management-howto-mutual-certificates.md).
* [Tworzenie wystąpienia usługi zarządzania interfejsami API](get-started-create-service-instance.md).
* [Zarządzaj pierwszym interfejsem API](import-and-publish.md).
