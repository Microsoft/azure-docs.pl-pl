---
title: Blokuj połączenia dla określonych łączników interfejsu API
description: Ograniczanie tworzenia i używania połączeń interfejsu API w programie Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310033"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Blokuj połączenia utworzone przez łączniki w Azure Logic Apps

Jeśli Twoja organizacja nie zezwala na łączenie się z ograniczonymi lub niezatwierdzonymi zasobami przy użyciu łączników w Azure Logic Apps, można zablokować możliwość tworzenia i używania tych połączeń w przepływach pracy aplikacji logiki. Za pomocą [Azure Policy](../governance/policy/overview.md)można definiować i wymuszać [zasady](../governance/policy/overview.md#policy-definition) , które uniemożliwiają tworzenie lub używanie połączeń dla łączników, które mają być blokowane. Ze względów bezpieczeństwa warto na przykład zablokować połączenia z konkretnymi platformami mediów społecznościowych lub innymi usługami i systemami.

W tym temacie opisano sposób konfigurowania zasad, które blokują określone połączenia przy użyciu Azure Portal, ale można tworzyć definicje zasad w inny sposób, na przykład za pośrednictwem interfejsu API REST platformy Azure, Azure PowerShell, interfejsu wiersza polecenia platformy Azure i szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

* Identyfikator odwołania dla łącznika, który ma zostać zablokowany. Aby uzyskać więcej informacji, zobacz [Znajdowanie identyfikatora odwołania łącznika](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Znajdź identyfikator odwołania do łącznika

Jeśli masz już aplikację logiki z połączeniem, które chcesz zablokować, postępuj zgodnie z instrukcjami [dotyczącymi Azure Portal](#connector-ID-portal). Jeśli nie, wykonaj następujące kroki:

1. Odwiedź [listę łączników Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Znajdź stronę referencyjną łącznika, który chcesz zablokować.

   Na przykład jeśli chcesz zablokować łącznik usługi Instagram, który jest przestarzały, przejdź do tej strony:

   `https://docs.microsoft.com/connectors/instagram/`

1. Na stronie adres URL strony Skopiuj i Zapisz identyfikator odwołania łącznika na końcu bez ukośnika ( `/` ), na przykład `instagram` .

   Później, podczas tworzenia definicji zasad, należy użyć tego identyfikatora w instrukcji warunku definicji, na przykład:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure Portal

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz aplikację logiki.

1. W menu aplikacji logiki wybierz **Widok kod aplikacji logiki** , aby można było wyświetlić definicję JSON aplikacji logiki.

   ![Otwórz "Widok kodu aplikacji logiki", aby znaleźć identyfikator łącznika](./media/block-connections-connectors/code-view-connector-id.png)

1. Znajdź `parameters` obiekt, który zawiera `$connections` obiekt, który zawiera `{connection-name}` obiekt dla każdego połączenia w aplikacji logiki i określa informacje o tym połączeniu:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Na przykład w przypadku łącznika usługi Instagram Znajdź `instagram` obiekt, który identyfikuje połączenie usługi Instagram:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. W przypadku połączenia, które chcesz zablokować, Znajdź `id` Właściwość i wartość, która jest zgodna z następującym formatem: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Na przykład poniżej przedstawiono `id` Właściwość i wartość połączenia usługi Instagram:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. Na podstawie `id` wartości właściwości skopiuj i Zapisz identyfikator odwołania łącznika na końcu, na przykład `instagram` .

   Później, podczas tworzenia definicji zasad, należy użyć tego identyfikatora w instrukcji warunku definicji, na przykład:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Tworzenie zasad w celu zablokowania tworzenia połączeń

Aby całkowicie zablokować Tworzenie połączenia w aplikacji logiki, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W polu wyszukiwania portalu wprowadź `policy` i wybierz pozycję **zasady**.

   ![W Azure Portal, Znajdź i wybierz pozycję "zasady"](./media/block-connections-connectors/find-select-azure-policy.png)

1. W menu **zasady** w obszarze **Tworzenie** wybierz pozycję **definicje**  >  **+ Definicja zasad**.

   ![Wybierz "Definicje" > "+ Definicja zasad"](./media/block-connections-connectors/add-new-policy-definition.png)

1. W obszarze **Definicja zasad** podaj informacje dotyczące definicji zasad w oparciu o właściwości opisane w przykładzie:

   ![Zrzut ekranu przedstawiający właściwości "Definicja zasad".](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Lokalizacja definicji** | Tak | <*Azure — nazwa subskrypcji*> | Subskrypcja platformy Azure do użycia w definicji zasad <p><p>1. Aby znaleźć subskrypcję, wybierz przycisk wielokropka (**...**). <br>2. z listy **subskrypcji** Znajdź i wybierz swoją subskrypcję. <br>3. po zakończeniu wybierz pozycję **Wybierz**. |
   | **Nazwa** | Tak | <*Policy-Definicja-name*> | Nazwa do użycia w definicji zasad |
   | **Opis** | Nie | <*Policy-Definicja-name*> | Opis definicji zasad |
   | **Kategoria** | Tak | **Aplikacje logiki** | Nazwa istniejącej kategorii lub nowej kategorii dla definicji zasad |
   | **Egzekwowanie zasad** | Tak | **Włączono** | To ustawienie określa, czy należy włączyć lub wyłączyć definicję zasad podczas zapisywania pracy. |
   ||||

1. W obszarze **reguła zasad** pole edycji JSON jest wstępnie wypełnione szablonem definicji zasad. Zastąp ten szablon [definicją zasad](../governance/policy/concepts/definition-structure.md) w oparciu o właściwości opisane w poniższej tabeli i następującą składnią:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | `mode` | `All` | Tryb określający typy zasobów, które są obliczane przez zasady. <p><p>W tym scenariuszu jest ustawiana `mode` `All` wartość, która stosuje zasady do grup zasobów platformy Azure, subskrypcji i wszystkich typów zasobów. <p><p>Aby uzyskać więcej informacji, zobacz temat [Struktura definicji zasad — tryb](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Warunek określający, kiedy należy wymusić zasadę zasad <p><p>W tym scenariuszu program `{condition-to-evaluate}` określa, czy `api.id` wartość w jest `Microsoft.Web/connections/api.id` zgodna z wartością `*managedApis/{connector-name}` , która określa symbol wieloznaczny (*). <p><p>Aby uzyskać więcej informacji, zobacz [Struktura definicji zasad — reguła zasad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | `field`Wartość do porównania z warunkiem <p><p>W tym scenariuszu `field` używa [*alias*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id` , aby uzyskać dostęp do wartości we właściwości łącznika `api.id` . |
   | `like` | `*managedApis/{connector-name}` | Operator logiczny i wartość do użycia podczas porównywania `field` wartości <p><p>W tym scenariuszu `like` operator i znak symbolu wieloznacznego (*) Upewnij się, że reguła działa niezależnie od regionu, a ciąg, `*managedApis/{connector-name}` , jest wartością do dopasowania, gdzie `{connector-name}` jest identyfikatorem łącznika, który ma zostać zablokowany. <p><p>Załóżmy na przykład, że chcesz zablokować tworzenie połączeń do platform mediów społecznościowych lub baz danych: <p><p>Ekran `twitter` <br>Usługi Instagram `instagram` <br>Serwis `facebook` <br>Pinterest `pinterest` <br>-SQL Server lub Azure SQL: `sql` <p><p>Aby znaleźć te identyfikatory łączników, zobacz sekcję [Znajdź identyfikator odwołania do łącznika](#connector-reference-ID) wcześniej w tym temacie. |
   | `then` | `{effect-to-apply}` | Efekt do zastosowania, gdy `if` warunek jest spełniony <p><p>W tym scenariuszu `{effect-to-apply}` jest blokowane i niepowodzenie żądania lub operacji, które nie są zgodne z zasadami. <p><p>Aby uzyskać więcej informacji, zobacz [Struktura definicji zasad — reguła zasad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`Jest to blok żądania, który polega na utworzeniu określonego połączenia <p><p>Aby uzyskać więcej informacji, zobacz [opis Azure Policy efektów — Odmów](../governance/policy/concepts/effects.md#deny). |
   ||||

   Załóżmy na przykład, że chcesz zablokować tworzenie połączeń przy użyciu łącznika usługi Instagram. Oto definicja zasad, której można użyć:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Oto sposób wyświetlania pola **reguła zasad** :

   ![Zrzut ekranu przedstawiający pole "reguła zasad" z przykładem reguły zasad.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Dla wielu łączników można dodać więcej warunków, na przykład:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Po zakończeniu wybierz pozycję **Zapisz**. Po zapisaniu definicji zasad Azure Policy generuje i dodaje więcej wartości właściwości do definicji zasad.

1. Następnie, aby przypisać definicję zasad, w której chcesz wymuszać zasady, [Utwórz przypisanie zasad](#create-policy-assignment).

Aby uzyskać więcej informacji na temat definicji zasad platformy Azure, zobacz następujące tematy:

* [Definicja struktury zasad](../governance/policy/concepts/definition-structure.md)
* [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy wbudowane definicje zasad dla Azure Logic Apps](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Utwórz zasady blokujące używanie połączeń

Po utworzeniu połączenia wewnątrz aplikacji logiki to połączenie istnieje jako osobny zasób platformy Azure. Jeśli usuniesz tylko aplikację logiki, połączenie nie zostanie automatycznie usunięte i będzie nadal istniało do momentu usunięcia. Może istnieć scenariusz, w którym połączenie już istnieje lub w którym należy utworzyć połączenie do użycia poza aplikacją logiki. Nadal można zablokować możliwość używania istniejącego połączenia w aplikacji logiki przez utworzenie zasad, które uniemożliwiają zapisywanie aplikacji logiki z ograniczonym lub niezatwierdzonym połączeniem.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W polu wyszukiwania portalu wprowadź `policy` i wybierz pozycję **zasady**.

   ![W Azure Portal, Znajdź i wybierz pozycję "zasady"](./media/block-connections-connectors/find-select-azure-policy.png)

1. W menu **zasady** w obszarze **Tworzenie** wybierz pozycję **definicje**  >  **+ Definicja zasad**.

   ![Wybierz "Definicje" > "+ Definicja zasad"](./media/block-connections-connectors/add-new-policy-definition.png)

1. W obszarze **Definicja zasad** podaj informacje dotyczące definicji zasad na podstawie właściwości opisanych w przykładzie i Kontynuuj, używając usługi Instagram jako przykładu:

   ![Właściwości definicji zasad](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Lokalizacja definicji** | Tak | <*Azure — nazwa subskrypcji*> | Subskrypcja platformy Azure do użycia w definicji zasad <p><p>1. Aby znaleźć subskrypcję, wybierz przycisk wielokropka (**...**). <br>2. z listy **subskrypcji** Znajdź i wybierz swoją subskrypcję. <br>3. po zakończeniu wybierz pozycję **Wybierz**. |
   | **Nazwa** | Tak | <*Policy-Definicja-name*> | Nazwa do użycia w definicji zasad |
   | **Opis** | Nie | <*Policy-Definicja-name*> | Opis definicji zasad |
   | **Kategoria** | Tak | **Aplikacje logiki** | Nazwa istniejącej kategorii lub nowej kategorii dla definicji zasad |
   | **Egzekwowanie zasad** | Tak | **Włączono** | To ustawienie określa, czy należy włączyć lub wyłączyć definicję zasad podczas zapisywania pracy. |
   ||||

1. W obszarze **reguła zasad** pole edycji JSON jest wstępnie wypełnione szablonem definicji zasad. Zastąp ten szablon [definicją zasad](../governance/policy/concepts/definition-structure.md) w oparciu o właściwości opisane w poniższej tabeli i następującą składnią:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | `mode` | `All` | Tryb określający typy zasobów, które są obliczane przez zasady. <p><p>W tym scenariuszu jest ustawiana `mode` `All` wartość, która stosuje zasady do grup zasobów platformy Azure, subskrypcji i wszystkich typów zasobów. <p><p>Aby uzyskać więcej informacji, zobacz temat [Struktura definicji zasad — tryb](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Warunek określający, kiedy należy wymusić zasadę zasad <p><p>W tym scenariuszu program `{condition-to-evaluate}` określa, czy ciąg wyjściowy z `[string(field('Microsoft.Logic/workflows/parameters'))]` , zawiera ciąg, `{connector-name}` . <p><p>Aby uzyskać więcej informacji, zobacz [Struktura definicji zasad — reguła zasad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | Wartość do porównania z warunkiem <p><p>W tym scenariuszu `value` jest ciągiem wyjściowym `[string(field('Microsoft.Logic/workflows/parameters'))]` , który konwertuje `$connectors` obiekt wewnątrz `Microsoft.Logic/workflows/parameters` obiektu na ciąg. |
   | `contains` | `{connector-name}` | Operator logiczny i wartość do porównania z `value` właściwością <p><p>W tym scenariuszu `contains` operator sprawdza, czy reguła działa niezależnie od tego, gdzie `{connector-name}` pojawia się, gdzie String, `{connector-name}` jest identyfikatorem łącznika, który ma zostać ograniczony lub zablokowany. <p><p>Załóżmy na przykład, że chcesz zablokować używanie połączeń z platformami mediów społecznościowych lub bazami danych: <p><p>Ekran `twitter` <br>Usługi Instagram `instagram` <br>Serwis `facebook` <br>Pinterest `pinterest` <br>-SQL Server lub Azure SQL: `sql` <p><p>Aby znaleźć te identyfikatory łączników, zobacz sekcję [Znajdź identyfikator odwołania do łącznika](#connector-reference-ID) wcześniej w tym temacie. |
   | `then` | `{effect-to-apply}` | Efekt do zastosowania, gdy `if` warunek jest spełniony <p><p>W tym scenariuszu `{effect-to-apply}` jest to zablokowanie i niepowodzenie żądania lub operacji, które nie są zgodne z zasadami. <p><p>Aby uzyskać więcej informacji, zobacz [Struktura definicji zasad — reguła zasad](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`To `deny` lub Zablokuj żądanie zapisania aplikacji logiki korzystającej z określonego połączenia <p><p>Aby uzyskać więcej informacji, zobacz [opis Azure Policy efektów — Odmów](../governance/policy/concepts/effects.md#deny). |
   ||||

   Załóżmy na przykład, że chcesz zablokować zapisywanie aplikacji logiki, które używają połączeń usługi Instagram. Oto definicja zasad, której można użyć:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Oto sposób wyświetlania pola **reguła zasad** :

   ![Reguła definicji zasad](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Po zakończeniu wybierz pozycję **Zapisz**. Po zapisaniu definicji zasad Azure Policy generuje i dodaje więcej wartości właściwości do definicji zasad.

1. Następnie, aby przypisać definicję zasad, w której chcesz wymuszać zasady, [Utwórz przypisanie zasad](#create-policy-assignment).

Aby uzyskać więcej informacji na temat definicji zasad platformy Azure, zobacz następujące tematy:

* [Definicja struktury zasad](../governance/policy/concepts/definition-structure.md)
* [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy wbudowane definicje zasad dla Azure Logic Apps](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Utwórz przypisanie zasad

Następnie należy przypisać definicję zasad, w której chcesz wymusić zasady, na przykład do pojedynczej grupy zasobów, wielu grup zasobów, dzierżawy usługi Azure Active Directory (Azure AD) lub subskrypcji platformy Azure. W przypadku tego zadania wykonaj następujące kroki, aby utworzyć przypisanie zasad:

1. Jeśli wylogowano, zaloguj się ponownie do [Azure Portal](https://portal.azure.com). W polu wyszukiwania portalu wprowadź `policy` i wybierz pozycję **zasady**.

   ![W Azure Portal, Znajdź i wybierz pozycję "zasady"](./media/block-connections-connectors/find-select-azure-policy.png)

1. W menu **zasady** w obszarze **Tworzenie** wybierz pozycję **przypisania**  >  **przypisywanie zasad**.

   ![Wybierz pozycję "przypisania" > "Przypisz"](./media/block-connections-connectors/add-new-policy-assignment.png)

1. W obszarze **podstawowe** podaj następujące informacje dotyczące przypisywania zasad:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Zakres** | Tak | Zasoby, w których chcesz wymusić przypisanie zasad. <p><p>1. obok pola **zakres** wybierz przycisk wielokropka (**...**). <br>2. z listy **subskrypcja** wybierz subskrypcję platformy Azure. <br>3. opcjonalnie z listy **Grupa zasobów** wybierz grupę zasobów. <br>4. gdy skończysz, wybierz pozycję **Wybierz**. |
   | **Wykluczenia** | Nie | Wszystkie zasoby platformy Azure, które mają zostać wykluczone z przypisania zasad. <p><p>1. obok pola **wykluczenia** wybierz przycisk wielokropka (**...**). <br>2. z listy **zasobów** wybierz zasób, > **dodać do wybranego zakresu**. <br>3. gdy skończysz, wybierz pozycję **Zapisz**. |
   | **Definicja zasad** | Tak | Nazwa definicji zasad, która ma zostać przypisana i wymuszać. Ten przykład kontynuuje działanie z przykładowymi zasadami usługi instagramymi "Block usługi Instagram Connections". <p><p>1. obok pola **Definicja zasad** wybierz przycisk wielokropka (**...**). <br>2. Znajdź i wybierz definicję zasad przy użyciu filtru **typu** lub pola **wyszukiwania** . <br>3. po zakończeniu wybierz pozycję **Wybierz**. |
   | **Nazwa przypisania** | Tak | Nazwa, która ma zostać użyta do przypisania zasad, jeśli różni się od definicji zasad |
   | **Identyfikator przypisania** | Tak | Wygenerowany automatycznie identyfikator dla przypisania zasad |
   | **Opis** | Nie | Opis przypisania zasad |
   | **Egzekwowanie zasad** | Tak | Ustawienie, które włącza lub wyłącza przypisanie zasad |
   | **Przypisane przez** | Nie | Nazwa osoby, która utworzyła i stosowała przypisanie zasad |
   ||||

   Na przykład, aby przypisać zasady do grupy zasobów platformy Azure przy użyciu przykładu usługi Instagram:

   ![Właściwości przypisania zasad](./media/block-connections-connectors/policy-assignment-basics.png)

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**.

   Po utworzeniu zasad może być konieczne odczekanie do 15 minut, zanim zasady zostaną zastosowane. Zmiany mogą również mieć podobne opóźnione skutki.

1. Po zastosowaniu zasad można [testować zasady](#test-policy).

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testowanie zasad

Aby wypróbować zasady, Rozpocznij tworzenie połączenia za pomocą łącznika z ograniczeniami w usłudze Logic App Designer. Kontynuując usługi Instagram przykład po zalogowaniu się do usługi Instagram, ten błąd występuje, gdy aplikacja logiki nie mogła utworzyć połączenia:

![Niepowodzenie połączenia z powodu zastosowania zasad](./media/block-connections-connectors/connection-failure-message.png)

Komunikat zawiera następujące informacje:

| Opis | Zawartość |
|-------------|---------|
| Przyczyna niepowodzenia | `"Resource 'instagram' was disallowed by policy."` |
| Nazwa przypisania | `"Block Instagram connections"` |
| Identyfikator przypisania | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| Identyfikator definicji zasad | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Policy](../governance/policy/overview.md)