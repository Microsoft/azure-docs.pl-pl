---
title: 'Samouczek: Zarządzanie zarządzaniem tagami'
description: W tym samouczku użyj modyfikuj efekt zasad platformy Azure, aby utworzyć i wymusić model nadzoru tagów na nowych i istniejących zasobach.
ms.date: 04/21/2020
ms.topic: tutorial
ms.openlocfilehash: 6319bbde2fdc8f78e2743dd5f1565c8680433fea
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759069"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Samouczek: Zarządzanie zarządzaniem znacznikami za pomocą zasad platformy Azure

[Tagi](../../../azure-resource-manager/management/tag-resources.md) są kluczowym elementem organizowania zasobów platformy Azure w taksonomię. Jeśli przestrzegasz [najlepszych rozwiązań dotyczących zarządzania tagami,](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)tagi mogą być podstawą do stosowania zasad biznesowych za pomocą zasad platformy Azure lub [śledzenia kosztów za pomocą usługi Zarządzanie kosztami.](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources)
Bez względu na to, jak i dlaczego używasz tagów, ważne jest, aby szybko dodawać, zmieniać i usuwać te znaczniki w zasobach platformy Azure. Aby sprawdzić, czy twój zasób platformy Azure obsługuje tagowanie, zobacz [Obsługa znaczników](../../../azure-resource-manager/management/tag-support.md).

