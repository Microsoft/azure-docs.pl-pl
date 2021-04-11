---
title: Osadź Azure Active Directory B2C interfejs użytkownika w aplikacji przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak osadzić interfejs użytkownika Azure Active Directory B2C w aplikacji przy użyciu zasad niestandardowych
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dc4450fb1e21211b43bc178d94cf4bdfe6da58e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256790"
---
# <a name="embedded-sign-in-experience"></a>Wbudowane środowisko logowania

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby uprościć logowanie, można uniknąć przekierowywania użytkowników do osobnej strony logowania lub wyłączania okna podręcznego. Przy użyciu wbudowanego elementu ramki `<iframe>` można osadzić interfejs użytkownika logowania Azure AD B2C bezpośrednio w aplikacji sieci Web.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Osadzona Rejestracja aplikacji sieci Web

Element ramki wbudowanej `<iframe>` służy do osadzania dokumentu na stronie sieci Web HTML5. Można użyć elementu iframe, aby osadzić interfejs użytkownika logowania Azure AD B2C bezpośrednio w aplikacji sieci Web, jak pokazano w następującym przykładzie:

![Logowanie przy użyciu funkcji umieszczania bloków DIV](media/embedded-login/login-hovering.png)

W przypadku używania elementu IFRAME należy wziąć pod uwagę następujące kwestie:

- Osadzona Rejestracja jest obsługiwana tylko na kontach lokalnych. Większość dostawców tożsamości społecznościowych (np. Google i Facebook) uniemożliwia renderowanie stron logowania w ramkach wbudowanych.
- Ponieważ pliki cookie sesji Azure AD B2C w elemencie iframe są uznawane za pliki cookie innych firm, niektóre przeglądarki (na przykład Safari lub Chrome w trybie incognito) blokują lub wyczyścili te pliki cookie, co może powodować niepożądane środowisko użytkownika. Aby uniknąć tego problemu, upewnij się, że nazwa domeny aplikacji i domena Azure AD B2C są *takie same*. Aby użyć tego samego źródła, [Włącz domeny niestandardowe](custom-domain.md) dla Azure AD B2C dzierżawy, a następnie skonfiguruj aplikację sieci Web przy użyciu tego samego źródła. Na przykład aplikacja hostowana na " https://app.contoso.com " ma takie samo źródło jak Azure AD B2C uruchomiona na " https://login.contoso.com ".

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki opisane w temacie [wprowadzenie do zasad niestandardowych w Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).
* [Włącz domeny niestandardowe](custom-domain.md) dla zasad.

## <a name="configure-your-policy"></a>Konfigurowanie zasad

Aby umożliwić osadzenie Azure AD B2C interfejsu użytkownika w elemencie iframe, `Content-Security-Policy` `X-Frame-Options` należy uwzględnić w Azure AD B2C nagłówki odpowiedzi HTTP zasady zabezpieczeń zawartości i ramki. Te nagłówki umożliwiają uruchamianie Azure AD B2C interfejsu użytkownika w ramach nazwy domeny aplikacji.

Dodaj element **JourneyFraming** wewnątrz elementu [RelyingParty](relyingparty.md) .  Element **UserJourneyBehaviors** musi być zgodny z **DefaultUserJourney**. Element **UserJourneyBehaviors** powinien wyglądać podobnie do tego przykładu:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

Atrybut **sources** zawiera identyfikator URI aplikacji sieci Web. Dodaj spację między identyfikatorami URI. Każdy identyfikator URI musi spełniać następujące wymagania:

- Identyfikator URI musi być zaufany i własnością Twojej aplikacji.
- Identyfikator URI musi używać schematu https.  
- Należy określić pełny identyfikator URI aplikacji sieci Web. Symbole wieloznaczne nie są obsługiwane.

Ponadto zaleca się również, aby w elemencie iframe zablokować własną nazwę domeny, przez ustawienie nagłówków Content-Security-Policy i X-Frame-Options odpowiednio na stronach aplikacji. Pozwoli to uniknąć problemów z bezpieczeństwem w przypadku starszych przeglądarek związanych z zagnieżdżonym osadzaniem elementów iframe.

## <a name="adjust-policy-user-interface"></a>Dostosuj interfejs użytkownika zasad

