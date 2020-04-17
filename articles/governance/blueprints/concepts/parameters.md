---
title: Używanie parametrów do tworzenia dynamicznych planów
description: Dowiedz się więcej o parametrach statycznych i dynamicznych oraz o tym, jak ich używać do tworzenia bezpiecznych i dynamicznych planów.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: e5953617d5fa27098380f3f0e95843c69800f823
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458492"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Tworzenie dynamicznych planów za pomocą parametrów

W pełni zdefiniowany plan z różnymi artefaktami (takimi jak grupy zasobów, szablony Menedżera zasobów, zasady lub przypisania ról) oferuje szybkie tworzenie i spójne tworzenie obiektów na platformie Azure. Aby umożliwić elastyczne korzystanie z tych wzorców projektowych wielokrotnego użytku i kontenerów, usługi Azure Blueprints obsługuje parametry. Parametr tworzy elastyczność, zarówno podczas definicji i przypisania, aby zmienić właściwości artefaktów wdrożonych przez plan.

Prostym przykładem jest artefakt grupy zasobów. Podczas tworzenia grupy zasobów ma dwie wymagane wartości, które muszą być podane: nazwa i lokalizacja. Podczas dodawania grupy zasobów do planu, jeśli parametry nie istnieją, należy zdefiniować tę nazwę i lokalizację dla każdego użycia planu. To powtórzenie spowodowałoby każde użycie planu do tworzenia artefaktów w tej samej grupie zasobów. Zasoby wewnątrz tej grupy zasobów staną się zduplikowane i spowodować konflikt.

> [!NOTE]
> Nie jest to problem dla dwóch różnych planów, aby uwzględnić grupę zasobów o tej samej nazwie.
> Jeśli grupa zasobów zawarte w planie już istnieje, plan nadal tworzyć artefakty pokrewne w tej grupie zasobów. Może to spowodować konflikt, ponieważ dwa zasoby o tej samej nazwie i typie zasobu nie mogą istnieć w ramach subskrypcji.

Rozwiązaniem tego problemu są parametry. Plany platformy Azure umożliwia zdefiniowanie wartości dla każdej właściwości artefaktu podczas przypisywania do subskrypcji. Parametr umożliwia ponowne użycie planu, który tworzy grupę zasobów i inne zasoby w ramach jednej subskrypcji bez konfliktów.

## <a name="blueprint-parameters"></a>Parametry strategii