Efekt [modyfikuj](../concepts/effects.md#modify) zasady platformy Azure ma na celu pomoc w zarządzaniu tagami bez względu na etap zarządzania zasobami, na jakim się znajdujesz. **Modyfikowanie** pomaga, gdy:

- Jesteś nowy w chmurze i nie masz nadzoru tagów
- Masz już tysiące zasobów bez zarządzania znacznikami
- Masz już istniejącą taksonomię, której potrzebujesz,

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> - Określanie wymagań biznesowych
> - Mapowanie każdego wymagania do definicji zasad
> - Grupowanie zasad tagów w inicjatywę

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="identify-requirements"></a>Określanie wymagań

Jak każde dobre wdrożenie kontroli zarządzania, wymagania powinny pochodzić z twoich potrzeb biznesowych i być dobrze zrozumiane przed utworzeniem kontroli technicznych. W tym samouczku scenariusza następujące elementy są nasze wymagania biznesowe:

- Dwa wymagane tagi we wszystkich zasobach: _CostCenter_ i _Env_
- _CostCenter_ musi istnieć we wszystkich kontenerach i poszczególnych zasobach
  - Zasoby dziedziczą z kontenera, w który się w niej zajdą, ale mogą być indywidualnie zastąpione
- _Env_ musi istnieć na wszystkich kontenerach i poszczególnych zasobach
  - Zasoby określają środowisko według schematu nazewnictwa kontenerów i mogą nie zostać zastąpione
  - Wszystkie zasoby w kontenerze są częścią tego samego środowiska

## <a name="configure-the-costcenter-tag"></a>Konfigurowanie tagu CostCenter

Pod względem specyficznym dla środowiska platformy Azure zarządzanego przez usługę Azure Policy wymagania dotyczące tagów _CostCenter_ wymagają następujących rozwiązań:

- Odmów grup zasobów z pominięciem znacznika _CostCenter_
- Modyfikowanie zasobów w celu dodania znacznika _CostCenter_ z nadrzędnej grupy zasobów w przypadku braku

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Odmów grup zasobów z pominięciem znacznika CostCenter

Ponieważ _costcenter_ dla grupy zasobów nie może być określona przez nazwę grupy zasobów, musi mieć znacznik zdefiniowany w żądaniu, aby utworzyć grupę zasobów. Następująca reguła zasad z efektem [Odmów](../concepts/effects.md#deny) uniemożliwia tworzenie lub aktualizowanie grup zasobów, które nie mają tagu _CostCenter:_

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
> Ponieważ ta reguła zasad jest przeznaczona dla grupy zasobów, _tryb_ definicji zasad musi być "Wszystkie" zamiast "Indeksowane".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modyfikowanie zasobów w celu dziedziczenia znacznika CostCenter w przypadku braku

Druga potrzeba _CostCenter_ jest dla wszystkich zasobów do dziedziczenia tagu z nadrzędnej grupy zasobów, gdy brakuje. Jeśli tag jest już zdefiniowany w zasobie, nawet jeśli różni się od nadrzędnej grupy zasobów, musi pozostać sam. Następująca reguła zasad używa [Modyfikuj:](../concepts/effects.md#modify)

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

Ta reguła zasad używa operacji **dodawania** zamiast **addOrReplace,** ponieważ nie chcemy zmieniać wartości znacznika, jeśli jest on obecny podczas [korygowania](../how-to/remediate-resources.md) istniejących zasobów. Używa również funkcji `[resourcegroup()]` szablonu, aby uzyskać wartość znacznika z nadrzędnej grupy zasobów.

> [!NOTE]
> Ponieważ ta reguła zasad jest przeznaczona dla zasobów obsługujących tagi, _tryb_ definicji zasad musi być "Indeksowany". Ta konfiguracja zapewnia również, że ta zasada pomija grupy zasobów.

## <a name="configure-the-env-tag"></a>Konfigurowanie tagu Env

Pod względem specyficznym dla środowiska platformy Azure zarządzanego przez usługę Azure Policy wymagania tagów _Env_ wymagają następujących rozwiązań:

- Modyfikowanie znacznika _Env_ w grupie zasobów na podstawie schematu nazewnictwa grupy zasobów
- Modyfikowanie znacznika _Env_ dla wszystkich zasobów w grupie zasobów do tej samej co nadrzędna grupa zasobów

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modyfikowanie tagu Env grup zasobów na podstawie nazwy

Zasady [modyfikowania](../concepts/effects.md#modify) jest wymagane dla każdego środowiska, które istnieje w środowisku platformy Azure. Modyfikuj zasady dla każdego wygląda mniej więcej tak definicji zasad:

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
> Ponieważ ta reguła zasad jest przeznaczona dla grupy zasobów, _tryb_ definicji zasad musi być "Wszystkie" zamiast "Indeksowane".

Ta zasada dopasowuje tylko grupy zasobów do przykładowego schematu nazewnictwa używanego dla zasobów produkcyjnych `prd-`programu . Bardziej złożony schemat nazewnictwa można osiągnąć za pomocą kilku warunków **dopasowania** zamiast **jednego, jak** w tym przykładzie.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modyfikowanie zasobów w celu dziedziczenia znacznika Env

Wymagania biznesowe wymaga wszystkich zasobów, aby mieć _env_ tag, że ich nadrzędnej grupy zasobów nie. Ten tag nie może zostać zastąpiony, więc użyjemy operacji **addOrReplace** z efektem [Modyfikowania.](../concepts/effects.md#modify) Przykładowa reguła modyfikowania wygląda następująco:

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
> Ponieważ ta reguła zasad jest przeznaczona dla zasobów obsługujących tagi, _tryb_ definicji zasad musi być "Indeksowany". Ta konfiguracja zapewnia również, że ta zasada pomija grupy zasobów.

Ta reguła zasad wyszukuje dowolny zasób, który nie ma wartości nadrzędnych grup zasobów dla tagu _Env_ lub nie ma tagu _Env._ Pasujące zasoby mają ich _env_ tag ustawiony na wartość nadrzędnych grup zasobów, nawet jeśli tag już istniał w zasobie, ale z inną wartością.

## <a name="assign-the-initiative-and-remediate-resources"></a>Przypisywanie inicjatywy i korygowanie zasobów

Po utworzeniu powyższych zasad tagów dołącz do nich w jedną inicjatywę dotycząca nadzoru tagów i przypisz je do grupy zarządzania lub subskrypcji. Inicjatywa i uwzględnione zasady następnie ocenić zgodność istniejących zasobów i zmienia żądania dla nowych lub zaktualizowanych zasobów, które odpowiadają **if** właściwości w regule zasad. Jednak zasady nie automatycznie aktualizują istniejące zasoby niezgodne ze zdefiniowanymi zmianami znaczników.

Podobnie jak [deployIfNotExists](../concepts/effects.md#deployifnotexists) zasad, **modyfikuj** zasady używa zadań korygowania, aby zmienić istniejące zasoby niezgodne. Postępuj zgodnie ze wskazówkami [dotyczącymi korygowania zasobów](../how-to/remediate-resources.md) w celu zidentyfikowania niezgodnych **zasobów Modyfikuj** zasoby i popraw znaczniki do zdefiniowanej taksonomii.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz dalszej pracy z zasobami utworzonymi w tym samouczku, wykonaj poniższe kroki, aby usunąć wszystkie utworzone powyżej przypisania lub definicje:

1. Wybierz pozycję **Definicje** (lub **Przypisania**, jeśli próbujesz usunąć przypisanie) w obszarze **Tworzenie** w lewej części strony usługi Azure Policy.

1. Wyszukaj nowo utworzoną definicję inicjatywy lub zasad (albo przypisanie), którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wiersz albo wybierz wielokropek na końcu definicji lub przypisania, a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="review"></a>Przegląd

W tym samouczku dowiesz się o następujących zadaniach:

> [!div class="checklist"]
> - Określono wymagania biznesowe
> - Mapowanie każdego wymogu do definicji zasad
> - Pogrupowanie zasad znaczników w inicjatywę

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji zasad platformy Azure](../concepts/definition-structure.md)