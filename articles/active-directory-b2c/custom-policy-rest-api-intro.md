---
title: Wymiana oświadczeń interfejsu API REST w zasadach niestandardowych B2C
titleSuffix: Azure AD B2C
description: Wprowadzenie do tworzenia Azure AD B2Cych podróży użytkowników, które współdziałają z usługami RESTful Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89594190"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integruj wymianę oświadczeń interfejsu API REST w zasadach niestandardowych Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Platforma obsługi tożsamości, która jest zależna od Azure Active Directory B2C (Azure AD B2C), może być zintegrowana z interfejsami API RESTful w trakcie podróży użytkownika. W tym artykule przedstawiono sposób tworzenia podróży użytkowników, które współdziałają z usługą RESTful przy użyciu [profilu technicznego RESTful](restful-technical-profile.md).

Za pomocą Azure AD B2C można dodać własną logikę biznesową do podróży użytkownika, wywołując własną usługę RESTful. Platforma obsługi tożsamości może wysyłać i odbierać dane z usługi RESTful w celu wymiany oświadczeń. Możesz na przykład:

- **Sprawdź poprawność danych wejściowych użytkownika**. Można na przykład sprawdzić, czy w bazie danych klienta istnieje adres e-mail podany przez użytkownika, a jeśli nie, występuje błąd.
- **Przetwarzanie oświadczeń**. Jeśli użytkownik wpisze swoją pierwszą nazwę na wszystkich małych i wielkich liter, interfejs API REST może sformatować nazwę tylko przy użyciu pierwszej litery i zwrócić go do Azure AD B2C.
- **Wzbogacaj dane użytkowników przez dalsze integrację z firmowymi aplikacjami biznesowymi**. Usługa RESTful może odbierać adres e-mail użytkownika, wysyłać zapytania do bazy danych klienta i zwracać numer lojalnościowy użytkownika w celu Azure AD B2C. Następnie zwracane oświadczenia mogą być przechowywane na koncie usługi Azure AD użytkownika, oceniane w następnych krokach aranżacji lub zawarte w tokenie dostępu.
- **Uruchom niestandardową logikę biznesową**. Możesz wysyłać powiadomienia wypychane, aktualizować korporacyjne bazy danych, uruchamiać proces migracji użytkowników, zarządzać uprawnieniami, bazami danych inspekcji i wykonywać inne przepływy pracy.

