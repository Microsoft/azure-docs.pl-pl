---
title: Informacje o działaniu efektów
description: Definicje Azure Policy mają różne skutki, które określają sposób zarządzania i zgłaszania zgodności.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: 67445b3d0d63b3827f82822de00412bdab67c5ab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741824"
---
# <a name="understand-azure-policy-effects"></a>Zrozumienie efektów Azure Policy

Każda definicja zasad w usłudze Azure Policy ma odzwierciedlenie w pojedynczym efekcie. Ten efekt określa, co się dzieje, gdy reguła zasad zostanie oceniona pod kątem zgodności. Efekty te działają inaczej, jeśli są dla nowego zasobu, zaktualizowanego zasobu lub istniejącego zasobu.

Te efekty są obecnie obsługiwane w definicji zasad:

- [Append](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [Modify](#modify)

Następujące efekty są _przestarzałe_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> Zamiast efektów **EnforceOPAConstraint** lub **EnforceRegoPolicy** należy używać _inspekcji_ i _Odmów_ z trybem dostawcy zasobów `Microsoft.Kubernetes.Data` . Wbudowane definicje zasad zostały zaktualizowane. Gdy istniejące przypisania zasad tych wbudowanych definicji zasad są modyfikowane, należy zmienić parametr _efektu_ na wartość na zaktualizowanej liście _allowedValues_ .

## <a name="order-of-evaluation"></a>Kolejność obliczania

Żądania utworzenia lub zaktualizowania zasobu są oceniane przez Azure Policy pierwsze. Azure Policy tworzy listę wszystkich przypisań, które są stosowane do zasobu, a następnie szacuje zasób dla każdej definicji. W przypadku [trybu Menedżer zasobów](./definition-structure.md#resource-manager-modes)Azure Policy przetwarza kilka efektów przed wysłaniem żądania do odpowiedniego dostawcy zasobów. To zamówienie zapobiega niepotrzebnemu przetwarzaniu przez dostawcę zasobów, gdy zasób nie jest zgodny z zaprojektowanymi kontrolkami ładu Azure Policy. W [trybie dostawcy zasobów](./definition-structure.md#resource-provider-modes)dostawca zasobów zarządza oceną i wynikiem oraz raportuje wyniki z powrotem do Azure Policy.

- **Wyłączone** jest najpierw zaznaczone, aby określić, czy reguła zasad powinna zostać oceniona.
- Następnie są **oceniane i** **modyfikowane** . Ponieważ może on zmienić żądanie, wprowadzona zmiana może uniemożliwić wywoływanie inspekcji lub skutków odmowy. Efekty te są dostępne tylko w trybie Menedżer zasobów.
- Następnie zostanie obliczone **odmowa** . Oceniając odmowę przed inspekcją, nie jest blokowane podwójne rejestrowanie niepożądanego zasobu.
- **Inspekcja** jest szacowana jako Ostatnia.

Gdy dostawca zasobów zwróci kod sukcesu w żądaniu w trybie Menedżer zasobów, **AuditIfNotExists** i **DeployIfNotExists** ocenę, aby określić, czy wymagane jest dodatkowe rejestrowanie zgodności lub akcja.

Ponadto `PATCH` żądania, które modyfikują tylko `tags` powiązane pola, ograniczają ocenę zasad do zasad zawierających warunki, które sprawdzają `tags` powiązane pola.

## <a name="append"></a>Append

Dołączanie służy do dodawania dodatkowych pól do żądanego zasobu podczas tworzenia lub aktualizowania. Typowym przykładem jest określenie dozwolonych adresów IP dla zasobu magazynu.

> [!IMPORTANT]
> Dołączenie jest przeznaczone do użycia z właściwościami niebędącymi tagami. Podczas gdy Dołączanie może dodawać Tagi do zasobu w trakcie żądania utworzenia lub aktualizacji, zaleca się użycie zamiast nich efektów [modyfikacji](#modify) dla tagów.

### <a name="append-evaluation"></a>Dołączanie oceny

Dołączanie daje ocenę przed przetworzeniem żądania przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje pola do zasobu, gdy spełniony **jest warunek reguły** zasad. Jeśli efekt dołączania spowodowałoby zmianę wartości w oryginalnym żądaniu z inną wartością, działa jako efekt odmowy i odrzuca żądanie. Aby dołączyć nową wartość do istniejącej tablicy, użyj **\[\*\]** wersji aliasu.

Gdy definicja zasad używająca efektu dołączania jest uruchamiana w ramach cyklu oceny, nie wprowadza zmian do już istniejących zasobów. Zamiast tego oznacza wszelkie zasoby, które spełniają warunek **if** jako niezgodne.

### <a name="append-properties"></a>Dołącz właściwości

Efekt dołączania zawiera tylko tablicę **szczegółów** , która jest wymagana. Ponieważ **szczegóły** są tablicami, może przyjmować jedną parę **pól/wartości** lub wiele. Zapoznaj się ze [strukturą definicji](definition-structure.md#fields) , aby uzyskać listę akceptowalnych pól.

### <a name="append-examples"></a>Dołącz przykłady

Przykład 1: pojedyncze pary **pól/wartości** używające **\[\*\]** [niealiasu](definition-structure.md#aliases) z **wartością** tablicową do ustawiania reguł IP na koncie magazynu. Gdy **\[\*\]** alias nie jest tablicą, efekt dołącza **wartość** jako całą tablicę. Jeśli tablica już istnieje, zdarzenie odmowy występuje z powodu konfliktu.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Przykład 2: pojedyncze pary **pól/wartości** przy użyciu **\[\*\]** [aliasu](definition-structure.md#aliases) z **wartością** tablicy do ustawiania reguł IP na koncie magazynu. Korzystając z **\[\*\]** aliasu, efekt dołącza **wartość** do potencjalnie istniejącej tablicy. Jeśli tablica jeszcze nie istnieje, zostanie utworzona.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>Inspekcja

Inspekcja służy do tworzenia zdarzenia ostrzegawczego w dzienniku aktywności podczas oceniania niezgodnego zasobu, ale nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena inspekcji

Inspekcja jest ostatnim efektem sprawdzonym przez Azure Policy podczas tworzenia lub aktualizowania zasobu. W przypadku trybu Menedżer zasobów Azure Policy następnie wysyła zasób do dostawcy zasobów. Inspekcja działa tak samo dla żądania zasobu i cyklu oceny. W przypadku nowych i zaktualizowanych zasobów Azure Policy dodaje `Microsoft.Authorization/policies/audit/action` do dziennika aktywności operację i oznacza zasób jako niezgodny.

### <a name="audit-properties"></a>Właściwości inspekcji

W przypadku trybu Menedżer zasobów efekt inspekcji nie ma żadnych dodatkowych właściwości do użycia w warunku **then** definicji zasad.

W przypadku trybu dostawcy zasobów dla programu `Microsoft.Kubernetes.Data` efekt inspekcji ma następujące dodatkowe właściwości podrzędne. 

- **constraintTemplate** (wymagane)
  - Szablon ograniczenia CustomResourceDefinition (CRD), który definiuje nowe ograniczenia. Szablon definiuje logikę rego, schemat ograniczenia i parametry ograniczenia, które są przekazane za pośrednictwem **wartości** z Azure Policy.
- **ograniczenie** (wymagane)
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przesyłanych za pośrednictwem **wartości** jako `{{ .Values.<valuename> }}` . W przykładzie 2 poniżej te wartości są `{{ .Values.excludedNamespaces }}` i `{{ .Values.allowedContainerImagesRegex }}` .
- **wartości** (opcjonalne)
  - Definiuje wszelkie parametry i wartości, które mają zostać przekazane do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="audit-example"></a>Przykład inspekcji

Przykład 1: używanie efektu inspekcji dla trybów Menedżer zasobów.

```json
"then": {
    "effect": "audit"
}
```

Przykład 2: używanie efektu inspekcji dla trybu dostawcy zasobów `Microsoft.Kubernetes.Data` . Dodatkowe informacje w obszarze **szczegóły** definiują szablon ograniczenia i CRD, które mają być używane w Kubernetes w celu ograniczenia dozwolonych obrazów kontenerów.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists umożliwia inspekcję zasobów _związanych_ z zasobem, które pasują do warunku **if** , ale nie mają właściwości określonych w **szczegółów** warunku **then** .

### <a name="auditifnotexists-evaluation"></a>Ocena AuditIfNotExists

AuditIfNotExists jest uruchamiany po obsłudze żądania Create lub Update zasobu przez dostawcę zasobów i zwróciło kod stanu sukcesu. Inspekcja występuje, gdy nie ma żadnych powiązanych zasobów lub jeśli zasoby zdefiniowane przez **ExistenceCondition** nie są oceniane na wartość true. W przypadku nowych i zaktualizowanych zasobów Azure Policy dodaje `Microsoft.Authorization/policies/audit/action` do dziennika aktywności operację i oznacza zasób jako niezgodny. Gdy wyzwalane, zasób, który spełnił warunek **if** , jest zasobem oznaczonym jako niezgodny.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

Właściwość **Details** efektów AuditIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania.

- **Typ** (wymagany)
  - Określa typ powiązanego zasobu do dopasowania.
  - Jeśli **details. Type** jest typem zasobu poniżej zasobu warunku **if** , zasady zapytania dotyczące zasobów tego **typu** w zakresie szacowanego zasobu. W przeciwnym razie zapytania zasad w ramach tej samej grupy zasobów co obliczony zasób.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady umożliwiają pobranie jednego określonego zasobu zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku dla opcji **IF. Field. Type** i **then. details. Type** są zgodne, **Nazwa** jest _wymagana_ i musi być `[field('name')]` lub `[field('fullName')]` dla zasobu podrzędnego.
    Jednak zamiast tego należy rozważyć efekt [inspekcji](#audit) .
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu do pochodzącego z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - Wartość domyślna to grupa zasobów warunku **if** .
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcja_ i _resourceName_.
  - Ustawia zakres lokalizacji, z której ma zostać pobrane powiązane zasoby.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - W _przypadku grupy_ zasobów należy ograniczyć liczbę do zasobu warunku **if** lub grupy zasobów określonej w **ResourceGroupName**.
  - W przypadku _subskrypcji_ program wysyła zapytanie do całej subskrypcji powiązanego zasobu.
  - Wartość domyślna to _resourceName_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnią efekt i nie wyzwalają inspekcji.
  - Używa tego samego języka, co reguła zasad dla warunku **if** , ale jest oceniane osobno dla każdego powiązanego zasobu.
  - Jeśli którykolwiek z pasujących zasobów pokrewnych zwróci wartość true, efekt jest spełniony i nie wyzwala inspekcji.
  - Można użyć [Field ()], aby sprawdzić równoważność z wartościami w warunku **if** .
  - Można na przykład użyć do sprawdzenia, czy zasób nadrzędny (w warunku **if** ) znajduje się w tej samej lokalizacji zasobu co pasujący zasób powiązany.

### <a name="auditifnotexists-example"></a>Przykład AuditIfNotExists

Przykład: oblicza Virtual Machines, aby określić, czy rozszerzenie chroniące przed złośliwym kodem istnieje, a następnie przeprowadza inspekcję w razie braku.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>Zablokuj

Odmów służy do zapobiegania żądaniu zasobu, który nie jest zgodny ze zdefiniowanymi standardami za pomocą definicji zasad i nie powoduje wykonania żądania.

### <a name="deny-evaluation"></a>Odmowa oceny

Podczas tworzenia lub aktualizowania dopasowanego zasobu w trybie Menedżer zasobów Odmów to żądanie przed wysłaniem go do dostawcy zasobów. Żądanie jest zwracane jako `403 (Forbidden)` . W portalu dostęp zabroniony może być wyświetlany jako stan wdrożenia, które zostało uniemożliwione przez przypisanie zasad. W przypadku trybu dostawcy zasobów dostawca zasobów zarządza oceną zasobu.

W trakcie obliczania istniejących zasobów zasoby zgodne z definicją zasad Odmów są oznaczane jako niezgodne.

### <a name="deny-properties"></a>Właściwości Odmów

W przypadku trybu Menedżer zasobów efekt odmowy nie ma żadnych dodatkowych właściwości do użycia w warunku **then** definicji zasad.

W przypadku trybu dostawcy zasobów dla programu `Microsoft.Kubernetes.Data` efekt odmowy ma następujące dodatkowe właściwości podrzędne. 

- **constraintTemplate** (wymagane)
  - Szablon ograniczenia CustomResourceDefinition (CRD), który definiuje nowe ograniczenia. Szablon definiuje logikę rego, schemat ograniczenia i parametry ograniczenia, które są przekazane za pośrednictwem **wartości** z Azure Policy.
- **ograniczenie** (wymagane)
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przesyłanych za pośrednictwem **wartości** jako `{{ .Values.<valuename> }}` . W przykładzie 2 poniżej te wartości są `{{ .Values.excludedNamespaces }}` i `{{ .Values.allowedContainerImagesRegex }}` .
- **wartości** (opcjonalne)
  - Definiuje wszelkie parametry i wartości, które mają zostać przekazane do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="deny-example"></a>Odmów przykładu

Przykład 1: użycie efektu odmowy dla trybów Menedżer zasobów.

```json
"then": {
    "effect": "deny"
}
```

Przykład 2: użycie efektu Odmów dla trybu dostawcy zasobów `Microsoft.Kubernetes.Data` . Dodatkowe informacje w obszarze **szczegóły** definiują szablon ograniczenia i CRD, które mają być używane w Kubernetes w celu ograniczenia dozwolonych obrazów kontenerów.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Podobnie jak w przypadku AuditIfNotExists, definicja zasad DeployIfNotExists wykonuje wdrożenie szablonu po spełnieniu warunku.

> [!NOTE]
> [Szablony zagnieżdżone](../../../azure-resource-manager/templates/linked-templates.md#nested-template) są obsługiwane w programie **deployIfNotExists**, ale [połączone szablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nie są obecnie obsługiwane.

### <a name="deployifnotexists-evaluation"></a>Ocena DeployIfNotExists

DeployIfNotExists uruchamia około 15 minut od momentu, gdy dostawca zasobów obsłużył Tworzenie lub aktualizowanie subskrypcji lub żądania zasobu, a następnie zwrócił kod stanu sukcesu. Wdrożenie szablonu występuje, jeśli nie ma żadnych powiązanych zasobów lub jeśli zasoby zdefiniowane przez **ExistenceCondition** nie są oceniane na wartość true. Czas trwania wdrożenia zależy od złożoności zasobów zawartych w szablonie.

W cyklu oceny definicje zasad z DeployIfNotExistsm efektem dopasowania zasobów są oznaczane jako niezgodne, ale nie są podejmowane żadne działania dotyczące tego zasobu. Istniejące niezgodne zasoby można skorygować przy użyciu [zadania korygowania](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

Właściwość **Details** efektu DeployIfNotExists ma wszystkie właściwości, które definiują powiązane zasoby do dopasowania oraz wdrożenie szablonu do wykonania.

- **Typ** (wymagany)
  - Określa typ powiązanego zasobu do dopasowania.
  - Uruchamia się, próbując pobrać zasób poniżej zasobu warunku **if** , a następnie wykonać zapytania w tej samej grupie zasobów co zasób warunku **if** .
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady umożliwiają pobranie jednego określonego zasobu zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku dla opcji **IF. Field. Type** i **then. details. Type** są zgodne, **Nazwa** jest _wymagana_ i musi być `[field('name')]` lub `[field('fullName')]` dla zasobu podrzędnego.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu do pochodzącego z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - Wartość domyślna to grupa zasobów warunku **if** .
  - Wdrożenie szablonu zostanie wdrożone w grupie zasobów tej wartości.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcja_ i _resourceName_.
  - Ustawia zakres lokalizacji, z której ma zostać pobrane powiązane zasoby.
  - Nie ma zastosowania, jeśli **Typ** jest zasobem, który byłby poniżej zasobu warunku **if** .
  - W _przypadku grupy_ zasobów należy ograniczyć liczbę do zasobu warunku **if** lub grupy zasobów określonej w **ResourceGroupName**.
  - W przypadku _subskrypcji_ program wysyła zapytanie do całej subskrypcji powiązanego zasobu.
  - Wartość domyślna to _resourceName_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, wszystkie powiązane zasoby **typu** spełnią skutek i nie wyzwalają wdrożenia.
  - Używa tego samego języka, co reguła zasad dla warunku **if** , ale jest oceniane osobno dla każdego powiązanego zasobu.
  - Jeśli dowolny pasujący zasób ma wartość true, efekt jest spełniony i nie wyzwala wdrożenia.
  - Można użyć [Field ()], aby sprawdzić równoważność z wartościami w warunku **if** .
  - Można na przykład użyć do sprawdzenia, czy zasób nadrzędny (w warunku **if** ) znajduje się w tej samej lokalizacji zasobu co pasujący zasób powiązany.
- **roleDefinitionIds** (wymagane)
  - Ta właściwość musi zawierać tablicę ciągów, które pasują do identyfikatora roli kontroli dostępu opartej na rolach dostępnej w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — Konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcjonalnie)
  - Dozwolone wartości to _subskrypcja_ i _resourceName_.
  - Ustawia typ wdrożenia, które ma zostać wyzwolone. _Subskrypcja_ wskazuje [wdrożenie na poziomie subskrypcji](../../../azure-resource-manager/templates/deploy-to-subscription.md) _, Grupa_ zasobów wskazuje wdrożenie w grupie.
  - W przypadku korzystania z wdrożeń na poziomie subskrypcji należy określić właściwość _Location_ we _wdrożeniu_ .
  - Wartość domyślna to _resourceName_.
- **Wdrożenie** (wymagane)
  - Ta właściwość powinna obejmować pełne wdrożenie szablonu, ponieważ zostanie przesłane do `Microsoft.Resources/deployments` interfejsu API Put. Aby uzyskać więcej informacji, zobacz [interfejs API REST wdrożeń](/rest/api/resources/deployments).

  > [!NOTE]
  > Wszystkie funkcje wewnątrz właściwości **wdrożenia** są oceniane jako składniki szablonu, a nie zasady. Wyjątkiem jest właściwość **Parameters** , która przekazuje wartości z zasad do szablonu. **Wartość** w tej sekcji w kolumnie Nazwa parametru szablonu służy do przekazywania tej wartości (zobacz _FullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład DeployIfNotExists

Przykład: oblicza SQL Server baz danych, aby określić, czy transparentDataEncryption jest włączony. Jeśli nie, zostanie wykonane wdrożenie do włączenia.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>Disabled

Ten efekt jest przydatny do testowania sytuacji lub w przypadku, gdy definicja zasad ma sparametryzowane skutki. Ta elastyczność umożliwia wyłączenie pojedynczego przypisania zamiast wyłączania wszystkich przypisań zasad.

Alternatywą dla wyłączonego efektu jest **wymuszmode**, który jest ustawiany w przypisaniu zasad.
Gdy **wymuszanie** jest _wyłączone_, nadal są oceniane zasoby. Rejestrowanie, takie jak dzienniki aktywności, i efekt zasad nie wystąpi. Aby uzyskać więcej informacji, zobacz [Tryb wymuszania przypisywania zasad](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Ten efekt jest używany z _trybem_ definicji zasad `Microsoft.Kubernetes.Data` . Jest on używany do przekazywania reguł kontroli przyjęcia strażnika v3 zdefiniowanych za pomocą [platformy ograniczeń nieprzez](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) , aby [otworzyć agenta zasad](https://www.openpolicyagent.org/) (Nieprzez) do klastrów Kubernetes na platformie Azure.

> [!IMPORTANT]
> Definicje zasad z ograniczeniami w wersji zapoznawczej z efektem **EnforceOPAConstraint** i pokrewną kategorią **usługi Kubernetes** są _przestarzałe_. Zamiast tego należy użyć trybu _inspekcji_ efektów i _Odmów_ przy użyciu dostawcy zasobów `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>Ocena EnforceOPAConstraint

Otwarty kontroler przyjmowania agentów zasad umożliwia ocenę każdego nowego żądania w klastrze w czasie rzeczywistym.
Co 15 minut ukończono pełne skanowanie klastra i wyniki zgłoszone do Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Właściwości EnforceOPAConstraint

Właściwość **Details** efektu EnforceOPAConstraint ma właściwości SubProperties opisujące regułę kontroli dostępu strażnik v3.

- **constraintTemplate** (wymagane)
  - Szablon ograniczenia CustomResourceDefinition (CRD), który definiuje nowe ograniczenia. Szablon definiuje logikę rego, schemat ograniczenia i parametry ograniczenia, które są przekazane za pośrednictwem **wartości** z Azure Policy.
- **ograniczenie** (wymagane)
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przesyłanych za pośrednictwem **wartości** jako `{{ .Values.<valuename> }}` . W poniższym przykładzie te wartości są `{{ .Values.cpuLimit }}` i `{{ .Values.memoryLimit }}` .
- **wartości** (opcjonalne)
  - Definiuje wszelkie parametry i wartości, które mają zostać przekazane do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="enforceopaconstraint-example"></a>Przykład EnforceOPAConstraint

Przykład: strażnik v3 reguła kontroli w celu ustawienia limitów zasobów procesora i pamięci kontenera w Kubernetes.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Ten efekt jest używany z _trybem_ definicji zasad `Microsoft.ContainerService.Data` . Służy do przekazywania reguł kontroli wpływu strażnika v2 zdefiniowanych za pomocą [rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) , aby [otworzyć agenta zasad](https://www.openpolicyagent.org/) (Nieprzez) w [usłudze Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> Definicje zasad z ograniczeniami w wersji zapoznawczej z efektem **EnforceRegoPolicy** i pokrewną kategorią **usługi Kubernetes** są _przestarzałe_. Zamiast tego należy użyć trybu _inspekcji_ efektów i _Odmów_ przy użyciu dostawcy zasobów `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>Ocena EnforceRegoPolicy

Otwarty kontroler przyjmowania agentów zasad umożliwia ocenę każdego nowego żądania w klastrze w czasie rzeczywistym.
Co 15 minut ukończono pełne skanowanie klastra i wyniki zgłoszone do Azure Policy.

### <a name="enforceregopolicy-properties"></a>Właściwości EnforceRegoPolicy

Właściwość **Details** efektu EnforceRegoPolicy ma właściwości, które opisują regułę kontroli dostępu strażnik v2.

- **policyId** (wymagane)
  - Unikatowa nazwa przenoszona jako parametr do reguły rego Admission Control.
- **zasady** (wymagane)
  - Określa identyfikator URI reguły rego Admission Control.
- **policyParameters** (opcjonalnie)
  - Definiuje wszelkie parametry i wartości, które mają zostać przekazane do zasad regoymi.

### <a name="enforceregopolicy-example"></a>Przykład EnforceRegoPolicy

Przykład: strażnika v2 reguła kontroli, aby zezwalać tylko na określone obrazy kontenerów w AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>Modyfikowanie

Modyfikowanie służy do dodawania, aktualizowania lub usuwania właściwości lub tagów w ramach subskrypcji lub zasobu podczas tworzenia lub aktualizowania. Typowym przykładem jest aktualizowanie tagów w zasobach, takich jak costCenter. Istniejące niezgodne zasoby można skorygować przy użyciu [zadania korygowania](../how-to/remediate-resources.md). Pojedyncza reguła modyfikowania może zawierać dowolną liczbę operacji.

Następujące operacje są obsługiwane przez modyfikację:

- Dodawanie, zastępowanie lub usuwanie tagów zasobów. W przypadku tagów zasady modyfikowania powinny mieć `mode` ustawioną wartość _Indexed_ , chyba że zasób docelowy jest grupą zasobów.
- Dodaj lub Zastąp wartość zarządzanego typu tożsamości ( `identity.type` ) maszyn wirtualnych i zestawów skalowania maszyn wirtualnych.
- Dodaj lub Zamień wartości niektórych aliasów (wersja zapoznawcza).
  - Korzystanie z polecenia `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    w Azure PowerShell **4.6.0** lub wyższej, aby uzyskać listę aliasów, które mogą być używane z modyfikacją.

> [!IMPORTANT]
> Jeśli zarządzasz tagami, zaleca się korzystanie z funkcji Modify zamiast Dołącz jako Modyfikuj udostępnia dodatkowe typy operacji i możliwość korygowania istniejących zasobów. Jednakże zaleca się dołączenie, jeśli nie można utworzyć tożsamości zarządzanej lub modyfikacja nie obsługuje jeszcze aliasu dla właściwości zasobu.

### <a name="modify-evaluation"></a>Modyfikuj ocenę

Modyfikacja jest szacowana przed przetworzeniem żądania przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Operacje modyfikowania są stosowane do zawartości **żądania, gdy spełniony jest warunek reguły** zasad. Każda operacja modyfikowania może określić warunek, który określa, kiedy ma być stosowany. Operacje z warunkami, które są oceniane na _wartość false_ , są pomijane.

Po określeniu aliasu wykonywane są następujące dodatkowe kontrole w celu upewnienia się, że operacja Modyfikuj nie zmienia zawartości żądania w taki sposób, aby dostawca zasobów mógł go odrzucić:

- Właściwość mapowania aliasu na wartość jest oznaczona jako "modyfikowalna" w wersji interfejsu API żądania.
- Typ tokenu w operacji Modify jest zgodny z oczekiwanym typem tokenu dla właściwości w wersji interfejsu API żądania.

Jeśli jeden z tych sprawdzeń zakończy się niepowodzeniem, Ocena zasad powróci do określonego **conflictEffect**.

> [!IMPORTANT]
> Jest to Recommeneded, że modyfikacje definicji, które obejmują aliasy, używają **efektu konfliktu** _inspekcji_ , aby uniknąć nieudanych żądań przy użyciu wersji interfejsu API, gdzie zmapowana właściwość nie jest "modyfikowalna". Jeśli ten sam alias działa inaczej między wersjami interfejsu API, warunkowe operacje modyfikowania mogą służyć do określania operacji modyfikowania używanych dla każdej wersji interfejsu API.

Gdy definicja zasad używająca efektu Modyfikuj jest uruchamiana w ramach cyklu oceny, nie wprowadza zmian do już istniejących zasobów. Zamiast tego oznacza wszelkie zasoby, które spełniają warunek **if** jako niezgodne.

### <a name="modify-properties"></a>Modyfikuj właściwości

Właściwość **Details** efektu Modyfikuj ma wszystkie właściwości, które definiują uprawnienia, które są konieczne do korygowania, oraz **operacje** , które służą do dodawania, aktualizowania lub usuwania wartości tagów.

- **roleDefinitionIds** (wymagane)
  - Ta właściwość musi zawierać tablicę ciągów, które pasują do identyfikatora roli kontroli dostępu opartej na rolach dostępnej w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — Konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
  - Zdefiniowana rola musi obejmować wszystkie operacje przyznane do roli [współautor](../../../role-based-access-control/built-in-roles.md#contributor) .
- **conflictEffect** (opcjonalnie)
  - Określa definicję zasad "WINS" w przypadku, gdy więcej niż jedna definicja zasad modyfikuje tę samą właściwość lub gdy operacja Modyfikuj nie działa na określonym aliasie.
    - W przypadku nowych lub zaktualizowanych zasobów definicja zasad z _Odmów_ ma pierwszeństwo. Definicje zasad z _inspekcją_ pomija wszystkie **operacje**. Jeśli więcej niż jedna definicja zasad ma _odmowę_, żądanie jest odrzucane jako konflikt. Jeśli wszystkie definicje zasad mają _inspekcję_, wówczas żadna z **operacji** w sprzecznych definicjach zasad nie zostanie przetworzona.
    - W przypadku istniejących zasobów, jeśli więcej niż jedna definicja zasad ma _odmowę_, stan zgodności jest w _konflikcie_. Jeśli co najmniej jedna definicja zasad ma _odmowę_, każde przypisanie zwraca stan zgodności _niezgodny_.
  - Dostępne wartości: _Inspekcja_, _Odmów_, _wyłączone_.
  - Wartość domyślna to _Odmów_.
- **operacje** (wymagane)
  - Tablica wszystkich operacji tagów do wykonania na pasujących zasobach.
  - Właściwości:
    - **operacja** (wymagana)
      - Definiuje akcję, która ma zostać podjęta względem pasującego zasobu. Dostępne opcje to: _addOrReplace_, _Add_, _Remove_. _Dodaj_ zachowania podobne do efektu [dołączania](#append) .
    - **pole** (wymagane)
      - Tag do dodania, zastępowania lub usunięcia. Nazwy tagów muszą być zgodne z tą samą konwencją nazewnictwa dla innych [pól](./definition-structure.md#fields).
    - **wartość** (opcjonalnie)
      - Wartość, dla której ma zostać ustawiony tag.
      - Ta właściwość jest wymagana, jeśli **operacja** jest _addOrReplace_ lub _Dodaj_.
    - **warunek** (opcjonalnie)
      - Ciąg zawierający wyrażenie języka Azure Policy z [funkcją zasad](./definition-structure.md#policy-functions) , która daje w wyniku _wartość true_ lub _false_.
      - Program nie obsługuje następujących funkcji zasad: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Modyfikuj operacje

Tablica właściwości **operacji** umożliwia zmianę kilku tagów na różne sposoby z jednej definicji zasad. Każda operacja składa się z właściwości **operacji**, **pola** i **wartości** . Operacja określa, jakie działanie ma wykonać zadanie korygowania tagów, pole określa, który znacznik jest modyfikowany, a wartość definiuje nowe ustawienie dla tego tagu. Poniższy przykład powoduje zmianę następujących tagów:

- Ustawia `environment` tag na "test", nawet jeśli istnieje już z inną wartością.
- Usuwa tag `TempResource` .
- Ustawia `Dept` tag dla parametru zasad _deptname_ skonfigurowany w przypisaniu zasad.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Właściwość **Operation** ma następujące opcje:

|Operacja |Opis |
|-|-|
|addOrReplace |Dodaje zdefiniowaną właściwość lub tag i wartość do zasobu, nawet jeśli właściwość lub tag już istnieje z inną wartością. |
|Dodaj |Dodaje zdefiniowaną właściwość lub tag i wartość do zasobu. |
|Usuń |Usuwa zdefiniowaną właściwość lub tag z zasobu. |

### <a name="modify-examples"></a>Modyfikuj przykłady

Przykład 1: Dodaj `environment` tag i Zastąp istniejące `environment` tagi "test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Przykład 2: Usuń `env` tag i Dodaj `environment` tag lub Zastąp istniejące `environment` Tagi wartością sparametryzowane:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

Przykład 3: Upewnij się, że konto magazynu nie zezwala na dostęp publiczny obiektu BLOB, operacja modyfikowania jest stosowana tylko podczas oceniania żądań z wersją interfejsu API nowszą lub równą "2019-04-01":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Definicje zasad dotyczących warstw

Na zasób może mieć wpływ kilka przypisań. Te przydziały mogą znajdować się w tym samym zakresie lub w różnych zakresach. Każdy z tych przypisań jest również prawdopodobnie zdefiniowanym innym efektem. Warunek i wpływ dla każdej zasady są oceniane niezależnie. Na przykład:

- Zasady 1
  - Ogranicza lokalizację zasobu do "zachodnie"
  - Przypisane do subskrypcji A
  - Odmowa
- Zasady 2
  - Ogranicza lokalizację zasobu do "Wschód"
  - Przypisane do grupy zasobów B w subskrypcji A
  - Inspekcja — efekt
  
Spowoduje to następujące wyniki:

- Wszystkie zasoby znajdujące się już w grupie zasobów B w "Wschodnie" są zgodne z zasadami 2 i niezgodne z zasadami 1
- Wszelkie zasoby znajdujące się już w grupie zasobów B nie w "Wschodnie" są niezgodne z zasadami 2 i niezgodne z zasadami 1, jeśli nie są w "zachodnie"
- Wszystkie nowe zasoby w subskrypcji A nie w "zachodnich" są odrzucane przez zasady 1
- Wszystkie nowe zasoby w subskrypcji A i grupy zasobów B w obszarze "zachodnie" są tworzone i niezgodne z zasadami 2

Jeśli zasada 1 i zasady 2 miały wpływ na odmowę, sytuacja zmieni się na:

- Wszystkie zasoby znajdujące się już w grupie zasobów B nie w "Wschodnie" są niezgodne z zasadami 2
- Wszystkie zasoby znajdujące się już w grupie zasobów B nie w "zachodnich" są niezgodne z zasadami 1
- Wszystkie nowe zasoby w subskrypcji A nie w "zachodnich" są odrzucane przez zasady 1
- Odmówiono wszelkich nowych zasobów w grupie zasobów B subskrypcji A

Każde przypisanie jest oceniane indywidualnie. W związku z tym nie istnieje możliwość poślizgania zasobu przez przerwę od różnic między zakresami. Wynik sieci definicji zasad warstw jest traktowany jako **skumulowany najbardziej restrykcyjny**. Jeśli na przykład zasady 1 i 2 mają skutek odmowy, zasób zostałby zablokowany przez nakładające się i sprzeczne definicje zasad. Jeśli nadal potrzebujesz zasobu, który ma zostać utworzony w zakresie docelowym, przejrzyj wykluczenia poszczególnych przypisań, aby sprawdzić, czy odpowiednie przypisania zasad mają wpływ na właściwe zakresy.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
