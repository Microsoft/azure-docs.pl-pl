---
title: Jak dostosować Azure Active Directory poświadczenia do zweryfikowania (wersja zapoznawcza)
description: W tym artykule pokazano, jak utworzyć własne niestandardowe poświadczenia do zweryfikowania
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: c830f9c7edb252508824b3a92bd31b6fad31395d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170069"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Jak dostosować zweryfikowane poświadczenia (wersja zapoznawcza)

Zweryfikowane poświadczenia składają się z dwóch składników, reguł i plików wyświetlanych. Plik reguł określa, co użytkownik musi podać przed otrzymaniem zweryfikowanego poświadczenia. Plik wyświetlania kontroluje znakowanie poświadczeń i stylów oświadczeń. W tym przewodniku wyjaśniono sposób modyfikacji obu plików w celu spełnienia wymagań organizacji. 

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Plik reguł: wymagania użytkownika

Plik reguł to prosty plik JSON, który opisuje ważne właściwości zweryfikowanych poświadczeń. W szczególności opisuje sposób, w jaki oświadczenia są używane do wypełniania zweryfikowanego poświadczenia.

Obecnie istnieją trzy typy danych wejściowych, które są dostępne do skonfigurowania w pliku reguł. Te typy są używane przez zweryfikowaną usługę wydawania poświadczeń w celu wstawiania oświadczeń do zweryfikowanego poświadczenia i zaświadczania do tych informacji. Poniżej przedstawiono trzy typy z wyjaśnieniami.

- Identyfikator tokenu
- Zweryfikowanie poświadczeń za pośrednictwem prezentacji możliwej do zweryfikowania.
- Self-Attested oświadczenia

**Identyfikator tokenu:** Przykładowa aplikacja i samouczek używają tokenu identyfikatora. Gdy ta opcja jest skonfigurowana, należy podać identyfikator URI konfiguracji połączenia Open ID i uwzględnić oświadczenia, które powinny być zawarte w VC. Użytkownik zostanie poproszony o zalogowanie się w aplikacji uwierzytelniającej w celu spełnienia tego wymagania i dodania skojarzonych oświadczeń ze swojego konta. 

**Zweryfikowane poświadczenia:** Wynik końcowy przepływu wystawiania ma na celu utworzenie zweryfikowanego poświadczenia, ale może również polecić użytkownikowi zaprezentowanie poświadczenia, aby je wystawić. Plik reguł może przyjmować określone oświadczenia z podanego zweryfikowanego poświadczenia i obejmować te oświadczenia w nowo wystawionym poświadczeniach zweryfikowanych z Twojej organizacji. 

**Zaświadczone przez siebie oświadczenia:** Po wybraniu tej opcji użytkownik będzie mógł bezpośrednio wpisywać informacje do uwierzytelniania. W tej chwili ciągi są jedynymi obsługiwanymi danymi wejściowymi dla oświadczeń zagwarantowanych przez siebie. 

![szczegółowy widok karty poświadczenia możliwej do zweryfikowania](media/credential-design/issuance-doc.png) 

**Oświadczenia statyczne:** Ponadto możemy zadeklarować oświadczenie statyczne w pliku reguł, ale dane wejściowe nie pochodzą od użytkownika. Wystawca definiuje statycznego żądania w pliku reguł i będzie wyglądać podobnie jak każde inne zgłoszenie do zweryfikowanego poświadczenia. Po prostu Dodaj element credentialSubject po elemencie VC. Wpisz i Zadeklaruj atrybut oraz oświadczenie. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Typ danych wejściowych: token identyfikatora

