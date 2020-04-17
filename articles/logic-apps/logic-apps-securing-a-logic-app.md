---
title: Zabezpieczanie dostępu i danych
description: Bezpieczny dostęp do danych wejściowych, danych wyjściowych, wyzwalaczy opartych na żądaniach, historia uruchamiania, zadania zarządzania i dostęp do innych zasobów w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: e591a7035db82425952a16f5c4c220e25d8517fe
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457182"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Bezpieczny dostęp i dane w usłudze Azure Logic Apps

Aby kontrolować dostęp i chronić dane w usłudze Azure Logic Apps, można skonfigurować zabezpieczenia w następujących obszarach:

* [Dostęp do wyzwalaczy opartych na żądaniach](#secure-triggers)
* [Dostęp do operacji aplikacji logiki](#secure-operations)
* [Dostęp do uruchamiania danych wejściowych i wyjściowych historii](#secure-run-history)
* [Dostęp do danych wejściowych parametrów](#secure-action-parameters)
* [Dostęp do usług i systemów wywoływanych z aplikacji logicznych](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Dostęp do wyzwalaczy opartych na żądaniach

Jeśli aplikacja logiki używa wyzwalacza opartego na żądaniu, który odbiera przychodzące wywołania lub żądania, takie jak [żądanie](../connectors/connectors-native-reqres.md) lub [webhook](../connectors/connectors-native-webhook.md) wyzwalacza, można ograniczyć dostęp, dzięki czemu tylko autoryzowani klienci mogą wywołać aplikację logiki. Wszystkie żądania odebrane przez aplikację logiki są szyfrowane i zabezpieczone protokołem TLS (Transport Layer Security), wcześniej znanym jako Secure Sockets Layer (SSL).

Oto opcje, które mogą pomóc w zabezpieczeniu dostępu do tego typu wyzwalacza:

* [Generowanie podpisów dostępu współdzielonego](#sas)
* [Ograniczanie przychodzących adresów IP](#restrict-inbound-ip-addresses)
* [Dodawanie usługi Azure Active Directory OAuth lub innych zabezpieczeń](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generowanie sygnatur dostępu współdzielonego (SAS)

Każdy punkt końcowy żądania w aplikacji logiki ma [sygnaturę dostępu współdzielonego (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) w adresie URL punktu końcowego, który jest zgodny z tym formatem:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Każdy adres `sp`URL `sv`zawiera `sig` parametr , i kwerendy, jak opisano w tej tabeli:

| Parametr kwerendy | Opis |
|-----------------|-------------|
| `sp` | Określa uprawnienia do używania dozwolonych metod HTTP. |
| `sv` | Określa wersję sygnatury dostępu Współdzielonego do generowania podpisu. |
| `sig` | Określa podpis używany do uwierzytelniania dostępu do wyzwalacza. Ten podpis jest generowany przy użyciu algorytmu SHA256 z kluczem dostępu tajnego na wszystkich ścieżkach i właściwościach adresu URL. Nigdy nie naświetlone lub opublikowane, ten klucz jest szyfrowany i przechowywane za pomocą aplikacji logiki. Aplikacja logiki autoryzuje tylko te wyzwalacze, które zawierają prawidłowy podpis utworzony za pomocą klucza tajnego. |
|||

Aby uzyskać więcej informacji na temat zabezpieczania dostępu za pomocą sygnatury dostępu współdzielonego, zobacz następujące sekcje w tym temacie:

* [Generowanie ponowne kluczy dostępu](#access-keys)
* [Tworzenie wygasających adresów URL wywołań zwrotnych](#expiring-urls)
* [Tworzenie adresów URL przy przy tym przy za pomocą klucza podstawowego lub pomocniczego](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Aby wygenerować nowy klucz dostępu do zabezpieczeń w dowolnym momencie, użyj interfejsu API usługi Azure REST lub witryny Azure portal. Wszystkie wcześniej wygenerowane adresy URL, które używają starego klucza, są unieważnione i nie mają już autoryzacji do wyzwalania aplikacji logiki. Adresy URL pobierane po regeneracji są podpisane za pomocą nowego klucza dostępu.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki, która ma klucz, który chcesz ponownie wygenerować.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Klawisze dostępu**.

1. Wybierz klucz, który chcesz ponownie wygenerować i zakończyć proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Tworzenie wygasających adresów URL wywołań zwrotnych

Jeśli udostępniasz adres URL punktu końcowego wyzwalacza opartego na żądaniu innym stronom, możesz wygenerować adresy URL wywołania zwrotnego, które używają określonych kluczy i mają daty wygaśnięcia. W ten sposób można bezproblemowo rolki kluczy lub ograniczyć dostęp do wyzwalania aplikacji logiki na podstawie określonego czasu. Aby określić datę wygaśnięcia adresu URL, użyj [interfejsu API REST aplikacji logiki,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)na przykład:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści uwzględnić `NotAfter`właściwość przy użyciu ciągu daty JSON. Ta właściwość zwraca adres URL wywołania `NotAfter` zwrotnego, który jest prawidłowy tylko do daty i godziny.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Tworzenie adresów URL przy przy tym przy za pomocą podstawowego lub pomocniczego klucza tajnego

Podczas generowania adresów URL wywołań zwrotnych lub listy dla wyzwalacza opartego na żądaniu można określić klucz do podpisywania adresu URL. Aby wygenerować adres URL podpisany za pomocą określonego klucza, użyj [interfejsu API REST aplikacji logiki,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)na przykład:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

W treści należy `KeyType` uwzględnić właściwość jako jeden `Primary` lub `Secondary`. Ta właściwość zwraca adres URL, który jest podpisany przez określony klucz zabezpieczeń.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Ograniczanie przychodzących adresów IP

Wraz z sygnaturą dostępu współdzielonego (SAS) można w szczególności ograniczyć klientów, którzy mogą wywoływać aplikację logiki. Na przykład jeśli zarządzasz punktem końcowym żądania przy użyciu usługi Azure API Management, można ograniczyć aplikacji logiki do akceptowania żądań tylko z adresu IP dla wystąpienia usługi API Management.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Ograniczanie przychodzących zakresów adresów IP w witrynie Azure portal

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja** > kontroli dostępu**Dozwolone przychodzące adresy IP**wybierz pozycję **Określone zakresy adresów IP**.

1. W obszarze **Zakresy adresów IP dla wyzwalaczy**określ zakresy adresów IP akceptowane przez wyzwalacz.

   Prawidłowy zakres adresów IP używa następujących formatów: *x.x.x.x/x* lub *x.x.x.x.x.x.x*

Jeśli chcesz, aby aplikacja logiki wyzwalała tylko jako zagnieżdżoną aplikację logiki, z listy **Dozwolone przychodzące adresy IP** wybierz pozycję **Tylko inne aplikacje logiki**. Ta opcja zapisuje pustą tablicę do zasobu aplikacji logiki. W ten sposób tylko wywołania z usługi Logic Apps (nadrzędne aplikacje logiki) mogą wyzwalać zagnieżdżoną aplikację logiki.

> [!NOTE]
> Niezależnie od adresu IP nadal można uruchomić aplikację logiki, która `/triggers/<trigger-name>/run` ma wyzwalacz oparty na żądaniu przy użyciu interfejsu API rest platformy Azure lub za pośrednictwem usługi API Management. Jednak ten scenariusz nadal wymaga uwierzytelniania względem interfejsu API rest platformy Azure. Wszystkie zdarzenia są wyświetlane w dzienniku inspekcji platformy Azure. Upewnij się, że odpowiednio ustawiono zasady kontroli dostępu.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Ograniczanie przychodzących zakresów adresów IP w szablonie usługi Azure Resource Manager

W przypadku [automatyzacji wdrażania aplikacji logiki przy użyciu szablonów Menedżera zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) `accessControl` można określić zakresy adresów IP za pomocą sekcji z sekcją `triggers` w definicji zasobów aplikacji logiki, na przykład:

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
               "triggers": {
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

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Dodawanie usługi Azure Active Directory OAuth lub innych zabezpieczeń

Aby dodać więcej protokołów autoryzacji do aplikacji logiki, należy rozważyć użycie usługi [Azure API Management.](../api-management/api-management-key-concepts.md) Ta usługa ułatwia udostępnianie aplikacji logiki jako interfejsu API i oferuje zaawansowane monitorowanie, zabezpieczenia, zasady i dokumentację dla dowolnego punktu końcowego. Zarządzanie interfejsami API może udostępnić publiczny lub prywatny punkt końcowy dla aplikacji logiki. Aby autoryzować dostęp do tego punktu końcowego, można użyć [usługi Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), [certyfikat klienta](#client-certificate-authentication)lub inne standardy zabezpieczeń do autoryzowania dostępu do tego punktu końcowego. Gdy usługa API Management odbiera żądanie, usługa wysyła żądanie do aplikacji logiki, również dokonywania wszelkich niezbędnych przekształceń lub ograniczeń po drodze. Aby umożliwić tylko zarządzanie interfejsami API wyzwalać aplikację logiki, można użyć aplikacji logiki przychodzących ustawień zakresu adresów IP.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Dostęp do operacji aplikacji logiki

Można zezwolić tylko określonym użytkownikom lub grupom na uruchamianie określonych zadań, takich jak zarządzanie, edytowanie i wyświetlanie aplikacji logiki. Aby kontrolować ich uprawnienia, należy użyć [kontroli dostępu opartej na rolach platformy Azure (RBAC),](../role-based-access-control/role-assignments-portal.md) aby przypisać dostosowane lub wbudowane role do członków w ramach subskrypcji platformy Azure:

* [Współautor aplikacji logiki:](../role-based-access-control/built-in-roles.md#logic-app-contributor)umożliwia zarządzanie aplikacjami logicznymi, ale nie można zmienić dostępu do nich.

* [Operator aplikacji logiki:](../role-based-access-control/built-in-roles.md#logic-app-operator)umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki, ale nie można ich edytować ani aktualizować.

Aby uniemożliwić innym osobom zmianę lub usunięcie aplikacji logiki, można użyć [blokady zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md). Ta funkcja uniemożliwia innym osobom zmienianie lub usuwanie zasobów produkcyjnych.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Dostęp do uruchamiania danych historii

Podczas uruchamiania aplikacji logiki wszystkie dane [są szyfrowane podczas przesyłania](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) przy użyciu zabezpieczeń warstwy transportowej (TLS) i w [spoczynku.](../security/fundamentals/encryption-atrest.md) Po zakończeniu działania aplikacji logiki, można wyświetlić historię dla tego uruchomienia, w tym kroki, które zostały uruchomione wraz ze stanem, czas trwania, dane wejściowe i wyjścia dla każdej akcji. Ten bogaty szczegół zapewnia wgląd w sposób uruchomiony aplikacji logiki i gdzie można rozpocząć rozwiązywanie problemów, które powstają.

Podczas wyświetlania historii uruchamiania aplikacji logiki, aplikacje logiki uwierzytelnia dostęp, a następnie udostępnia łącza do danych wejściowych i wyjściowych dla żądań i odpowiedzi dla każdego uruchomienia. Jednak dla akcji, które obsługują wszelkie hasła, wpisy tajne, klucze lub inne poufne informacje, należy uniemożliwić innym osobom wyświetlanie i uzyskiwanie dostępu do tych danych. Na przykład jeśli aplikacja logiki pobiera klucz tajny z [usługi Azure Key Vault](../key-vault/general/overview.md) do użycia podczas uwierzytelniania akcji HTTP, chcesz ukryć ten klucz tajny z widoku.

Aby kontrolować dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki, dostępne są następujące opcje:

* [Ograniczanie dostępu według zakresu adresów IP](#restrict-ip).

  Ta opcja ułatwia bezpieczny dostęp do historii uruchamiania na podstawie żądań z określonego zakresu adresów IP.

* [Ukryj dane z historii uruchamiania za pomocą zaciemniania](#obfuscate).

  W wielu wyzwalaczy i akcji można ukryć ich dane wejściowe, dane wyjściowe lub oba z historii uruchamiania aplikacji logiki.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Ograniczanie dostępu według zakresu adresów IP

Można ograniczyć dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki, dzięki czemu tylko żądania z określonych zakresów adresów IP mogą wyświetlać te dane. Na przykład, aby zablokować komukolwiek dostęp do wejść i wyjść, należy określić zakres adresów IP, takich jak `0.0.0.0-0.0.0.0`. Tylko osoba z uprawnieniami administratora może usunąć to ograniczenie, które zapewnia możliwość dostępu "just-in-time" do danych aplikacji logiki. Można określić zakresy adresów IP, aby ograniczyć za pomocą witryny Azure portal lub szablonu usługi Azure Resource Manager, który jest używany do wdrażania aplikacji logiki.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Ograniczanie zakresów adresów IP w witrynie Azure portal

1. W witrynie Azure portal otwórz aplikację logiki w projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Konfiguracja** > kontroli dostępu**Dozwolone przychodzące adresy IP**wybierz pozycję **Określone zakresy adresów IP**.

1. W obszarze **Zakresy adresów IP dla zawartości**określ zakresy adresów IP, które mogą uzyskiwać dostęp do zawartości z wejść i wyjść. 

   Prawidłowy zakres adresów IP używa następujących formatów: *x.x.x.x/x* lub *x.x.x.x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Ograniczanie zakresów adresów IP w szablonie usługi Azure Resource Manager

W przypadku [automatyzacji wdrażania aplikacji logiki przy użyciu szablonów Menedżera zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) `accessControl` można określić zakresy adresów IP za pomocą sekcji z sekcją `contents` w definicji zasobów aplikacji logiki, na przykład:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Ukrywanie danych z historii uruchamiania przy użyciu zaciemniania

Wiele wyzwalaczy i akcji ma ustawienia, aby ukryć dane wejściowe, dane wyjściowe lub oba z historii uruchamiania aplikacji logiki. Oto kilka [zagadnień do sprawdzenia](#obfuscation-considerations) podczas korzystania z tych ustawień, aby ułatwić zabezpieczanie tych danych.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Ukrywanie wejść i wyjść w projektancie

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

   ![Otwórz aplikację logiki w projektancie aplikacji logiki](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. W wyzwalaczu lub akcji, w której chcesz ukryć poufne dane, wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz wyzwalacz lub ustawienia akcji](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Włącz bezpieczne **wejścia,** **bezpieczne wyjścia**lub oba. Po zakończeniu wybierz pozycję **Gotowe**.

   ![Włącz "Bezpieczne wejścia" lub "Bezpieczne wyjścia"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Akcja lub wyzwalacz pokazuje teraz ikonę kłódki na pasku tytułu.

   ![Pasek tytułu akcji lub wyzwalacza pokazuje ikonę kłódki](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokeny reprezentujące zabezpieczone dane wyjściowe z poprzednich akcji również wyświetlą ikony blokady. Na przykład po wybraniu takiego wyjścia z listy zawartości dynamicznej do użycia w akcji, że token pokazuje ikonę kłódki.

   ![Wybierz token dla zabezpieczonych danych wyjściowych](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Po uruchomieniu aplikacji logiki można wyświetlić historię dla tego uruchomienia.

   1. W okienku **Przegląd** aplikacji logiki wybierz przebieg, który chcesz wyświetlić.

   1. W okienku **uruchamiania aplikacji logiki** rozwiń akcje, które chcesz przejrzeć.

      Jeśli wybrano zasłanianie zarówno danych wejściowych, jak i wyjść, wartości te są teraz wyświetlane jako ukryte.

      ![Ukryte wejścia i wyjścia w historii przebiegu](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Ukrywanie wejść i wyjść w widoku kodu

W podstawowej definicji wyzwalacza lub `runtimeConfiguration.secureData.properties` akcji dodaj lub zaktualizuj tablicę za pomocą jednej lub obu z tych wartości:

* `"inputs"`: Zabezpiecza dane wejściowe w historii przebiegu.
* `"outputs"`: Zabezpiecza dane wyjściowe w historii przebiegu.

Oto kilka [zagadnień do sprawdzenia](#obfuscation-considerations) podczas korzystania z tych ustawień, aby ułatwić zabezpieczanie tych danych.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Zagadnienia dotyczące ukrywania danych wejściowych i wyjściowych

* Po zasłonieniu danych wejściowych lub wyjściowych wyzwalacza lub akcji, aplikacje logiki nie wysyła zabezpieczonych danych do usługi Azure Log Analytics. Ponadto nie można dodać [śledzone właściwości](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) do tego wyzwalacza lub akcji do monitorowania.

* [Interfejs API aplikacji logiki do obsługi historii przepływu pracy](https://docs.microsoft.com/rest/api/logic/) nie zwraca zabezpieczonych wyjść.

* Aby ukryć dane wyjściowe z akcji, która zasłania dane wejściowe lub jawnie zasłania dane wyjściowe, ręcznie włącz **bezpieczne wyjścia** w tej akcji.

* Upewnij się, że **włączysz bezpieczne wejścia** lub bezpieczne wyjścia w **dalszych działaniach,** w których oczekujesz, że historia uruchamiania zasłania te dane.

  **Ustawienie Bezpieczne wyjścia**

  Po ręcznym włączeniu **bezpieczne dane wyjściowe** w wyzwalaczu lub akcji, aplikacje logiki zabezpiecza te dane wyjściowe w historii uruchamiania. Jeśli akcja niższego rzędu jawnie używa tych zabezpieczonych wyjść jako danych wejściowych, aplikacje logiki ukrywa dane wejściowe tej akcji w historii uruchamiania, ale *nie włącza* ustawienia bezpieczne **dane wejściowe** akcji.

  ![Zabezpieczone wyjścia jako wejścia i wpływ na większość działań](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Akcje Compose, Parse JSON i Response ma tylko ustawienie **Bezpieczne dane wejściowe.** Po włączeniu ustawienie powoduje również ukrycie danych wyjściowych tych akcji. Jeśli te akcje jawnie używają zabezpieczonych wyjść nadrzędnych jako danych wejściowych, aplikacje logiki ukrywa te akcje dane wejściowe i wyjściowe, ale *nie włącza* tych akcji **secure inputs** ustawienie. Jeśli akcja niższego rzędu jawnie używa ukrytych wyjść z akcji Compose, Parse JSON lub Response jako danych wejściowych, aplikacje logiki *nie ukrywają danych wejściowych lub wyjściowych tej dalszej akcji.*

  ![Zabezpieczone wyjścia jako wejścia o wpływie na konkretne działania](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Ustawienie bezpieczne wejścia**

  Po ręcznym włączeniu **bezpieczne dane wejściowe** w wyzwalaczu lub akcji, aplikacje logiki zabezpiecza te dane wejściowe w historii uruchamiania. Jeśli akcja podrzędna jawnie używa widocznych wyjść z tego wyzwalacza lub akcji jako dane wejściowe, aplikacje logiki ukrywa dane wejściowe tej dalszej akcji w historii uruchamiania, ale *nie włącza* **bezpiecznych danych wejściowych** w tej akcji i nie ukrywa danych wyjściowych tej akcji.

  ![Zabezpieczone nakłady i wpływ na większość działań](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Jeśli compose, Parse JSON i Response akcje jawnie używać widocznych wyjść z wyzwalacza lub akcji, która ma zabezpieczone dane wejściowe, aplikacje logiki ukrywa te akcje dane wejściowe i wyjściowe, ale *nie włącza* tych akcji **bezpieczne dane wejściowe** ustawienie. Jeśli akcja niższego rzędu jawnie używa ukrytych wyjść z akcji Compose, Parse JSON lub Response jako danych wejściowych, aplikacje logiki *nie ukrywają danych wejściowych lub wyjściowych tej dalszej akcji.*

  ![Zabezpieczone nakłady i wpływ na konkretne działania](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Dostęp do danych wejściowych parametrów

Jeśli wdrażasz w różnych środowiskach, należy rozważyć parametryzację wartości w definicji przepływu pracy, które różnią się w zależności od tych środowisk. W ten sposób można uniknąć zakodowanych danych przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) do wdrażania aplikacji logiki, ochrony poufnych danych przez zdefiniowanie zabezpieczonych parametrów i przekazywania tych danych jako oddzielnych danych wejściowych za pomocą [parametrów szablonu](../azure-resource-manager/templates/template-parameters.md) przy użyciu [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

Na przykład jeśli uwierzytelniasz akcje HTTP za pomocą [usługi Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication)można zdefiniować i zasłonić parametry, które akceptują identyfikator klienta i klucz tajny klienta, które są używane do uwierzytelniania. Aby zdefiniować te parametry w `parameters` aplikacji logiki, należy użyć sekcji w definicji przepływu pracy aplikacji logiki i szablonu Menedżera zasobów do wdrożenia. Aby ukryć wartości parametrów, które nie mają być wyświetlane podczas edytowania aplikacji `securestring` logiki lub wyświetlania historii uruchamiania, należy zdefiniować parametry przy użyciu lub `secureobject` tekst i używać kodowania w razie potrzeby. Parametry, które mają tego typu nie są zwracane z definicją zasobu i nie są dostępne podczas wyświetlania zasobu po wdrożeniu. Aby uzyskać dostęp do tych wartości `@parameters('<parameter-name>')` parametrów w czasie wykonywania, należy użyć wyrażenia wewnątrz definicji przepływu pracy. To wyrażenie jest oceniane tylko w czasie wykonywania i jest opisane przez [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Jeśli używasz parametru w nagłówku lub treści żądania, ten parametr może być widoczny podczas wyświetlania historii uruchamiania aplikacji logiki i wychodzącego żądania HTTP. Upewnij się, że odpowiednio ustawisz zasady dostępu do zawartości. Zaciemnienie można również użyć do [ukrycia](#obfuscate) wejść i wyjść w historii uruchamiania. Nagłówki autoryzacji nigdy nie są widoczne za pośrednictwem danych wejściowych lub wyjściowych. Więc jeśli tajny jest używany tam, że klucz tajny nie jest możliwe do odzyskania.

Aby uzyskać więcej informacji, zobacz te sekcje w tym temacie:

* [Bezpieczne parametry w definicjach przepływów pracy](#secure-parameters-workflow)
* [Ukrywanie danych z historii uruchamiania przy użyciu zaciemniania](#obfuscate)

W przypadku [automatyzacji wdrażania dla aplikacji logiki przy użyciu szablonów Menedżera zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można zdefiniować `securestring` `secureobject` parametry zabezpieczonego [szablonu](../azure-resource-manager/templates/template-parameters.md), które są oceniane podczas wdrażania, przy użyciu i typów. Aby zdefiniować parametry szablonu, użyj `parameters` sekcji najwyższego poziomu szablonu, która `parameters` jest oddzielona i różni się od sekcji definicji przepływu pracy. Aby podać wartości parametrów szablonu, należy użyć oddzielnego [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

Na przykład jeśli używasz wpisów tajnych, można zdefiniować i używać zabezpieczonych parametrów szablonu, które pobierają te wpisy tajne z [usługi Azure Key Vault](../key-vault/general/overview.md) podczas wdrażania. Następnie można odwołać się do magazynu kluczy i klucza tajnego w pliku parametrów. Więcej informacji można znaleźć w następujących tematach:

* [Przekazywanie poufnych wartości podczas wdrażania przy użyciu usługi Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Bezpieczne parametry w szablonach usługi Azure Resource Manager](#secure-parameters-deployment-template) w dalszej części tego tematu

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Bezpieczne parametry w definicjach przepływów pracy

Aby chronić poufne informacje w definicji przepływu pracy aplikacji logiki, należy użyć zabezpieczonych parametrów, aby te informacje nie były widoczne po zapisaniu aplikacji logiki. Załóżmy na przykład, że akcja HTTP wymaga uwierzytelniania podstawowego, które używa nazwy użytkownika i hasła. W definicji przepływu `parameters` pracy sekcja `basicAuthPasswordParam` definiuje `basicAuthUsernameParam` parametry i `securestring` parametry przy użyciu typu. Definicja akcji następnie odwołuje się `authentication` do tych parametrów w sekcji.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Bezpieczne parametry w szablonach usługi Azure Resource Manager

Szablon [Menedżera zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) dla aplikacji `parameters` logiki ma wiele sekcji. Aby chronić hasła, klucze, wpisy tajne i inne poufne informacje, należy zdefiniować zabezpieczone parametry na poziomie szablonu i na poziomie definicji przepływu pracy przy użyciu `securestring` lub `secureobject` typu. Następnie można przechowywać te wartości w [usłudze Azure Key Vault](../key-vault/general/overview.md) i użyć [pliku parametru,](../azure-resource-manager/templates/parameter-files.md) aby odwołać się do magazynu kluczy i klucza tajnego. Następnie szablon pobiera te informacje podczas wdrażania. Aby uzyskać więcej informacji, zobacz [Przekazywanie poufnych wartości podczas wdrażania przy użyciu usługi Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Oto więcej informacji `parameters` na temat tych sekcji:

* Na najwyższym poziomie szablonu `parameters` sekcja definiuje parametry wartości, których szablon używa podczas *wdrażania*. Na przykład te wartości mogą obejmować parametry połączenia dla określonego środowiska wdrażania. Następnie można zapisać te wartości w oddzielnym [pliku parametrów,](../azure-resource-manager/templates/parameter-files.md)co ułatwia zmianę tych wartości.

* Wewnątrz definicji zasobów aplikacji logiki, ale poza `parameters` definicją przepływu pracy, sekcja określa wartości parametrów definicji przepływu pracy. W tej sekcji można przypisać te wartości przy użyciu wyrażeń szablonu, które odwołują się do parametrów szablonu. Te wyrażenia są oceniane podczas wdrażania.

* Wewnątrz definicji przepływu `parameters` pracy sekcja definiuje parametry, które aplikacja logiki używa w czasie wykonywania. Następnie można odwoływać się do tych parametrów wewnątrz przepływu pracy aplikacji logiki przy użyciu wyrażeń definicji przepływu pracy, które są oceniane w czasie wykonywania.

Ten przykładowy szablon, który ma wiele `securestring` zabezpieczonych definicji parametrów, które używają tego typu:

| Nazwa parametru | Opis |
|----------------|-------------|
| `TemplatePasswordParam` | Parametr szablonu akceptujący hasło, które jest następnie przekazywane `basicAuthPasswordParam` do parametru definicji przepływu pracy |
| `TemplateUsernameParam` | Parametr szablonu akceptujący nazwę użytkownika, który jest następnie `basicAuthUserNameParam` przekazywany do parametru definicji przepływu pracy |
| `basicAuthPasswordParam` | Parametr definicji przepływu pracy akceptujący hasło do uwierzytelniania podstawowego w akcji HTTP |
| `basicAuthUserNameParam` | Parametr definicji przepływu pracy akceptujący nazwę użytkownika dla uwierzytelniania podstawowego w akcji HTTP |
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

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Dostęp do usług i systemów wywoływanych z aplikacji logicznych

Oto kilka sposobów, które mogą pomóc w zabezpieczeniu punktów końcowych, które odbierają połączenia lub żądania z aplikacji logiki:

* Dodaj uwierzytelnianie do żądań wychodzących.

  Podczas pracy z wyzwalaczem lub akcją opartą na protokei HTTP, która wywołuje połączenia wychodzące, takie jak HTTP, HTTP + Swagger lub Webhook, można dodać uwierzytelnianie do żądania wysyłanego przez aplikację logiki. Na przykład można użyć tych typów uwierzytelniania:

  * [Uwierzytelnianie podstawowe](#basic-authentication)

  * [Uwierzytelnianie certyfikatu klienta](#client-certificate-authentication)

  * [Uwierzytelnianie OAuth usługi Active Directory](#azure-active-directory-oauth-authentication)

  * [Uwierzytelnianie tożsamości zarządzanej](#managed-identity-authentication)
  
  Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](#add-authentication-outbound) w dalszej części tego tematu.

* Ogranicz dostęp z adresów IP aplikacji logiki.

  Wszystkie wywołania do punktów końcowych z aplikacji logiki pochodzą z określonych adresów IP wyznaczonych, które są oparte na regionach aplikacji logiki. Można dodać filtrowanie, które akceptuje żądania tylko z tych adresów IP. Aby uzyskać te adresy IP, zobacz [Limity i konfiguracja usługi Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Zwiększ bezpieczeństwo połączeń z systemami lokalnymi.

  Usługa Azure Logic Apps zapewnia integrację z tymi usługami, aby zapewnić bezpieczniejszą i bardziej niezawodną komunikację lokalną.

  * Lokalna brama danych

    Wiele łączników zarządzanych w usłudze Azure Logic Apps ułatwia zabezpieczane połączenia z systemami lokalnymi, takimi jak System plików, SQL, SharePoint i DB2. Brama wysyła dane ze źródeł lokalnych w zaszyfrowanych kanałach za pośrednictwem usługi Azure Service Bus. Cały ruch pochodzi jako bezpieczny ruch wychodzący z agenta bramy. Dowiedz [się, jak działa lokalna brama danych](logic-apps-gateway-install.md#gateway-cloud-service).

  * Łączenie się za pośrednictwem usługi Azure API Management

    [Usługa Azure API Management](../api-management/api-management-key-concepts.md) udostępnia opcje połączenia lokalnego, takie jak wirtualna sieć prywatna lokacja-lokacja i integracja usługi ExpressRoute dla zabezpieczonego serwera proxy i komunikacji z systemami lokalnymi. Z przepływu pracy aplikacji logiki w Logic App Designer, można wybrać interfejs API, który jest udostępniany przez zarządzanie interfejsem API, który zapewnia szybki dostęp do systemów lokalnych.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Dodawanie uwierzytelniania do połączeń wychodzących

Punkty końcowe HTTP i HTTPS obsługują różne rodzaje uwierzytelniania. Na podstawie wyzwalacza lub akcji, które są używane do wywołania wychodzącego lub żądań, które uzyskują dostęp do tych punktów końcowych, można wybrać z różnych zakresów typów uwierzytelniania. Aby upewnić się, że chronisz wszelkie poufne informacje obsługiwane przez aplikację logiki, należy użyć zabezpieczonych parametrów i zakodować dane w razie potrzeby. Aby uzyskać więcej informacji na temat używania i zabezpieczania parametrów, zobacz [Dostęp do danych wejściowych parametrów](#secure-action-parameters).

> [!NOTE]
> W logic app designer, **Authentication** właściwość może być ukryte w niektórych wyzwalaczy i akcji, gdzie można określić typ uwierzytelniania. Aby właściwość była wyświetlana w takich przypadkach, w wyzwalaczu lub akcji otwórz listę **Dodaj nowy parametr** i wybierz pozycję **Uwierzytelnianie**. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie dostępu z tożsamością zarządzana](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Typ uwierzytelniania | Obsługiwane przez |
|---------------------|--------------|
| [Podstawowy](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Certyfikat klienta](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Usługa Azure API Management, usługi azure app services, usługi azure, protokół HTTP, HTTP + Swagger, interfejs http webhook |
| [Nieprzetworzone](#raw-authentication) | Usługa Azure API Management, usługi azure app services, usługi azure, protokół HTTP, HTTP + Swagger, interfejs http webhook |
| [Tożsamość zarządzana](#managed-identity-authentication) | Usługa Azure API Management, usługi azure app services, usługi azure, protokół HTTP, HTTP + Swagger, interfejs http webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Jeśli opcja [Basic](../active-directory-b2c/secure-rest-api.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | Podstawowy | Typ uwierzytelniania, którego można użyć |
| **Nazwę użytkownika** | `username` | Tak | <*nazwa użytkownika*>| Nazwa użytkownika do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
| **Hasło** | `password` | Tak | <*Hasło*> | Hasło do uwierzytelniania dostępu do docelowego punktu końcowego usługi |
||||||

Korzystając z [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie usługi Azure Resource Manager do automatyzacji wdrażania,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. W tym przykładzie definicja `type` `Basic` akcji HTTP określa uwierzytelnianie jako i używa [funkcji parameters(),](../logic-apps/workflow-definition-language-functions-reference.md#parameters) aby uzyskać wartości parametrów:

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

Jeśli opcja [Certyfikat klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) jest dostępna, określ następujące wartości właściwości:

| Właściwość (projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | **Certyfikat klienta** <br>lub <br>`ClientCertificate` | Typ uwierzytelniania używany dla certyfikatów klientów TLS/SSL. Podczas gdy certyfikaty z podpisem własnym są obsługiwane, certyfikaty z podpisem własnym dla protokołu TLS/SSL nie są obsługiwane. |
| **Pfx** | `pfx` | Tak | <*zakodowana zawartość pliku pfx*> | Zawartość zakodowana w bazie 64 z pliku wymiany informacji osobistych (PFX) <p><p>Aby przekonwertować plik PFX na format zakodowany w formacie base64, można użyć programu PowerShell, wykonując następujące czynności: <p>1. Zapisz zawartość certyfikatu w zmiennej: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Przekonwertuj `ToBase64String()` zawartość certyfikatu za pomocą funkcji i zapisz tę zawartość w pliku tekstowym: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Hasło** | `password`| Nie | <*hasło-dla-pfx-file*> | Hasło dostępu do pliku PFX |
|||||

Korzystając z [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie usługi Azure Resource Manager do automatyzacji wdrażania,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. W tym przykładzie definicja `type` `ClientCertificate` akcji HTTP określa uwierzytelnianie jako i używa [funkcji parameters(),](../logic-apps/workflow-definition-language-functions-reference.md#parameters) aby uzyskać wartości parametrów:

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

Aby uzyskać więcej informacji na temat zabezpieczania usług przy użyciu uwierzytelniania certyfikatów klienta, zobacz następujące tematy:

* [Zwiększ bezpieczeństwo interfejsów API przy użyciu uwierzytelniania certyfikatów klienta w usłudze Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Zwiększ bezpieczeństwo usług zaplecza przy użyciu uwierzytelniania certyfikatów klienta w usłudze Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Zwiększ bezpieczeństwo usługi RESTfuL przy użyciu certyfikatów klientów](../active-directory-b2c/secure-rest-api.md)
* [Poświadczenia certyfikatu do uwierzytelniania aplikacji](../active-directory/develop/active-directory-certificate-credentials.md)
* [Używanie certyfikatu TLS/SSL w kodzie w usłudze Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Uwierzytelnianie OAuth usługi Azure Active Directory

Jeśli dostępna jest opcja [OAuth usługi Active Directory,](../active-directory/develop/about-microsoft-identity-platform.md) określ następujące wartości właściwości:

| Właściwość (projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | **Active Directory OAuth** <br>lub <br>`ActiveDirectoryOAuth` | Typ uwierzytelniania do użycia. Logic Apps jest obecnie zgodna z [protokołem OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Urząd** | `authority` | Nie | <*Adres URL dla organu-token-wystawca*> | Adres URL urzędu, który udostępnia token uwierzytelniania. Domyślnie ta wartość `https://login.windows.net`to . |
| **Dzierżawa** | `tenant` | Tak | <*identyfikator dzierżawy*> | Identyfikator dzierżawy dzierżawy usługi Azure AD |
| **Grupy odbiorców** | `audience` | Tak | <*zasobów do autoryzacji*> | Zasób, którego chcesz użyć do autoryzacji, na przykład`https://management.core.windows.net/` |
| **Client ID (Identyfikator klienta)** | `clientId` | Tak | <*identyfikator klienta*> | Identyfikator klienta aplikacji żądającej autoryzacji |
| **Typ poświadczeń** | `credentialType` | Tak | Certyfikat <br>lub <br>Wpis tajny | Typ poświadczeń używany przez klienta do żądania autoryzacji. Ta właściwość i wartość nie są wyświetlane w podstawowej definicji aplikacji logiki, ale określa właściwości, które pojawiają się dla wybranego typu poświadczeń. |
| **Wpis tajny** | `secret` | Tak, ale tylko dla typu poświadczeń "Tajne" | <*klucz tajny klienta*> | Klucz tajny klienta do żądania autoryzacji |
| **Pfx** | `pfx` | Tak, ale tylko dla typu poświadczeń "Certyfikat" | <*zakodowana zawartość pliku pfx*> | Zawartość zakodowana w bazie 64 z pliku wymiany informacji osobistych (PFX) |
| **Hasło** | `password` | Tak, ale tylko dla typu poświadczeń "Certyfikat" | <*hasło-dla-pfx-file*> | Hasło dostępu do pliku PFX |
|||||

Korzystając z [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie usługi Azure Resource Manager do automatyzacji wdrażania,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. W tym przykładzie definicja `type` `ActiveDirectoryOAuth`akcji HTTP określa `Secret`uwierzytelnianie jako typ poświadczeń jako , i używa [funkcji parameters(),](../logic-apps/workflow-definition-language-functions-reference.md#parameters) aby uzyskać wartości parametrów:

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

### <a name="raw-authentication"></a>Uwierzytelnianie nieprzetworzone

Jeśli opcja **Raw** jest dostępna, można użyć tego typu uwierzytelniania, gdy trzeba użyć [schematów uwierzytelniania,](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) które nie są zgodne [z protokołem OAuth 2.0](https://oauth.net/2/). W przypadku tego typu należy ręcznie utworzyć wartość nagłówka autoryzacji, którą wysyłasz z żądaniem wychodzącym, i określić tę wartość nagłówka w wyzwalaczu lub akcji.

Na przykład oto przykładowy nagłówek żądania HTTPS, który następuje po [protokole OAuth 1.0:](https://tools.ietf.org/html/rfc5849)

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

W wyzwalaczu lub akcji obsługującej uwierzytelnianie nieprzetworzone określ następujące wartości właściwości:

| Właściwość (projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
|---------------------|-----------------|----------|-------|-------------|
| **Authentication** | `type` | Tak | Nieprzetworzone | Typ uwierzytelniania, którego można użyć |
| **Wartość** | `value` | Tak | <*autoryzacja-header-wartość*> | Wartość nagłówka autoryzacji używana do uwierzytelniania |
||||||

Korzystając z [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie usługi Azure Resource Manager do automatyzacji wdrażania,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. W tym przykładzie definicja `type` `Raw`akcji HTTP określa uwierzytelnianie jako i używa [funkcji parameters(),](../logic-apps/workflow-definition-language-functions-reference.md#parameters) aby uzyskać wartości parametrów:

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

Jeśli opcja [Tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) jest dostępna, aplikacja logiki może używać tożsamości przypisanej do systemu lub *pojedynczej* ręcznie utworzonej tożsamości przypisanej przez użytkownika do uwierzytelniania dostępu do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD) bez logowania. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ nie trzeba podawać ani obracać wpisów tajnych. Dowiedz się więcej o [usługach platformy Azure, które obsługują tożsamości zarządzane dla uwierzytelniania usługi Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Zanim aplikacja logiki będzie mogła używać tożsamości zarządzanej, wykonaj kroki opisane w obszarze [Uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w usłudze Azure Logic Apps.](../logic-apps/create-managed-service-identity.md) Te kroki umożliwiają tożsamość zarządzana w aplikacji logiki i skonfigurować dostęp tej tożsamości do docelowego zasobu platformy Azure.

1. Zanim funkcja platformy Azure będzie mogła używać tożsamości zarządzanej, najpierw [włącz uwierzytelnianie dla funkcji platformy Azure.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

1. W wyzwalaczu lub akcji, w której chcesz użyć tożsamości zarządzanej, określ następujące wartości właściwości:

   | Właściwość (projektant) | Właściwość (JSON) | Wymagany | Wartość | Opis |
   |---------------------|-----------------|----------|-------|-------------|
   | **Authentication** | `type` | Tak | **Tożsamość zarządzana** <br>lub <br>`ManagedServiceIdentity` | Typ uwierzytelniania, którego można użyć |
   | **Tożsamość zarządzana** | `identity` | Tak | * **Tożsamości zarządzanej przypisanej do systemu** <br>lub <br>`SystemAssigned` <p><p>* <*nazwa tożsamości przypisana przez użytkownika*> | Tożsamość zarządzana do użycia |
   | **Grupy odbiorców** | `audience` | Tak | <*identyfikator zasobu docelowego*> | Identyfikator zasobu docelowego, do którego chcesz uzyskać dostęp. <p>Na przykład `https://storage.azure.com/` sprawia, że tokeny dostępu do uwierzytelniania prawidłowe dla wszystkich kont magazynu. Można jednak również określić adres URL `https://fabrikamstorageaccount.blob.core.windows.net` usługi głównej, na przykład dla określonego konta magazynu. <p>**Uwaga:** **Właściwość Odbiorca** może być ukryta w niektórych wyzwalaczach lub działaniach. Aby ta właściwość była widoczna, w wyzwalaczu lub akcji otwórz listę **Dodaj nowy parametr** i wybierz pozycję Grupa **odbiorców**. <p><p>**Ważne:** Upewnij się, że ten identyfikator zasobu *docelowego dokładnie odpowiada* wartości, której oczekuje usługa Azure AD, w tym wszelkie wymagane końcowe ukośniki. Tak więc `https://storage.azure.com/` identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga ukośnika końcowego. Aby znaleźć te identyfikatory zasobów, zobacz [usługi platformy Azure obsługujące usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Korzystając z [zabezpieczonych parametrów](#secure-action-parameters) do obsługi i ochrony poufnych informacji, na przykład w [szablonie usługi Azure Resource Manager do automatyzacji wdrażania,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)można użyć wyrażeń, aby uzyskać dostęp do tych wartości parametrów w czasie wykonywania. W tym przykładzie definicja `type` `ManagedServiceIdentity` akcji HTTP określa uwierzytelnianie jako i używa [funkcji parameters(),](../logic-apps/workflow-definition-language-functions-reference.md#parameters) aby uzyskać wartości parametrów:

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

* [Automatyzacja wdrażania dla aplikacji logiki azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnozowanie błędów i problemów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Tworzenie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
