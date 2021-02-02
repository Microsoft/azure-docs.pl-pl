---
title: Problemy z logowaniem do aplikacji skonfigurowanych przy użyciu logowania jednokrotnego opartego na protokole SAML
description: Wskazówki dotyczące określonych błędów podczas logowania do aplikacji skonfigurowanej do federacyjnego logowania jednokrotnego opartego na protokole SAML przy użyciu Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: feeed07021e31dfee44fa9372927e45b53131d1e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254057"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problemy z logowaniem do aplikacji skonfigurowanych przy użyciu logowania jednokrotnego opartego na protokole SAML
Aby rozwiązać problemy związane z logowaniem poniżej, zalecamy wykonanie poniższych czynności w celu lepszego zdiagnozowania i zautomatyzowania kroków rozwiązania:

- Zainstaluj [rozszerzenie przeglądarki My Apps Secure](my-apps-deployment-plan.md), aby ułatwić usłudze Azure Active Directory (Azure AD) zapewnienie lepszej diagnostyki i rozwiązań podczas korzystania ze środowiska testowego w witrynie Azure Portal.
- Odtwórz błąd przy użyciu środowiska testowego na stronie Konfiguracja aplikacji w witrynie Azure Portal. Dowiedz się więcej na temat [debugowania aplikacji logowania jednokrotnego opartego na protokole SAML](./debug-saml-sso-issues.md)

Jeśli używasz [środowiska testowego](./debug-saml-sso-issues.md) w Azure Portal z rozszerzeniem moje aplikacje bezpieczne przeglądarki, nie musisz ręcznie wykonać poniższe kroki, aby otworzyć stronę konfiguracyjną Logowanie jednokrotne oparte na protokole SAML.

Aby otworzyć stronę konfiguracji logowania jednokrotnego opartego na protokole SAML:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.
1.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
1.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
1.  Wybierz pozycję **Aplikacje dla przedsiębiorstw** z menu nawigacji usługi Azure Active Directory po lewej stronie.
1.  Wybierz pozycję **Wszystkie aplikacje**, aby wyświetlić listę wszystkich aplikacji.

    Jeśli wymagana aplikacja nie jest widoczna w tym miejscu, użyj kontrolki **filtru** w górnej części **listy Wszystkie aplikacje**, a następnie ustaw opcję **Pokaż** na wartość **Wszystkie aplikacje**.

1.  Wybierz aplikację, dla której chcesz skonfigurować logowanie jednokrotne.
1. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne z menu nawigacji po lewej stronie aplikacji.
1. Wybierz pozycję Logowanie jednokrotne oparte na protokole SAML.

## <a name="application-not-found-in-directory"></a>Nie znaleziono aplikacji w katalogu
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Możliwa przyczyna**

`Issuer`Atrybut wysłany z aplikacji do usługi Azure AD w ŻĄDANIU SAML nie jest zgodny z wartością identyfikatora, która jest skonfigurowana dla aplikacji w usłudze Azure AD.

**Rozwiązanie**

Upewnij się, że `Issuer` atrybut w ŻĄDANIU SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD.

Na stronie Konfiguracja logowania jednokrotnego opartego na protokole SAML w sekcji **Podstawowa konfiguracja SAML** Sprawdź, czy wartość w polu tekstowym identyfikatora jest zgodna z wartością identyfikatora wyświetlaną w błędzie.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adres odpowiedzi nie jest zgodny z adresami odpowiedzi skonfigurowanymi dla aplikacji
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Możliwa przyczyna**

`AssertionConsumerServiceURL`Wartość w ŻĄDANIU SAML nie jest zgodna z wartością adresu URL odpowiedzi lub wzorcem skonfigurowanym w usłudze Azure AD. `AssertionConsumerServiceURL`Wartość w ŻĄDANIU SAML jest adresem URL widocznym w błędzie.

**Rozwiązanie**

Upewnij się, że `AssertionConsumerServiceURL` wartość w ŻĄDANIU SAML jest zgodna z wartością adresu URL odpowiedzi skonfigurowaną w usłudze Azure AD. 

Sprawdź lub zaktualizuj wartość w polu tekstowym adres URL odpowiedzi, aby dopasować `AssertionConsumerServiceURL` wartość w ŻĄDANIU SAML.   