Aby uzyskać token identyfikatora jako dane wejściowe, plik reguł musi skonfigurować dobrze znany punkt końcowy systemu tożsamości zgodnego z OIDC. W tym systemie należy zarejestrować aplikację z poprawnymi informacjami z [przykładów komunikacji usługi wystawcy](issuer-openid.md). Ponadto client_id należy umieścić w pliku reguł, a także podać parametr zakresu w prawidłowych zakresach. Na przykład Azure Active Directory wymaga zakresu wiadomości e-mail, jeśli chcesz zwrócić w tokenie identyfikator.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Właściwość | Opis |
| -------- | ----------- |
| `attestations.idTokens` | Tablica tożsamości OpenID Connect Connect Providers, które są obsługiwane na potrzeby pozyskiwania informacji o użytkowniku. |
| `...mapping` | Obiekt, który opisuje sposób, w jaki oświadczenia w każdym tokenie identyfikatora są mapowane na atrybuty w rezultacie zweryfikowane poświadczenia. |
| `...mapping.{attribute-name}` | Atrybut, który powinien zostać wypełniony w uzyskanym zweryfikowanym poświadczeniu. |
| `...mapping.{attribute-name}.claim` | W tokenach identyfikatorów, których wartość powinna być używana do wypełniania atrybutu. |
| `...configuration` | Lokalizacja dokumentu konfiguracji dostawcy tożsamości. Ten adres URL musi być zgodny z [metadanymi dostawcy tożsamości w standardzie OpenID Connect Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). Dokument konfiguracji musi zawierać `issuer` `authorization_endpoint` pola,, `token_endpoint` i `jwks_uri` . |
| `...client_id` | Identyfikator klienta uzyskany podczas procesu rejestracji klienta. |
| `...scope` | Rozdzielana spacjami lista zakresów, które dostawcy tożsamości muszą być w stanie zwracać poprawne oświadczenia w tokenie ID. |
| `...redirect_uri` | Zawsze należy używać wartości `vcclient://openid/` . |
| `validityInterval` | Czas trwania (w sekundach) reprezentujący okres istnienia zweryfikowanych poświadczeń. Po upływie tego czasu poświadczenia zweryfikowane nie będą już prawidłowe. Pominięcie tej wartości oznacza, że każde zweryfikowane poświadczenie pozostanie ważne do momentu, gdy zostanie jawnie odwołane. |
| `vc.type` | Tablica ciągów wskazujących schematy, które są spełniane przez zweryfikowane poświadczenia. Więcej szczegółów znajduje się w sekcji poniżej. |

### <a name="vctype-choose-credential-types"></a>VC. Type: Wybierz typy poświadczeń 

Wszystkie poświadczenia podlegające weryfikacji muszą deklarować swój "typ" w pliku reguł. Typ poświadczenia odróżnia zweryfikowane poświadczenia od poświadczeń wystawionych przez inne organizacje i zapewnia współdziałanie między wystawcami i weryfikatorami. Aby wskazać typ poświadczeń, należy podać jeden lub więcej typów poświadczeń, które spełniają poświadczenia. Każdy typ jest reprezentowany przez unikatowy ciąg — często jest używany identyfikator URI, aby zapewnić globalną unikatowość. Identyfikator URI nie musi być adresowany; jest ona traktowana jako ciąg. 

Przykładowo poświadczenie dyplomu wydane przez firmę Contoso University może zadeklarować następujące typy:

| Typ | Przeznaczenie |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Deklaruje, że dyplomy wystawione przez firmę Contoso University zawierają atrybuty zdefiniowane przez obiekt Schema. org `EducationaCredential` . |
| `https://schemas.ed.gov/universityDiploma2020` | Deklaruje, że dyplomy wydane przez firmę Contoso University zawierają atrybuty zdefiniowane przez Stany Zjednoczone Departamentu Edukacji. |
| `https://schemas.contoso.edu/diploma2020` | Deklaruje, że dyplomy wystawione przez firmę Contoso University zawierają atrybuty zdefiniowane przez firmę Contoso University. |

Deklarując wszystkie trzy typy, dyplomy uniwersytetów firmy Contoso mogą służyć do zaspokojenia różnych żądań od weryfikatorów. Bank może zażądać zestawu `EducationCredential` s od użytkownika, a dyplom może służyć do spełnienia żądania. Ale firma Contoso University studentów Association może zażądać poświadczeń typu `https://schemas.contoso.edu/diploma2020` , a dyplom będzie również spełniał żądanie.

Aby zapewnić współdziałanie poświadczeń, zalecamy ścisłe współdziałanie z powiązanymi organizacjami, aby zdefiniować typy poświadczeń, schematy i identyfikatory URI do użycia w branży. Wiele branżowych organów zapewnia wskazówki dotyczące struktury oficjalnych dokumentów, które można zmienić w celu zdefiniowania zawartości zweryfikowanych poświadczeń. Należy również ściśle współpracować z weryfikatorami Twoich poświadczeń, aby zrozumieć, jak zamierzają żądać i korzystać z zweryfikowanych poświadczeń.

