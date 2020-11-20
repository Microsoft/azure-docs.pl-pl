---
title: Korzystanie z AppAuth w aplikacji systemu iOS
titleSuffix: Azure AD B2C
description: Jak utworzyć aplikację dla systemu iOS, która korzysta z AppAuth z Azure Active Directory B2C, aby zarządzać tożsamościami użytkowników i uwierzytelniać użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6064bd2c62922abea44508b8bf6cdfa3e7ecbc92
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953308"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Logowanie przy użyciu aplikacji systemu iOS

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Korzystanie z protokołu Open standard oferuje deweloperom większą możliwość wyboru podczas wybierania biblioteki do integracji z naszymi usługami. Firma Microsoft udostępniła ten przewodnik i inne osoby, które ułatwiają deweloperom pisanie aplikacji łączących się z platformą tożsamości firmy The. Większość bibliotek implementujących [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) jest w stanie połączyć się z platformą tożsamości firmy Microsoft.

> [!WARNING]
> Firma Microsoft nie udostępnia poprawek do bibliotek innych firm i nie przeprowadzono przeglądu tych bibliotek. Ten przykład korzysta z biblioteki innej firmy o nazwie AppAuth, która została przetestowana pod kątem zgodności w podstawowych scenariuszach z Azure AD B2C. Problemy i żądania funkcji powinny być kierowane do projektu open-source biblioteki. Aby uzyskać więcej informacji, zobacz [ten artykuł](../active-directory/develop/reference-v2-libraries.md).
>
>

Jeśli dopiero zaczynasz korzystać z programu OAuth2 lub OpenID Connect Connect, większość tej przykładowej konfiguracji może nie mieć sensu. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie zarejestruj aplikację w dzierżawie Azure AD B2C. Dzięki temu usługa Azure AD musi uzyskać bezpieczne komunikowanie się z Twoją aplikacją.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Zapisz także niestandardowy identyfikator URI przekierowania do użycia w późniejszym kroku. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Tworzenie przepływów użytkownika
W Azure AD B2C każde środowisko użytkownika jest definiowane przez [przepływ użytkownika](user-flow-overview.md). Ta aplikacja zawiera jedno środowisko tożsamości: połączone logowanie i logowanie. Podczas tworzenia przepływu użytkownika upewnij się, że:

* W obszarze **atrybuty rejestracji** wybierz **nazwę wyświetlaną** atrybutu.  Możesz również wybrać inne atrybuty.
* W obszarze **oświadczenia aplikacji** wybierz **nazwę wyświetlaną** oświadczeń i **Identyfikator obiektu użytkownika**. Możesz również wybrać inne oświadczenia.
* Skopiuj **nazwę** każdego przepływu użytkownika po jego utworzeniu. Nazwa przepływu użytkownika jest poprzedzona prefiksem `b2c_1_` podczas zapisywania przepływu użytkownika.  Nazwa przepływu użytkownika będzie potrzebna później.

Po utworzeniu przepływów użytkowników możesz utworzyć aplikację.

## <a name="download-the-sample-code"></a>Pobieranie przykładowego kodu
Udostępnimy przykład roboczy, który używa usługi AppAuth z usługą Azure AD B2C [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Możesz pobrać kod i uruchomić go. Aby korzystać z własnej dzierżawy Azure AD B2C, postępuj zgodnie z instrukcjami podanymi w [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Ten przykład został utworzony przez następujące instrukcje Readme dotyczące [projektu AppAuth systemu iOS w witrynie GitHub](https://github.com/openid/AppAuth-iOS). Aby uzyskać więcej informacji o tym, jak działa przykład i biblioteka, odwołuje się do pliku Readme AppAuth w witrynie GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji do użycia Azure AD B2C z AppAuth

> [!NOTE]
> AppAuth obsługuje system iOS 7 lub nowszy.  Jednak w celu zapewnienia obsługi logowań społecznościowych w usłudze Google wymagane jest SFSafariViewController, które wymagają systemu iOS 9 lub nowszego.
>

### <a name="configuration"></a>Konfigurowanie

Komunikację z Azure AD B2C można skonfigurować, określając punkt końcowy autoryzacji i identyfikatory URI punktu końcowego tokenu.  Aby wygenerować te identyfikatory URI, potrzebne są następujące informacje:
* Identyfikator dzierżawy (na przykład contoso.onmicrosoft.com)
* Nazwa przepływu użytkownika (na przykład B2C \_ 1 \_ )

Identyfikator URI punktu końcowego tokenu można wygenerować, zastępując identyfikator dzierżawy \_ i nazwę zasady \_ następującym adresem URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Identyfikator URI punktu końcowego autoryzacji można wygenerować, zastępując identyfikator dzierżawy \_ i nazwę zasady \_ następującym adresem URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Uruchom następujący kod, aby utworzyć obiekt AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autoryzowanie

Po skonfigurowaniu lub pobraniu konfiguracji usługi autoryzacji można utworzyć żądanie autoryzacji. Aby można było utworzyć żądanie, potrzebne są następujące informacje:

* Identyfikator klienta (Identyfikator aplikacji), który został zarejestrowany wcześniej. Na przykład `00000000-0000-0000-0000-000000000000`.
* Niestandardowy identyfikator URI przekierowania, który został wcześniej zarejestrowany. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Oba elementy powinny być zapisane podczas [rejestrowania aplikacji](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Aby skonfigurować aplikację do obsługi przekierowania do identyfikatora URI przy użyciu niestandardowego schematu, należy zaktualizować listę "schematy adresów URL" w info. pList:
* Otwórz info. pList.
* Umieść kursor nad wierszem, taki jak "kod typu systemu operacyjnego" i kliknij \+ symbol.
* Zmień nazwę nowego wiersza "typy adresów URL".
* Kliknij strzałkę po lewej stronie "typy adresów URL", aby otworzyć drzewo.
* Kliknij strzałkę po lewej stronie elementu "Item 0", aby otworzyć drzewo.
* Zmień nazwę pierwszego elementu pod pozycją od 0 do "schematy adresów URL".
* Kliknij strzałkę po lewej stronie "schematy adresów URL", aby otworzyć drzewo.
* W kolumnie "value" istnieje puste pole po lewej stronie elementu "Item 0" poniżej "schematy adresów URL".  Ustaw wartość na unikatowy schemat aplikacji.  Wartość musi być zgodna ze schematem używanym w redirectURL podczas tworzenia obiektu OIDAuthorizationRequest.  W przykładzie używany jest schemat "com. onmicrosoft. fabrikamb2c. ExampleApp".

Zapoznaj się z [przewodnikiem AppAuth](https://openid.github.io/AppAuth-iOS/) dotyczącym kończenia pozostałej części procesu. Jeśli musisz szybko rozpocząć pracę z działającą aplikacją, zapoznaj [się z przykładem](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Postępuj zgodnie z instrukcjami w [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) , aby wprowadzić własną konfigurację Azure AD B2C.

Zawsze jest otwarta opinia i sugestii. Jeśli masz jakiekolwiek problemy z tym artykułem lub zawarto zalecenia dotyczące poprawy tej zawartości, będziemy wdzięczni za opinię w dolnej części strony. W przypadku żądań funkcji Dodaj je do usługi [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).