---
title: Przewodnik migracji biblioteki ADAL do MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Zapoznaj się z różnicami między programem MSAL for iOS/macOS i biblioteką uwierzytelniania usługi Azure AD dla ObjectiveC (ADAL. ObjC) i sposób migracji do MSAL dla systemu iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: fb66d8a4bf97a6f8a10534c9c4459123ad6a2654
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107924"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrowanie aplikacji do MSAL dla systemów iOS i macOS

Biblioteka uwierzytelniania Azure Active Directory ([cel ADAL-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) została utworzona w celu pracy z kontami Azure Active Directory za pośrednictwem punktu końcowego języka v 1.0.

Biblioteka uwierzytelniania firmy Microsoft dla systemów iOS i macOS (MSAL) została opracowana w celu współpracy ze wszystkimi tożsamościami firmy Microsoft, takimi jak konta usług Azure Active Directory (Azure AD), osobistymi kontami Microsoft i kontami Azure AD B2C za pośrednictwem platformy tożsamości firmy Microsoft (w formie punktu końcowego usługi Azure AD v 2.0).

Platforma tożsamości firmy Microsoft oferuje kilka najważniejszych różnic w Azure Active Directory v 1.0. W tym artykule wymieniono te różnice i przedstawiono wskazówki dotyczące migracji aplikacji z biblioteki ADAL do MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Różnice możliwości aplikacji ADAL i MSAL

### <a name="who-can-sign-in"></a>Kto może się zalogować

* Biblioteka ADAL obsługuje tylko konta służbowe — znane również jako konta usługi Azure AD.
* MSAL obsługuje osobiste konta Microsoft (konta MSA), takie jak Hotmail.com, Outlook.com i Live.com.
* MSAL obsługuje konta służbowe i konta Azure AD B2C.

### <a name="standards-compliance"></a>Zgodność ze standardami

* Punkt końcowy platformy tożsamości firmy Microsoft jest zgodny ze standardami OAuth 2,0 i OpenID Connect Connect.

### <a name="incremental-and-dynamic-consent"></a>Poprzednia i dynamiczna zgoda

* Punkt końcowy Azure Active Directory v 1.0 wymaga, aby wszystkie uprawnienia były zgłaszane z wyprzedzeniem podczas rejestracji aplikacji. Oznacza to, że te uprawnienia są statyczne.
* Platforma tożsamości firmy Microsoft umożliwia dynamiczne żądanie uprawnień. Aplikacje mogą prosić o uprawnienia tylko w razie potrzeby i żądać więcej, jak aplikacja je potrzebuje.

Aby uzyskać więcej informacji o różnicach między Azure Active Directory v 1.0 i platformą tożsamości firmy Microsoft, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="adal-and-msal-library-differences"></a>Różnice w bibliotece ADAL i MSAL

Publiczny interfejs API MSAL odzwierciedla kilka najważniejszych różnic między usługą Azure AD v 1.0 a platformą tożsamości firmy Microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication zamiast ADAuthenticationContext

`ADAuthenticationContext` to pierwszy obiekt tworzony przez aplikację ADAL. Reprezentuje utworzenie wystąpienia biblioteki ADAL. Aplikacje tworzą nowe wystąpienie `ADAuthenticationContext` dla każdej Azure Active Directory chmury i dzierżawcy (Urząd). Ta sama `ADAuthenticationContext` może służyć do uzyskiwania tokenów dla wielu publicznych aplikacji klienckich.

W programie MSAL główną interakcją jest użycie `MSALPublicClientApplication` obiektu, który jest modelem po stronie [klienta publicznego OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-2.1). Jedno wystąpienie programu `MSALPublicClientApplication` może służyć do korzystania z wielu chmur usługi AAD i dzierżawców bez konieczności tworzenia nowego wystąpienia dla każdego urzędu. W przypadku większości aplikacji `MSALPublicClientApplication` wystarczy jedno wystąpienie.

### <a name="scopes-instead-of-resources"></a>Zakresy zamiast zasobów

W bibliotece ADAL aplikacja musiała podać identyfikator *zasobu* , na przykład `https://graph.microsoft.com` w celu uzyskania tokenów z punktu końcowego Azure Active Directory v 1.0. Zasób może definiować wiele zakresów lub oAuth2Permissions w manifeście aplikacji, który rozumie. To dozwolone aplikacje klienckie do żądania tokenów z tego zasobu dla określonego zestawu zakresów wstępnie zdefiniowanych podczas rejestracji aplikacji.

W MSAL zamiast pojedynczego identyfikatora zasobu aplikacje udostępniają zestaw zakresów dla żądania. Zakres jest identyfikatorem zasobu, po którym następuje nazwa uprawnienia w formularzu zasób/uprawnienie. Na przykład `https://graph.microsoft.com/user.read`

Istnieją dwa sposoby udostępniania zakresów w MSAL:

* Podaj listę wszystkich uprawnień wymaganych przez aplikacje. Na przykład: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    W takim przypadku aplikacja żąda `directory.read` `directory.write` uprawnień i. Użytkownik zostanie poproszony o zgodę na te uprawnienia, jeśli nie wyraził zgodę na ich wcześniejszą aplikację. Aplikacja może również otrzymywać dodatkowe uprawnienia, które użytkownik już wyraził zgodę na aplikację. Użytkownik zostanie poproszony o zgodę na nowe uprawnienia lub uprawnienia, które nie zostały przyznane.

* `/.default`Zakres.

Jest to wbudowany zakres dla każdej aplikacji. Odnosi się do statycznej listy uprawnień skonfigurowanych podczas rejestrowania aplikacji. Jego zachowanie jest podobne do tego `resource` . Może to być przydatne podczas przeprowadzania migracji, aby zapewnić zachowanie podobnego zestawu zakresów i środowiska użytkownika.

Aby użyć `/.default` zakresu, Dołącz `/.default` do identyfikatora zasobu. Na przykład: `https://graph.microsoft.com/.default`. Jeśli zasób kończy się ukośnikiem ( `/` ), nadal powinien być dołączany `/.default` , łącznie z wiodącym ukośnikiem, co spowoduje powstanie zakresu, który ma podwójny ukośnik ( `//` ).

Więcej informacji na temat korzystania z zakresu "/.default" można znaleźć [tutaj](./v2-permissions-and-consent.md#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Obsługa różnych typów widoków WebView & przeglądarek

Biblioteka ADAL obsługuje tylko UIWebView/WKWebView dla systemu iOS oraz WebView dla macOS. MSAL dla systemu iOS obsługuje więcej opcji wyświetlania zawartości sieci Web podczas żądania kodu autoryzacji i nie jest już obsługiwana, `UIWebView` co może poprawić środowisko użytkownika i bezpieczeństwo.

Domyślnie MSAL w systemie iOS używa [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), który jest składnikiem sieci Web firmy Apple zalecaną do uwierzytelniania na urządzeniach z systemem iOS 12 lub nowszym. Zapewnia ona pojedyncze Sign-On (SSO) za pomocą udostępniania plików cookie między aplikacjami a przeglądarką Safari.

Możesz użyć innego składnika sieci Web w zależności od wymagań aplikacji i środowiska użytkownika końcowego. Więcej opcji można znaleźć w temacie [obsługiwane typy widoków sieci Web](customize-webviews.md) .

W przypadku migrowania z biblioteki ADAL do MSAL program `WKWebView` udostępnia środowisko użytkownika najbardziej podobne do biblioteki ADAL w systemach iOS i macOS. Zachęcamy do migracji do `ASWebAuthenticationSession` systemu iOS, jeśli to możliwe. W przypadku usługi macOS zachęcamy do korzystania z programu `WKWebView` .

### <a name="account-management-api-differences"></a>Różnice interfejsu API zarządzania kontami

W przypadku wywołania metod ADAL `acquireToken()` lub `acquireTokenSilent()` , otrzymujesz `ADUserInformation` obiekt zawierający listę oświadczeń z `id_token` , która reprezentuje konto, które jest uwierzytelniane. Ponadto `ADUserInformation` zwraca wartość `userId` na podstawie `upn` roszczeń. Po początkowym pozyskiwaniu tokenów ADAL oczekuje, że deweloper dostarczy `userId` wszystkie wywołania dyskretne.

Biblioteka ADAL nie udostępnia interfejsu API do pobierania znanych tożsamości użytkowników. Polega na tym, że aplikacja będzie zapisywać te konta i zarządzać nimi.

MSAL zawiera zestaw interfejsów API, aby wyświetlić listę wszystkich kont znanych MSAL bez konieczności uzyskiwania tokenu.

Podobnie jak ADAL, MSAL zwraca informacje o koncie, które przechowuje listę oświadczeń z `id_token` . Jest częścią `MSALAccount` obiektu wewnątrz `MSALResult` obiektu.

MSAL zawiera zestaw interfejsów API do usuwania kont, dzięki czemu usunięte konta są niedostępne dla aplikacji. Po usunięciu konta w późniejszym czasie wywołania do pozyskiwania tokenów będą monitować użytkownika o przejęcie interakcyjnego tokenu. Usunięcie konta dotyczy tylko aplikacji klienckiej, która ją uruchomiła, i nie powoduje usunięcia konta z innych aplikacji uruchomionych na urządzeniu ani z przeglądarki systemowej. Gwarantuje to, że użytkownik będzie nadal mieć środowisko logowania jednokrotnego na urządzeniu nawet po wylogowaniu z pojedynczej aplikacji.

Ponadto MSAL zwraca również identyfikator konta, którego można użyć do późniejszego żądania tokenu. Jednak identyfikator konta (dostępny za pomocą `identifier` właściwości w `MSALAccount` obiekcie) nie jest możliwy do odtworzenia i nie można założyć, jakiego formatu znajduje się w ani czy nie należy próbować go interpretować ani przeanalizować.

### <a name="migrating-the-account-cache"></a>Migrowanie pamięci podręcznej kont

Podczas migrowania z biblioteki ADAL aplikacje zwykle przechowują biblioteki ADAL `userId` , które nie są `identifier` wymagane przez MSAL. Jako jednorazowy krok migracji aplikacja może wysyłać zapytania do konta usługi MSAL przy użyciu identyfikatora użytkownika biblioteki ADAL z następującym interfejsem API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Ten interfejs API odczytuje pamięć podręczną MSAL i biblioteki ADAL, aby znaleźć konto według identyfikatora użytkownika ADAL (UPN).

Jeśli konto zostanie znalezione, deweloper powinien użyć konta do przejęcia tokenu dyskretnego. Pierwsze pozyskiwanie tokenów dyskretnych będzie efektywnie uaktualnić konto, a deweloper uzyska MSAL zgodny identyfikator konta w wyniku MSAL ( `identifier` ). Po wykonaniu tej `identifier` czynności należy używać tylko do wyszukiwania kont przy użyciu następującego interfejsu API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Chociaż istnieje możliwość kontynuowania korzystania z biblioteki ADAL `userId` dla wszystkich operacji w programie MSAL, ponieważ `userId` jest ona oparta na głównej nazwy użytkownika, podlega wielu ograniczeniom, które powodują złe środowisko użytkownika. Na przykład jeśli nazwa UPN ulegnie zmianie, użytkownik musi zalogować się ponownie. Zalecamy, aby wszystkie aplikacje korzystały z konta niedrukowalnego `identifier` dla wszystkich operacji.

Przeczytaj więcej na temat [migracji stanu pamięci podręcznej](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Zmiany dotyczące pozyskiwania tokenu

MSAL wprowadza pewne zmiany w wywołaniu tokenu pozyskiwania:

* Podobnie jak ADAL, `acquireTokenSilent` zawsze powoduje żądanie dyskretne.
* W przeciwieństwie do biblioteki ADAL, zawsze powoduje, że interfejs użytkownika z możliwością `acquireToken` działania użytkownika można wykonać za pomocą widoku sieci Web lub aplikacji Microsoft Authenticator. W zależności od stanu logowania jednokrotnego w programie WebView/Microsoft Authenticator użytkownik może zostać poproszony o wprowadzenie poświadczeń.
* W bibliotece ADAL, `acquireToken` przy `AD_PROMPT_AUTO` pierwszej próbie pozyskania tokenów dyskretnych i wyświetla tylko interfejs użytkownika, jeśli żądanie dyskretne nie powiedzie się. W MSAL, tę logikę można osiągnąć przez pierwsze wywołanie `acquireTokenSilent` i wywołanie tylko w `acquireToken` przypadku niepowodzenia dyskretnego pozyskiwania. Pozwala to deweloperom na dostosowanie środowiska użytkownika przed rozpoczęciem pozyskiwania tokenów interaktywnych.

### <a name="error-handling-differences"></a>Różnice w obsłudze błędów

MSAL zapewnia więcej przejrzystości między błędami, które mogą być obsługiwane przez aplikację, oraz tych, które wymagają interwencji użytkownika. Istnieje ograniczona liczba błędów, które Deweloper musi obsłużyć:

* `MSALErrorInteractionRequired`: Użytkownik musi wykonać żądanie interaktywne. Może to być spowodowane różnymi przyczynami, takimi jak wygasła sesja uwierzytelniania, zasady dostępu warunkowego zostały zmienione, token odświeżania wygasł lub został odwołany, nie ma prawidłowych tokenów w pamięci podręcznej i tak dalej.
* `MSALErrorServerDeclinedScopes`: Żądanie nie zostało w pełni ukończone i niektóre zakresy nie udzieliły dostępu. Może to być spowodowane tym, że użytkownik odrzuca zgodę na co najmniej jeden zakres.

Obsługa wszystkich innych błędów na [ `MSALError` liście](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) jest opcjonalna. Informacje w tych błędach można wykorzystać w celu ulepszenia środowiska użytkownika.

Zobacz [Obsługa wyjątków i błędów za pomocą MSAL,](msal-error-handling-ios.md) Aby uzyskać więcej informacji na temat obsługi błędów MSAL.

### <a name="broker-support"></a>Obsługa brokera

MSAL, począwszy od wersji 0.3.0, zapewnia obsługę uwierzytelniania obsługiwanego przez brokera przy użyciu aplikacji Microsoft Authenticator. Microsoft Authenticator również umożliwia obsługę scenariuszy dostępu warunkowego. Przykłady scenariuszy dostępu warunkowego obejmują zasady zgodności urządzeń, które wymagają od użytkownika zarejestrowania urządzenia w usłudze Intune lub zarejestrowanie się w usłudze AAD w celu uzyskania tokenu. I zasady dostępu warunkowego do zarządzania aplikacjami mobilnymi (MAM), które wymagają weryfikacji zgodności, zanim aplikacja będzie mogła uzyskać token.

Aby włączyć brokera dla aplikacji:

1. Zarejestruj format URI przekierowania zgodnego z brokerem dla aplikacji. Format identyfikatora URI przekierowania zgodnego z brokerem to `msauth.<app.bundle.id>://auth` . Zamień `<app.bundle.id>` na identyfikator pakietu aplikacji. Jeśli przeprowadzasz migrację z biblioteki ADAL, a Twoja aplikacja została już obsługiwana przez brokera, nie musisz wykonywać żadnych dodatkowych czynności. Poprzedni identyfikator URI przekierowania jest w pełni zgodny z MSAL, więc możesz przejść do kroku 3.

2. Dodaj schemat identyfikatora URI przekierowania aplikacji do pliku info. plist. W przypadku domyślnego identyfikatora URI przekierowania MSAL format to `msauth.<app.bundle.id>` . Na przykład:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Dodaj następujące schematy do informacji o aplikacji. plist w obszarze LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Dodaj następujący plik do pliku AppDelegate. m, aby obsługiwać wywołania zwrotne: cel-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Adres
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Firma-firma (B2B)

W bibliotece ADAL utworzysz oddzielne wystąpienia `ADAuthenticationContext` dla każdej dzierżawy, dla której aplikacja żąda tokenów. Nie jest to już wymagane w MSAL. W programie MSAL można utworzyć jedno wystąpienie `MSALPublicClientApplication` i użyć go dla każdej chmury i organizacji usługi AAD, określając inny urząd dla wywołań acquireToken i acquireTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>Usługa SSO w partnerstwie z innymi zestawami SDK

MSAL dla systemu iOS mogą uzyskać Logowanie jednokrotne za pomocą ujednoliconej pamięci podręcznej z następującymi zestawami SDK:

- Biblioteka ADAL — C 2.7. x +
- MSAL.NET dla platformy Xamarin 2.4. x +
- ADAL.NET for Xamarin 4.4. x +

Logowanie jednokrotne jest realizowane za pośrednictwem udostępniania łańcucha kluczy systemu iOS i jest dostępne tylko między aplikacjami opublikowanymi z tego samego konta dla deweloperów firmy Apple.

Logowanie jednokrotne w systemie iOS jest jedynym dyskretnym typem logowania jednokrotnego.

W systemie macOS MSAL może uzyskać Logowanie jednokrotne z innymi MSAL dla aplikacji opartych na systemach iOS i macOS oraz aplikacji opartych na języku C.

MSAL w systemie iOS obsługuje również dwa inne typy logowania jednokrotnego:

* Logowanie jednokrotne za pomocą przeglądarki sieci Web. Usługa MSAL dla systemu iOS obsługuje `ASWebAuthenticationSession` , która zapewnia Logowanie jednokrotne za pomocą plików cookie współużytkowanych przez inne aplikacje na urządzeniu i w specjalnej przeglądarce Safari.
* Logowanie jednokrotne za pośrednictwem brokera uwierzytelniania. Na urządzeniu z systemem iOS Microsoft Authenticator działa jako Broker uwierzytelniania. Może ona być zgodna z zasadami dostępu warunkowego, takimi jak wymaganie zgodnego urządzenia i zapewnia Logowanie jednokrotne dla zarejestrowanych urządzeń. Zestawy SDK MSAL, począwszy od wersji 0.3.0, domyślnie obsługują brokera.

## <a name="intune-mam-sdk"></a>Zestaw SDK MAM usługi Intune

[Zestaw SDK mam usługi Intune](/intune/app-sdk-get-started) obsługuje MSAL dla systemu iOS, począwszy od wersji [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL i ADAL w tej samej aplikacji

W wersji ADAL 2.7.0 i nowszej nie można współistnieć z MSAL w tej samej aplikacji. Głównym powodem jest ze względu na wspólny kod modułu podrzędnego. Ponieważ cel-C nie obsługuje przestrzeni nazw, w przypadku dodania do aplikacji obu platform ADAL i MSAL będą dwa wystąpienia tej samej klasy. Nie ma gwarancji, dla której pobranie zostało pobrane w czasie wykonywania. Jeśli oba zestawy SDK korzystają z tej samej wersji klasy powodującej konflikt, aplikacja może nadal funkcjonować. Jeśli jednak jest to inna wersja, w aplikacji mogą wystąpić nieoczekiwane awarie, które trudno zdiagnozować.

Uruchamianie biblioteki ADAL i MSAL w tej samej aplikacji produkcyjnej nie jest obsługiwane. Jeśli jednak tylko testujesz i migrujesz użytkowników z celu biblioteki ADAL cel-C do MSAL dla systemów iOS i macOS, możesz kontynuować korzystanie z [biblioteki ADAL cel-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Jest to jedyna wersja, która współpracuje z MSAL w tej samej aplikacji. Dla tej wersji biblioteki ADAL nie będą dostępne żadne nowe aktualizacje funkcji, dlatego powinny one być używane tylko na potrzeby migracji i testowania. Twoja aplikacja nie powinna opierać się na czasie długoterminowym ADAL i MSAL.

Współistnienie biblioteki ADAL i MSAL w tej samej aplikacji nie jest obsługiwane.
Współistnienie biblioteki ADAL i MSAL między wieloma aplikacjami jest w pełni obsługiwane.

## <a name="practical-migration-steps"></a>Praktyczne kroki migracji

### <a name="app-registration-migration"></a>Migracja rejestracji aplikacji

Nie musisz zmieniać istniejącej aplikacji usługi AAD, aby przełączyć się na MSAL i włączyć konta usługi AAD. Jeśli jednak aplikacja oparta na bibliotece ADAL nie obsługuje uwierzytelniania obsługiwanego przez brokera, należy zarejestrować nowy identyfikator URI przekierowania dla aplikacji przed przełączeniem do MSAL.

Identyfikator URI przekierowania powinien mieć następujący format: `msauth.<app.bundle.id>://auth` . Zamień `<app.bundle.id>` na identyfikator pakietu aplikacji. Określ identyfikator URI przekierowania w [Azure Portal](https://aka.ms/MobileAppReg).

W przypadku tylko systemu iOS w celu obsługi uwierzytelniania opartego na certyfikatach w aplikacji musi być zarejestrowany dodatkowy identyfikator URI przekierowania, a Azure Portal w następującym formacie: `msauth://code/<broker-redirect-uri-in-url-encoded-form>` . Na przykład `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Zalecamy, aby wszystkie aplikacje rejestrowali oba identyfikatory URI przekierowania.

Jeśli chcesz dodać pomoc techniczną do wyrażania zgody, wybierz interfejsy API i uprawnienia, dla których aplikacja została skonfigurowana, aby zażądać dostępu do rejestracji aplikacji na karcie **uprawnienia interfejsu API** .

W przypadku migrowania z biblioteki ADAL i zapewnienia obsługi kont usługi AAD i MSA należy zaktualizować istniejącą rejestrację aplikacji, aby obsługiwała obie te konta. Nie zalecamy aktualizowania istniejącej aplikacji produkcyjnej do obsługi usługi AAD i MSA od razu. Zamiast tego Utwórz inny identyfikator klienta, który obsługuje zarówno usługi AAD, jak i MSA do testowania, a po sprawdzeniu, że wszystkie scenariusze działają, zaktualizuj istniejącą aplikację.

### <a name="add-msal-to-your-app"></a>Dodawanie MSAL do aplikacji

Zestaw SDK MSAL można dodać do aplikacji przy użyciu preferowanego narzędzia do zarządzania pakietami. [Szczegółowe instrukcje](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)znajdują się tutaj.

### <a name="update-your-apps-infoplist-file"></a>Aktualizowanie pliku info. plist aplikacji

Tylko dla systemu iOS Dodaj schemat identyfikatora URI przekierowania aplikacji do pliku info. plist. W przypadku aplikacji zgodnych z brokerem ADAL powinna już istnieć. Domyślny schemat URI przekierowania MSAL będzie w formacie: `msauth.<app.bundle.id>` .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Dodaj następujące schematy do informacji o aplikacji. plist w sekcji `LSApplicationQueriesSchemes` .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aktualizowanie kodu AppDelegate

Tylko w przypadku systemu iOS Dodaj następujący plik do pliku AppDelegate. m:

Cel-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Adres

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Jeśli używasz Xcode 11**, zamiast tego należy umieścić w pliku wywołanie zwrotne MSAL `SceneDelegate` .
W przypadku obsługi UISceneDelegate i UIApplicationDelegate w celu zapewnienia zgodności ze starszymi wersjami systemu iOS należy umieścić wywołanie zwrotne w obu plikach.

Cel-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Adres

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Dzięki temu MSAL może obsługiwać odpowiedzi z brokera i składnika sieci Web.
Nie jest to konieczne w bibliotece ADAL, ponieważ metody delegatów aplikacji "swizzled" są automatycznie. Ręczne dodanie go jest mniej podatne na błędy i zapewnia aplikacji większą kontrolę.

### <a name="enable-token-caching"></a>Włącz buforowanie tokenów

Domyślnie MSAL pamięci podręcznej tokeny aplikacji w pęku kluczy systemu iOS lub macOS. 

Aby włączyć buforowanie tokenów:
1. Upewnij się, że aplikacja jest prawidłowo podpisana
2. Przejdź do pozycji Ustawienia projektu Xcode > **karcie możliwości**  >  **Włącz udostępnianie łańcucha kluczy**
3. Kliknij **+** i wprowadź następujący wpis **grupy pęku kluczy** : 3. a dla systemu iOS wprowadź `com.microsoft.adalcache` 3. b dla macOS ENTER `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Tworzenie MSALPublicClientApplication i przełączanie do jego wywołań acquireToken i acquireTokeSilent

Można utworzyć `MSALPublicClientApplication` przy użyciu następującego kodu:

Cel-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Adres

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Następnie Wywołaj interfejs API zarządzania kontami, aby sprawdzić, czy w pamięci podręcznej znajdują się jakieś konta:

Cel-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Adres

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



lub zapoznaj się ze wszystkimi kontami:

Cel-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Adres

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Jeśli zostanie znalezione konto, wywołaj `acquireTokenSilent` interfejs API MSAL:

Cel-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Adres

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)