## <a name="input-type-verifiable-credential"></a>Typ danych wejściowych: poświadczenia możliwe do zweryfikowania

>[!NOTE]
>Pliki reguł, które pytają o poświadczenia do zweryfikowania, nie używają formatu wymiany prezentacji do żądania poświadczeń. Ta wartość zostanie zaktualizowana, gdy usługa wystawiania obsługuje standardową manifest poświadczeń. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Właściwość | Opis |
| -------- | ----------- |
| `attestations.presentations` | Tablica zweryfikowanych poświadczeń, które są żądane jako dane wejściowe. |
| `...mapping` | Obiekt, który opisuje sposób, w jaki oświadczenia w każdym z prezentowanych poświadczeń zweryfikowanych są mapowane na atrybuty w trakcie zweryfikowanego poświadczenia. |
| `...mapping.{attribute-name}` | Atrybut, który powinien zostać wypełniony w uzyskanym zweryfikowanym poświadczeniu. |
| `...mapping.{attribute-name}.claim` | W ramach poświadczenia zweryfikowanego, którego wartość powinna zostać użyta do wypełnienia atrybutu. |
| `...mapping.{attribute-name}.indexed` | Można włączyć tylko jeden z poświadczeń do zweryfikowania w celu zapisania do odwołania. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym odwoływania poświadczeń](how-to-issuer-revoke.md) . |
| `credentialType` | Wartość CredentialType poświadczenia do zweryfikowania, do którego użytkownik prosi użytkownika. |
| `contracts` | Identyfikator URI kontraktu w portalu usługi poświadczeń do zweryfikowania. |
| `issuers.iss` | Wystawca zażądał zweryfikowania poświadczeń użytkownika. |
| `validityInterval` | Czas trwania (w sekundach) reprezentujący okres istnienia zweryfikowanych poświadczeń. Po upływie tego czasu poświadczenia zweryfikowane nie będą już prawidłowe. Pominięcie tej wartości oznacza, że każde zweryfikowane poświadczenie pozostanie ważne do momentu, gdy zostanie jawnie odwołane. |
| `vc.type` | Tablica ciągów wskazujących schematy, które są spełniane przez zweryfikowane poświadczenia. |


## <a name="input-type-self-attested-claims"></a>Typ danych wejściowych: Self-Attested oświadczeń

W trakcie przepływu wystawiania użytkownik może zostać poproszony o wprowadzenie niektórych informacji z zaświadczeniem własnym. Obecnie jedynym typem danych wejściowych jest "String". 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Właściwość | Opis |
| -------- | ----------- |
| `attestations.selfIssued` | Tablica oświadczeń wystawionych przez siebie, które wymagają wprowadzenia danych przez użytkownika. |
| `...mapping` | Obiekt, który opisuje sposób, w jaki wystawione przez siebie oświadczenia są mapowane na atrybuty w rezultacie zweryfikowane poświadczenia. |
| `...mapping.alias` | Atrybut, który powinien zostać wypełniony w uzyskanym zweryfikowanym poświadczeniu. |
| `...mapping.alias.claim` | W ramach poświadczenia zweryfikowanego, którego wartość powinna zostać użyta do wypełnienia atrybutu. |
| `validityInterval` | Czas trwania (w sekundach) reprezentujący okres istnienia zweryfikowanych poświadczeń. Po upływie tego czasu poświadczenia zweryfikowane nie będą już prawidłowe. Pominięcie tej wartości oznacza, że każde zweryfikowane poświadczenie pozostanie ważne do momentu, gdy zostanie jawnie odwołane. |
| `vc.type` | Tablica ciągów wskazujących schematy, które są spełniane przez zweryfikowane poświadczenia. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Plik wyświetlania: poświadczenia podlegające weryfikacji w Microsoft Authenticator

