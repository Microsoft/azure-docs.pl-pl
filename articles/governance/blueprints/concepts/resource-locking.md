---
title: Zrozumienie blokowania zasobów
description: Dowiedz się więcej na temat opcji blokowania w planach platformy Azure w celu ochrony zasobów podczas przypisywania planu.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 30d5528b4613dc04d1e825d10e11b7eeadc57698
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534866"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Zrozumienie blokowania zasobów w planach platformy Azure

Tworzenie spójnych środowisk na dużą skalę jest naprawdę cenne, jeśli istnieje mechanizm zapewnienia spójności. W tym artykule wyjaśniono, jak blokada zasobów działa w planach platformy Azure. Aby zapoznać się z przykładem blokowania zasobów i stosowania _przypisań Odmów_, zobacz artykuł [Ochrona nowych zasobów](../tutorials/protect-new-resources.md) .

> [!NOTE]
> Blokady zasobów wdrożone przez plany platformy Azure są stosowane tylko do zasobów wdrożonych przez przypisanie planu. Istniejące zasoby, takie jak te w grupach zasobów, które już istnieją, nie mają do nich dodanych blokad.

## <a name="locking-modes-and-states"></a>Tryby blokowania i Stany

Tryb blokowania ma zastosowanie do przypisania planu i ma trzy opcje: **nie blokuj**, **tylko do odczytu**lub nie **usuwaj**. Tryb blokowania jest konfigurowany podczas wdrażania artefaktu podczas jego przypisywania. Inny tryb blokowania można ustawić, aktualizując przypisanie planu.
Nie można jednak zmienić trybów blokowania poza planami platformy Azure.

Zasoby utworzone przez artefakty w przypisaniu planu mają cztery stany: **nie zablokowane**, **tylko do odczytu**, **nie można edytować/usuwać**ani **nie mogą go usunąć**. Każdy typ artefaktu może być w stanie **niezablokowanym** . Poniższa tabela może służyć do określenia stanu zasobu:

|Tryb|Typ zasobu artefaktu|Stan|Opis|
|-|-|-|-|
|Nie blokuj|*|Niezablokowane|Zasoby nie są chronione przez plany platformy Azure. Ten stan jest również używany w przypadku zasobów dodanych do elementu " **tylko do odczytu** " lub nie **usuwaj** artefaktu grupy zasobów spoza przypisania planu.|
|Tylko do odczytu|Grupa zasobów|Nie można edytować/usunąć|Grupa zasobów jest tylko do odczytu i nie można modyfikować tagów w grupie zasobów. **Niezablokowane** zasoby mogą być dodawane, przenoszone, zmieniane lub usuwane z tej grupy zasobów.|
|Tylko do odczytu|Grupa niezasobów|Tylko do odczytu|Nie można zmienić zasobu w jakikolwiek sposób. Nie wprowadzono żadnych zmian i nie można go usunąć.|
|Nie usuwaj|*|Nie można usunąć|Zasoby można zmienić, ale nie można ich usunąć. **Niezablokowane** zasoby mogą być dodawane, przenoszone, zmieniane lub usuwane z tej grupy zasobów.|

## <a name="overriding-locking-states"></a>Zastępowanie Stanów blokowania

Jest to zwykle możliwe dla kogoś, kto ma odpowiednią [kontrolę dostępu opartą na rolach (Azure RBAC)](../../../role-based-access-control/overview.md) w ramach subskrypcji, taką jak rola "właściciel", aby umożliwić zmianę lub usunięcie dowolnego zasobu. Ten dostęp nie jest dozwolony w przypadku, gdy plany platformy Azure stosują blokowanie w ramach wdrożonego przypisania. Jeśli przypisanie zostało ustawione przy użyciu opcji **tylko do odczytu** lub **nie usuwaj** , a właściciel subskrypcji może wykonać zablokowaną akcję dla chronionego zasobu.

Ten środek zabezpieczeń chroni spójność zdefiniowanego planu i środowiska, które zostało zaprojektowane do tworzenia z przypadkowego lub programowego usunięcia lub zmiany.

### <a name="assign-at-management-group"></a>Przypisz w grupie zarządzania

Dodatkowa opcja uniemożliwiająca właścicielom subskrypcji usuwanie przypisania strategii polega na przypisaniu planu do grupy zarządzania. W tym scenariuszu tylko **właściciele** grupy zarządzania mają uprawnienia, które są konieczne do usunięcia przypisania planu.

Aby przypisać plan do grupy zarządzania zamiast subskrypcji, wywołanie interfejsu API REST zmieni się, aby wyglądać następująco:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Grupa zarządzania zdefiniowana przez `{assignmentMG}` musi znajdować się w hierarchii grupy zarządzania lub być tą samą grupą zarządzania, w której jest zapisywana definicja planu.

