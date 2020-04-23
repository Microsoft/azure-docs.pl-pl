---
title: Rejestracja aplikacji interfejsu API chronionego w sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i informacje potrzebne do zarejestrowania aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 214d379525f2ee534415d713aa298ec858a84c92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868844"
---
# <a name="protected-web-api-app-registration"></a>Chroniony interfejs API sieci Web: rejestracja aplikacji

W tym artykule opisano szczegóły rejestracji aplikacji dla chronionego interfejsu API sieci web.

Aby zapoznać się z typowymi krokami, aby zarejestrować aplikację, zobacz [Szybki start: Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Zaakceptowana wersja tokenu

Punkt końcowy platformy tożsamości firmy Microsoft może wystawiać tokeny w wersji 1.0 i tokeny w wersji 2.0. Aby uzyskać więcej informacji na temat tych tokenów, zobacz [Tokeny programu Access](access-tokens.md).

Zaakceptowana wersja tokenu zależy od wartości **typów obsługiwanych kont,** którą wybierzesz podczas tworzenia aplikacji.

- Jeśli wartością **obsługiwanych typów kont** są konta w dowolnym katalogu **organizacyjnym i osobistych kontach Microsoft (np Outlook.com.**
- W przeciwnym razie zaakceptowana wersja tokenu jest w wersji 1.0.

Po utworzeniu aplikacji można określić lub zmienić zaakceptowana wersja tokenu, wykonując następujące kroki:

1. W witrynie Azure portal wybierz aplikację, a następnie wybierz **pozycję Manifest**.
1. Znajdź **właściwość accessTokenAcceptedVersion** w manifeście. Wartość domyślna właściwości to 2.
1. Wartość określa usługi Azure Active Directory (Azure AD), którą wersję tokenu akceptuje internetowy interfejs API.
    - Jeśli wartość wynosi 2, internetowy interfejs API akceptuje tokeny w wersji 2.0.
    - Jeśli wartość ma **wartość null,** internetowy interfejs API akceptuje tokeny w wersji 1.0.
1. Jeśli zmieniono wersję tokenu, wybierz pozycję **Zapisz**.

> [!NOTE]
> Internetowy interfejs API określa, którą wersję tokenu akceptuje. Gdy klient żąda tokenu dla interfejsu API sieci web z punktu końcowego platformy tożsamości firmy Microsoft (wersja 2.0), klient otrzymuje token wskazujący, którą wersję tokenu akceptuje internetowy interfejs API.

## <a name="no-redirect-uri"></a>Brak identyfikatora URI przekierowania

Interfejsy API sieci Web nie muszą rejestrować identyfikatora URI przekierowania, ponieważ żaden użytkownik nie jest zalogowany.

## <a name="exposed-api"></a>Odsłonięty interfejs API

Inne ustawienia specyficzne dla interfejsów API sieci web są narażony interfejs API i zakresy narażone.

### <a name="application-id-uri-and-scopes"></a>Identyfikator URI i zakresy identyfikatora aplikacji

Zakresy zwykle mają `resourceURI/scopeName`formularz . W przypadku programu Microsoft Graph zakresy mają skróty. Na przykład `User.Read` jest skrótem do `https://graph.microsoft.com/user.read`.

Podczas rejestracji aplikacji należy zdefiniować następujące parametry:

- Identyfikator URI zasobu
- Jeden lub więcej zakresów
- Co najmniej jedna rola aplikacji

Domyślnie portal rejestracji aplikacji zaleca użycie identyfikatora `api://{clientId}`URI zasobu . Ten identyfikator URI jest unikatowy, ale nie czytelny dla człowieka. Jeśli zmienisz identyfikator URI, upewnij się, że nowa wartość jest unikatowa.

Do aplikacji klienckich zakresy są wyświetlane jako *delegowane uprawnienia,* a role aplikacji są wyświetlane jako *uprawnienia aplikacji* dla internetowego interfejsu API.

Zakresy są również wyświetlane w oknie zgody, które jest prezentowane użytkownikom aplikacji. Dlatego musisz podać odpowiednie ciągi, które opisują zakres:

- Jak widać przez użytkownika.
- Jak widać przez administratora dzierżawy, który może udzielić zgody administratora.

### <a name="exposing-delegated-permissions-scopes"></a>Udostępnianie uprawnień delegowanych (zakresów)

1. Wybierz **pozycję Uwidaczniaj interfejs API** w rejestracji aplikacji.
1. Wybierz polecenie **Dodaj zakres**.
1. Jeśli zostanie wyświetlony monit, zaakceptuj proponowany`api://{clientId}`identyfikator URI identyfikatora aplikacji ( ) wybierając **pozycję Zapisz i Kontynuuj**.
1. Określ następujące wartości:
    - Wybierz **nazwę zakresu** i wprowadź **access_as_user**.
    - Wybierz **pozycję Kto może wyrazić zgodę** i upewnij się, że **wybrano administratorów i użytkowników.**
    - Wybierz **nazwę wyświetlaną zgody administratora** i wprowadź **access TodoListService jako użytkownika**.
    - Wybierz **opis zgody administratora** i wprowadź **dostęp do internetowego interfejsu API TodoListService jako użytkownika**.
    - Wybierz **nazwę wyświetlaną zgody użytkownika** i wprowadź access **TodoListService jako użytkownika**.
    - Wybierz **opis zgody użytkownika** i wprowadź dostęp do **internetowego interfejsu API TodoListService jako użytkownika**.
    - Zachowaj wartość **Stan** ustawioną na **Włączono**.
 1. Wybierz **pozycję Dodaj zakres**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Jeśli internetowy interfejs API jest wywoływany przez aplikację demona

W tej sekcji dowiesz się, jak zarejestrować chroniony interfejs API sieci Web, aby aplikacje demonów mogły bezpiecznie go wywołać.

- Deklarujesz i udostępniasz tylko *uprawnienia aplikacji,* ponieważ aplikacje demonów nie wchodzą w interakcje z użytkownikami. Delegowane uprawnienia nie miałoby sensu.
- Administratorzy dzierżawy mogą wymagać usługi Azure AD do wystawiania tokenów interfejsu API sieci web tylko do aplikacji, które zostały zarejestrowane, aby uzyskać dostęp do jednego z uprawnień aplikacji interfejsu API.

#### <a name="exposing-application-permissions-app-roles"></a>Udostępnianie uprawnień aplikacji (role aplikacji)

Aby udostępnić uprawnienia aplikacji, należy edytować manifest.

1. W rejestracji aplikacji dla aplikacji **wybierz**manifest .
1. Aby edytować manifest, `appRoles` znajdź ustawienie i dodaj role aplikacji. Definicje ról znajdują się w poniższym przykładowym bloku JSON.
1. `allowedMemberTypes` Pozostaw `"Application"` tylko na ustaw.
1. Upewnij `id` się, że jest to unikatowy identyfikator GUID.
1. Upewnij `displayName` się `value` i nie zawiera spacji.
1. Zapisz manifest.

Poniższy przykład przedstawia `appRoles`zawartość , gdzie `id` wartość może być dowolny unikatowy identyfikator GUID.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Zapewnienie, że usługa Azure AD wystawia tokeny dla internetowego interfejsu API tylko dla klientów

Internetowy interfejs API sprawdza rolę aplikacji. Ta rola jest sposób dewelopera oprogramowania, aby udostępnić uprawnienia aplikacji. Można również skonfigurować usługę Azure AD do wystawiania tokenów interfejsu API tylko do aplikacji, które administrator dzierżawy zatwierdza dla dostępu do interfejsu API.

Aby dodać zwiększone bezpieczeństwo:

1. Przejdź do strony Przegląd aplikacji, **aby** uzyskać rejestrację aplikacji.
1. W obszarze **Aplikacja zarządzana w katalogu lokalnym**wybierz łącze z nazwą aplikacji. Etykieta dla tego zaznaczenia może być obcięty. Na przykład możesz zobaczyć **aplikację zarządzana w ...**

   > [!NOTE]
   >
   > Po wybraniu tego łącza przejdź do strony **Przegląd aplikacji przedsiębiorstwa.** Ta strona jest skojarzona z jednostką usługi dla aplikacji w dzierżawie, w której została utworzona. Możesz przejść do strony rejestracji aplikacji za pomocą przycisku Wstecz przeglądarki.

1. Wybierz stronę **Właściwości** w sekcji **Zarządzanie** na stronach aplikacji Przedsiębiorstwo.
1. Jeśli chcesz, aby usługa Azure AD zezwalała na dostęp do internetowego **Yes**interfejsu API tylko od niektórych klientów, ustaw **przypisanie użytkownika wymagane?**

   > [!IMPORTANT]
   >
   > Jeśli **ustawisz przypisanie użytkownika wymagane na** **Tak,** usługa Azure AD sprawdza przypisania roli aplikacji klienta, gdy żąda tokenu dostępu do interfejsu API sieci Web. Jeśli klient nie jest przypisany do żadnych ról aplikacji, usługa Azure AD zwróci komunikat o błędzie "invalid_client: AADSTS501051: \<Nazwa\> aplikacji nie jest przypisana do roli dla internetowego \<interfejsu API\>".
   >
   > Jeśli **zachowasz przypisanie użytkownika wymagane?** ustawiona na **Nie**, usługa Azure AD nie będzie sprawdzać przypisań ról aplikacji, gdy klient żąda tokenu dostępu dla internetowego interfejsu API. Każdy klient demona, co oznacza, że każdy klient przy użyciu przepływu poświadczeń klienta, można uzyskać token dostępu dla interfejsu API tylko określając jego odbiorców. Każda aplikacja może uzyskać dostęp do interfejsu API bez konieczności żądania uprawnień dla niego.
   >
   > Ale jak wyjaśniono w poprzedniej sekcji, interfejs API sieci web zawsze można sprawdzić, czy aplikacja ma właściwą rolę, która jest autoryzowana przez administratora dzierżawy. Interfejs API wykonuje tę weryfikację, sprawdzając, czy token dostępu ma oświadczenie ról i czy wartość dla tego oświadczenia jest poprawna. W poprzednim przykładzie JSON `access_as_application`wartość jest .

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-protected-web-api-app-configuration.md)