Zweryfikowane poświadczenia oferują ograniczony zestaw opcji, których można użyć do odzwierciedlenia marki. Ten artykuł zawiera instrukcje dotyczące sposobu dostosowywania poświadczeń oraz najlepsze rozwiązania dotyczące projektowania poświadczeń, które są dobrze dostępne dla użytkowników.

Poświadczenia podlegające weryfikacji dla użytkowników są wyświetlane jako karty w Microsoft Authenticator. Jako administrator możesz wybrać kolor karty, ikonę i ciągi tekstowe, aby pasowały do marki organizacji.

![Dokumentacja wystawiania](media/credential-design/detailed-view.png) 

Karty zawierają również dostosowywalne pola, za pomocą których użytkownicy mogą znać przeznaczenie karty, zawarte w nich atrybuty i inne.

## <a name="create-a-credential-display-file"></a>Tworzenie pliku wyświetlanego poświadczeń

Podobnie jak w przypadku pliku reguł, plik wyświetlany to prosty plik JSON, który opisuje, w jaki sposób zawartość zweryfikowanych poświadczeń powinna być wyświetlana w aplikacji Microsoft Authenticator. 

>[!NOTE]
> W tej chwili ten model wyświetlania jest używany tylko przez Microsoft Authenticator.

Plik wyświetlany ma poniższą strukturę.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Właściwość | Opis |
| -------- | ----------- |
| `locale` | Język poświadczenia do zweryfikowania. Zarezerwowane do użytku w przyszłości. | 
| `card.title` | Wyświetla typ poświadczenia dla użytkownika. Zalecana maksymalna długość 25 znaków. | 
| `card.issuedBy` | Wyświetla nazwę organizacji wystawiającej dla użytkownika. Zalecana maksymalna długość wynosząca 40 znaków. |
| `card.backgroundColor` | Określa kolor tła karty w formacie szesnastkowym. Gradient delikatny zostanie zastosowany do wszystkich kart. |
| `card.textColor` | Określa kolor tekstu karty w formacie szesnastkowym. Zalecane do używania czerni lub bieli. |
| `card.logo` | Logo, które jest wyświetlane na karcie. Podany adres URL musi być publicznie adresowany. Zalecana maksymalna wysokość 36 pikseli i maksymalna szerokość 100 pikseli niezależnie od rozmiaru telefonu. Zalecamy PNG z przezroczystym tłem. | 
| `card.description` | Dodatkowy tekst wyświetlany obok każdej karty. Może być używany do dowolnego celu. Zalecana maksymalna długość wynosząca 100 znaków. |
| `consent.title` | Dodatkowy tekst wyświetlany podczas wystawiania karty. Służy do przekazywania szczegółowych informacji o procesie wystawiania. Zalecana długość wynosząca 100 znaków. |
| `consent.instructions` | Dodatkowy tekst wyświetlany podczas wystawiania karty. Służy do przekazywania szczegółowych informacji o procesie wystawiania. Zalecana długość wynosząca 100 znaków. |
| `claims` | Umożliwia podanie etykiet dla atrybutów zawartych w każdym poświadczeniu. |
| `claims.{attribute}` | Wskazuje atrybut poświadczenia, do którego ma zastosowanie etykieta. |
| `claims.{attribute}.type` | Wskazuje typ atrybutu. Obecnie obsługujemy tylko ciąg "String". |
| `claims.{attribute}.label` | Wartość, która powinna być używana jako etykieta dla atrybutu, który będzie wyświetlany w uwierzytelnianiu. Może się to różnić od etykiety, która została użyta w pliku reguł. Zalecana maksymalna długość wynosząca 40 znaków. |

>[!note]
  >Jeśli w pliku reguł zostanie uwzględniona wartość, a następnie pominięta w pliku wyświetlanym, istnieją dwa różne typy środowisk. W systemie iOS nie będzie ono wyświetlane w sekcji szczegółów pokazanej na powyższym obrazie, a w systemie Android zostanie wyświetlone zgłoszenie.  

## <a name="next-steps"></a>Następne kroki

Teraz można lepiej zrozumieć, jaki jest możliwy do zweryfikowania projekt poświadczeń oraz jak można utworzyć własne, aby zaspokoić Twoje potrzeby.

- [Przykłady komunikacji usługi wystawcy](issuer-openid.md)