Treść żądania przypisania strategii wygląda następująco:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Kluczową różnicą w tej treści żądania i jedną przypisaną do subskrypcji jest `properties.scope` Właściwość. Ta wymagana właściwość musi być ustawiona na subskrypcję, do której ma zastosowanie przypisanie planu. Subskrypcja musi być bezpośrednim elementem podrzędnym hierarchii grupy zarządzania, w której jest przechowywane przypisanie planu.

> [!NOTE]
> Plan przypisany do zakresu grupy zarządzania nadal działa jako przypisanie strategii na poziomie subskrypcji. Jedyną różnicą jest to, że przypisanie strategii jest przechowywane, aby uniemożliwić właścicielom subskrypcji usuwanie przypisania i skojarzonych blokad.

## <a name="removing-locking-states"></a>Usuwanie Stanów blokowania

Jeśli konieczna będzie modyfikacja lub usunięcie zasobu chronionego przez przypisanie, istnieją dwa sposoby tego celu.

- Aktualizowanie przypisania planu do trybu blokowania **nie blokuj**
- Usuń przypisanie planu

Po usunięciu przypisania blokady utworzone przez plany platformy Azure są usuwane. Zasób jest jednak pozostawiony i musi zostać usunięty w normalny sposób.

## <a name="how-blueprint-locks-work"></a>Jak działają blokowane plany

Akcja Odmów [przypisywania Odmów dostępu](../../../role-based-access-control/deny-assignments.md) RBAC na platformie Azure jest stosowana do zasobów artefaktów podczas przypisywania planu, jeśli przypisanie zabrało opcję **tylko do odczytu** lub **nie usuwaj** . Akcja Odmów jest dodawana przez zarządzaną tożsamość przypisania planu i może zostać usunięta tylko z zasobów artefaktów przez tę samą tożsamość zarządzaną. Ta miara zabezpieczeń wymusza mechanizm blokowania i uniemożliwia usunięcie blokady planu poza planami platformy Azure.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Zrzut ekranu przedstawiający stronę kontroli dostępu (I M) oraz kartę Odmów przypisań dla grupy zasobów." border="false":::

[Właściwości przypisania Odmów](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) dla każdego trybu są następujące:

|Tryb |Uprawnienia. akcje |Uprawnienia. nonaruszone |Podmioty zabezpieczeń [i]. Wprowadź |ExcludePrincipals [i]. #C1 | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Tylko do odczytu |**\*** |**\*/read** |SystemDefined (wszyscy) |przypisanie strategii i zdefiniowane przez użytkownika w **excludedPrincipals** |Grupa zasobów — _true_; Zasób — _Fałsz_ |
|Nie usuwaj |**\*/** | |SystemDefined (wszyscy) |przypisanie strategii i zdefiniowane przez użytkownika w **excludedPrincipals** |Grupa zasobów — _true_; Zasób — _Fałsz_ |

> [!IMPORTANT]
> Azure Resource Manager buforuje Szczegóły przydziału roli przez maksymalnie 30 minut. W związku z tym odmowa akcji Odmów przypisań na zasoby strategii może nie być od razu całkowicie w pełni funkcjonalna. W tym czasie może być możliwe usunięcie zasobu przeznaczonego do ochrony przez blokady planu.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Wyklucz podmiot zabezpieczeń z przypisania Odmów

W niektórych scenariuszach projektowych lub zabezpieczających może być konieczne wykluczenie podmiotu zabezpieczeń z [przydziału Odmów](../../../role-based-access-control/deny-assignments.md) , które tworzy. Ten krok jest realizowany w interfejsie API REST przez dodanie maksymalnie pięciu wartości do tablicy **excludedPrincipals** we właściwości **Locks** podczas [tworzenia przypisania](/rest/api/blueprints/assignments/createorupdate). Następująca definicja przypisania jest przykładem treści żądania, która zawiera **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Wyklucz akcję z przypisania odmowy

Podobnie jak w przypadku [wykluczenia podmiotu zabezpieczeń](#exclude-a-principal-from-a-deny-assignment) [w przypisaniu planu](../../../role-based-access-control/deny-assignments.md) , można wykluczyć określone [operacje dostawcy zasobów platformy Azure](../../../role-based-access-control/resource-provider-operations.md). W bloku **właściwości. blokady** w tym samym miejscu, w którym znajduje się **excludedPrincipals** , można dodać **excludedActions** :

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Mimo że **excludedPrincipals** musi być jawne, wpisy **excludedActions** mogą używać `*` do dopasowania symboli wieloznacznych w ramach operacji dostawcy zasobów.

## <a name="next-steps"></a>Następne kroki

- Postępuj zgodnie z samouczkiem [Ochrona nowych zasobów](../tutorials/protect-new-resources.md) .
- Uzyskaj informacje na temat [cyklu życia strategii](./lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](./parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](./sequencing-order.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).