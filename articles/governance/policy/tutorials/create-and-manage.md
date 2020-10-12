---
title: 'Samouczek: Tworzenie zasad w celu wymuszenia zgodności'
description: W tym samouczku użyjesz zasad, aby wymusić standardy, kontrolować koszty, obsługiwać zabezpieczenia i nakładać zasady projektowania całego przedsiębiorstwa.
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: d8dc65d50182b5336a683c2da8e2a5d8ebb9e849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650088"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności

Poznanie sposobu tworzenia zasad i zarządzania nimi na platformie Azure jest ważne w celu zachowania zgodności ze standardami firmy i umowami dotyczącymi poziomu usług. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Policy wykonywać niektóre bardziej typowe zadania związane z tworzeniem i przypisywaniem zasad oraz zarządzaniem nimi w całej organizacji, na przykład:

> [!div class="checklist"]
> - Przypisywanie zasad w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> - Tworzenie i przypisywanie definicji inicjatywy w celu śledzenia zgodności dla wielu zasobów
> - Rozwiązywanie problemu w przypadku niezgodnego lub odrzuconego zasobu
> - Implementowanie nowych zasad w całej organizacji

Przejrzyj artykuły Szybki start, aby dowiedzieć się, jak przypisać zasady w celu identyfikowania bieżącego stanu zgodności istniejących zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="assign-a-policy"></a>Przypisywanie zasad

Pierwszym krokiem w celu wymuszenia zgodności za pomocą usługi Azure Policy jest przypisanie definicji zasad. Definicja zasad określa, w jakim przypadku zasady zostaną wymuszone oraz jaki efekt przyniosą. W tym przykładzie należy przypisać wbudowaną definicję zasad o nazwie _Dziedzicz tag z grupy zasobów, jeśli nie istnieje_ , aby dodać określony tag z wartością z nadrzędnej grupy zasobów do nowych lub zaktualizowanych zasobów, w których brakuje znacznika.

1. Przejdź do Azure Portal, aby przypisać zasady. Wyszukaj i wybierz pozycję **zasady**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Wybierz pozycję **Przypisz zasady** w górnej części strony **Zasady — Przypisania**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Na stronie **przypisywanie zasad** i **podstawowe informacje** wybierz **zakres** , wybierając wielokropek i wybrać grupę zarządzania lub subskrypcję. Opcjonalnie możesz wybrać grupę zasobów. Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad.
   Następnie wybierz pozycję **Wybierz** w dolnej części strony **zakres** .

   W tym przykładzie użyto subskrypcji **Contoso**. Twoja subskrypcja będzie inna.

1. Zasoby można wykluczyć na podstawie opcji **Zakres**. **Wykluczenia** są uruchamiane o jeden poziom niżej od poziomu opcji **Zakres**. Pole **Wykluczenia** jest opcjonalne, więc na razie można je pozostawić puste.

1. W polu **Definicja zasad** wybierz wielokropek, aby otworzyć listę dostępnych definicji. Możesz filtrować **Typ** definicji zasad, aby _wbudowany_ , wyświetlić wszystkie i przeczytać ich opisy.

1. **Jeśli nie ma, wybierz opcję Dziedzicz tag z grupy zasobów**. Jeśli nie możesz znaleźć go od razu, wpisz polecenie **Dziedzicz tag** w polu wyszukiwania, a następnie naciśnij klawisz ENTER lub zaznacz pole wyszukiwania.
   Po znalezieniu i wybraniu definicji zasad wybierz **opcję Wybierz** w dolnej części strony **dostępne definicje** .

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania.":::

1. W polu **Nazwa przypisania** jest automatycznie wpisywana nazwa wybranej zasady, ale można ją zmienić. Na potrzeby tego przykładu w razie braku należy pozostawić w _grupie zasobów tag dziedziczenia_. Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące danego przypisania zasad.