Dzięki Azure AD B2C [dostosowywania interfejsu użytkownika](customize-ui.md)masz prawie pełną kontrolę nad zawartością HTML i CSS prezentowaną użytkownikom. Postępuj zgodnie z instrukcjami dotyczącymi dostosowywania strony HTML przy użyciu definicji zawartości. Aby dopasować interfejs użytkownika Azure AD B2C do rozmiaru iframe, należy podać czystą stronę HTML bez tła i dodatkowych spacji.  

Poniższy kod CSS ukrywa elementy Azure AD B2C HTML i dostosowuje rozmiar panelu do wypełnienia elementu iframe.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

W niektórych przypadkach możesz chcieć wysłać powiadomienie do swojej aplikacji, która Azure AD B2C strona jest aktualnie wyświetlana. Na przykład, gdy użytkownik wybierze opcję rejestracji, możesz chcieć, aby aplikacja mogła reagować, ukrywając linki do logowania za pomocą konta społecznościowego lub dostosowując rozmiar elementu iframe.

Aby powiadomić aplikację o bieżącej Azure AD B2C, [Włącz zasady dla języka JavaScript](./javascript-and-page-layout.md), a następnie użyj komunikatów post HTML5. Poniższy kod JavaScript wysyła wiadomość post do aplikacji przy użyciu `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Konfigurowanie aplikacji sieci Web

Gdy użytkownik wybierze przycisk Zaloguj, [aplikacja sieci Web](code-samples.md#web-apps-and-apis) generuje żądanie autoryzacji umożliwiające użytkownikowi Azure AD B2C środowiska logowania. Po zakończeniu logowania Azure AD B2C zwraca token identyfikatora lub kod autoryzacji do skonfigurowanego identyfikatora URI przekierowania w aplikacji.

Aby można było obsługiwać osadzoną nazwę logowania, właściwość iframe **src** wskazuje kontroler logowania, na przykład `/account/SignUpSignIn` , który generuje żądanie autoryzacji i przekierowuje użytkownika do zasad Azure AD B2C.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Po odebraniu i sprawdzeniu poprawności tokenu identyfikatora przez aplikację przepływ autoryzacji zostanie ukończony, a aplikacja rozpoznaje użytkownika i ufa go. Ponieważ przepływ autoryzacji odbywa się wewnątrz elementu iframe, należy ponownie załadować stronę główną. Po ponownym załadowaniu strony przycisk logowania zostanie zmieniony na "Wyloguj się", a nazwa użytkownika jest wyświetlana w interfejsie użytkownika.  

Poniżej przedstawiono przykład pokazujący, jak identyfikator URI przekierowania logowania może odświeżyć stronę główną:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Dodawanie logowania przy użyciu kont społecznościowych do aplikacji sieci Web

Dostawcy tożsamości społecznościowej blokują strony logowania przed renderowaniem w ramkach wbudowanych. Możesz użyć osobnych zasad dla kont społecznościowych lub użyć jednej zasady w przypadku logowania i kont lokalnych i społecznościowych. Następnie można użyć `domain_hint` parametru ciągu zapytania. Parametr wskazówki domeny pobiera użytkownika bezpośrednio do strony logowania dostawcy tożsamości społecznościowej.

W aplikacji Dodaj przyciski Zaloguj się przy użyciu konta społecznościowego. Gdy użytkownik kliknie jeden z przycisków konta społecznościowego, formant musi zmienić nazwę zasad lub ustawić parametr wskazówki domeny.

<!-- TBD: add a diagram -->

Identyfikator URI przekierowania może być tym samym identyfikatorem URI przekierowania używanym przez element IFRAME. Możesz pominąć ładowanie strony.

## <a name="configure-a-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

W przypadku aplikacji jednostronicowej potrzebna jest również druga strona HTML logowania, która ładuje do elementu iframe. Ta strona logowania zawiera kod biblioteki uwierzytelniania, który generuje kod autoryzacji i zwraca token.

Gdy aplikacja jednostronicowa wymaga tokenu dostępu, użyj kodu JavaScript, aby uzyskać token dostępu z elementu IFRAME i obiektu, który go zawiera.

> [!NOTE]
> Uruchamianie MSAL 2,0 w elemencie iframe nie jest obecnie obsługiwane.

Poniższy kod jest przykładem, który jest uruchamiany na stronie głównej i wywołuje kod JavaScript elementu iframe:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami:

- [Dostosowywanie interfejsu użytkownika](customize-ui.md)
- Odwołanie do elementu [RelyingParty](relyingparty.md)
- [Włączanie zasad dla języka JavaScript](./javascript-and-page-layout.md)
- [Przykłady kodu](code-samples.md)

::: zone-end
