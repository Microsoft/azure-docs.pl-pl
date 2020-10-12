---
title: Rejestracja aplikacji chronionego interfejsu API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i informacje potrzebne do zarejestrowania aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c1fab15cade2ce23e053bc73028e6420692c3d8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518278"
---
# <a name="protected-web-api-app-registration"></a>Chroniony internetowy interfejs API: Rejestracja aplikacji

W tym artykule opisano informacje o rejestracji aplikacji dla chronionego internetowego interfejsu API.

Aby zapoznać się z typowymi krokami rejestracji aplikacji, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Zaakceptowana wersja tokenu

Punkt końcowy platformy tożsamości firmy Microsoft może wystawiać tokeny 1.0 i tokeny v 2.0. Aby uzyskać więcej informacji na temat tych tokenów, zobacz [tokeny dostępu](access-tokens.md).

Wersja tokenu, która może być akceptowana przez interfejs API, zależy od **obsługiwanego wyboru typów kont** podczas tworzenia rejestracji aplikacji internetowego interfejsu api w Azure Portal.

- Jeśli wartość **obsługiwanych typów kont** to **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)**, zaakceptowana wersja tokenu musi mieć wartość v 2.0.
- W przeciwnym razie zaakceptowany token może być w wersji 1.0.

Po utworzeniu aplikacji możesz określić lub zmienić zaakceptowaną wersję tokenu, wykonując następujące czynności:

1. W Azure Portal wybierz aplikację, a następnie wybierz pozycję **manifest**.
1. Znajdź właściwość **accessTokenAcceptedVersion** w manifeście.
1. Wartość określa Azure Active Directory (Azure AD), która wersja tokenu akceptuje interfejs API sieci Web.
    - Jeśli wartość jest równa 2, internetowy interfejs API akceptuje tokeny v 2.0.
    - Jeśli wartość jest **równa null**, internetowy interfejs API akceptuje tokeny v 1.0.
1. Jeśli została zmieniona wersja tokenu, wybierz pozycję **Zapisz**.

> [!NOTE]
> Internetowy interfejs API określa, która wersja tokenu akceptuje. Gdy klient zażąda tokenu dla internetowego interfejsu API z punktu końcowego Microsoft Identity platform (v 2.0), klient otrzymuje token wskazujący, która wersja tokenu jest akceptowana przez internetowy interfejs API.

## <a name="no-redirect-uri"></a>Brak identyfikatora URI przekierowania

Interfejsy API sieci Web nie muszą rejestrować identyfikatora URI przekierowania, ponieważ żaden użytkownik nie jest zalogowany interaktywnie.

## <a name="exposed-api"></a>Uwidoczniony interfejs API

Inne ustawienia specyficzne dla interfejsów API sieci Web to uwidoczniony interfejs API oraz uwidocznione zakresy lub role aplikacji.

### <a name="application-id-uri-and-scopes"></a>Identyfikator URI i zakresy identyfikatora aplikacji

Zakresy mają zwykle postać `resourceURI/scopeName` . W przypadku Microsoft Graph zakresy mają skróty. Na przykład, `User.Read` jest skrótem dla `https://graph.microsoft.com/user.read` .

Podczas rejestracji aplikacji należy zdefiniować następujące parametry:

- Identyfikator URI zasobu
- Co najmniej jeden zakres
- Co najmniej jedna rola aplikacji

Domyślnie Portal rejestracji aplikacji zaleca użycie identyfikatora URI zasobu `api://{clientId}` . Ten identyfikator URI jest unikatowy, ale nie jest możliwy do odczytania przez człowieka. Jeśli zmienisz identyfikator URI, upewnij się, że nowa wartość jest unikatowa. Portal rejestracji aplikacji zapewni użycie [skonfigurowanej domeny wydawcy](howto-configure-publisher-domain.md)

W przypadku aplikacji klienckich zakresy są wyświetlane jako uprawnienia *delegowane* i role aplikacji są wyświetlane jako *uprawnienia aplikacji* dla internetowego interfejsu API.

Zakresy są również wyświetlane w oknie zgody prezentowanym użytkownikom aplikacji. W związku z tym należy podać odpowiednie ciągi opisujące zakres:

- Widziane przez użytkownika.
- Jak widać Administrator dzierżawy, który może udzielić zgody administratora.

Role aplikacji nie mogą być wysyłane przez użytkownika (ponieważ są używane przez aplikację, która wywołuje internetowy interfejs API w imieniu siebie). Administrator dzierżawy będzie musiał wyrazić zgodę na aplikacje klienckie interfejsu API sieci Web, który uwidacznia role aplikacji. Aby uzyskać szczegółowe informacje, zobacz [zgoda administratora](v2-admin-consent.md)

### <a name="exposing-delegated-permissions-scopes"></a>Uwidacznianie delegowanych uprawnień (zakresy)

