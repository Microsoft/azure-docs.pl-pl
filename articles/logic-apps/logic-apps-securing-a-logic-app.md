---
title: Zabezpieczanie dostępu i danych
description: Bezpieczny dostęp do wejść, danych wyjściowych, wyzwalaczy opartych na żądaniach, historii uruchamiania, zadań zarządzania i dostępu do innych zasobów w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7979d1288cd99f8e28a421663383a930e0346357
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199607"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Zabezpieczanie dostępu i danych w Azure Logic Apps

Aby kontrolować dostęp i chronić poufne dane w Azure Logic Apps, można skonfigurować zabezpieczenia dla następujących obszarów:

* [Dostęp do wyzwalaczy opartych na żądaniach](#secure-triggers)
* [Dostęp do operacji aplikacji logiki](#secure-operations)
* [Dostęp do danych wejściowych i wyjściowych historii uruchamiania](#secure-run-history)
* [Dostęp do danych wejściowych parametrów](#secure-action-parameters)
* [Dostęp do usług i systemów wywoływanych z usługi Logic Apps](#secure-outbound-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Dostęp do wyzwalaczy opartych na żądaniach

Jeśli aplikacja logiki korzysta z wyzwalacza opartego na żądaniach, który odbiera przychodzące wywołania lub żądania, takie jak wyzwalacz [żądania](../connectors/connectors-native-reqres.md) lub [elementu webhook](../connectors/connectors-native-webhook.md) , można ograniczyć dostęp, tak aby tylko autoryzowani klienci mogli wywołać aplikację logiki. Wszystkie żądania odebrane przez aplikację logiki są szyfrowane i zabezpieczone przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako SSL (SSL).

Poniżej przedstawiono opcje, które mogą pomóc w zabezpieczeniu dostępu do tego typu wyzwalacza:

* [Generowanie sygnatur dostępu współdzielonego](#sas)
* [Włącz Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth)](#enable-oauth)
* [Ogranicz przychodzące adresy IP](#restrict-inbound-ip-addresses)
* [Dodaj Azure Active Directory Otwórz uwierzytelnianie (Azure AD OAuth) lub inne zabezpieczenia](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generowanie sygnatur dostępu współdzielonego (SAS)

Każdy punkt końcowy żądania w aplikacji logiki ma [sygnaturę dostępu współdzielonego (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) w adresie URL punktu końcowego, który jest następujący:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Każdy adres URL zawiera `sp` `sv` parametr, i, `sig` zgodnie z opisem w tej tabeli:

| Parametr zapytania | Opis |
|-----------------|-------------|
| `sp` | Określa uprawnienia dla dozwolonych metod HTTP do użycia. |
| `sv` | Określa wersję sygnatury dostępu współdzielonego, która ma być używana do generowania podpisu. |
| `sig` | Określa podpis, który ma być używany do uwierzytelniania dostępu do wyzwalacza. Ta sygnatura jest generowana przy użyciu algorytmu SHA256 z kluczowym kluczem dostępu dla wszystkich ścieżek URL i właściwości. Nie jest nigdy narażony ani publikowany, ten klucz jest przechowywany w postaci zaszyfrowanej i przechowywanej w aplikacji logiki. Aplikacja logiki autoryzuje tylko te wyzwalacze, które zawierają prawidłowy podpis utworzony przy użyciu klucza tajnego. |
|||

Aby uzyskać więcej informacji na temat zabezpieczania dostępu za pomocą SAS, zobacz następujące sekcje w tym temacie:

* [Generowanie ponowne kluczy dostępu](#access-keys)
* [Tworzenie wygasających adresów URL wywołania zwrotnego](#expiring-urls)
* [Tworzenie adresów URL z kluczem podstawowym lub pomocniczym](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Aby wygenerować nowy klucz dostępu zabezpieczeń w dowolnym momencie, użyj interfejsu API REST lub Azure Portal platformy Azure. Wszystkie wcześniej wygenerowane adresy URL używające starego klucza są unieważnione i nie mają już autoryzacji do wyzwalania aplikacji logiki. Adresy URL, które należy pobrać po ponownym uruchomieniu, są podpisane przy użyciu nowego klucza dostępu.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki, która ma klucz, który chcesz wygenerować ponownie.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **klucze dostępu**.

1. Wybierz klucz, który chcesz ponownie wygenerować i zakończyć proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Tworzenie wygasających adresów URL wywołania zwrotnego

Jeśli adres URL punktu końcowego jest współużytkowany dla wyzwalacza opartego na żądaniach innych stron, można wygenerować adresy URL wywołania zwrotnego, które używają określonych kluczy i mają daty wygaśnięcia. Dzięki temu można bezproblemowo rzutować klucze lub ograniczyć dostęp do wyzwalania aplikacji logiki na podstawie określonego przedziału czasu. Aby określić datę wygaśnięcia dla adresu URL, należy użyć [interfejsu API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści Uwzględnij właściwość przy `NotAfter` użyciu ciągu daty JSON. Ta właściwość zwraca adres URL wywołania zwrotnego, który jest prawidłowy tylko do `NotAfter` daty i godziny.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL przy użyciu podstawowego lub pomocniczego klucza tajnego

Podczas generowania lub wyświetlania adresów URL wywołania zwrotnego dla wyzwalacza opartego na żądaniach można określić klucz używany do podpisywania adresu URL. Aby wygenerować adres URL podpisany za pomocą określonego klucza, użyj [interfejsu API REST Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), na przykład:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści należy uwzględnić `KeyType` Właściwość jako `Primary` lub `Secondary` . Ta właściwość zwraca adres URL, który jest podpisany przez określony klucz zabezpieczeń.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Włącz Azure Active Directory OAuth

Jeśli aplikacja logiki rozpoczyna się od wyzwalacza żądania, można włączyć [Azure Active Directory Otwórz uwierzytelnianie](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) do autoryzacji wywołań przychodzących do wyzwalacza żądania. Przed włączeniem tego uwierzytelniania zapoznaj się z następującymi kwestiami:

* Twoja aplikacja logiki jest ograniczona do maksymalnej liczby zasad autoryzacji. Każda zasada autoryzacji ma również maksymalną liczbę [oświadczeń](../active-directory/develop/developer-glossary.md#claim). Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Zasady autoryzacji muszą zawierać co najmniej element Claim **wystawcy** , który ma wartość rozpoczynającą się od `https://sts.windows.net/` identyfikatora wystawcy usługi Azure AD.

* Wywołanie przychodzące do aplikacji logiki może korzystać tylko z jednego schematu autoryzacji, uwierzytelniania OAuth usługi Azure AD lub [sygnatur dostępu współdzielonego (SAS)](#sas).

* Tokeny OAuth są obsługiwane tylko dla wyzwalacza żądania.

* Tylko schematy autoryzacji [typu okaziciela](../active-directory/develop/active-directory-v2-protocols.md#tokens) są obsługiwane w przypadku tokenów OAuth.

Aby włączyć usługę Azure AD OAuth, wykonaj następujące kroki, aby dodać do aplikacji logiki co najmniej jedną zasadę autoryzacji.

1. W [Azure Portal](https://portal.microsoft.com)Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **autoryzacja**. Po otwarciu okienka autoryzacji wybierz pozycję **Dodaj zasady**.

   ![Wybierz pozycję "Autoryzacja" > "Dodaj zasady"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Podaj informacje o zasadach autoryzacji, określając typy i wartości [zgłoszeń](../active-directory/develop/developer-glossary.md#claim) , których oczekuje aplikacja logiki w tokenie uwierzytelniania przedstawionym przez każde wywołanie przychodzące do wyzwalacza żądania:

   ![Podaj informacje dotyczące zasad autoryzacji](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa zasad** | Yes | Nazwa, która ma być używana dla zasad autoryzacji |
   | **Roszczenia** | Yes | Typy i wartości zgłoszeń akceptowane przez aplikację logiki z wywołań przychodzących. Oto dostępne typy zgłoszeń: <p><p>- **Issuer** <br>- **Publiczn** <br>- **Temat** <br>- **Identyfikator JWT** (Identyfikator tokenu sieci Web JSON) <p><p>Na liście **oświadczeń** musi znajdować się oświadczenie **wystawcy** , które ma wartość ROZPOCZYNAJĄCą się od `https://sts.windows.net/` identyfikatora wystawcy usługi Azure AD. Aby uzyskać więcej informacji na temat tych typów oświadczeń, zobacz [oświadczenia w tokenach zabezpieczeń usługi Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Możesz również określić własny typ i wartość zgłoszenia. |
   |||

1. Aby dodać kolejną pozycję, wybierz jedną z następujących opcji:

   * Aby dodać inny typ typu, wybierz pozycję **Dodaj zgłoszenie standardowe**, wybierz typ, a następnie określ wartość żądania.

   * Aby dodać własne zgłoszenie, wybierz pozycję **Dodaj niestandardową**pozycję i określ wartość niestandardowego żądania.

1. Aby dodać kolejne zasady autoryzacji, wybierz pozycję **Dodaj zasady**. Powtórz poprzednie kroki, aby skonfigurować zasady.

1. Po zakończeniu wybierz pozycję **Zapisz**.

Aplikacja logiki jest teraz skonfigurowana do korzystania z protokołu OAuth usługi Azure AD do autoryzacji żądań przychodzących. Gdy aplikacja logiki otrzymuje żądanie przychodzące, które zawiera token uwierzytelniania, Azure Logic Apps porównuje oświadczenia tokenu dotyczące oświadczeń w poszczególnych zasadach autoryzacji. Jeśli istnieje dopasowanie między oświadczeniami tokenu a wszystkimi oświadczeniami w co najmniej jednym z zasad, autoryzacja powiedzie się dla żądania przychodzącego. Token może mieć więcej oświadczeń niż liczba określona przez zasady autoryzacji.

Załóżmy na przykład, że aplikacja logiki ma zasady autoryzacji, które wymagają dwóch typów, wystawców i odbiorców. Ten przykładowy zdekodowany [token dostępu](../active-directory/develop/access-tokens.md) obejmuje zarówno te typy roszczeń:

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

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Ogranicz przychodzące adresy IP

Wraz z sygnaturą dostępu współdzielonego można jawnie ograniczyć liczbę klientów, którzy mogą wywołać aplikację logiki. Na przykład Jeśli zarządzasz punktem końcowym żądania przy użyciu usługi Azure API Management, możesz ograniczyć aplikację logiki do akceptowania żądań tylko z adresu IP dla wystąpienia API Management.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Ogranicz zakresy adresów IP dla ruchu przychodzącego w Azure Portal

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja kontroli dostępu**  >  **dozwolone adresy IP dla ruchu przychodzącego**wybierz pozycję **określone zakresy adresów IP**.

1. W obszarze **zakresy adresów IP dla wyzwalaczy**określ zakresy adresów IP akceptowane przez wyzwalacz.

   Prawidłowy zakres adresów IP używa następujących formatów: *x. x. x. x/x* lub *x. x. x. x-x. x. x. x*

Jeśli aplikacja logiki ma być wyzwalana tylko jako zagnieżdżona aplikacja logiki, z listy **dozwolone przychodzące adresy IP** wybierz **tylko inne Logic Apps**. Ta opcja umożliwia zapisanie pustej tablicy do zasobu aplikacji logiki. Dzięki temu tylko wywołania z usługi Logic Apps (nadrzędne Aplikacje logiki) mogą wyzwolić zagnieżdżoną aplikację logiki.

> [!NOTE]
> Niezależnie od adresu IP można nadal uruchamiać aplikację logiki, która ma wyzwalacz oparty na żądaniach za `/triggers/<trigger-name>/run` pośrednictwem interfejsu API REST platformy Azure lub za pośrednictwem API Management. Jednak ten scenariusz nadal wymaga [uwierzytelniania](../active-directory/develop/authentication-scenarios.md) względem interfejsu API REST platformy Azure. Wszystkie zdarzenia pojawiają się w dzienniku inspekcji platformy Azure. Upewnij się, że zasady kontroli dostępu zostały odpowiednio skonfigurowane.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Ogranicz zakresy adresów IP dla ruchu przychodzącego w szablonie Azure Resource Manager

W przypadku [automatyzowania wdrażania aplikacji logiki za pomocą szablonów Menedżer zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można określić zakresy adresów IP w formacie *x. x. x. x/x* lub *x. x. x. x-x* . x. x. x przy użyciu sekcji i, `accessControl` dołączając `triggers` `actions` sekcje i w definicji zasobu aplikacji logiki, na przykład:

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
                  "allowedCallerIpAddresses:" : []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Dodawanie Azure Active Directory otwieranie uwierzytelniania lub inne zabezpieczenia

Aby dodać więcej protokołów [uwierzytelniania](../active-directory/develop/authentication-scenarios.md) do aplikacji logiki, rozważ użycie usługi [Azure API Management](../api-management/api-management-key-concepts.md) . Ta usługa ułatwia uwidocznienie aplikacji logiki jako interfejsu API i oferuje zaawansowane monitorowanie, zabezpieczenia, zasady i dokumentację dla dowolnego punktu końcowego. API Management może uwidaczniać publiczny lub prywatny punkt końcowy dla aplikacji logiki. Aby autoryzować dostęp do tego punktu końcowego, można użyć [Azure Active Directory Otwórz uwierzytelnianie](#azure-active-directory-oauth-authentication) (Azure AD OAuth), [certyfikat klienta](#client-certificate-authentication)lub inne standardy zabezpieczeń do autoryzowania dostępu do tego punktu końcowego. Gdy API Management odbiera żądanie, usługa wysyła żądanie do aplikacji logiki, a także przeprowadza wszelkie niezbędne przekształcenia lub ograniczenia. Aby umożliwić API Management Wyzwól aplikację logiki, możesz użyć ustawień zakresu przychodzącego adresu IP aplikacji logiki.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Dostęp do operacji aplikacji logiki

Można zezwolić tylko określonym użytkownikom lub grupom na uruchamianie określonych zadań, takich jak zarządzanie, edytowanie i przeglądanie aplikacji logiki. Aby kontrolować ich uprawnienia, użyj [Access Control opartej na rolach (RBAC) platformy Azure](../role-based-access-control/role-assignments-portal.md) , aby można było przypisywać niestandardowe lub wbudowane role do członków w ramach subskrypcji platformy Azure:

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

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja kontroli dostępu**  >  **dozwolone adresy IP dla ruchu przychodzącego**wybierz pozycję **określone zakresy adresów IP**.

1. W obszarze **zakresy adresów IP dla pozycji zawartość**określ zakresy adresów IP, które mogą uzyskiwać dostęp do zawartości z danych wejściowych i wyjściowych. 

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

1. Włącz opcję **Zabezpiecz dane wejściowe**, **bezpieczne wyjście**lub oba te elementy. Po zakończeniu wybierz pozycję **Gotowe**.

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

* [Interfejs API Logic Apps obsługujący historię przepływu pracy](https://docs.microsoft.com/rest/api/logic/) nie zwraca zabezpieczonych danych wyjściowych.

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
> Jeśli używasz parametru w nagłówku żądania lub treści, ten parametr może być widoczny podczas wyświetlania historii uruchamiania aplikacji logiki i wychodzącego żądania HTTP. Upewnij się, że ustawiono również zasady dostępu do zawartości. Można również użyć [zaciemniania](#obfuscate) , aby ukryć wejścia i wyjścia w historii uruchamiania. Nagłówki autoryzacji nigdy nie są widoczne za poorednictwem danych wejściowych lub wyjściowych. Dlatego jeśli w tym miejscu jest używany wpis tajny, ten klucz tajny nie jest możliwy do pobierania.

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
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Dostęp do usług i systemów wywoływanych z usługi Logic Apps

Oto kilka sposobów zabezpieczania punktów końcowych, które odbierają wywołania lub żądania z aplikacji logiki:

* Dodawanie uwierzytelniania do żądań wychodzących.

  Podczas pracy z wyzwalaczem lub akcją opartą na protokole HTTP, która wykonuje wywołania wychodzące, takie jak HTTP, HTTP + Swagger lub element webhook, można dodać uwierzytelnianie do żądania wysyłanego przez aplikację logiki. Można na przykład wybrać następujące typy uwierzytelniania:

  * [Uwierzytelnianie podstawowe](#basic-authentication)

  * [Uwierzytelnianie certyfikatu klienta](#client-certificate-authentication)

  * [Active Directory uwierzytelniania OAuth](#azure-active-directory-oauth-authentication)

  * [Uwierzytelnianie tożsamości zarządzanej](#managed-identity-authentication)
  
  Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do wywołań wychodzących](#add-authentication-outbound) w dalszej części tego tematu.

* Ogranicz dostęp z adresów IP aplikacji logiki.

  Wszystkie wywołania punktów końcowych z usługi Logic Apps pochodzą z określonych wyznaczone adresy IP, które są oparte na regionach aplikacji logiki. Można dodać filtrowanie akceptujące żądania tylko z tych adresów IP. Aby uzyskać te adresy IP, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Zwiększ bezpieczeństwo połączeń z systemami lokalnymi.

  Azure Logic Apps zapewnia integrację z tymi usługami, aby zapewnić lepszą bezpieczną i niezawodną komunikację lokalną.

  * Lokalna brama danych

    Wiele zarządzanych łączników w Azure Logic Apps ułatwiają bezpieczne połączenia z systemami lokalnymi, takimi jak system plików, SQL, SharePoint i DB2. Brama wysyła dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem Azure Service Bus. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Dowiedz się [, jak działa lokalna Brama danych](logic-apps-gateway-install.md#gateway-cloud-service).

  * Nawiązywanie połączenia za pomocą usługi Azure API Management

    [Usługa Azure API Management](../api-management/api-management-key-concepts.md) udostępnia opcje połączenia lokalnego, takie jak wirtualna sieć prywatna typu lokacja-lokacja i integracja ExpressRoute dla zabezpieczonego serwera proxy i komunikacji z systemami lokalnymi. W przepływie pracy aplikacji logiki w Projektancie aplikacji logiki można wybrać interfejs API, który jest udostępniany przez API Management, który zapewnia szybki dostęp do systemów lokalnych.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Dodawanie uwierzytelniania do wywołań wychodzących

Punkty końcowe HTTP i HTTPS obsługują różne rodzaje uwierzytelniania. Na podstawie wyzwalacza lub akcji, która służy do wykonywania wywołań wychodzących lub żądań, które uzyskują dostęp do tych punktów końcowych, można wybierać spośród różnych zakresów typów uwierzytelniania. Aby upewnić się, że zabezpieczasz wszelkie poufne informacje, które obsługuje aplikacja logiki, użyj zabezpieczonych parametrów i Koduj dane w razie potrzeby. Aby uzyskać więcej informacji o używaniu i zabezpieczaniu parametrów, zobacz [dostęp do danych wejściowych parametrów](#secure-action-parameters).

> [!NOTE]
> W Projektancie aplikacji logiki Właściwość **Authentication** może być ukryta w niektórych wyzwalaczach i akcjach, w których można określić typ uwierzytelniania. Aby właściwość była wyświetlana w takich przypadkach, na wyzwalaczu lub akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **uwierzytelnianie**. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie dostępu przy użyciu tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Typ uwierzytelniania | Obsługiwane przez |
|---------------------|--------------|
| [Podstawowe](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Certyfikat klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Nieprzetworzone](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
| [Tożsamość zarządzana](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, element webhook protokołu HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Jeśli opcja [podstawowa](../active-directory-b2c/secure-rest-api.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Yes | Podstawowy | Typ uwierzytelniania do użycia |
| **Uż** | `username` | Yes | <*Nazwa użytkownika*>| Nazwa użytkownika służąca do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
| **Hasło** | `password` | Yes | <*hasło*> | Hasło do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
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

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Yes | **Certyfikat klienta** <br>lub <br>`ClientCertificate` | Typ uwierzytelniania do użycia dla certyfikatów klienta protokołu TLS/SSL <p><p>**Uwaga**: w przypadku certyfikatów z podpisem własnym certyfikaty z podpisem własnym dla protokołów TLS/SSL nie są obsługiwane. Łącznik protokołu HTTP nie obsługuje pośrednich certyfikatów protokołu TLS/SSL. |
| **PFX** | `pfx` | Yes | <*zakodowany plik PFX — zawartość*> | Zawartość zakodowana algorytmem Base64 z pliku wymiany informacji osobistych (PFX) <p><p>Aby przekonwertować plik PFX na format szyfrowany algorytmem Base64, można użyć programu PowerShell, wykonując następujące czynności: <p>1. Zapisz zawartość certyfikatu w zmiennej: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Przekonwertuj zawartość certyfikatu przy użyciu `ToBase64String()` funkcji i Zapisz tę zawartość do pliku tekstowego: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
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

Wyzwalacze żądań umożliwiają uwierzytelnianie wywołań przychodzących po [skonfigurowaniu zasad autoryzacji usługi Azure AD](#enable-oauth) dla aplikacji logiki przy użyciu [Azure Active Directory Open Authentication](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth). Dla wszystkich innych wyzwalaczy i akcji, które zapewniają **Active Directory** typ uwierzytelniania OAuth do wybrania, określ następujące wartości właściwości:

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Yes | **Active Directory OAuth** <br>lub <br>`ActiveDirectoryOAuth` | Typ uwierzytelniania do użycia. Logic Apps jest obecnie zgodny z [protokołem OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Urząd** | `authority` | Nie | <*Adres URL-urząd-token-wystawca*> | Adres URL urzędu dostarczającego token uwierzytelniania. Domyślnie ta wartość to `https://login.windows.net` . |
| **Dzierżawa** | `tenant` | Yes | <*Identyfikator dzierżawy*> | Identyfikator dzierżawy dla dzierżawy usługi Azure AD |
| **Grupy odbiorców** | `audience` | Yes | <*zasób do autoryzacji*> | Zasób, który ma być używany na potrzeby autoryzacji, na przykład`https://management.core.windows.net/` |
| **Client ID (Identyfikator klienta)** | `clientId` | Yes | <*Identyfikator klienta*> | Identyfikator klienta aplikacji żądającej autoryzacji |
| **Typ poświadczeń** | `credentialType` | Yes | Certyfikat <br>lub <br>Wpis tajny | Typ poświadczeń, którego klient używa do żądania autoryzacji. Ta właściwość i wartość nie pojawiają się w podstawowej definicji aplikacji logiki, ale określają właściwości, które są wyświetlane dla wybranego typu poświadczenia. |
| **Wpis tajny** | `secret` | Tak, ale tylko dla typu poświadczeń "wpis tajny" | <*Klient-klucz tajny*> | Wpis tajny klienta na potrzeby żądania autoryzacji |
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

| Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Yes | Nieprzetworzone | Typ uwierzytelniania do użycia |
| **Wartość** | `value` | Yes | <*Authorization-header-Value*> | Wartość nagłówka autoryzacji do użycia na potrzeby uwierzytelniania |
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

Jeśli dostępna jest opcja [tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) , aplikacja logiki może użyć tożsamości przypisanej do systemu lub *pojedynczej* ręcznie utworzonej tożsamości przypisanej przez użytkownika do uwierzytelniania dostępu do zasobów w innych dzierżawach Azure Active Directory (Azure AD) bez logowania. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie trzeba podawać ani obrócić wpisów tajnych. Dowiedz się więcej [na temat usług platformy Azure, które obsługują tożsamości zarządzane na potrzeby uwierzytelniania w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Aby aplikacja logiki mogła korzystać z tożsamości zarządzanej, wykonaj kroki opisane w temacie [uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Te kroki umożliwiają włączenie zarządzanej tożsamości w aplikacji logiki oraz skonfigurowanie dostępu tej tożsamości do docelowego zasobu platformy Azure.

1. Zanim funkcja platformy Azure będzie mogła korzystać z tożsamości zarządzanej, należy najpierw [włączyć uwierzytelnianie dla usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. W wyzwalaczu lub akcji, w której ma być używana tożsamość zarządzana, określ następujące wartości właściwości:

   | Właściwość (Projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentication** | `type` | Yes | **Tożsamość zarządzana** <br>lub <br>`ManagedServiceIdentity` | Typ uwierzytelniania do użycia |
   | **Tożsamość zarządzana** | `identity` | Yes | * **Tożsamość zarządzana przypisana przez system** <br>lub <br>`SystemAssigned` <p><p>* <*przypisanej do użytkownika-Identity-Name*> | Zarządzana tożsamość do użycia |
   | **Grupy odbiorców** | `audience` | Yes | <*docelowy — identyfikator zasobu*> | Identyfikator zasobu dla zasobu docelowego, do którego chcesz uzyskać dostęp. <p>Załóżmy na przykład, że `https://storage.azure.com/` [tokeny dostępu](../active-directory/develop/access-tokens.md) są prawidłowe dla wszystkich kont magazynu. Można jednak określić adres URL usługi głównej, `https://fabrikamstorageaccount.blob.core.windows.net` na przykład dla określonego konta magazynu. <p>**Uwaga**: Właściwość **odbiorców** może być ukryta w niektórych wyzwalaczach lub akcjach. Aby ta właściwość była widoczna, w wyzwalaczu lub akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **odbiorcy**. <p><p>**Ważne**: Upewnij się, że identyfikator zasobu docelowego *dokładnie pasuje* do wartości oczekiwanej przez usługę Azure AD, w tym wszystkich wymaganych końcowych ukośników. W związku z tym `https://storage.azure.com/` Identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga końcowej kreski ułamkowej. Aby znaleźć te identyfikatory zasobów, zobacz [usługi platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
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

## <a name="next-steps"></a>Następne kroki

* [Automatyzowanie wdrażania Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnozowanie błędów i problemów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Tworzenie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