![Diagram wymiany oświadczeń usługi RESTful Service](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Jeśli usługa RESTful jest powolna lub nie ma odpowiedzi na Azure AD B2C, limit czasu wynosi 30 sekund, a liczba ponownych prób wynosi 2 razy (oznacza to, że w sumie są 3 próby). Nie można obecnie konfigurować ustawień limitu czasu i liczby ponownych prób.

## <a name="calling-a-restful-service"></a>Wywoływanie usługi RESTful

Interakcja obejmuje wymianę informacji między oświadczeniami interfejsu API REST a Azure AD B2C. Integrację z usługami RESTful można zaprojektować w następujący sposób:

- **Profil techniczny weryfikacji**. Wywołanie usługi RESTful odbywa się w ramach [walidacji profilu technicznego](validation-technical-profile.md) z określonym [własnym profilem technicznym](self-asserted-technical-profile.md)lub [kontrolki wyświetlania](display-control-verification.md) [kontrolki wyświetlania](display-controls.md). Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed przejściem użytkownika do przodu. Profil techniczny weryfikacji umożliwia:

  - Wyślij oświadczenia do interfejsu API REST.
  - Sprawdź poprawność oświadczeń i Zgłoś niestandardowe komunikaty o błędach, które są wyświetlane użytkownikowi.
  - Wyślij z powrotem oświadczenia z interfejsu API REST do kolejnych kroków aranżacji.

- **Wymiana oświadczeń**. Bezpośrednią wymianę oświadczeń można skonfigurować, wywołując profil techniczny interfejsu API REST bezpośrednio z kroku aranżacji [użytkownika](userjourneys.md). Ta definicja jest ograniczona do:

  - Wyślij oświadczenia do interfejsu API REST.
  - Sprawdź poprawność oświadczeń i Zgłoś niestandardowe komunikaty o błędach, które są zwracane do aplikacji.
  - Wyślij z powrotem oświadczenia z interfejsu API REST do kolejnych kroków aranżacji.

Wywołanie interfejsu API REST można dodać w dowolnym kroku w podróży użytkownika zdefiniowanej przez zasady niestandardowe. Można na przykład wywołać interfejs API REST:

- Podczas logowania, tuż przed Azure AD B2C weryfikują poświadczenia.
- Natychmiast po zalogowaniu się.
- Przed Azure AD B2C tworzy nowe konto w katalogu.
- Po Azure AD B2C zostanie utworzone nowe konto w katalogu.
- Zanim Azure AD B2C wystawia token dostępu.

![Sprawdzanie poprawności kolekcji profilów technicznych](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Wysyłanie danych

W [profilu technicznym RESTful](restful-technical-profile.md) `InputClaims` element zawiera listę oświadczeń do wysłania do usługi RESTful. Możesz zmapować nazwę oświadczenia na nazwę zdefiniowaną w usłudze RESTful, ustawić wartość domyślną i użyć [resolverów oświadczeń](claim-resolver-overview.md).

Można skonfigurować sposób, w jaki oświadczenia wejściowe są wysyłane do dostawcy oświadczeń RESTful za pomocą atrybutu SendClaimsIn. Możliwe wartości są następujące:

- **Treść** wysłana w treści żądania HTTP post w formacie JSON.
- **Formularz** wysłany w treści żądania HTTP post w formacie wartości klucza "&".
- **Nagłówek**, wysłany w nagłówku żądania HTTP GET.
- **Kolekcja QueryString** wysłana w ciągu zapytania żądania HTTP GET.

W przypadku skonfigurowania opcji **treści** profil techniczny interfejsu API REST umożliwia wysyłanie złożonego ładunku JSON do punktu końcowego. Aby uzyskać więcej informacji, zobacz [wysyłanie ładunku JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Pobieranie danych

`OutputClaims`Element [profilu technicznego RESTful](restful-technical-profile.md) zawiera listę oświadczeń zwracanych przez interfejs API REST. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w interfejsie API REST. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości interfejsu API REST, o ile atrybut DefaultValue jest ustawiony.

Oświadczenia wynikowe analizowane przez dostawcę oświadczeń RESTful zawsze oczekują na przeanalizowanie płaskiej reakcji treści JSON, na przykład:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Oświadczenia wyjściowe powinny wyglądać następująco:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Aby przeanalizować zagnieżdżoną odpowiedź treści JSON, ustaw dla metadanych ResolveJsonPathsInJsonTokens wartość true. W obszarze żądania danych wyjściowych ustaw wartość PartnerClaimType na element Path JSON, który ma zostać wyprowadzony.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Oświadczenia wyjściowe powinny wyglądać następująco:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy chronić punkt końcowy interfejsu API REST, aby tylko uwierzytelnieni klienci mogli z nią komunikować. Interfejs API REST musi używać punktu końcowego HTTPS. Ustaw metadane Uwierzytelnianiatype na jedną z następujących metod uwierzytelniania:

- **Certyfikat klienta** ogranicza dostęp przy użyciu uwierzytelniania za pomocą certyfikatu klienta. Tylko usługi, które mają odpowiednie certyfikaty, mogą uzyskać dostęp do interfejsu API. Certyfikat klienta jest przechowywany w kluczu zasad Azure AD B2C. Dowiedz się więcej na temat [zabezpieczania usługi RESTful przy użyciu certyfikatów klienta](secure-rest-api.md#https-client-certificate-authentication).
- **Podstawowa** zabezpiecza interfejs API REST przy użyciu podstawowego uwierzytelniania HTTP. Tylko zweryfikowani użytkownicy, w tym Azure AD B2C, mogą uzyskiwać dostęp do interfejsu API. Nazwa użytkownika i hasło są przechowywane w kluczach zasad Azure AD B2C. Dowiedz się [, jak zabezpieczyć usługi RESTful przy użyciu uwierzytelniania podstawowego protokołu HTTP](secure-rest-api.md#http-basic-authentication).
- **Osoba przewożąca** ogranicza dostęp przy użyciu tokenu dostępu OAuth2 klienta. Token dostępu jest przechowywany w kluczu zasad Azure AD B2C. Dowiedz się więcej na temat [zabezpieczania usługi RESTful przy użyciu tokenu okaziciela](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Platforma interfejsu API REST
Interfejs API REST może być oparty na dowolnej platformie i zapisany w dowolnym języku programowania, o ile jest zabezpieczony i może wysyłać i odbierać oświadczenia zgodnie z opisem w [profilu technicznym usługi RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Lokalizowanie interfejsu API REST
W profilu technicznym RESTful może być konieczne wysłanie języka/ustawień regionalnych bieżącej sesji i w razie potrzeby podniesienia zlokalizowanego komunikatu o błędzie. Za pomocą programu [rozpoznawania oświadczeń](claim-resolver-overview.md)można wysłać oświadczenie kontekstowe, takie jak język użytkownika. Poniższy przykład przedstawia profil techniczny RESTful pokazujący ten scenariusz.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Obsługa komunikatów o błędach

Interfejs API REST może wymagać zwrócenia komunikatu o błędzie, takiego jak "nie znaleziono użytkownika w systemie CRM". Jeśli wystąpi błąd, interfejs API REST powinien zwrócić komunikat o błędzie HTTP 409 (kod stanu odpowiedzi konfliktu). Aby uzyskać więcej informacji, zobacz [profil techniczny RESTful](restful-technical-profile.md#returning-validation-error-message).

Można to osiągnąć tylko przez wywołanie profilu technicznego interfejsu API REST z poziomu profilu technicznego weryfikacji. Dzięki temu użytkownik może skorygować dane na stronie i ponownie uruchomić weryfikację przy przesyłaniu strony.

Odpowiedź HTTP 409 jest wymagana, aby zapobiec przetwarzaniu wszelkich kolejnych weryfikacji profilów technicznych w ramach tego kroku aranżacji.

Jeśli odwołujesz się do profilu technicznego interfejsu API REST bezpośrednio z podróży użytkownika, użytkownik zostanie przekierowany z powrotem do aplikacji jednostki uzależnionej z odpowiednim komunikatem o błędzie.

## <a name="publishing-your-rest-api"></a>Publikowanie interfejsu API REST

Żądanie do usługi interfejsu API REST pochodzi z serwerów Azure AD B2C. Usługa interfejsu API REST musi być opublikowana w publicznie dostępnym punkcie końcowym HTTPS. Wywołania interfejsu API REST zostaną dostarczone z adresu IP centrum danych platformy Azure.

Zaprojektuj usługę interfejsu API REST i jej podstawowe składniki (takie jak baza danych i system plików) tak, aby były wysoce dostępne.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z przykładami dotyczącymi korzystania z profilu technicznego RESTful:

- [Wskazówki: integruje wymianę oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika podczas sprawdzania poprawności danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md)
- [Przewodnik: Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpieczanie usług interfejsu API REST](secure-rest-api.md)
- [Dokumentacja: profil techniczny RESTful](restful-technical-profile.md)