Za pośrednictwem interfejsu API REST parametry mogą być tworzone na plan sam. Parametry te różnią się od parametrów w każdym z obsługiwanych artefaktów. Gdy parametr jest tworzony na planie, może być używany przez artefakty w tym planie. Przykładem może być prefiks nazewnictwa grupy zasobów. Artefakt można użyć parametru planu, aby utworzyć parametr "głównie dynamiczny". Ponieważ parametr można również zdefiniować podczas przypisywania, ten wzorzec umożliwia spójność, która może być zgodna z regułami nazewnictwa. Aby uzyskać instrukcje, zobacz [ustawianie parametrów statycznych - parametr poziomu planu](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Korzystanie z parametrów secureString i secureObject

Podczas gdy _artefakt_ szablonu Menedżera zasobów obsługuje parametry typów **secureString** i **secureObject,** plany platformy Azure wymagają, aby każdy z nich był połączony z usługą Azure Key Vault. Ten środek bezpieczeństwa zapobiega niebezpiecznej praktyce przechowywania tajemnic wraz z planem działania i zachęca do zatrudniania bezpiecznych wzorców. Usługa Azure Blueprints obsługuje tę miarę zabezpieczeń, wykrywając włączenie jednego z bezpiecznych parametrów w _artefaktu_szablonu Menedżera zasobów. Następnie usługa monituje podczas przypisywania następujących właściwości usługi Key Vault na wykryty bezpieczny parametr:

- Identyfikator zasobu magazynu kluczy
- Nazwa tajnego magazynu kluczy
- Tajna wersja magazynu kluczy

Jeśli przypisanie planu używa **tożsamości zarządzanej przypisanej do systemu,** magazyn kluczy, do którego istnieje odwołanie, _musi_ istnieć w tej samej subskrypcji, do której jest przypisana definicja planu.

Jeśli przypisanie planu używa **tożsamości zarządzanej przypisanej przez użytkownika,** w scentralizowanej subskrypcji _może istnieć_ usługa Key Vault, do której istnieje odwołanie. Tożsamość zarządzana musi mieć odpowiednie prawa do magazynu kluczy przed przypisaniem planu.

> [!IMPORTANT]
> W obu przypadkach magazyn kluczy musi mieć **włącz dostęp do usługi Azure Resource Manager dla wdrażania szablonów** skonfigurowany na stronie **zasady programu Access.** Aby uzyskać wskazówki dotyczące włączania tej funkcji, zobacz [Usługa Key Vault — włączanie wdrażania szablonów](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Omówienie usługi Key Vault](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Typy parametrów

### <a name="static-parameters"></a>Parametry statyczne

Wartość parametru zdefiniowana w definicji planu jest nazywana **parametrem statycznym,** ponieważ każde użycie planu wdroży artefakt przy użyciu tej wartości statycznej. W przykładzie grupy zasobów, podczas gdy nie ma sensu dla nazwy grupy zasobów, może mieć sens dla lokalizacji. Następnie każde przypisanie planu spowoduje utworzenie grupy zasobów, niezależnie od tego, jak nazywa się ją podczas przypisywania, w tej samej lokalizacji. Ta elastyczność pozwala być selektywne w tym, co można zdefiniować jako wymagane vs co można zmienić podczas przypisywania.

#### <a name="setting-static-parameters-in-the-portal"></a>Ustawianie parametrów statycznych w portalu

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **definicje planu** ze strony po lewej stronie.

1. Kliknij istniejący plan, a następnie kliknij pozycję **Edytuj plan** lub kliknij **+ Utwórz plan** i wypełnij informacje na karcie **Podstawy.**

1. Kliknij **dalej: Artefakty** LUB kliknij kartę **Artefakty.**

1. Artefakty dodane do planu, które mają opcje **parametrów,** wyświetlają parametry X Y wypełnione w kolumnie **Parametry.** Kliknij wiersz artefaktu, aby edytować parametry artefaktu.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Parametry planu w definicji planu" border="false":::

1. Na stronie **Edytuj artefakt** są wyświetlane opcje wartości odpowiednie dla klikniętyego artefaktu. Każdy parametr artefaktu ma tytuł, pole wartości i pole wyboru. Ustaw to pole wyboru jako niezaznaczone, aby uczynić go **parametrem statycznym**. W poniższym przykładzie tylko _Lokalizacja_ jest **parametrem statycznym,** ponieważ jest on niesprawdzony i _nazwa grupy zasobów_ jest zaznaczona.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Parametry statyczne planu na artefakt planu" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Ustawianie parametrów statycznych z interfejsu REST API

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

##### <a name="blueprint-level-parameter"></a>Parametr poziomu planu

Podczas tworzenia planu za pośrednictwem interfejsu API REST możliwe jest utworzenie [parametrów planu](#blueprint-parameters). Aby to zrobić, należy użyć następującego identyfikatora URI interfejsu API REST i formatu treści:

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Po utworzeniu parametru poziomu planu, może być używany na artefakty dodane do tego planu.
Poniższy przykład interfejsu API REST tworzy artefakt przypisania roli na planie i używa parametru poziomu planu.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

W tym przykładzie **principalIds** właściwość używa **parametru** poziomu `[parameters('owners')]`planu właścicieli przy użyciu wartości . Ustawienie parametru na artefakt przy użyciu parametru poziomu planu jest nadal przykładem **parametru statycznego**. Nie można ustawić parametru poziomu planu podczas przypisywania planu i będzie to ta sama wartość dla każdego przypisania.

##### <a name="artifact-level-parameter"></a>Parametr poziomu artefaktu

Tworzenie **parametrów statycznych** na artefakt jest podobny, ale ma `parameters()` wartość prostą zamiast korzystania z funkcji. Poniższy przykład tworzy dwa parametry statyczne: **tagName** i **tagValue**. Wartość na każdym z nich jest bezpośrednio podana i nie używa wywołania funkcji.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parametry dynamiczne

Przeciwieństwem parametru statycznego jest **parametr dynamiczny**. Ten parametr nie jest zdefiniowany w planie, ale zamiast tego jest zdefiniowany podczas każdego przypisania planu. W przykładzie grupy zasobów użycie **parametru dynamicznego** ma sens dla nazwy grupy zasobów. Zawiera inną nazwę dla każdego przypisania planu. Aby uzyskać listę funkcji planu, zobacz odwołanie do [funkcji planu.](../reference/blueprint-functions.md)

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ustawianie parametrów dynamicznych w portalu

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **definicje planu** ze strony po lewej stronie.

1. Kliknij prawym przyciskiem myszy plan, który chcesz przypisać. Wybierz **pozycję Przypisz plan** lub kliknij plan, który chcesz przypisać, a następnie kliknij przycisk **Przypisz plan.**

1. Na stronie **Przypisywanie planu** znajdź sekcję **Parametry artefaktu.** Każdy artefakt z co najmniej jednym **parametrem dynamicznym** wyświetla artefakt i opcje konfiguracji. Przed przypisaniem planu należy podać wymagane wartości do parametrów. W poniższym przykładzie _Nazwa_ jest **parametrem dynamicznym,** który musi zostać zdefiniowany w celu ukończenia przypisania planu.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Parametr dynamiczny planu podczas przypisywania planu" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ustawianie parametrów dynamicznych z interfejsu REST API

Ustawianie **parametrów dynamicznych** podczas przypisywania odbywa się bezpośrednio przez wprowadzenie wartości. Zamiast funkcji, takiej jak [parameters(),](../reference/blueprint-functions.md#parameters)podana wartość jest odpowiednim ciągiem. Artefakty dla grupy zasobów są definiowane za pomocą "nazwy szablonu", **nazwy**i właściwości **lokalizacji.** Wszystkie inne parametry dla dołączonego artefaktu są definiowane w ramach **parametrów** z parą kluczy ** \<\> nazwy** i **wartości.** Jeśli plan jest skonfigurowany dla parametru dynamicznego, który nie jest podany podczas przypisywania, przypisanie zakończy się niepowodzeniem.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Następne kroki

- Zobacz listę [funkcji planu](../reference/blueprint-functions.md).
- Uzyskaj informacje na temat [cyklu życia strategii](lifecycle.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).