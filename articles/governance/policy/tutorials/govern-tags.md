---
title: 'Samouczek: zarządzanie tagami tagów'
description: W tym samouczku użyjesz efektu Modyfikuj Azure Policy, aby utworzyć i wymusić model ładu znacznika dla nowych i istniejących zasobów.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 1e8850dd6fe380b0ea38a2a1722734d917c63d0c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093286"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Samouczek: Zarządzanie zarządzaniem tagów przy użyciu Azure Policy

[Tagi](../../../azure-resource-manager/management/tag-resources.md) są kluczową częścią organizowania zasobów platformy Azure w taksonomię. Po zastosowaniu [najlepszych rozwiązań w zakresie zarządzania tagami](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)Tagi mogą być podstawą stosowania zasad firmowych z Azure Policy lub [śledzeniem kosztów z Cost Management](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources).
Niezależnie od tego, jak i dlaczego używasz tagów, ważne jest, aby można je szybko dodawać, zmieniać i usuwać z zasobów platformy Azure. Aby sprawdzić, czy zasób platformy Azure obsługuje tagowanie, zobacz [obsługa tagów](../../../azure-resource-manager/management/tag-support.md).

Efekt [modyfikacji](../concepts/effects.md#modify) Azure Policy został zaprojektowany w celu ułatwienia zarządzania tagami niezależnie od tego, jaki etap nadzoru zasobów należy do Ciebie. **Modyfikuj** ułatwia:

- Jesteś nowym chmurą i nie posiadasz ładu
- Ma już tysiące zasobów bez zarządzania tagami
- Masz już istniejącą taksonomię, którą chcesz zmienić

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> - Określanie wymagań biznesowych
> - Mapuj każde wymaganie do definicji zasad
> - Grupowanie zasad znaczników w ramach inicjatywy

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="identify-requirements"></a>Określanie wymagań

Podobnie jak jakakolwiek dobra implementacja kontroli ładu, wymagania powinny pochodzić z Twoich potrzeb firmy i być dobrze zrozumiałe przed utworzeniem kontroli technicznej. W tym samouczku przedstawiono następujące wymagania biznesowe:

- Dwa wymagane Tagi dla wszystkich zasobów: _CostCenter_ i _ENV_
- _CostCenter_ musi istnieć na wszystkich kontenerach i poszczególnych zasobach
  - Zasoby dziedziczą z kontenera, w którym się znajdują, ale mogą być indywidualnie zastępowane
- _Koperta_ musi istnieć na wszystkich kontenerach i poszczególnych zasobach
  - Zasoby określają środowisko przez schemat nazewnictwa kontenerów i nie mogą zostać zastąpione
  - Wszystkie zasoby w kontenerze są częścią tego samego środowiska

## <a name="configure-the-costcenter-tag"></a>Konfigurowanie tagu CostCenter

W odniesieniu do środowiska platformy Azure zarządzanego przez Azure Policy wymagania dotyczące tagów _CostCenter_ są zgodne z następującymi wynikami:

- Odmów braku tagu _CostCenter_ w grupach zasobów
- Modyfikuj zasoby, aby dodać tag _CostCenter_ z nadrzędnej grupy zasobów, gdy brakuje

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Odmów braku tagu CostCenter w grupach zasobów

Ponieważ _CostCenter_ dla grupy zasobów nie może być określony przez nazwę grupy zasobów, musi mieć tag zdefiniowany w żądaniu, aby utworzyć grupę zasobów. Następująca reguła zasad z efektem [odmowy](../concepts/effects.md#deny) uniemożliwia tworzenie lub aktualizowanie grup zasobów, które nie mają znacznika _CostCenter_ :

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Ponieważ ta reguła zasad jest przeznaczona dla grupy zasobów, _tryb_ definicji zasad musi mieć wartość "All", a nie "Indexed".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modyfikuj zasoby, aby dziedziczyły tag CostCenter w razie braku

Druga _CostCenter_ potrzebna jest dla wszystkich zasobów, aby odziedziczyć tag z nadrzędnej grupy zasobów, gdy nie ma. Jeśli tag jest już zdefiniowany w zasobie, nawet jeśli różni się od nadrzędnej grupy zasobów, musi pozostać pojedynczo. Następująca reguła zasad używa [modyfikacji](../concepts/effects.md#modify):

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Ta reguła zasad używa operacji **dodawania** zamiast **addOrReplace** , ponieważ nie chcemy zmieniać wartości tagu, jeśli występuje, gdy [korygowaniem](../how-to/remediate-resources.md) istniejące zasoby. Używa również `[resourcegroup()]` funkcji szablonu, aby pobrać wartość tagu z nadrzędnej grupy zasobów.

> [!NOTE]
> Ponieważ ta reguła zasad odwołuje się do zasobów, które obsługują Tagi, _tryb_ w definicji zasad musi mieć wartość "Indexed". Ta konfiguracja gwarantuje również, że te zasady pomijają grupy zasobów.

## <a name="configure-the-env-tag"></a>Konfigurowanie tagu ENV

W odniesieniu do środowiska platformy Azure zarządzanego przez Azure Policy, wymagania dotyczące tagów _ENV_ są zgodne z następującymi wynikami:

- Zmodyfikuj tag _ENV_ w grupie zasobów w oparciu o schemat nazewnictwa grupy zasobów.
- Zmodyfikuj tag _ENV_ dla wszystkich zasobów w grupie zasobów, tak samo jak nadrzędna grupa zasobów

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modyfikuj tag ENV grup zasobów na podstawie nazwy

Zasady [modyfikowania](../concepts/effects.md#modify) są wymagane dla każdego środowiska, które istnieje w środowisku platformy Azure. Zasady Modyfikuj dla każdej z nich wyglądają podobnie jak w przypadku tej definicji zasad:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Ponieważ ta reguła zasad jest przeznaczona dla grupy zasobów, _tryb_ definicji zasad musi mieć wartość "All", a nie "Indexed".

Te zasady są zgodne z grupami zasobów z przykładowym schematem nazewnictwa używanym w przypadku zasobów produkcyjnych programu `prd-` . Bardziej skomplikowany schemat nazewnictwa można osiągnąć przy użyciu kilku warunków **dopasowania** zamiast jednego **takiego jak** w tym przykładzie.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modyfikuj zasoby, aby dziedziczyły tag ENV

Wymagania biznesowe dla wszystkich zasobów mają tag _ENV_ , którego nadrzędna grupa zasobów wykonuje. Ten tag nie może zostać zastąpiony, dlatego użyjemy operacji **addOrReplace** z efektem [modyfikacji](../concepts/effects.md#modify) . Przykładowa modyfikacja zasad wygląda następująco:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Ponieważ ta reguła zasad odwołuje się do zasobów, które obsługują Tagi, _tryb_ w definicji zasad musi mieć wartość "Indexed". Ta konfiguracja gwarantuje również, że te zasady pomijają grupy zasobów.

Ta reguła zasad szuka dowolnego zasobu, który nie ma wartości nadrzędnych grup zasobów dla tagu _ENV_ lub nie zawiera taga _ENV_ . Pasujące zasoby mają swój tag _ENV_ ustawiony na wartość nadrzędnych grup zasobów, nawet jeśli tag już istnieje w zasobie, ale z inną wartością.

## <a name="assign-the-initiative-and-remediate-resources"></a>Przypisywanie inicjatywy i korygowanie zasobów

Po utworzeniu powyższych zasad dotyczących tagów Dołącz je do jednej inicjatywy w celu zarządzania tagami i przypisz je do grupy zarządzania lub subskrypcji. W ramach inicjatywy i uwzględnionych zasad należy oszacować zgodność istniejących zasobów i zmienić żądania dotyczące nowych lub zaktualizowanych zasobów, które pasują do właściwości **if** w regule zasad. Jednak zasady nie aktualizują automatycznie istniejących niezgodnych zasobów ze zdefiniowanymi zmianami tagu.

Podobnie jak zasady [deployIfNotExists](../concepts/effects.md#deployifnotexists) , zasady **modyfikowania** używają zadań korygowania do zmiany istniejących niezgodnych zasobów. Postępuj zgodnie z instrukcjami dotyczącymi [sposobu korygowania zasobów](../how-to/remediate-resources.md) w celu zidentyfikowania niezgodnych zasobów **modyfikacji** i skorygowania tagów do zdefiniowanej taksonomii.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz dalszej pracy z zasobami utworzonymi w tym samouczku, wykonaj poniższe kroki, aby usunąć wszystkie utworzone powyżej przypisania lub definicje:

1. Wybierz pozycję **Definicje** (lub **Przypisania**, jeśli próbujesz usunąć przypisanie) w obszarze **Tworzenie** w lewej części strony usługi Azure Policy.

1. Wyszukaj nowo utworzoną definicję inicjatywy lub zasad (albo przypisanie), którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wiersz albo wybierz wielokropek na końcu definicji lub przypisania, a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="review"></a>Przegląd

W tym samouczku przedstawiono informacje o następujących zadaniach:

> [!div class="checklist"]
> - Określono wymagania biznesowe
> - Zamapowane każde wymaganie do definicji zasad
> - Grupowanie zasad znaczników w ramach inicjatywy

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji zasad platformy Azure](../concepts/definition-structure.md)