1. Wybierz opcję **Uwidacznianie interfejsu API** w rejestracji aplikacji.
1. Wybierz polecenie **Dodaj zakres**.
1. Jeśli zostanie wyświetlony monit, zaakceptuj proponowany identyfikator URI aplikacji ( `api://{clientId}` ), wybierając pozycję **Zapisz i Kontynuuj**.
1. Określ następujące wartości:
    - Wybierz pozycję **Nazwa zakresu** i wprowadź **access_as_user**.
    - Wybierz **osoby, które mogą wyrazić zgodę** i upewnij się, że wybrano **administratorów i użytkowników** .
    - Wybierz opcję **Nazwa wyświetlana zgody administratora** i wprowadź **TodoListService dostępu jako użytkownika**.
    - Wybierz pozycję **Opis zgody administratora** i wprowadź **dostęp do internetowego interfejsu API TodoListService jako użytkownika**.
    - Wybierz pozycję **Nazwa wyświetlana zgody użytkownika** i wprowadź **TodoListService dostępu jako użytkownika**.
    - Wybierz pozycję **Opis zgody użytkownika** i wprowadź **dostęp do internetowego interfejsu API TodoListService jako użytkownika**.
    - Zachowaj wartość **stanu** ustawioną na **włączone**.
 1. Wybierz pozycję **Dodaj zakres**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Jeśli internetowy interfejs API jest wywoływany przez aplikację demona

W tej sekcji dowiesz się, jak zarejestrować chroniony internetowy interfejs API, dzięki czemu aplikacje demona mogą bezpiecznie je wywoływać.

- Należy zadeklarować i uwidocznić tylko *uprawnienia aplikacji* , ponieważ aplikacje demona nie współpracują z użytkownikami. Delegowane uprawnienia nie będą mieć sensu.
- Administratorzy dzierżawy mogą wymagać, aby usługa Azure AD wydawała tokeny interfejsu API sieci Web tylko aplikacjom, które zostały zarejestrowane w celu uzyskania dostępu do jednego z uprawnień aplikacji interfejsu API.

#### <a name="exposing-application-permissions-app-roles"></a>Udostępnianie uprawnień aplikacji (ról aplikacji)

Aby uwidocznić uprawnienia aplikacji, należy edytować manifest.

1. W obszarze Rejestracja aplikacji dla aplikacji wybierz pozycję **manifest**.
1. Aby edytować manifest, Znajdź `appRoles` ustawienie i Dodaj role aplikacji. Definicje ról są podane w następującym przykładowym bloku JSON.
1. Pozostaw `allowedMemberTypes` ustawiony `"Application"` tylko wartość.
1. Upewnij `id` się, że jest unikatowym identyfikatorem GUID.
1. Upewnij się `displayName` , że `value` nie zawierają spacji.
1. Zapisz manifest.

Poniższy przykład pokazuje zawartość `appRoles` , gdzie wartość `id` może być dowolnym UNIKATOWYm identyfikatorem GUID.

```json
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Upewnienie się, że tokeny wystawia usługi Azure AD dla internetowego interfejsu API tylko dla dozwolonych klientów

Interfejs API sieci Web sprawdza rolę aplikacji. Ta rola jest programistą oprogramowania do udostępniania uprawnień aplikacji. Usługę Azure AD można także skonfigurować do wystawiania tokenów interfejsu API tylko dla aplikacji, które Administrator dzierżawy zatwierdził dostęp do interfejsu API.

Aby dodać zwiększone zabezpieczenia:

1. Przejdź do strony **Przegląd** aplikacji dotyczącej rejestracji aplikacji.
1. W obszarze **aplikacja zarządzana w katalogu lokalnym**wybierz łącze z nazwą aplikacji. Etykieta dla tego zaznaczenia może zostać obcięta. Na przykład **aplikacja zarządzana może być widoczna w...**

   > [!NOTE]
   >
   > Po wybraniu tego linku przejdź do strony **przeglądu aplikacji przedsiębiorstwa** . Ta strona jest skojarzona z jednostką usługi dla Twojej aplikacji w dzierżawie, w której została utworzona. Możesz przejść do strony rejestracji aplikacji, korzystając z przycisku Wstecz w przeglądarce.

1. Wybierz stronę **Właściwości** w sekcji **Zarządzanie** na stronach aplikacji dla przedsiębiorstw.
1. Jeśli chcesz, aby usługa Azure AD zezwalała na dostęp do internetowego interfejsu API tylko z niektórych klientów, należy ustawić **wymagane przypisanie użytkownika?** na **wartość tak**.

   > [!IMPORTANT]
   >
   > Jeśli ustawisz **przypisanie użytkownika jako wymagane?** na **tak**, usługa Azure AD sprawdzi przypisania roli aplikacji klienta, gdy zażąda tokenu dostępu do interfejsu API sieci Web. Jeśli klient nie jest przypisany do żadnych ról aplikacji, usługa Azure AD zwróci komunikat o błędzie "invalid_client: AADSTS501051: aplikacja \<application name\> nie jest przypisana do roli dla \<web API\> ".
   >
   > Jeśli jest **wymagane przypisanie użytkownika?** wartość **nie**, usługa Azure AD nie będzie sprawdzać przypisań roli aplikacji, gdy klient zażąda tokenu dostępu dla internetowego interfejsu API. Każdy klient demona, który oznacza dowolnego klienta używający przepływu poświadczeń klienta, może uzyskać token dostępu dla interfejsu API tylko przez określenie jego odbiorców. Każda aplikacja może uzyskać dostęp do interfejsu API bez konieczności żądania uprawnień do niego.
   >
   > Jednak zgodnie z opisem w poprzedniej sekcji internetowy interfejs API może zawsze sprawdzić, czy aplikacja ma odpowiednią rolę, która jest autoryzowana przez administratora dzierżawy. Interfejs API wykonuje tę weryfikację, sprawdzając, czy token dostępu ma rolę i czy wartość tego żądania jest poprawna. W poprzednim przykładzie JSON wartość jest `access_as_application` .

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-protected-web-api-app-configuration.md)
