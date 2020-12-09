---
title: Zabezpieczanie dostępu i danych
description: Bezpieczny dostęp do wejść, danych wyjściowych, wyzwalaczy opartych na żądaniach, historii uruchamiania, zadań zarządzania i dostępu do innych zasobów w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/08/2020
ms.openlocfilehash: cdaa054559be9db52eeef6f3aaa0f86ccf84206f
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922937"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpieczanie dostępu i danych w Azure Logic Apps

Azure Logic Apps korzysta z [usługi Azure Storage](../storage/index.yml) , aby przechowywać i automatycznie [szyfrować dane](../security/fundamentals/encryption-atrest.md)przechowywane. To szyfrowanie chroni dane i pomaga sprostać zobowiązaniom dotyczącym bezpieczeństwa i zgodności w organizacji. Domyślnie usługa Azure Storage używa kluczy zarządzanych przez firmę Microsoft do szyfrowania danych. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).

Aby dodatkowo kontrolować dostęp i chronić poufne dane w Azure Logic Apps, można skonfigurować dodatkowe zabezpieczenia w następujących obszarach:

* [Dostęp do wywołań przychodzących do wyzwalaczy opartych na żądaniach](#secure-inbound-requests)
* [Dostęp do operacji aplikacji logiki](#secure-operations)
* [Dostęp do danych wejściowych i wyjściowych historii uruchamiania](#secure-run-history)
* [Dostęp do danych wejściowych parametrów](#secure-action-parameters)
* [Dostęp do wychodzących wywołań do innych usług i systemów](#secure-outbound-requests)
* [Blokuj tworzenie połączeń dla określonych łączników](#block-connections)
* [Wskazówki dotyczące izolacji aplikacji logiki](#isolation-logic-apps)
* [Podstawa zabezpieczeń platformy Azure dla Azure Logic Apps](../logic-apps/security-baseline.md)

Aby uzyskać więcej informacji o zabezpieczeniach na platformie Azure, zobacz następujące tematy:

* [Omówienie szyfrowania na platformie Azure](../security/fundamentals/encryption-overview.md)
* [Szyfrowanie danych platformy Azure — w spoczynku](../security/fundamentals/encryption-atrest.md)
* [Test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Dostęp do wywołań przychodzących do wyzwalaczy opartych na żądaniach

Wywołania przychodzące, które aplikacja logiki odbiera za pośrednictwem wyzwalacza opartego na żądaniach, takie jak wyzwalacz [żądania](../connectors/connectors-native-reqres.md) lub wyzwalacz [elementu webhook protokołu HTTP](../connectors/connectors-native-webhook.md) , obsługują szyfrowanie i są zabezpieczone przy użyciu [Transport Layer Security (TLS) 1,2 w minimalnej](https://en.wikipedia.org/wiki/Transport_Layer_Security), znanej wcześniej jako SSL (SSL). Logic Apps wymusza tę wersję podczas otrzymywania wywołania przychodzącego do wyzwalacza żądania lub wywołania zwrotnego do wyzwalacza lub akcji elementu webhook protokołu HTTP. W przypadku uzyskiwania błędów uzgadniania protokołu TLS upewnij się, że używasz protokołu TLS 1,2. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemu z protokołem TLS 1,0](/security/solving-tls1-problem).

Wywołania przychodzące obsługują następujące mechanizmy szyfrowania:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Poniżej przedstawiono dodatkowe sposoby ograniczenia dostępu do wyzwalaczy, które odbierają wywołania przychodzące do aplikacji logiki, tak aby tylko autoryzowani klienci mogli wywołać aplikację logiki:

* [Generowanie sygnatur dostępu współdzielonego (SAS)](#sas)
* [Włącz Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth)](#enable-oauth)
* [Uwidacznianie aplikacji logiki za pomocą usługi Azure API Management](#azure-api-management)
* [Ogranicz przychodzące adresy IP](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generowanie sygnatur dostępu współdzielonego (SAS)

Każdy punkt końcowy żądania w aplikacji logiki ma [sygnaturę dostępu współdzielonego (SAS)](/rest/api/storageservices/constructing-a-service-sas) w adresie URL punktu końcowego, który jest następujący:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Każdy adres URL zawiera `sp` `sv` parametr, i, `sig` zgodnie z opisem w tej tabeli:

| Parametr zapytania | Opis |
|-----------------|-------------|
| `sp` | Określa uprawnienia dla dozwolonych metod HTTP do użycia. |
| `sv` | Określa wersję sygnatury dostępu współdzielonego, która ma być używana do generowania podpisu. |
| `sig` | Określa podpis, który ma być używany do uwierzytelniania dostępu do wyzwalacza. Ta sygnatura jest generowana przy użyciu algorytmu SHA256 z kluczowym kluczem dostępu dla wszystkich ścieżek URL i właściwości. Nie jest nigdy narażony ani publikowany, ten klucz jest przechowywany w postaci zaszyfrowanej i przechowywanej w aplikacji logiki. Aplikacja logiki autoryzuje tylko te wyzwalacze, które zawierają prawidłowy podpis utworzony przy użyciu klucza tajnego. |
|||

Wywołania przychodzące do punktu końcowego żądania mogą korzystać tylko z jednego schematu autoryzacji lub uwierzytelniania SAS lub [Azure Active Directory Open Authentication](#enable-oauth). Chociaż użycie jednego schematu nie powoduje wyłączenia innego schematu, użycie obu tych schematów w tym samym czasie powoduje błąd, ponieważ usługa nie wie, który schemat wybrać.

Aby uzyskać więcej informacji na temat zabezpieczania dostępu za pomocą SAS, zobacz następujące sekcje w tym temacie:

* [Generowanie ponowne kluczy dostępu](#access-keys)
* [Tworzenie wygasających adresów URL wywołania zwrotnego](#expiring-urls)
* [Tworzenie adresów URL z kluczem podstawowym lub pomocniczym](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Aby wygenerować nowy klucz dostępu zabezpieczeń w dowolnym momencie, użyj interfejsu API REST lub Azure Portal platformy Azure. Wszystkie wcześniej wygenerowane adresy URL używające starego klucza są unieważnione i nie mają już autoryzacji do wyzwalania aplikacji logiki. Adresy URL, które należy pobrać po ponownym uruchomieniu, są podpisane przy użyciu nowego klucza dostępu.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki, która ma klucz, który chcesz wygenerować ponownie.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **klucze dostępu**.

1. Wybierz klucz, który chcesz ponownie wygenerować i zakończyć proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Tworzenie wygasających adresów URL wywołania zwrotnego

Jeśli adres URL punktu końcowego jest współużytkowany dla wyzwalacza opartego na żądaniach innych stron, można wygenerować adresy URL wywołania zwrotnego, które używają określonych kluczy i mają daty wygaśnięcia. Dzięki temu można bezproblemowo rzutować klucze lub ograniczyć dostęp do wyzwalania aplikacji logiki na podstawie określonego przedziału czasu. Aby określić datę wygaśnięcia dla adresu URL, należy użyć [interfejsu API REST Logic Apps](/rest/api/logic/workflowtriggers), na przykład:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści Uwzględnij właściwość przy `NotAfter` użyciu ciągu daty JSON. Ta właściwość zwraca adres URL wywołania zwrotnego, który jest prawidłowy tylko do `NotAfter` daty i godziny.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL przy użyciu podstawowego lub pomocniczego klucza tajnego

Podczas generowania lub wyświetlania adresów URL wywołania zwrotnego dla wyzwalacza opartego na żądaniach można określić klucz używany do podpisywania adresu URL. Aby wygenerować adres URL podpisany za pomocą określonego klucza, użyj [interfejsu API REST Logic Apps](/rest/api/logic/workflowtriggers), na przykład:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści należy uwzględnić `KeyType` Właściwość jako `Primary` lub `Secondary` . Ta właściwość zwraca adres URL, który jest podpisany przez określony klucz zabezpieczeń.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Włącz Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth)

W przypadku wywołań przychodzących do punktu końcowego, który jest tworzony przez wyzwalacz oparty na żądaniach, można włączyć [Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth)](../active-directory/develop/index.yml) przez zdefiniowanie lub dodanie zasad autoryzacji dla aplikacji logiki. W ten sposób wywołania przychodzące używają [tokenów dostępu](../active-directory/develop/access-tokens.md) OAuth do autoryzacji.

Gdy aplikacja logiki odbiera żądanie przychodzące, które zawiera token dostępu OAuth, usługa Azure Logic Apps porównuje oświadczenia tokenu względem oświadczeń określonych przez poszczególne zasady autoryzacji. Jeśli istnieje dopasowanie między oświadczeniami tokenu a wszystkimi oświadczeniami w co najmniej jednym z zasad, autoryzacja powiedzie się dla żądania przychodzącego. Token może mieć więcej oświadczeń niż liczba określona przez zasady autoryzacji.

Przed włączeniem protokołu OAuth usługi Azure AD zapoznaj się z następującymi kwestiami:

* Wywołanie przychodzące do punktu końcowego żądania może korzystać tylko z jednego schematu autoryzacji, uwierzytelniania OAuth usługi Azure AD lub [sygnatury dostępu współdzielonego (SAS)](#sas). Chociaż użycie jednego schematu nie powoduje wyłączenia innego schematu, użycie obu tych schematów w tym samym czasie powoduje błąd, ponieważ usługa Logic Apps nie wie, który schemat wybrać.

* Tylko schematy autoryzacji [typu okaziciela](../active-directory/develop/active-directory-v2-protocols.md#tokens) są obsługiwane w przypadku tokenów dostępu OAuth usługi Azure AD, co oznacza, że `Authorization` nagłówek tokenu dostępu musi określać `Bearer` Typ.

* Twoja aplikacja logiki jest ograniczona do maksymalnej liczby zasad autoryzacji. Każda zasada autoryzacji ma również maksymalną liczbę [oświadczeń](../active-directory/develop/developer-glossary.md#claim). Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Zasady autoryzacji muszą zawierać co najmniej wystąpienie **wystawcy** , które ma wartość rozpoczynającą się od `https://sts.windows.net/` lub `https://login.microsoftonline.com/` (OAuth v2) jako identyfikator wystawcy usługi Azure AD.

  Załóżmy na przykład, że aplikacja logiki ma zasady autoryzacji, które wymagają dwóch typów, **odbiorców** i **wystawcy**. Ta przykładowa [sekcja ładunku](../active-directory/develop/access-tokens.md#payload-claims) dla zdekodowanego tokenu dostępu zawiera typy zgłoszeń `aud` , gdzie jest wartością **odbiorców** i `iss` jest wartością **wystawcy** :

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

<a name="define-authorization-policy-portal"></a>

#### <a name="define-authorization-policy-in-azure-portal"></a>Definiowanie zasad autoryzacji w Azure Portal

Aby włączyć usługę Azure AD OAuth dla aplikacji logiki w Azure Portal, wykonaj następujące kroki, aby dodać do aplikacji logiki co najmniej jedną zasadę autoryzacji:

1. W [Azure Portal](https://portal.microsoft.com)Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **autoryzacja**. Po otwarciu okienka autoryzacji wybierz pozycję **Dodaj zasady**.

   ![Wybierz pozycję "Autoryzacja" > "Dodaj zasady"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Podaj informacje dotyczące zasad autoryzacji, określając [typy](../active-directory/develop/developer-glossary.md#claim) i wartości, które aplikacja logiki oczekuje w tokenie dostępu przedstawionym przez każde wywołanie przychodzące do wyzwalacza żądania:

   ![Podaj informacje dotyczące zasad autoryzacji](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa zasad** | Tak | Nazwa, która ma być używana dla zasad autoryzacji |
   | **Roszczenia** | Tak | Typy i wartości zgłoszeń akceptowane przez aplikację logiki z wywołań przychodzących. Wartość żądania jest ograniczona do [maksymalnej liczby znaków](logic-apps-limits-and-config.md#authentication-limits). Oto dostępne typy zgłoszeń: <p><p>- **Issuer** <br>- **Publiczn** <br>- **Temat** <br>- **Identyfikator JWT** (Identyfikator tokenu sieci Web JSON) <p><p>Co najmniej lista **oświadczeń** musi zawierać oświadczenie **wystawcy** , które ma wartość rozpoczynającą się od `https://sts.windows.net/` lub `https://login.microsoftonline.com/` jako identyfikator wystawcy usługi Azure AD. Aby uzyskać więcej informacji na temat tych typów oświadczeń, zobacz [oświadczenia w tokenach zabezpieczeń usługi Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Możesz również określić własny typ i wartość zgłoszenia. |
   |||

1. Aby dodać kolejną pozycję, wybierz jedną z następujących opcji:

   * Aby dodać inny typ typu, wybierz pozycję **Dodaj zgłoszenie standardowe**, wybierz typ, a następnie określ wartość żądania.

   * Aby dodać własne zgłoszenie, wybierz pozycję **Dodaj niestandardową** pozycję i określ wartość niestandardowego żądania.

1. Aby dodać kolejne zasady autoryzacji, wybierz pozycję **Dodaj zasady**. Powtórz poprzednie kroki, aby skonfigurować zasady.

1. Po zakończeniu wybierz pozycję **Zapisz**.

1. Aby dołączyć `Authorization` Nagłówek z tokenu dostępu w danych wyjściowych wyzwalacza opartego na żądaniach, zobacz [Dołącz nagłówek "Autoryzacja" w danych wyjściowych wyzwalacza żądania](#include-auth-header).

<a name="define-authorization-policy-template"></a>

#### <a name="define-authorization-policy-in-azure-resource-manager-template"></a>Definiowanie zasad autoryzacji w szablonie Azure Resource Manager

Aby włączyć usługę Azure AD OAuth w szablonie ARM w celu wdrożenia aplikacji logiki, wykonaj następujące kroki i Poniższa składnia:

1. W `properties` sekcji [definicji zasobu aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)Dodaj `accessControl` obiekt, jeśli nie istnieje, który zawiera `triggers` obiekt.

   Aby uzyskać więcej informacji na temat `accessControl` obiektu, zobacz [ograniczanie zakresów adresów IP dla ruchu przychodzącego w szablonie Azure Resource Manager](#restrict-inbound-ip-template) i [Dokumentacja szablonu przepływów pracy Microsoft. Logic](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. W `triggers` obiekcie Dodaj `openAuthenticationPolicies` obiekt zawierający obiekt, w `policies` którym zdefiniowano co najmniej jedną zasadę autoryzacji.

1. Podaj nazwę zasad autoryzacji, ustaw typ zasad na `AAD` , a `claims` następnie Dołącz tablicę, w której określisz jeden lub więcej typów roszczeń.

   Co najmniej `claims` Tablica musi zawierać typ wystąpienia wystawcy, w którym Właściwość Claim zostanie ustawiona `name` na wartość i ustawiona na wartość `iss` `value` Rozpocznij od `https://sts.windows.net/` lub `https://login.microsoftonline.com/` jako identyfikator wystawcy usługi Azure AD. Aby uzyskać więcej informacji na temat tych typów oświadczeń, zobacz [oświadczenia w tokenach zabezpieczeń usługi Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Możesz również określić własny typ i wartość zgłoszenia.

1. Aby dołączyć `Authorization` Nagłówek z tokenu dostępu w danych wyjściowych wyzwalacza opartego na żądaniach, zobacz [Dołącz nagłówek "Autoryzacja" w danych wyjściowych wyzwalacza żądania](#include-auth-header).

Oto składnia do obserwowania:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Uwzględnij nagłówek "Authorization" w danych wyjściowych wyzwalacza żądania

W przypadku aplikacji logiki, które [umożliwiają Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth)](#enable-oauth) do autoryzowania wywołań przychodzących do wyzwalaczy opartych na żądaniach, można włączyć wyzwalacz żądania lub wyjściowe wyzwalacza http elementu webhook w celu uwzględnienia `Authorization` nagłówka z tokenu dostępu OAuth. W podstawowej definicji JSON wyzwalacza Dodaj i ustaw `operationOptions` Właściwość na `IncludeAuthorizationHeadersInOutputs` . Oto przykład dla wyzwalacza żądania:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Więcej informacji można znaleźć w następujących tematach:

* [Odwołanie do schematu dla wyzwalaczy i typów akcji — wyzwalacz żądania](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Odwołanie do schematu dla typów wyzwalaczy i akcji — wyzwalacz elementu webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Odwołanie do schematu dla typów wyzwalaczy i akcji — opcje operacji](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Uwidacznianie aplikacji logiki za pomocą usługi Azure API Management

Aby dodać więcej [protokołów uwierzytelniania](../active-directory/develop/authentication-vs-authorization.md) do aplikacji logiki, rozważ użycie usługi [Azure API Management](../api-management/api-management-key-concepts.md) . Ta usługa ułatwia uwidocznienie aplikacji logiki jako interfejsu API i oferuje zaawansowane monitorowanie, zabezpieczenia, zasady i dokumentację dla dowolnego punktu końcowego. API Management może uwidaczniać publiczny lub prywatny punkt końcowy dla aplikacji logiki. Aby autoryzować dostęp do tego punktu końcowego, można użyć uwierzytelniania OAuth usługi Azure AD, [certyfikatu klienta](#client-certificate-authentication)lub innych standardów zabezpieczeń w celu autoryzowania dostępu do tego punktu końcowego. Gdy API Management odbiera żądanie, usługa wysyła żądanie do aplikacji logiki, a także przeprowadza wszelkie niezbędne przekształcenia lub ograniczenia. Aby umożliwić API Management wywoływanie aplikacji logiki, możesz [ograniczyć przychodzące adresy IP aplikacji logiki](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Ogranicz przychodzące adresy IP

Wraz z sygnaturą dostępu współdzielonego można jawnie ograniczyć liczbę klientów, którzy mogą wywołać aplikację logiki. Na przykład Jeśli zarządzasz punktem końcowym żądania przy użyciu [usługi Azure API Management](../api-management/api-management-key-concepts.md), możesz ograniczyć aplikację logiki do akceptowania żądań tylko z adresu IP dla [tworzonego wystąpienia usługi API Management](../api-management/get-started-create-service-instance.md).

> [!NOTE]
> Niezależnie od określonych adresów IP można nadal uruchamiać aplikację logiki, która ma wyzwalacz oparty na żądaniach za [Logic Apps pomocą interfejsu API REST: wyzwalacze przepływu pracy — Uruchom](/rest/api/logic/workflowtriggers/run) żądanie lub przy użyciu API Management. Jednak ten scenariusz nadal wymaga [uwierzytelniania](../active-directory/develop/authentication-vs-authorization.md) względem interfejsu API REST platformy Azure. Wszystkie zdarzenia pojawiają się w dzienniku inspekcji platformy Azure. Upewnij się, że zasady kontroli dostępu zostały odpowiednio skonfigurowane.

<a name="restrict-inbound-ip-portal"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Ogranicz zakresy adresów IP dla ruchu przychodzącego w Azure Portal

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Ustawienia przepływu pracy**.

1. W sekcji **Konfiguracja kontroli dostępu** w obszarze **dozwolone adresy IP dla ruchu przychodzącego** wybierz ścieżkę dla danego scenariusza:

   * Aby aplikację logiki można było wywołać tylko jako zagnieżdżoną aplikację logiki przy użyciu wbudowanej [akcji Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md), zaznacz opcję **tylko inne Logic Apps**, która działa *tylko* wtedy, gdy używasz akcji **Azure Logic Apps** do wywoływania zagnieżdżonej aplikacji logiki.
   
     Ta opcja umożliwia zapisanie pustej tablicy do zasobu aplikacji logiki i wymaga, aby tylko wywołania z nadrzędnych aplikacji logiki, które używają wbudowanej akcji **Azure Logic Apps** mogą wyzwolić zagnieżdżoną aplikację logiki.

   * Aby aplikację logiki można było wywołać tylko jako aplikację zagnieżdżoną przy użyciu akcji HTTP, wybierz **określone zakresy adresów IP**, a *nie* **tylko inne Logic Apps**. Gdy pojawi się okno **zakresy adresów IP dla wyzwalaczy** , wprowadź [wyjściowe adresy IP](../logic-apps/logic-apps-limits-and-config.md#outbound)aplikacji logiki nadrzędnej. Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*.
   
     > [!NOTE]
     > Jeśli używasz **jedynej opcji Logic Apps** i akcji HTTP do wywoływania zagnieżdżonej aplikacji logiki, wywołanie zostanie zablokowane i zostanie wyświetlony komunikat o błędzie "401 bez autoryzacji".
        
   * W przypadku scenariuszy, w których chcesz ograniczyć wywołania przychodzące z innych adresów IP, gdy pojawi się okno **zakresy IP dla wyzwalaczy** , określ zakresy adresów IP akceptowane przez wyzwalacz. Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*.

1. Opcjonalnie w obszarze **Ogranicz wywołania do pobierania komunikatów wejściowych i wyjściowych z historii uruchamiania do podanych adresów IP** można określić zakresy adresów IP dla wywołań przychodzących, które mogą uzyskiwać dostęp do komunikatów wejściowych i wyjściowych w historii uruchamiania.

<a name="restrict-inbound-ip-template"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Ogranicz zakresy adresów IP dla ruchu przychodzącego w szablonie Azure Resource Manager

W przypadku [automatyzowania wdrażania aplikacji logiki za pomocą szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można określić dozwolone zakresy przychodzącego adresu IP w definicji zasobu aplikacji logiki przy użyciu `accessControl` sekcji. W tej sekcji Użyj `triggers` , `actions` i opcjonalne sekcje odpowiednio do `contents` potrzeb, dołączając `allowedCallerIpAddresses` sekcję z `addressRange` właściwością i ustawiając wartość właściwości na dozwolony zakres adresów IP w formacie *x. x. x. x/x* lub *x. x. x. x-x* . x. x. x.

* Jeśli zagnieżdżona aplikacja logiki używa **tylko innej Logic Apps** opcji, która zezwala na wywołania przychodzące tylko z innych aplikacji logiki, które używają akcji Azure Logic Apps, należy ustawić `addressRange` Właściwość na pustą tablicę (**[]**).

* Jeśli zagnieżdżona aplikacja logiki używa opcji **określonych zakresów adresów IP** dla innych wywołań przychodzących, takich jak inne aplikacje logiki, które używają akcji http, należy ustawić `addressRange` Właściwość na dozwolony zakres adresów IP.

Ten przykład przedstawia definicję zasobu dla zagnieżdżonej aplikacji logiki, która zezwala na wywołania przychodzące tylko z aplikacji logiki, które używają wbudowanej akcji Azure Logic Apps:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": []
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": []
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Ten przykład przedstawia definicję zasobu dla zagnieżdżonej aplikacji logiki, która zezwala na wywołania przychodzące z aplikacji logiki, które używają akcji HTTP:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Dostęp do operacji aplikacji logiki

Można zezwolić tylko określonym użytkownikom lub grupom na uruchamianie określonych zadań, takich jak zarządzanie, edytowanie i przeglądanie aplikacji logiki. Aby kontrolować ich uprawnienia, użyj funkcji [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) na platformie Azure, aby można było przypisywać niestandardowe lub wbudowane role do członków w ramach subskrypcji platformy Azure:

* [Współautor aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor): umożliwia zarządzanie aplikacjami logiki, ale nie pozwala na zmianę dostępu do nich.

* [Operator aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator): umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie można ich edytować ani aktualizować.

Aby uniemożliwić innym użytkownikom zmianę lub usunięcie aplikacji logiki, możesz użyć [blokady zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md). Ta funkcja uniemożliwia innym osobom zmianę lub usunięcie zasobów produkcyjnych.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Dostęp do danych historii uruchamiania

Podczas uruchamiania aplikacji logiki wszystkie dane są [szyfrowane podczas przesyłania](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) przy użyciu Transport Layer Security (TLS) i [w spoczynku](../security/fundamentals/encryption-atrest.md). Po zakończeniu działania aplikacji logiki można wyświetlić historię tego uruchomienia, w tym kroki, które zostały wykonane wraz ze stanem, czasem trwania, danymi wejściowymi i wynikami dla każdej akcji. Ta obszerna szczegółowo zawiera szczegółowe informacje na temat działania aplikacji logiki oraz miejsca, w którym można rozpocząć rozwiązywanie wszelkich powstających problemów.

Podczas wyświetlania historii uruchamiania aplikacji logiki usługa Logic Apps uwierzytelnia dostęp, a następnie dostarcza linki do wejść i wyjść dla żądań i odpowiedzi dla każdego przebiegu. Jednak w przypadku akcji, które obsługują hasła, wpisy tajne, klucze lub inne poufne informacje, chcesz uniemożliwić innym użytkownikom wyświetlanie tych danych i uzyskiwanie do nich dostępu. Na przykład jeśli aplikacja logiki pobiera wpis tajny z [Azure Key Vault](../key-vault/general/overview.md) , który ma być używany podczas uwierzytelniania akcji http, należy ukryć ten klucz tajny z widoku.

Aby kontrolować dostęp do wejść i wyjść w historii uruchamiania aplikacji logiki, dostępne są następujące opcje:

* [Ogranicz dostęp według zakresu adresów IP](#restrict-ip).

  Ta opcja pomaga w zabezpieczeniu dostępu do historii uruchamiania na podstawie żądań z określonego zakresu adresów IP.

* [Zabezpiecz dane w historii uruchamiania przy użyciu zaciemniania](#obfuscate).

  W wielu wyzwalaczach i akcjach można zabezpieczyć dane wejściowe, wyjściowe lub zarówno w historii uruchamiania aplikacji logiki.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Ograniczanie dostępu według zakresu adresów IP

Możesz ograniczyć dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki, tak aby tylko żądania z określonych zakresów adresów IP mogły wyświetlać te dane. Na przykład, aby zablokować wszystkim użytkownikom dostęp do wejść i wyjść, określ zakres adresów IP, taki jak `0.0.0.0-0.0.0.0` . Tylko osoba z uprawnieniami administratora może usunąć to ograniczenie, które zapewnia możliwość dostępu "just in Time" do danych aplikacji logiki. Możesz określić zakresy adresów IP, które mają być ograniczone przy użyciu Azure Portal lub szablonu Azure Resource Manager, który jest używany do wdrażania aplikacji logiki.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Ograniczanie zakresów adresów IP w Azure Portal

1. W Azure Portal Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja kontroli dostępu**  >  **dozwolone adresy IP dla ruchu przychodzącego** wybierz pozycję **określone zakresy adresów IP**.

1. W obszarze **zakresy adresów IP dla pozycji zawartość** określ zakresy adresów IP, które mogą uzyskiwać dostęp do zawartości z danych wejściowych i wyjściowych.

   Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Ogranicz zakresy adresów IP w szablonie Azure Resource Manager

W przypadku [automatyzowania wdrażania aplikacji logiki za pomocą szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można określić zakresy adresów IP za pomocą `accessControl` sekcji z `contents` sekcją w definicji zasobu aplikacji logiki, na przykład:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Zabezpieczanie danych w historii uruchamiania przy użyciu zaciemniania

Wiele wyzwalaczy i akcji ma ustawienia umożliwiające zabezpieczenie danych wejściowych, wyjściowych lub zarówno z historii uruchamiania aplikacji logiki. Przed użyciem tych ustawień, aby zabezpieczyć te dane, [Przejrzyj te zagadnienia](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Zabezpieczanie wejść i wyjść w projektancie

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

   ![Otwórz aplikację logiki w Projektancie aplikacji logiki](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Na wyzwalaczu lub akcji, w której chcesz zabezpieczyć poufne dane, wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz wyzwalacz lub ustawienia akcji](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Włącz opcję **Zabezpiecz dane wejściowe**, **bezpieczne wyjście** lub oba te elementy. Po zakończeniu wybierz pozycję **Gotowe**.

   ![Włącz "bezpieczne dane wejściowe" lub "bezpieczne wyjście"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akcja lub wyzwalacz zawiera teraz ikonę kłódki na pasku tytułu.

   ![Akcja lub pasek tytułu wyzwalacza pokazuje ikonę kłódki](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokeny, które reprezentują zabezpieczone dane wyjściowe z poprzednich akcji, również pokazują ikony blokady. Na przykład po wybraniu takich danych wyjściowych z listy zawartości dynamicznej do użycia w akcji token pokazuje ikonę kłódki.

   ![Wybierz token dla zabezpieczonych danych wyjściowych](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po uruchomieniu aplikacji logiki można wyświetlić historię tego uruchomienia.

   1. W okienku **Przegląd** aplikacji logiki wybierz przebieg, który chcesz wyświetlić.

   1. W okienku **uruchamiania aplikacji logiki** rozwiń akcje, które chcesz przejrzeć.

      W przypadku wybrania opcji przesłaniania danych wejściowych i wyjściowych te wartości zostaną wyświetlone jako ukryte.

      ![Ukryte dane wejściowe i wyjściowe w historii uruchamiania](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Zabezpiecz dane wejściowe i wyjściowe w widoku kodu

W podstawowym wyzwalaczu lub definicji akcji Dodaj lub zaktualizuj `runtimeConfiguration.secureData.properties` tablicę przy użyciu jednej lub obu tych wartości:

* `"inputs"`: Zabezpiecza dane wejściowe w historii uruchamiania.
* `"outputs"`: Zabezpiecza dane wyjściowe w historii uruchamiania.

Poniżej przedstawiono kilka [kwestii, które należy wziąć pod uwagę](#obfuscation-considerations) podczas korzystania z tych ustawień, aby zabezpieczyć te dane.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Zagadnienia dotyczące zabezpieczania danych wejściowych i wyjściowych

* Gdy ukrywasz dane wejściowe lub dane wyjściowe wyzwalacza lub akcji, Logic Apps nie wysyłaj zabezpieczonych danych do usługi Azure Log Analytics. Nie można również dodać [śledzonych właściwości](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) do tego wyzwalacza lub akcji do monitorowania.

* [Interfejs API Logic Apps obsługujący historię przepływu pracy](/rest/api/logic/) nie zwraca zabezpieczonych danych wyjściowych.

* Aby zabezpieczyć wyjście z akcji, która ukrywa dane wejściowe lub jawnie zasłania wyjścia, należy ręcznie włączyć **bezpieczne wyjście** w tej akcji.

* Upewnij się, że włączasz **bezpieczne dane wejściowe** lub **zabezpieczone wyjścia** w akcjach podrzędnych, w których oczekuje się, że historia uruchamiania ma zasłaniać te dane.

  **Ustawienia bezpiecznego wyjścia**

  Po ręcznym włączeniu **bezpiecznych danych wyjściowych** w wyzwalaczu lub akcji Logic Apps ukrywa te dane wyjściowe w historii uruchamiania. Jeśli akcja w trybie podrzędnym jawnie używa tych zabezpieczonych danych wyjściowych jako dane wejściowe, Logic Apps ukrywa dane wejściowe tej akcji w historii uruchamiania, ale *nie włącza* ustawienia **zabezpieczonych danych wejściowych** akcji.

  ![Zabezpieczone wyjście jako dane wejściowe i wpływ na efekt podrzędny w większości akcji](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akcje redagowania, przeanalizowania JSON i odpowiedzi mają tylko ustawienia **zabezpieczeń danych wejściowych** . Po włączeniu tego ustawienia ukrywa także te wyniki. Jeśli te akcje jawnie wykorzystują dane wyjściowe przesyłane strumieniowo do strumienia, Logic Apps ukrywa **dane wejściowe i** wyjściowe akcji, ale *nie włączają* tych akcji. Jeśli akcja w trybie podrzędnym jawnie używa ukrytych danych wyjściowych z akcji Redaguj, Analizuj dane JSON lub odpowiedzi jako dane wejściowe, Logic Apps *nie ukrywa danych wejściowych lub wyjściowych akcji podrzędnej*.

  ![Zabezpieczone wyjścia jako dane wejściowe z wpływem podrzędnym na określone akcje](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Bezpieczne ustawienie danych wejściowych**

  Po ręcznym włączeniu **zabezpieczeń danych wejściowych** wyzwalacza lub akcji Logic Apps ukrywa te dane wejściowe w historii uruchamiania. Jeśli akcja przekroczenia jawnie używa widocznego wyjścia z tego wyzwalacza lub akcji jako danych wejściowych, Logic Apps ukrywa dane wejściowe akcji podrzędnej w historii uruchamiania, ale *nie włącza* **bezpiecznego wejścia** w tej akcji i nie ukrywa danych wyjściowych tej akcji.

  ![Zabezpieczanie danych wejściowych i wpływu na wpływ na większość akcji](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Jeśli akcje redagowania, analizy JSON i odpowiedzi jawnie używają widocznych danych wyjściowych wyzwalacza lub akcji, które mają zabezpieczone dane wejściowe, Logic Apps ukrywają dane wejściowe i wyjściowe akcji, ale *nie włączają* ustawienia **zabezpieczonych danych wejściowych** tej akcji. Jeśli akcja w trybie podrzędnym jawnie używa ukrytych danych wyjściowych z akcji Redaguj, Analizuj dane JSON lub odpowiedzi jako dane wejściowe, Logic Apps *nie ukrywa danych wejściowych lub wyjściowych akcji podrzędnej*.

  ![Zabezpieczone wejścia i wpływ na określone akcje](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Dostęp do danych wejściowych parametrów

W przypadku wdrażania w różnych środowiskach należy rozważyć parametryzacja wartości w definicji przepływu pracy, które różnią się w zależności od tych środowisk. Dzięki temu można uniknąć zakodowanych danych przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) , aby wdrożyć aplikację logiki, chronić poufne dane przez zdefiniowanie zabezpieczonych parametrów i przekazać te dane jako osobny dane wejściowe za pomocą [parametrów szablonu](../azure-resource-manager/templates/template-parameters.md) przy użyciu [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

Na przykład jeśli uwierzytelniasz akcje HTTP przy użyciu [Azure Active Directory Open Authentication](#azure-active-directory-oauth-authentication) (Azure AD OAuth), możesz definiować i zasłaniać parametry, które akceptują identyfikator klienta i klucz tajny klienta, które są używane do uwierzytelniania. Aby zdefiniować te parametry w aplikacji logiki, użyj `parameters` sekcji w definicji przepływu pracy aplikacji logiki i szablonu Menedżer zasobów na potrzeby wdrożenia. Aby pomóc w zabezpieczeniu wartości parametrów, które nie mają być wyświetlane podczas edytowania aplikacji logiki lub wyświetlania historii uruchamiania, zdefiniuj parametry za pomocą `securestring` `secureobject` typu lub i użyj kodowania w razie potrzeby. Parametry, które mają ten typ nie są zwracane z definicją zasobu i nie są dostępne podczas wyświetlania zasobu po wdrożeniu. Aby uzyskać dostęp do tych wartości parametrów podczas wykonywania, użyj `@parameters('<parameter-name>')` wyrażenia wewnątrz definicji przepływu pracy. To wyrażenie jest oceniane tylko w czasie wykonywania i jest opisane przez [Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Jeśli używasz parametru w nagłówku żądania lub treści, ten parametr może być widoczny podczas wyświetlania historii uruchamiania aplikacji logiki i wychodzącego żądania HTTP. Upewnij się, że ustawiono również zasady dostępu do zawartości.
> Można również użyć [zaciemniania](#obfuscate) , aby ukryć wejścia i wyjścia w historii uruchamiania. Nagłówki autoryzacji nigdy nie są widoczne za poorednictwem danych wejściowych lub wyjściowych. Dlatego jeśli w tym miejscu jest używany wpis tajny, ten klucz tajny nie jest możliwy do pobierania.

Aby uzyskać więcej informacji, zobacz następujące sekcje w tym temacie:

* [Zabezpieczanie parametrów w definicjach przepływu pracy](#secure-parameters-workflow)
* [Zabezpieczanie danych w historii uruchamiania przy użyciu zaciemniania](#obfuscate)

W przypadku [automatyzowania wdrażania aplikacji logiki za pomocą szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można zdefiniować zabezpieczone [Parametry szablonu](../azure-resource-manager/templates/template-parameters.md), które są oceniane we wdrożeniu, przy użyciu `securestring` `secureobject` typów i. Aby zdefiniować parametry szablonu, użyj sekcji najwyższego poziomu szablonu `parameters` , która jest odrębna i inna od sekcji definicji przepływu pracy `parameters` . Aby podać wartości parametrów szablonu, użyj oddzielnego [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

Na przykład, jeśli używasz wpisów tajnych, możesz definiować i używać zabezpieczonych parametrów szablonu, które pobierają te wpisy tajne z [Azure Key Vault](../key-vault/general/overview.md) we wdrożeniu. Następnie można odwołać się do magazynu kluczy i wpisu tajnego w pliku parametrów. Więcej informacji można znaleźć w następujących tematach:

* [Przekaż wartości poufne podczas wdrażania przy użyciu Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Zabezpieczanie parametrów w szablonach Azure Resource Manager](#secure-parameters-deployment-template) w dalszej części tego tematu

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Zabezpieczanie parametrów w definicjach przepływu pracy

Aby chronić poufne informacje w definicji przepływu pracy aplikacji logiki, użyj zabezpieczonych parametrów, aby te informacje nie były widoczne po zapisaniu aplikacji logiki. Załóżmy na przykład, że akcja HTTP wymaga uwierzytelniania podstawowego, które używa nazwy użytkownika i hasła. W definicji przepływu pracy `parameters` sekcja definiuje `basicAuthPasswordParam` Parametry i przy `basicAuthUsernameParam` użyciu `securestring` typu. Definicja akcji odwołuje się do tych parametrów w `authentication` sekcji.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Zabezpieczanie parametrów w szablonach Azure Resource Manager

[Szablon Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) dla aplikacji logiki zawiera wiele `parameters` sekcji. Aby chronić hasła, klucze, wpisy tajne i inne informacje poufne, zdefiniuj zabezpieczone parametry na poziomie szablonu i na poziomie definicji przepływu pracy przy `securestring` użyciu `secureobject` typu lub. Następnie można przechowywać te wartości w [Azure Key Vault](../key-vault/general/overview.md) i używać [pliku parametrów](../azure-resource-manager/templates/parameter-files.md) do odwoływania się do magazynu kluczy i wpisu tajnego. Następnie szablon pobiera te informacje we wdrożeniu. Aby uzyskać więcej informacji, zobacz [przekazywanie poufnych wartości we wdrożeniu przy użyciu Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Poniżej znajduje się więcej informacji o tych `parameters` sekcjach:

* Na najwyższego poziomu szablonu `parameters` sekcja definiuje parametry dla wartości używanych przez szablon podczas *wdrażania*. Na przykład te wartości mogą zawierać parametry połączenia dla określonego środowiska wdrożenia. Następnie można przechowywać te wartości w osobnym [pliku parametrów](../azure-resource-manager/templates/parameter-files.md), co ułatwia zmianę tych wartości.

* W definicji zasobu aplikacji logiki, ale poza definicją przepływu pracy, `parameters` sekcja określa wartości parametrów definicji przepływu pracy. W tej sekcji można przypisać te wartości przy użyciu wyrażeń szablonu, które odwołują się do parametrów szablonu. Wyrażenia te są oceniane podczas wdrażania.

* W definicji przepływu pracy `parameters` sekcja definiuje parametry, które są używane przez aplikację logiki w czasie wykonywania. Następnie można odwoływać się do tych parametrów w przepływie pracy aplikacji logiki przy użyciu wyrażeń definicji przepływu pracy, które są oceniane w czasie wykonywania.

Ten przykładowy szablon, który ma wiele zabezpieczonych definicji parametrów, które używają `securestring` typu:

| Nazwa parametru | Opis |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr szablonu, który akceptuje hasło, które jest następnie przekazanie do parametru definicji przepływu pracy `basicAuthPasswordParam` |
| `TemplateUsernameParam` | Parametr szablonu, który akceptuje nazwę użytkownika, która jest następnie przenoszona do parametru definicji przepływu pracy `basicAuthUserNameParam` |
| `basicAuthPasswordParam` | Parametr definicji przepływu pracy, który akceptuje hasło uwierzytelniania podstawowego w akcji HTTP |
| `basicAuthUserNameParam` | Parametr definicji przepływu pracy, który akceptuje nazwę użytkownika uwierzytelniania podstawowego w akcji HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Dostęp do wychodzących wywołań do innych usług i systemów

W oparciu o możliwości docelowego punktu końcowego, wywołania wychodzące wysyłane przez [wyzwalacz http lub akcję http](../connectors/connectors-native-http.md), obsługują szyfrowanie i są zabezpieczone za pomocą [Transport Layer Security (TLS) 1,0, 1,1 lub 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security), wcześniej znanych jako SSL (SSL). Logic Apps negocjuje z docelowym punktem końcowym przy użyciu najwyższej możliwej wersji, która jest obsługiwana. Na przykład jeśli docelowy punkt końcowy obsługuje 1,2, wyzwalacz HTTP lub akcja używa najpierw 1,2. W przeciwnym razie łącznik używa następnej najwyższej obsługiwanej wersji.

Poniżej przedstawiono informacje na temat certyfikatów z podpisem własnym protokołu TLS/SSL:

* W przypadku aplikacji logiki w globalnym, wielodostępnym środowisku platformy Azure łącznik protokołu HTTP nie zezwala na certyfikaty TLS/SSL z podpisem własnym. Jeśli aplikacja logiki wysyła wywołanie HTTP do serwera i przedstawia certyfikat z podpisem własnym protokołu TLS/SSL, wywołanie HTTP kończy się niepowodzeniem z `TrustFailure` powodu błędu.

* W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)łącznik protokołu HTTP zezwala na certyfikaty z podpisem własnym dla UZGADNIANIA protokołów TLS/SSL. Należy jednak najpierw [włączyć obsługę certyfikatów](../logic-apps/create-integration-service-environment-rest-api.md#request-body) z podpisem własnym dla istniejących ISE lub nowych ISE przy użyciu interfejsu API REST Logic Apps i zainstalować certyfikat publiczny w `TrustedRoot` lokalizacji.

Oto więcej sposobów zabezpieczania punktów końcowych, które obsługują wywołania wysyłane z aplikacji logiki:

* [Dodawanie uwierzytelniania do żądań wychodzących](#add-authentication-outbound).

  W przypadku wysyłania wywołań wychodzących przy użyciu wyzwalacza HTTP lub akcji można dodać uwierzytelnianie do żądania wysyłanego przez aplikację logiki. Można na przykład wybrać następujące typy uwierzytelniania:

  * [Uwierzytelnianie podstawowe](#basic-authentication)

  * [Uwierzytelnianie certyfikatu klienta](#client-certificate-authentication)

  * [Active Directory uwierzytelniania OAuth](#azure-active-directory-oauth-authentication)

  * [Uwierzytelnianie tożsamości zarządzanej](#managed-identity-authentication)

* Ogranicz dostęp z adresów IP aplikacji logiki.

  Wszystkie wywołania punktów końcowych z usługi Logic Apps pochodzą z określonych wyznaczone adresy IP, które są oparte na regionach aplikacji logiki. Można dodać filtrowanie akceptujące żądania tylko z tych adresów IP. Aby uzyskać te adresy IP, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Zwiększ bezpieczeństwo połączeń z systemami lokalnymi.

  Azure Logic Apps zapewnia integrację z tymi usługami, aby zapewnić lepszą bezpieczną i niezawodną komunikację lokalną.

  * Lokalna brama danych

    Wiele zarządzanych łączników w Azure Logic Apps ułatwiają bezpieczne połączenia z systemami lokalnymi, takimi jak system plików, SQL, SharePoint i DB2. Brama wysyła dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem Azure Service Bus. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Dowiedz się [, jak działa lokalna Brama danych](logic-apps-gateway-install.md#gateway-cloud-service).

  * Nawiązywanie połączenia za pomocą usługi Azure API Management

    [Usługa Azure API Management](../api-management/api-management-key-concepts.md) udostępnia opcje połączenia lokalnego, takie jak wirtualna sieć prywatna typu lokacja-lokacja i integracja [ExpressRoute](../expressroute/expressroute-introduction.md) dla zabezpieczonego serwera proxy i komunikacji z systemami lokalnymi. Jeśli masz interfejs API, który zapewnia dostęp do systemu lokalnego i został on uwidoczniony przez utworzenie [wystąpienia usługi API Management](../api-management/get-started-create-service-instance.md), możesz wywołać ten interfejs API w przepływie pracy aplikacji logiki, wybierając wbudowany wyzwalacz API Management lub akcję w Projektancie aplikacji logiki.

    > [!NOTE]
    > Łącznik pokazuje tylko te usługi API Management, do których masz uprawnienia do wyświetlania i nawiązywania połączeń, ale nie wyświetlają usług API Management opartych na użyciu.

    1. W Projektancie aplikacji logiki wprowadź `api management` w polu wyszukiwania. Wybierz krok w zależności od tego, czy dodajesz wyzwalacz, czy akcję:<p>

       * Jeśli dodajesz wyzwalacz, który jest zawsze pierwszym krokiem w przepływie pracy, wybierz pozycję **Wybierz wyzwalacz usługi Azure API Management**.

       * Jeśli dodajesz akcję, wybierz pozycję **Wybierz akcję API Management platformy Azure**.

       Ten przykład dodaje wyzwalacz:

       ![Dodawanie wyzwalacza API Management platformy Azure](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Wybierz wcześniej utworzone wystąpienie usługi API Management.

       ![Wybierz wystąpienie usługi API Management](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Wybierz wywołanie interfejsu API, które ma być używane.

       ![Wybierz istniejący interfejs API](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Dodawanie uwierzytelniania do wywołań wychodzących

Punkty końcowe HTTP i HTTPS obsługują różne rodzaje uwierzytelniania. W przypadku niektórych wyzwalaczy i akcji, które są używane do wysyłania wywołań wychodzących lub żądań do tych punktów końcowych, można określić typ uwierzytelniania. W Projektancie aplikacji logiki wyzwalacze i akcje obsługujące wybór typu uwierzytelniania mają właściwość **Authentication** . Jednak ta właściwość może nie zawsze być wyświetlana domyślnie. W takich przypadkach na wyzwalaczu lub akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **uwierzytelnianie**.

> [!IMPORTANT]
> Aby chronić poufne informacje obsługiwane przez aplikację logiki, należy użyć zabezpieczonych parametrów i zakodować dane zgodnie z potrzebami.
> Aby uzyskać więcej informacji o używaniu i zabezpieczaniu parametrów, zobacz [dostęp do danych wejściowych parametrów](#secure-action-parameters).

W tej tabeli przedstawiono typy uwierzytelniania, które są dostępne dla wyzwalaczy i akcji, w których można wybrać typ uwierzytelniania:

| Typ uwierzytelniania | Obsługiwane wyzwalacze i akcje |
|---------------------|--------------------------------|
| [Podstawowa](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Certyfikat klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Nieprzetworzone](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Tożsamość zarządzana](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, webhook protokołu HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Jeśli opcja [podstawowa](../active-directory-b2c/secure-rest-api.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagane | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | Podstawowa | Typ uwierzytelniania do użycia |
| **Nazwa użytkownika** | `username` | Tak | <*Nazwa użytkownika*>| Nazwa użytkownika służąca do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
| **Hasło** | `password` | Tak | <*hasło*> | Hasło do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
||||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i zabezpieczania poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP Określa uwierzytelnianie `type` jako `Basic` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Uwierzytelnianie certyfikatu klienta

Jeśli opcja [certyfikat klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagane | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | **Certyfikat klienta** <br>lub <br>`ClientCertificate` | Typ uwierzytelniania do użycia. Można zarządzać certyfikatami za pomocą [usługi Azure API Management](../api-management/api-management-howto-mutual-certificates.md). <p></p>**Uwaga**: Łączniki niestandardowe nie obsługują uwierzytelniania opartego na certyfikatach dla wywołań przychodzących i wychodzących. |
| **PFX** | `pfx` | Tak | <*zakodowany plik PFX — zawartość*> | Zawartość zakodowana algorytmem Base64 z pliku wymiany informacji osobistych (PFX) <p><p>Aby przekonwertować plik PFX na format szyfrowany algorytmem Base64, można użyć programu PowerShell, wykonując następujące czynności: <p>1. Zapisz zawartość certyfikatu w zmiennej: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Przekonwertuj zawartość certyfikatu przy użyciu `ToBase64String()` funkcji i Zapisz tę zawartość do pliku tekstowego: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Hasło** | `password`| Nie | <*hasło dla pliku PFX*> | Hasło do uzyskiwania dostępu do pliku PFX |
|||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i zabezpieczania poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP Określa uwierzytelnianie `type` jako `ClientCertificate` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Aby uzyskać więcej informacji na temat zabezpieczania usług przy użyciu uwierzytelniania certyfikatów klientów, zobacz następujące tematy:

* [Ulepszanie zabezpieczeń interfejsów API przy użyciu uwierzytelniania certyfikatów klientów na platformie Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Ulepszanie zabezpieczeń usług zaplecza przy użyciu uwierzytelniania certyfikatów klientów na platformie Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Zwiększ bezpieczeństwo usługi RESTfuL przy użyciu certyfikatów klienta](../active-directory-b2c/secure-rest-api.md)
* [Poświadczenia certyfikatu na potrzeby uwierzytelniania aplikacji](../active-directory/develop/active-directory-certificate-credentials.md)
* [Użyj certyfikatu TLS/SSL w kodzie w Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory Otwórz uwierzytelnianie

Wyzwalacze żądań umożliwiają uwierzytelnianie wywołań przychodzących po [skonfigurowaniu zasad autoryzacji usługi Azure AD](#enable-oauth) dla aplikacji logiki przy użyciu [Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml). Dla wszystkich innych wyzwalaczy i akcji, które zapewniają **Active Directory** typ uwierzytelniania OAuth do wybrania, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagane | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | **Active Directory OAuth** <br>lub <br>`ActiveDirectoryOAuth` | Typ uwierzytelniania do użycia. Logic Apps jest obecnie zgodny z [protokołem OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Urząd** | `authority` | Nie | <*Adres URL-urząd-token-wystawca*> | Adres URL urzędu dostarczającego token dostępu. Domyślnie ta wartość to `https://login.windows.net` . |
| **Dzierżawa** | `tenant` | Tak | <*Identyfikator dzierżawy*> | Identyfikator dzierżawy dla dzierżawy usługi Azure AD |
| **Grupy odbiorców** | `audience` | Tak | <*zasób do autoryzacji*> | Zasób, który ma być używany na potrzeby autoryzacji, na przykład `https://management.core.windows.net/` |
| **Identyfikator klienta** | `clientId` | Tak | <*Identyfikator klienta*> | Identyfikator klienta aplikacji żądającej autoryzacji |
| **Typ poświadczeń** | `credentialType` | Tak | Certyfikat <br>lub <br>Wpis tajny | Typ poświadczeń, którego klient używa do żądania autoryzacji. Ta właściwość i wartość nie pojawiają się w podstawowej definicji aplikacji logiki, ale określają właściwości, które są wyświetlane dla wybranego typu poświadczenia. |
| **Wpisu** | `secret` | Tak, ale tylko dla typu poświadczeń "wpis tajny" | <*Klient-klucz tajny*> | Wpis tajny klienta na potrzeby żądania autoryzacji |
| **PFX** | `pfx` | Tak, ale tylko dla typu poświadczeń "certyfikat" | <*zakodowany plik PFX — zawartość*> | Zawartość zakodowana algorytmem Base64 z pliku wymiany informacji osobistych (PFX) |
| **Hasło** | `password` | Tak, ale tylko dla typu poświadczeń "certyfikat" | <*hasło dla pliku PFX*> | Hasło do uzyskiwania dostępu do pliku PFX |
|||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i zabezpieczania poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP Określa uwierzytelnianie `type` jako `ActiveDirectoryOAuth` , typ poświadczeń jako `Secret` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Uwierzytelnianie surowe

Jeśli opcja **RAW** jest dostępna, można użyć tego typu uwierzytelniania w przypadku korzystania ze [schematów uwierzytelniania](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) , które nie są zgodne z [protokołem OAuth 2,0](https://oauth.net/2/). Ten typ polega na ręcznym utworzeniu wartości nagłówka autoryzacji wysyłanej przy użyciu żądania wychodzącego i określeniu tej wartości nagłówka w wyzwalaczu lub akcji.

Oto przykład nagłówka żądania HTTPS zgodnego z [protokołem OAuth 1,0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

W wyzwalaczu lub akcji, która obsługuje uwierzytelnianie surowe, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagane | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | Nieprzetworzone | Typ uwierzytelniania do użycia |
| **Wartość** | `value` | Tak | <*Authorization-header-Value*> | Wartość nagłówka autoryzacji do użycia na potrzeby uwierzytelniania |
||||||

W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i zabezpieczania poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP Określa uwierzytelnianie `type` jako `Raw` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Uwierzytelnianie tożsamości zarządzanej

Jeśli opcja [zarządzanej tożsamości](../active-directory/managed-identities-azure-resources/overview.md) jest dostępna dla [określonego wyzwalacza lub akcji](#add-authentication-outbound), aplikacja logiki może użyć tożsamości przypisanej do systemu lub *pojedynczej* ręcznie utworzonej tożsamości przypisanej przez użytkownika do uwierzytelniania dostępu do innych zasobów chronionych przez Azure Active Directory (Azure AD) bez konieczności logowania. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie trzeba podawać ani obrócić wpisów tajnych. Dowiedz się więcej [na temat usług platformy Azure, które obsługują tożsamości zarządzane na potrzeby uwierzytelniania w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Aby aplikacja logiki mogła korzystać z tożsamości zarządzanej, wykonaj kroki opisane w temacie [uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Te kroki umożliwiają włączenie zarządzanej tożsamości w aplikacji logiki oraz skonfigurowanie dostępu tej tożsamości do docelowego zasobu platformy Azure.

1. Zanim funkcja platformy Azure będzie mogła korzystać z tożsamości zarządzanej, należy najpierw [włączyć uwierzytelnianie dla usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. W wyzwalaczu lub akcji, w której ma być używana tożsamość zarządzana, określ następujące wartości właściwości:

   | Właściwość (Projektant) | Właściwość (JSON) | Wymagane | Wartość | Opis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentication** | `type` | Tak | **Tożsamość zarządzana** <br>lub <br>`ManagedServiceIdentity` | Typ uwierzytelniania do użycia |
   | **Tożsamość zarządzana** | `identity` | Tak | * **Tożsamość zarządzana przypisana przez system** <br>lub <br>`SystemAssigned` <p><p>* <*przypisanej do użytkownika-Identity-Name*> | Zarządzana tożsamość do użycia |
   | **Grupy odbiorców** | `audience` | Tak | <*docelowy — identyfikator zasobu*> | Identyfikator zasobu dla zasobu docelowego, do którego chcesz uzyskać dostęp. <p>Załóżmy na przykład, że `https://storage.azure.com/` [tokeny dostępu](../active-directory/develop/access-tokens.md) są prawidłowe dla wszystkich kont magazynu. Można jednak określić adres URL usługi głównej, `https://fabrikamstorageaccount.blob.core.windows.net` na przykład dla określonego konta magazynu. <p>**Uwaga**: Właściwość **odbiorców** może być ukryta w niektórych wyzwalaczach lub akcjach. Aby ta właściwość była widoczna, w wyzwalaczu lub akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **odbiorcy**. <p><p>**Ważne**: Upewnij się, że identyfikator zasobu docelowego *dokładnie pasuje* do wartości oczekiwanej przez usługę Azure AD, w tym wszystkich wymaganych końcowych ukośników. W związku z tym `https://storage.azure.com/` Identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga końcowej kreski ułamkowej. Aby znaleźć te identyfikatory zasobów, zobacz [usługi platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   W przypadku używania [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i zabezpieczania poufnych informacji, na przykład w [szablonie Azure Resource Manager do automatyzowania wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. Ta przykładowa definicja akcji HTTP Określa uwierzytelnianie `type` jako `ManagedServiceIdentity` i używa [funkcji Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w celu uzyskania wartości parametrów:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blokuj tworzenie połączeń

Jeśli Twoja organizacja nie zezwala na łączenie się z określonymi zasobami przy użyciu ich łączników w Azure Logic Apps, można [zablokować możliwość tworzenia tych połączeń](../logic-apps/block-connections-connectors.md) dla określonych łączników w przepływach pracy aplikacji logiki przy użyciu [Azure Policy](../governance/policy/overview.md). Aby uzyskać więcej informacji, zobacz [blok połączeń utworzonych przez określone łączniki w Azure Logic Apps](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Wskazówki dotyczące izolacji aplikacji logiki

Azure Logic Apps w [Azure Government](../azure-government/documentation-government-welcome.md) obsłużyć wszystkie poziomy wpływu w regionach opisanych przez [Azure Government poziomu wpływu na poziom 5 wpływ na wskazówki dotyczące izolacji](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) i [działu obrony w chmurze (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). Aby spełnić te wymagania, Logic Apps obsługuje możliwość tworzenia i uruchamiania przepływów pracy w środowisku z dedykowanymi zasobami, dzięki czemu można zmniejszyć wpływ na wydajność innych dzierżawców platformy Azure w usłudze Logic Apps i uniknąć udostępniania zasobów obliczeniowych innym dzierżawcom.

* Aby uruchomić własny kod lub przeprowadzić transformację XML, [Utwórz i wywołaj funkcję platformy Azure](../logic-apps/logic-apps-azure-functions.md)zamiast korzystać z funkcji [kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md) lub dostarczaj [zestawy do użycia jako mapy](../logic-apps/logic-apps-enterprise-integration-maps.md), odpowiednio. Ponadto skonfiguruj środowisko hostingu dla aplikacji funkcji, aby zachować zgodność z wymaganiami dotyczącymi izolacji.

  Aby na przykład spełnić wymagania dotyczące poziomu 5, należy utworzyć aplikację funkcji z [planem App Service](../azure-functions/functions-scale.md#app-service-plan) przy użyciu [warstwy cenowej **izolowanej**](../app-service/overview-hosting-plans.md) wraz z [App Service Environment (ASE)](../app-service/environment/intro.md) , która również korzysta z **wyizolowanej** warstwy cenowej. W tym środowisku aplikacje funkcji są uruchamiane na dedykowanych maszynach wirtualnych platformy Azure i dedykowanych sieciach wirtualnych platformy Azure, które zapewniają izolację sieci w oparciu o izolację obliczeniową dla aplikacji i maksymalne możliwości skalowania w poziomie. Aby uzyskać więcej informacji, zobacz [Azure Government poziomu wpływu 5 wskazówek dotyczących izolacji — Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Więcej informacji można znaleźć w następujących tematach:<p>

  * [Plany Azure App Service](../app-service/overview-hosting-plans.md)
  * [Opcje sieciowe usługi Azure Functions](../azure-functions/functions-networking-options.md)
  * [Dedykowane hosty platformy Azure dla maszyn wirtualnych](../virtual-machines/dedicated-hosts.md)
  * [Izolacja maszyny wirtualnej na platformie Azure](../virtual-machines/isolation.md)
  * [Wdrażanie dedykowanych usług platformy Azure w sieciach wirtualnych](../virtual-network/virtual-network-for-azure-services.md)

* Aby utworzyć Aplikacje logiki, które są uruchamiane na dedykowanych zasobach i uzyskać dostęp do zasobów chronionych przez sieć wirtualną platformy Azure, można utworzyć [środowisko usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych ([prywatnego linku platformy Azure](../private-link/private-link-overview.md)) do zapewniania dostępu do usług PaaS platformy Azure, takich jak Azure Storage, Azure Cosmos DB lub Azure SQL Database, usług partnerskich lub usług klienta hostowanych na platformie Azure. Jeśli aplikacje logiki wymagają dostępu do sieci wirtualnych, które używają prywatnych punktów końcowych, należy utworzyć, wdrożyć i uruchomić te aplikacje logiki w ramach ISE.

  * Aby uzyskać większą kontrolę nad kluczami szyfrowania używanymi przez usługę Azure Storage, możesz skonfigurować własny klucz, korzystać z niego i zarządzać nim przy użyciu [Azure Key Vault](../key-vault/general/overview.md). Ta funkcja jest również znana jako "Bring Your Own Key" (BYOK), a klucz jest nazywany "kluczem zarządzanym przez klienta". Aby uzyskać więcej informacji, zobacz [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w środowiskach usługi integracji (ISEs) w Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Więcej informacji można znaleźć w następujących tematach:

* [Izolacja w chmurze publicznej platformy Azure](../security/fundamentals/isolation-choices.md)
* [Zabezpieczenia wysoce poufnych aplikacji IaaS na platformie Azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Następne kroki

* [Podstawa zabezpieczeń platformy Azure dla Azure Logic Apps](../logic-apps/security-baseline.md)
* [Automatyzowanie wdrażania Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps-log-analytics.md)