1. Pozostaw **wymuszanie zasad** jako _włączone_. Gdy to ustawienie jest _wyłączone_, umożliwia testowanie wyników zasad bez wyzwalania efektu. Aby uzyskać więcej informacji, zobacz [Tryb wymuszania](../concepts/assignment-structure.md#enforcement-mode).

1. Pole **Przypisane przez** jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Wybierz kartę **Parametry** w górnej części kreatora.

1. W obszarze **nazwa tagu**wpisz _Environment_.

1. Wybierz kartę **korygowanie** w górnej części kreatora.

1. Nie zaznaczaj zaznaczenia pola **Utwórz zadanie korygujące** . To pole umożliwia utworzenie zadania zmiany istniejących zasobów oprócz nowych lub zaktualizowanych zasobów. Aby uzyskać więcej informacji, zobacz [korygowanie zasobów](../how-to/remediate-resources.md).

1. **Tworzenie tożsamości zarządzanej** jest automatycznie sprawdzane, ponieważ ta definicja zasad używa efektu [modyfikowania](../concepts/effects.md#modify) . **Uprawnienia** są ustawiane _automatycznie na_ podstawie definicji zasad. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](../how-to/remediate-resources.md#how-remediation-security-works).

1. Wybierz kartę **Recenzja + tworzenie** w górnej części kreatora.

1. Przejrzyj wybrane opcje, a następnie wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="implement-a-new-custom-policy"></a>Implementowanie nowych zasad niestandardowych

Teraz, gdy wbudowana definicja zasad została przypisana, możesz wykonywać dalsze działania w ramach usługi Azure Policy. Następnie utwórz nowe zasady niestandardowe, aby zmniejszyć koszty, sprawdzając, czy maszyny wirtualne utworzone w środowisku nie mogą znajdować się w serii G. W ten sposób, za każdym razem, gdy użytkownik w organizacji próbuje utworzyć maszynę wirtualną w serii G, żądanie zostanie odrzucone.

1. W lewej części strony usługi Azure Policy wybierz opcję **Definicje** w obszarze **Tworzenie**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Wybierz **+ Definicja zasad** w górnej części strony. Ten przycisk powoduje otwarcie strony **Definicja zasad**.

1. Wprowadź następujące informacje:

   - Grupa zarządzania lub subskrypcji, w której zapisano definicję zasad. Wybierz, używając wielokropka w polu **Lokalizacja definicji**.

     > [!NOTE]
     > Jeśli planujesz zastosowanie tej definicji zasad w wielu subskrypcjach, lokalizacja musi być grupą zarządzania zawierającą subskrypcje, do których zostaną przypisane zasady. To samo dotyczy definicji inicjatywy.

   - Nazwa definicji zasad — _wymagane jednostki SKU maszyny wirtualnej nie są w serii G_
   - Opis definicji zasad przeznaczonych do wykonania — _Ta definicja zasad wymusza, że wszystkie maszyny wirtualne utworzone w tym zakresie mają jednostki SKU inne niż Seria G, aby zmniejszyć koszty._
   - Wybierz jedną z istniejących opcji (np. _Compute_) lub utwórz nową kategorię dla tej definicji zasad.
   - Skopiuj poniższy kod JSON, a następnie zaktualizuj go zgodnie ze swoimi potrzebami przy użyciu następujących danych:
      - Parametry zasad.
      - Warunki/reguły zasad, w tym przypadku jest to rozmiar jednostki SKU maszyny wirtualnej, który jest równy serii G
      - Efekt zasad, w tym przypadku **Deny**.

   Oto, jak powinien wyglądać kod JSON. Wklej poprawiony kod do witryny Azure Portal.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   Właściwość _Field_ w regule zasad musi być obsługiwaną wartością. Pełna lista wartości znajduje się w [polach struktury definicji zasad](../concepts/definition-structure.md#fields). Przykładem aliasu może być `"Microsoft.Compute/VirtualMachines/Size"`.

   Aby wyświetlić więcej przykładów Azure Policy, zobacz [Azure Policy Samples](../samples/index.md).

1. Wybierz pozycję **Zapisz**.

## <a name="create-a-policy-definition-with-rest-api"></a>Tworzenie definicji zasad za pomocą interfejsu API REST

Można utworzyć zasady za pomocą interfejsu API REST dla definicji Azure Policy. Interfejs API REST umożliwia tworzenie i usuwanie definicji zasad oraz uzyskiwanie informacji o istniejących definicjach. Aby utworzyć definicję zasad, skorzystaj z następującego przykładu:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Dołącz treść żądania podobną do poniższego przykładu:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Tworzenie definicji zasad za pomocą programu PowerShell

Przed kontynuowaniem pracy z przykładem programu PowerShell upewnij się, że zainstalowano najnowszą wersję Azure PowerShell AZ module.

Definicję zasad można utworzyć przy użyciu polecenia cmdlet `New-AzPolicyDefinition`.

Aby utworzyć definicję zasad na podstawie pliku, przekaż ścieżkę do tego pliku. W przypadku pliku zewnętrznego skorzystaj z następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

W przypadku pliku lokalnego skorzystaj z następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Aby utworzyć definicję zasad z wbudowaną regułą, skorzystaj z następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Dane wyjściowe są zapisywane w obiekcie `$definition`, który jest używany podczas przypisywania zasad. Poniższy przykład tworzy definicję zasad, która obejmuje parametry:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Wyświetlanie definicji zasad przy użyciu programu PowerShell

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Zwraca ono wszystkie dostępne definicje zasad, w tym wbudowane zasady. Poszczególne zasady są zwracane w następującym formacie:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Tworzenie definicji zasad za pomocą wiersza polecenia platformy Azure

Definicję zasad można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure z `az policy definition` poleceniem. Aby utworzyć definicję zasad z wbudowaną regułą, skorzystaj z następującego przykładu:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Wyświetlanie definicji zasad przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az policy definition list
```

Zwraca ono wszystkie dostępne definicje zasad, w tym wbudowane zasady. Poszczególne zasady są zwracane w następującym formacie:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Tworzenie i przypisywanie definicji inicjatywy

Za pomocą definicji inicjatywy możesz grupować kilka definicji zasad w celu osiągnięcia jednego ogólnego celu. Inicjatywa ocenia zasoby w zakresie przypisania pod kątem zgodności z uwzględnionymi zasadami. Aby uzyskać więcej informacji na temat definicji inicjatyw, zobacz [Omówienie usługi Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Tworzenie definicji inicjatywy

1. W lewej części strony usługi Azure Policy wybierz opcję **Definicje** w obszarze **Tworzenie**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. W górnej części strony wybierz pozycję **+ Definicja inicjatywy**. Spowoduje to przejście do formularza **Definicja inicjatywy**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Za pomocą wielokropka przy opcji **Lokalizacja definicji** wybierz grupę zarządzania lub subskrypcję, w której definicja ma być przechowywana. Jeśli poprzednia strona dotyczyła tylko jednej grupy zarządzania lub subskrypcji, pole **Lokalizacja definicji** zostanie wypełnione automatycznie. Po wybraniu są wypełniane **definicje dostępne** .

1. Wprowadź wartości w polach **Nazwa** i **Opis** inicjatywy.

   W tym przykładzie zapewniasz, że zasoby są zgodne z definicjami zasad w zakresie uzyskiwania bezpieczeństwa. Dla tej inicjatywy podaj nazwę **Uzyskiwanie bezpieczeństwa** i opis: **Ta inicjatywa została utworzona w celu obsługi wszystkich definicji zasad skojarzonych z zabezpieczaniem zasobów**.

1. W polu **Kategoria** wybierz jedną z istniejących opcji lub utwórz nową kategorię.

1. Przejrzyj listę **Dostępne definicje** (w lewej części strony**Definicja inicjatywy**) i wybierz definicje zasad, które chcesz dodać do tej inicjatywy. W przypadku inicjatywy **Get Secure** należy dodać następujące wbudowane definicje zasad, wybierając **+** obok informacji o definicji zasad lub wybierając wiersz definicji zasad, a następnie opcję **+ Dodaj** na stronie szczegółów:

   - Dozwolone lokalizacje
   - Monitoruj brakujące Endpoint Protection w Azure Security Center
   - Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone
   - Azure Backup powinna być włączona dla Virtual Machines
   - Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

   Po wybraniu definicji zasad z listy, każda zostanie dodana poniżej **kategorii**.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Jeśli definicja zasad dodawana do inicjatywy zawiera parametry, są one wyświetlane pod nazwą zasad w obszarze obszaru **kategorii** . Pole _Wartość_ może mieć ustawienie „Ustaw wartość” (wartość będzie zakodowana dla wszystkich przypisań tej inicjatywy) lub „Użyj parametru inicjatywy” (wartość będzie ustawiana w trakcie każdego przypisania inicjatywy). Jeśli wybrano opcję "Ustaw wartość", lista rozwijana z prawej strony _wartości_ umożliwia wprowadzanie lub wybieranie wartości. Jeśli wybrano pozycję „Użyj parametru inicjatywy”, zostanie wyświetlona nowa sekcja **Parametry inicjatywy**, umożliwiająca zdefiniowane parametru, który zostanie ustawiony podczas przypisania inicjatywy. Dozwolone wartości tego parametru inicjatywy mogą dodatkowo ograniczać wartości możliwe do ustawienia podczas przypisania inicjatywy.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

   > [!NOTE]
   > W przypadku niektórych parametrów `strongType` listy wartości nie można określić automatycznie. W takich przypadkach z prawej strony wiersza parametru jest wyświetlany symbol wielokropka. Wybranie tej opcji spowoduje otwarcie strony "zakres parametrów ( &lt; Nazwa parametru &gt; )". Na tej stronie wybierz subskrypcję, która ma zostać użyta do podania opcji wartości. Ten zakres parametru jest używany wyłącznie w trakcie tworzenia definicji inicjatywy i nie ma żadnego wpływu na ocenę zasad lub zakres inicjatywy podczas przypisania.

   Ustaw parametr "dozwolone lokalizacje" na wartość "Wschodnie stany USA 2" i pozostaw inne jako wartość domyślną "AuditifNotExists".

1. Wybierz pozycję **Zapisz**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Tworzenie definicji inicjatywy zasad przy użyciu interfejsu wiersza polecenia platformy Azure

Definicję inicjatywy zasad można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure z `az policy set-definition` poleceniem. Aby utworzyć definicję inicjatywy zasad z istniejącą definicją zasad, należy użyć następującego przykładu:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Tworzenie definicji inicjatywy zasad przy użyciu Azure PowerShell

Definicję inicjatywy zasad można utworzyć przy użyciu Azure PowerShell z `New-AzPolicySetDefinition` poleceniem cmdlet. Aby utworzyć definicję inicjatywy zasad z istniejącą definicją zasad, użyj następującego pliku definicji inicjatywy zasad jako `VMPolicySet.json` :

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Przypisywanie definicji inicjatywy

1. W lewej części strony usługi Azure Policy wybierz opcję **Definicje** w obszarze **Tworzenie**.

1. Zlokalizuj poprzednio utworzoną inicjatywę **Uzyskiwanie bezpieczeństwa** i wybierz ją. Wybierz polecenie **Przypisz** w górnej części strony, aby otworzyć stronę **Uzyskiwanie bezpieczeństwa: Przypisz inicjatywę**.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

   Możesz również kliknąć prawym przyciskiem myszy wybrany wiersz lub wybrać wielokropek na końcu wiersza menu kontekstowego. Następnie wybierz pozycję **Przypisz**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Wypełnij stronę **Uzyskiwanie bezpieczeństwa: Przypisz inicjatywę**, wprowadzając następujące przykładowe informacje. Możesz podać własne informacje.

   - Zakres: grupa zarządzania lub subskrypcja, dla której wcześniej została zapisana inicjatywa, staje się wartością domyślną.
     Można zmienić zakres, aby przypisać inicjatywę do subskrypcji lub grupy zasobów w lokalizacji zapisywania.
   - Wyjątki: skonfiguruj wszystkie zasoby w zakresie, aby zapobiec zastosowaniu wobec nich przypisania inicjatywy.
   - Definicja inicjatywy i Nazwa przypisania: „Uzyskiwanie bezpieczeństwa” (wypełniane wstępnie jako nazwa przypisywanej inicjatywy).
   - Opis: to przypisanie inicjatywy jest dostosowane w celu wymuszenia tej grupy definicji zasad.
   - Wymuszanie zasad: pozostaw domyślnie _włączone_.
   - Przypisane przez: jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Wybierz kartę **Parametry** w górnej części kreatora. Jeśli w poprzednich krokach skonfigurowano parametr Initiative, należy ustawić wartość w tym miejscu.

1. Wybierz kartę **korygowanie** w górnej części kreatora. Pozostaw pole **Utwórz tożsamość zarządzaną** niezaznaczone. To pole _należy_ zaznaczyć, gdy zasada lub przypisana inicjatywa zawiera zasady z efektami [deployIfNotExists](../concepts/effects.md#deployifnotexists) lub [Modify](../concepts/effects.md#modify) . Ponieważ w przypadku zasad stosowanych na potrzeby tego samouczka tak nie jest, pozostaw to pole puste. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](../how-to/remediate-resources.md#how-remediation-security-works).

1. Wybierz kartę **Recenzja + tworzenie** w górnej części kreatora.

1. Przejrzyj wybrane opcje, a następnie wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="check-initial-compliance"></a>Sprawdzanie zgodności początkowej

1. Wybierz pozycję **Zgodność** w lewej części strony usługi Azure Policy.

1. Znajdź inicjatywę **Get Secure** . Jej _Stan zgodności_ prawdopodobnie nadal ma wartość **Nie uruchomiono**.
   Wybierz inicjatywę, aby uzyskać pełne szczegóły postępu przypisywania.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Po ukończeniu przypisania inicjatywy strona zgodności jest aktualizowana — _Stan zgodności_ zmienia wartość na **Zgodne**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

1. Po wybraniu zasad na stronie Zgodność z inicjatywą zostanie otwarta strona szczegóły zgodności dla tych zasad. Ta strona zawiera szczegółowe informacje dotyczące zgodności na poziomie zasobów.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Dopuszczanie niezgodnego lub odrzuconego zasobu przy użyciu wykluczenia

Po przypisaniu inicjatywy zasad w celu wymagania określonej lokalizacji wszystkie zasoby utworzone w innej lokalizacji są odrzucane. W tej sekcji opisano sposób rozwiązywania odrzuconego żądania utworzenia zasobu przez utworzenie wykluczenia dla pojedynczej grupy zasobów. Wykluczenie uniemożliwia wymuszanie zasad (lub inicjatywę) w tej grupie zasobów. W poniższym przykładzie każda lokalizacja jest dozwolona w wykluczonej grupie zasobów. Wykluczenie może dotyczyć subskrypcji, grupy zasobów lub poszczególnych zasobów.

Wdrożenia nieobjęte przypisanymi zasadami lub inicjatywą można wyświetlić w grupie zasobów, której dotyczy wdrożenie: wybierz pozycję **wdrożenia** w lewej części strony, a następnie wybierz **nazwę wdrożenia** zakończonego niepowodzeniem. Zasób, do którego odmówiono dostępu, jest wyświetlany jako _Zabroniony_. Aby określić zasady lub inicjatywę i przypisanie, które odrzuciły zasób, wybierz pozycję **Niepowodzenie. Kliknij tutaj, aby uzyskać szczegółowe informacje — >** na stronie Przegląd wdrożenia. W prawej części strony wyświetli się okno z informacjami o błędzie. W obszarze **Szczegóły błędu** są widoczne identyfikatory GUID powiązanych obiektów zasad.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

Na stronie Azure Policy: wybierz pozycję **zgodność** w lewej części strony i wybierz pozycję **Pobierz bezpieczną** zasadę. Na tej stronie jest zwiększana liczba **odmowy** dla zablokowanych zasobów. Na karcie **zdarzenia** znajdują się szczegółowe informacje o tym, kto próbował utworzyć lub wdrożyć zasób, który został odrzucony przez definicję zasad.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

W tym przykładzie Trent Baker, jeden z doświadczonych specjalistów firmy Contoso w dziedzinie wirtualizacji, wykonywał swoją pracę. Musimy przyznać Trent miejsce na wyjątek. Utworzono nową grupę zasobów, **LocationsExcluded**i następne przyznaj jej wyjątek dla tego przypisania zasad.

### <a name="update-assignment-with-exclusion"></a>Aktualizacja przypisania z wykluczeniem

1. W lewej części strony usługi Azure Policy wybierz opcję **Przypisania** w obszarze **Tworzenie**.

1. Przejrzyj wszystkie przypisania zasad i Otwórz przypisanie zasady _Pobierz zabezpieczenie_ .

1. Ustaw **wykluczenie** , wybierając wielokropek i wybierając grupę zasobów do wykluczenia, _LocationsExcluded_ w tym przykładzie. Wybierz pozycję **Dodaj do wybranego zakresu** , a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad na pasku wyszukiwania." border="false":::

   > [!NOTE]
   > W zależności od definicji zasad i jej działania wykluczenia można także udzielić określonym zasobom w grupie zasobów w ramach zakresu przypisania. Ponieważ w tym samouczku użyto efektu **odmowy** , nie ma sensu ustawienia wykluczenia dla określonego zasobu, który już istnieje.

1. Wybierz pozycję **Recenzja + Zapisz** , a następnie wybierz pozycję **Zapisz**.

W tej sekcji rozwiązano problem z odmową żądania przez utworzenie wykluczenia pojedynczej grupy zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wykonujesz pracę z zasobami z tego samouczka, wykonaj następujące kroki, aby usunąć wszystkie utworzone powyżej przypisania zasad lub definicje:

1. Wybierz pozycję **Definicje** (lub **Przypisania**, jeśli próbujesz usunąć przypisanie) w obszarze **Tworzenie** w lewej części strony usługi Azure Policy.

1. Wyszukaj nowo utworzoną definicję inicjatywy lub zasad (albo przypisanie), którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wiersz albo wybierz wielokropek na końcu definicji lub przypisania, a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="review"></a>Przegląd

W tym samouczku pomyślnie wykonano następujące czynności:

> [!div class="checklist"]
> - Przypisano zasady w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> - Utworzono i przypisano definicję inicjatywy w celu śledzenia zgodności dla wielu zasobów
> - Rozwiązano problem w przypadku niezgodnego lub odrzuconego zasobu
> - Zaimplementowano nowe zasady w całej organizacji

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji zasad platformy Azure](../concepts/definition-structure.md)