Po zaktualizowaniu wartości adresu URL odpowiedzi w usłudze Azure AD, która jest zgodna z wartością wysyłaną przez aplikację w żądaniu SAML, powinno być możliwe zalogowanie się do aplikacji.

## <a name="user-not-assigned-a-role"></a>Użytkownik nie ma przypisanej roli
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Możliwa przyczyna**

Użytkownikowi nie udzielono dostępu do aplikacji w usłudze Azure AD.

**Rozwiązanie**

Aby bezpośrednio przypisać co najmniej jednego użytkownika do aplikacji, zobacz [Szybki Start: przypisywanie użytkowników do aplikacji](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>To nie jest prawidłowe żądanie SAML
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Możliwa przyczyna**

Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Typowe problemy to:
- Brak wymaganych pól w żądaniu SAML
- Zakodowana metoda żądania SAML

**Rozwiązanie**

1. Przechwyć żądanie SAML. Postępuj zgodnie z samouczkiem jak debugować Logowanie jednokrotne oparte na protokole [SAML do aplikacji w usłudze Azure AD](./debug-saml-sso-issues.md) , aby dowiedzieć się, jak przechwycić żądanie SAML.
1. Skontaktuj się z dostawcą aplikacji i udostępnij następujące informacje:
    - Żądanie SAML
    - [Wymagania dotyczące protokołu SAML logowania jednokrotnego usługi Azure AD](../develop/single-sign-on-saml-protocol.md)

Dostawca aplikacji powinien sprawdzić, czy obsługują one implementację protokołu SAML usługi Azure AD w celu logowania jednokrotnego.

## <a name="misconfigured-application"></a>Błędna konfiguracja aplikacji
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Możliwa przyczyna**

`Issuer`Atrybut wysłany z aplikacji do usługi Azure AD w ŻĄDANIU SAML nie jest zgodny z wartością identyfikatora skonfigurowaną dla aplikacji w usłudze Azure AD.

**Rozwiązanie**

Upewnij się, że `Issuer` atrybut w ŻĄDANIU SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD. 

Sprawdź, czy wartość w polu tekstowym identyfikatora jest zgodna z wartością identyfikatora wyświetlaną w błędzie.

## <a name="certificate-or-key-not-configured"></a>Nie skonfigurowano certyfikatu lub klucza
`Error AADSTS50003: No signing key configured.`

**Możliwa przyczyna**

Obiekt aplikacji jest uszkodzony, a usługa Azure AD nie rozpoznaje certyfikatu skonfigurowanego dla aplikacji.

**Rozwiązanie**

Aby usunąć i utworzyć nowy certyfikat, wykonaj następujące czynności:
1. Na ekranie Konfiguracja logowania jednokrotnego opartego na protokole SAML wybierz pozycję **Utwórz nowy certyfikat** w sekcji **certyfikat podpisywania SAML** .
1. Wybierz datę wygaśnięcia, a następnie kliknij przycisk **Zapisz**.
1. Zaznacz pole wyboru **Utwórz nowy certyfikat jako aktywny** , aby zastąpić aktywny certyfikat. Następnie kliknij przycisk **Zapisz** w górnej części okienka i zaakceptuj, aby aktywować certyfikat przerzucania.
1. W sekcji **certyfikat podpisywania SAML** kliknij przycisk **Usuń** , aby usunąć **nieużywany** certyfikat.

## <a name="saml-request-not-present-in-the-request"></a>Żądanie SAML nie występuje w żądaniu
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Możliwa przyczyna**

Usługa Azure AD nie mogła zidentyfikować żądania SAML w parametrach adresu URL w żądaniu HTTP. Może się tak zdarzyć, jeśli aplikacja nie korzysta z powiązania przekierowania HTTP podczas wysyłania żądania SAML do usługi Azure AD.

**Rozwiązanie**

Aplikacja musi wysłać żądanie SAML zakodowane w nagłówku lokalizacji przy użyciu powiązania przekierowywania HTTP. Aby dowiedzieć się więcej o sposobie implementacji tego rozwiązania, zapoznaj się z sekcją powiązania przekierowania HTTP w [dokumencie specyfikacji protokołu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Usługa Azure AD wysyła token do nieprawidłowego punktu końcowego
**Możliwa przyczyna**

W trakcie logowania jednokrotnego, jeśli żądanie logowania nie zawiera jawnego adresu URL odpowiedzi (adresu URL usługi Konsumenckej potwierdzenia), usługa Azure AD wybierze dowolny ze skonfigurowanych adresów URL odpowiedzi dla tej aplikacji. Nawet jeśli dla aplikacji jest skonfigurowany jawny adres URL odpowiedzi, użytkownik może mieć możliwość przekierowania https://127.0.0.1:444 . 

Podczas dodawania tej aplikacji jako aplikacji spoza galerii usługa Azure Active Directory utworzyła ten adres URL odpowiedzi jako wartość domyślną. To działanie zostało zmienione i usługa Azure Active Directory nie dodaje już domyślnie tego adresu URL. 

**Rozwiązanie**

Usuń nieużywane adresy URL odpowiedzi skonfigurowane dla aplikacji.

Na stronie Konfiguracja logowania jednokrotnego opartego na protokole SAML w sekcji **adres URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** Usuń nieużywane lub domyślne adresy URL odpowiedzi utworzone przez system. Na przykład `https://127.0.0.1:444/applications/default.aspx`.


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>Metoda uwierzytelniania, za pomocą której użytkownik uwierzytelniony przy użyciu usługi nie jest zgodna z żądaną metodą uwierzytelniania
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**Możliwa przyczyna**

`RequestedAuthnContext`Znajduje się w ŻĄDANIU SAML. Oznacza to, że aplikacja oczekuje `AuthnContext` określonego przez `AuthnContextClassRef` . Jednak użytkownik został już uwierzytelniony przed uzyskaniem dostępu do aplikacji, a `AuthnContext` (metoda uwierzytelniania) użyta w przypadku tego poprzedniego uwierzytelniania różni się od żądania. Na przykład użytkownik federacyjny ma dostęp do aplikacji Moje aplikacje i WIA. `AuthnContextClassRef`Będzie `urn:federation:authentication:windows` . Usługa AAD nie będzie wykonywać nowego żądania uwierzytelnienia, będzie używać kontekstu uwierzytelniania, który został przekazano przez dostawcy tożsamości (ADFS lub inną usługę federacyjną w tym przypadku). W związku z tym wystąpi niezgodność, jeśli żądania aplikacji są inne niż `urn:federation:authentication:windows` . Inny scenariusz jest używany, gdy użyto wieloskładnikowego: `'X509, MultiFactor` .

**Rozwiązanie**


`RequestedAuthnContext` jest wartością opcjonalną. Następnie, jeśli to możliwe, poproszenie aplikacji o jej usunięcie.

Inną opcją jest upewnienie się, że `RequestedAuthnContext` będzie to możliwe. Zostanie to zrobione przez zażądanie nowego uwierzytelniania. Dzięki temu, gdy żądanie SAML zostanie przetworzone, zostanie wykonane nowe uwierzytelnianie i `AuthnContext` zostanie uznane. Aby zażądać nowego uwierzytelniania, żądanie SAML ma większość wartości `forceAuthn="true"` . 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem podczas dostosowywania oświadczeń SAML wysyłanych do aplikacji
Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Błędy związane ze niepoprawnie skonfigurowanymi aplikacjami
Sprawdź, czy konfiguracje w portalu pasują do zawartości w aplikacji. W tym celu Porównaj identyfikatory klienta/aplikacji, adresy URL odpowiedzi, klucze tajne klienta/klucza i identyfikator URI aplikacji.

Porównaj zasób, do którego żądasz dostępu, w kodzie ze skonfigurowanymi uprawnieniami na karcie **wymagane zasoby** , aby upewnić się, że zażądano tylko skonfigurowanych zasobów.

## <a name="next-steps"></a>Następne kroki
- [Seria szybkiego startu w zarządzaniu aplikacjami](add-application-portal-assign-users.md)
- [Jak debugować Logowanie jednokrotne oparte na protokole SAML do aplikacji w usłudze Azure AD](./debug-saml-sso-issues.md)
- [Wymagania dotyczące protokołu SAML logowania jednokrotnego usługi Azure AD](../develop/single-sign-on-saml-protocol.md)