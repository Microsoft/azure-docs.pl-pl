---
title: Informacje o tym, jak działają efekty
description: Azure Policy mają różne skutki, które określają sposób zarządzania zgodnością i jej raportów.
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e0d6eb5fb37ecf1b13edd945de52398b1e12f192
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861557"
---
# <a name="understand-azure-policy-effects"></a>Zrozumienie Azure Policy danych

Każda definicja zasad w usłudze Azure Policy ma odzwierciedlenie w pojedynczym efekcie. Ten efekt określa, co się stanie, gdy reguła zasad zostanie oceniona jako dopasowana. Efekty zachowują się inaczej, jeśli są związane z nowym zasobem, zaktualizowanym zasobem lub istniejącym zasobem.

Te efekty są obecnie obsługiwane w definicji zasad:

- [Append](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [Modify](#modify)

Następujące efekty są _przestarzałe:_

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> W miejsce efektów **EnforceOPAConstraint** **lub EnforceRegoPolicy** należy użyć inspekcji i odmowy w _trybie_ dostawcy  `Microsoft.Kubernetes.Data` zasobów. Wbudowane definicje zasad zostały zaktualizowane. Po zmodyfikowaniu istniejących przypisań zasad tych  wbudowanych definicji zasad należy zmienić parametr efektu na wartość na _zaktualizowanej liście allowedValues._

## <a name="order-of-evaluation"></a>Kolejność obliczania

Żądania utworzenia lub zaktualizowania zasobu są najpierw oceniane przez Azure Policy zasobów. Azure Policy tworzy listę wszystkich przypisań, które mają zastosowanie do zasobu, a następnie ocenia zasób względem każdej definicji. W [trybie Resource Manager](./definition-structure.md#resource-manager-modes)program Azure Policy kilka efektów przed zgłoszeniem żądania do odpowiedniego dostawcy zasobów. Taka kolejność zapobiega niepotrzebnemu przetwarzaniu przez dostawcę zasobów, gdy zasób nie spełnia wymagań zaprojektowanych kontrolek ładu Azure Policy. W [trybie dostawcy zasobów](./definition-structure.md#resource-provider-modes)dostawca zasobów zarządza oceną i wynikiem oraz raportuje wyniki z powrotem do Azure Policy.

- **Wyłączone** jest sprawdzany najpierw, aby określić, czy reguła zasad powinna być oceniana.
- **Następnie oceniane** są właściwości Append i **Modify.** Ponieważ któraś z tych zmian może zmienić żądanie, wyzwolenie efektu inspekcji lub odmowy może być uniemożliwione. Te efekty są dostępne tylko w Resource Manager trybie.
- **Następnie jest** oceniana wartość Odmów. Dzięki ocenie odmowy przed inspekcją podwójne rejestrowanie niepożądanego zasobu jest blokowane.
- **Inspekcja** jest oceniana jako ostatnia.

Gdy dostawca zasobów zwraca kod powodzenia w żądaniu w trybie Resource Manager, klasy **AuditIfNotExists** i **DeployIfNotExists** oceniają, czy wymagane jest dodatkowe rejestrowanie zgodności lub akcja.

Ponadto żądania, które modyfikują tylko powiązane pola, ograniczają ocenę zasad do zasad zawierających warunki, `PATCH` `tags` które sprawdzają powiązane `tags` pola.

## <a name="append"></a>Append

Dołączanie służy do dodawania dodatkowych pól do żądanego zasobu podczas tworzenia lub aktualizowania. Częstym przykładem jest określanie dozwolonych ip dla zasobu magazynu.

> [!IMPORTANT]
> Dołączanie jest przeznaczone do użytku z właściwościami innymi niż tag. Mimo że dołączanie może dodawać tagi do zasobu podczas żądania utworzenia lub aktualizacji, zaleca się zamiast tego użycie efektu [Modyfikuj](#modify) dla tagów.

### <a name="append-evaluation"></a>Ocena dołączania

Dołączanie ocenia, zanim żądanie zostanie przetworzone przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Dołącz dodaje pola do zasobu, gdy **warunek jeśli** reguła zasad jest spełniony. Jeśli efekt dołączania zastąpi wartość w oryginalnym żądaniu inną wartością, działa jako efekt odmowy i odrzuca żądanie. Aby dołączyć nową wartość do istniejącej tablicy, użyj **\[\*\]** wersji aliasu.

Gdy definicja zasad używająca efektu dołączania jest uruchamiana w ramach cyklu oceny, nie wprowadza ona zmian w już istniejących zasobach. Zamiast tego oznacza każdy zasób, który spełnia **warunek if,** jako niezgodny.

### <a name="append-properties"></a>Dołączanie właściwości

Efekt dołączania ma tylko **tablicę szczegółów,** która jest wymagana. Ponieważ **szczegóły** są tablicą, może ona przyjmować jedną parę **pól/wartości** lub wielokrotności. Zapoznaj się [ze strukturą](definition-structure.md#fields) definicji, aby uzyskać listę dopuszczalnych pól.

### <a name="append-examples"></a>Dołączanie przykładów

Przykład 1: para **jedno pole/wartość** używająca aliasu inne niż z wartością tablicy w celu ustawienia reguł **\[\*\]** 
 [](definition-structure.md#aliases) adresów IP na koncie  magazynu. Gdy alias niebędący **\[\*\]** aliasem jest tablicą,  efekt dołącza wartość jako całą tablicę. Jeśli tablica już istnieje, zdarzenie odmowy występuje z konfliktu.

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

Przykład 2: para **jedno pole/wartość** **\[\*\]** [używająca aliasu](definition-structure.md#aliases) z wartością tablicy w celu ustawienia reguł adresów IP na koncie magazynu.  Przy użyciu aliasu efekt dołącza wartość do potencjalnie istniejącej **\[\*\]** tablicy.  Jeśli tablica jeszcze nie istnieje, zostanie utworzona.

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

Inspekcja służy do tworzenia zdarzenia ostrzeżenia w dzienniku aktywności podczas oceny niezgodnego zasobu, ale nie zatrzymuje żądania.

### <a name="audit-evaluation"></a>Ocena inspekcji

Inspekcja to ostatni efekt sprawdzany Azure Policy podczas tworzenia lub aktualizowania zasobu. W przypadku Resource Manager zasobów Azure Policy następnie wysyła zasób do dostawcy zasobów. Inspekcja działa tak samo w przypadku żądania zasobu i cyklu oceny. W przypadku nowych i zaktualizowanych zasobów Azure Policy do dziennika aktywności i oznacza zasób jako `Microsoft.Authorization/policies/audit/action` niezgodny.

### <a name="audit-properties"></a>Właściwości inspekcji

W trybie Resource Manager efekt inspekcji nie ma żadnych dodatkowych właściwości do  użycia w warunku definicji zasad.

W przypadku trybu dostawcy zasobów efekt inspekcji ma następujące dodatkowe właściwości `Microsoft.Kubernetes.Data` podrzędne **szczegółów**.

- **constraintTemplate** (wymagane)
  - Szablon ograniczenia CustomResourceDefinition (CRD) definiujący nowe ograniczenia. Szablon definiuje logikę Rego, schemat ograniczenia i parametry ograniczenia,  które są przekazywane za pośrednictwem wartości z Azure Policy.
- **ograniczenie** (wymagane)
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przekazywanych za **pośrednictwem wartości** jako `{{ .Values.<valuename> }}` . W poniższym przykładzie 2 te wartości to `{{ .Values.excludedNamespaces }}` i `{{ .Values.allowedContainerImagesRegex }}` .
- **wartości** (opcjonalnie)
  - Definiuje wszelkie parametry i wartości do przekazania do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="audit-example"></a>Przykład inspekcji

Przykład 1: Używanie efektu inspekcji dla Resource Manager trybów.

```json
"then": {
    "effect": "audit"
}
```

Przykład 2: Używanie efektu inspekcji dla trybu dostawcy zasobów `Microsoft.Kubernetes.Data` . Dodatkowe informacje w **szczegółach definiują** szablon ograniczenia i definicję CRD do użycia na kubernetes w celu ograniczenia dozwolonych obrazów kontenerów.

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

AuditIfNotExists umożliwia inspekcję  zasobów związanych z zasobem, który pasuje do warunku **if,** ale nie ma właściwości określonych w szczegółach warunku **then.** 

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists , ocena

AuditIfNotExists jest uruchamiany po obsłużeniu żądania utworzenia lub zaktualizowania zasobu przez dostawcę zasobów i zwróceniu kodu stanu powodzenia. Inspekcja ma miejsce, jeśli nie ma powiązanych zasobów lub jeśli zasoby zdefiniowane przez **wartość ExistenceCondition** nie mają wartości true. W przypadku nowych i zaktualizowanych zasobów Azure Policy do dziennika aktywności i oznacza zasób jako `Microsoft.Authorization/policies/audit/action` niezgodny. Po wyzwoleniu zasób, który spełnia **warunek if,** jest zasobem oznaczonym jako niezgodny.

### <a name="auditifnotexists-properties"></a>Właściwości AuditIfNotExists

Właściwość **details** efektów AuditIfNotExists ma wszystkie podwłaściwości, które definiują powiązane zasoby do dopasowania.

- **Typ** (wymagany)
  - Określa typ powiązanego zasobu do dopasowania.
  - Jeśli **typ details.type** jest typem zasobu poniżej zasobu warunku  **if,** zasady wysyłają zapytanie o zasoby tego typu w zakresie ocenianego zasobu. W przeciwnym razie zasady będą wysyłać zapytania w tej samej grupie zasobów co oceniany zasób.
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady pobierają jeden określony zasób zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku **if.field.type** i **then.details.type** są  zgodne, to pole **Name** staje się wymagane i musi mieć wartość `[field('name')]` , lub dla zasobu `[field('fullName')]` podrzędnego.
    Zamiast tego [należy jednak wziąć](#audit) pod uwagę efekt inspekcji.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowywanie powiązanego zasobu z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **typ** jest zasobem, który znajduje się poniżej zasobu **warunku if.**
  - Wartość domyślna **to if** warunek grupy zasobów zasobu.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _Subscription_ i _ResourceGroup._
  - Określa zakres, z którego ma być pobierany powiązany zasób.
  - Nie ma zastosowania, jeśli **typ** jest zasobem, który znajduje się poniżej zasobu **warunku if.**
  - W _przypadku grupy_ zasobów parametr ograniczałby się do grupy zasobów warunku **if** lub grupy zasobów określonej w **parametrze ResourceGroupName**.
  - W _przypadku_ subskrypcji program wysyła zapytanie do całej subskrypcji o powiązany zasób.
  - Wartość domyślna to _ResourceGroup._
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, żaden powiązany **zasób** typu spełnia ten efekt i nie wyzwala inspekcji.
  - Używa tego samego języka co reguła zasad dla warunku **if,** ale jest oceniana osobno dla każdego powiązanego zasobu.
  - Jeśli dowolny pasujący powiązany zasób ma wartość true, efekt jest spełniony i nie wyzwoli inspekcji.
  - Może używać funkcji [field()], aby sprawdzić równoważność z wartościami w **warunku if.**
  - Na przykład można użyć do zweryfikowania, czy zasób nadrzędny (w warunku **if)** znajduje się w tej samej lokalizacji zasobu co pasujący powiązany zasób.

### <a name="auditifnotexists-example"></a>Przykład AuditIfNotExists

Przykład: ocenia, Virtual Machines, czy rozszerzenie ochrony przed złośliwym oprogramowaniem istnieje, a następnie przeprowadza inspekcję w przypadku jego braku.

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

Odmowa służy do zapobiegania żądaniu zasobu, które nie jest zgodne ze zdefiniowanymi standardami za pośrednictwem definicji zasad i kończy się niepowodzeniem żądania.

### <a name="deny-evaluation"></a>Odmowa oceny

Podczas tworzenia lub aktualizowania dopasowanego zasobu w trybie Resource Manager odmowa uniemożliwia żądanie przed wysłaniem do dostawcy zasobów. Żądanie jest zwracane jako `403 (Forbidden)` . W portalu dostęp zabroniony można wyświetlić jako stan wdrożenia, które zostało uniemożliwione przez przypisanie zasad. W przypadku trybu dostawcy zasobów dostawca zasobów zarządza oceną zasobu.

Podczas oceny istniejących zasobów zasoby zgodne z definicją zasad odmowy są oznaczane jako niezgodne.

### <a name="deny-properties"></a>Odmów właściwości

W trybie Resource Manager efekt odmowy nie ma żadnych dodatkowych właściwości do  użycia w warunku definicji zasad.

W przypadku trybu dostawcy zasobów w programie efekt odmowy `Microsoft.Kubernetes.Data` ma następujące dodatkowe właściwości podrzędne **szczegółów**.

- **constraintTemplate** (wymagane)
  - Szablon ograniczenia CustomResourceDefinition (CRD) definiujący nowe ograniczenia. Szablon definiuje logikę Rego, schemat ograniczenia i parametry ograniczenia,  które są przekazywane za pośrednictwem wartości z Azure Policy.
- **ograniczenie** (wymagane)
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przekazanych za **pośrednictwem wartości** jako `{{ .Values.<valuename> }}` . W poniższym przykładzie 2 te wartości to `{{ .Values.excludedNamespaces }}` i `{{ .Values.allowedContainerImagesRegex }}` .
- **wartości** (opcjonalnie)
  - Definiuje wszystkie parametry i wartości do przekazania do ograniczenia. Każda wartość musi istnieć w crd szablonu ograniczenia.

### <a name="deny-example"></a>Przykład odmowy

Przykład 1: Używanie efektu odmowy dla Resource Manager trybów.

```json
"then": {
    "effect": "deny"
}
```

Przykład 2: Użycie efektu odmowy dla trybu dostawcy zasobów `Microsoft.Kubernetes.Data` . Dodatkowe informacje w **szczegółach definiują** szablon ograniczenia i definicję CRD, które mają być dostępne na kubernetes w celu ograniczenia dozwolonych obrazów kontenerów.

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

Podobnie jak w przypadku auditIfNotExists, definicja zasad DeployIfNotExists wykonuje wdrożenie szablonu, gdy warunek zostanie spełniony.

> [!NOTE]
> [Szablony zagnieżdżone](../../../azure-resource-manager/templates/linked-templates.md#nested-template) są obsługiwane przez **obiekt deployIfNotExists,** ale połączone [szablony](../../../azure-resource-manager/templates/linked-templates.md#linked-template) nie są obecnie obsługiwane.

### <a name="deployifnotexists-evaluation"></a>Ocena deployIfNotExists

Program DeployIfNotExists jest uruchamiany około 15 minut po obsłużeniu przez dostawcę zasobów subskrypcji lub żądania zasobu tworzenia lub aktualizacji i zwróceniu kodu stanu powodzenia. Wdrożenie szablonu ma miejsce, jeśli nie ma powiązanych zasobów lub jeśli zasoby zdefiniowane przez **wartość ExistenceCondition** nie mają wartości true. Czas trwania wdrożenia zależy od złożoności zasobów zawartych w szablonie.

Podczas cyklu oceny definicje zasad z efektem DeployIfNotExists, które pasują do zasobów, są oznaczane jako niezgodne, ale dla tego zasobu nie są podejmowane żadne działania. Istniejące niezgodne zasoby można skorygować za pomocą [zadania korygowania](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Właściwości DeployIfNotExists

Właściwość **details** efektu DeployIfNotExists zawiera wszystkie podwłaściwości definiujące powiązane zasoby do dopasowania oraz wdrożenie szablonu do wykonania.

- **Typ** (wymagany)
  - Określa typ powiązanego zasobu do dopasowania.
  - Rozpoczyna się od próby pobrania zasobu poniżej zasobu **warunku if,** a następnie wykonuje zapytanie w tej samej grupie zasobów co **zasób warunku if.**
- **Nazwa** (opcjonalnie)
  - Określa dokładną nazwę zasobu do dopasowania i powoduje, że zasady pobierają jeden określony zasób zamiast wszystkich zasobów określonego typu.
  - Gdy wartości warunku **if.field.type** i **then.details.type** są  zgodne, wówczas nazwa staje się wymagana i musi mieć wartość , lub dla zasobu  `[field('name')]` `[field('fullName')]` podrzędnego.
- **ResourceGroupName** (opcjonalnie)
  - Umożliwia dopasowanie powiązanego zasobu z innej grupy zasobów.
  - Nie ma zastosowania, jeśli **typ jest** zasobem, który znajduje się poniżej zasobu **warunku if.**
  - Wartość domyślna to grupa zasobów warunku **if.**
  - Jeśli wdrożenie szablonu jest wykonywane, jest ono wdrażane w grupie zasobów tej wartości.
- **ExistenceScope** (opcjonalnie)
  - Dozwolone wartości to _Subscription i_ _ResourceGroup._
  - Określa zakres, z którego ma być pobierany powiązany zasób.
  - Nie ma zastosowania, jeśli **typ jest** zasobem, który znajduje się poniżej zasobu **warunku if.**
  - W _przypadku grupy zasobów_ parametr ograniczałby się do grupy zasobów warunku **if** lub grupy zasobów określonej w **parametrze ResourceGroupName**.
  - W _przypadku subskrypcji_ program wysyła zapytanie do całej subskrypcji o powiązany zasób.
  - Wartość domyślna to _ResourceGroup_.
- **ExistenceCondition** (opcjonalnie)
  - Jeśli nie zostanie określony, dowolny powiązany zasób **typu** spełnia ten efekt i nie wyzwala wdrożenia.
  - Używa tego samego języka co reguła zasad dla warunku **if,** ale jest oceniana osobno dla każdego powiązanego zasobu.
  - Jeśli dowolny pasujący powiązany zasób ma wartość true, efekt jest spełniony i nie wyzwoli wdrożenia.
  - Może używać funkcji [field()] do sprawdzania równoważności z wartościami w warunku **if.**
  - Na przykład można użyć elementu do zweryfikowania, czy zasób nadrzędny (w warunku **if)** znajduje się w tej samej lokalizacji zasobu co pasujący powiązany zasób.
- **roleDefinitionIds** (wymagane)
  - Ta właściwość musi zawierać tablicę ciągów, które pasują do identyfikatora roli kontroli dostępu opartej na rolach dostępnego dla subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcjonalnie)
  - Dozwolone wartości to _Subscription_ i _ResourceGroup._
  - Ustawia typ wdrożenia do wyzwolenia. _Subskrypcja_ wskazuje wdrożenie [na poziomie subskrypcji](../../../azure-resource-manager/templates/deploy-to-subscription.md), grupa _zasobów_ wskazuje wdrożenie w grupie zasobów.
  - Podczas _korzystania_ z wdrożeń  na poziomie subskrypcji należy określić właściwość lokalizacji w ramach wdrożenia.
  - Wartość domyślna to _ResourceGroup._
- **Wdrożenie** (wymagane)
  - Ta właściwość powinna zawierać pełne wdrożenie szablonu, tak jak powinno zostać przekazane do interfejsu `Microsoft.Resources/deployments` API PUT. Aby uzyskać więcej informacji, zobacz [Deployments REST API (Interfejs API REST wdrożeń).](/rest/api/resources/deployments)

  > [!NOTE]
  > Wszystkie funkcje wewnątrz **właściwości Deployment** są oceniane jako składniki szablonu, a nie zasady. Wyjątkiem jest właściwość **parameters,** która przekazuje wartości z zasad do szablonu. Wartość **w** tej sekcji pod nazwą parametru szablonu służy do przekazywania tej wartości (zobacz _wartość fullDbName_ w przykładzie DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Przykład deployIfNotExists

Przykład: ocenia SQL Server, aby określić, czy włączono funkcję transparentDataEncryption. Jeśli nie, zostanie wykonane wdrożenie do włączenia.

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

Ten efekt jest przydatny podczas testowania sytuacji lub gdy definicja zasad sparametryzuje efekt. Ta elastyczność umożliwia wyłączenie pojedynczego przypisania zamiast wyłączania wszystkich przypisań tych zasad.

Alternatywą dla efektu Disabled jest **tryb enforcementMode,** który jest ustawiany w przypisaniu zasad.
Gdy **tryb enforcementMode** ma _wartość Disabled,_ zasoby są nadal oceniane. Rejestrowanie, takie jak dzienniki aktywności, i efekt zasad nie występuje. Aby uzyskać więcej informacji, zobacz [Przypisywanie zasad — tryb wymuszania.](./assignment-structure.md#enforcement-mode)

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Ten efekt jest używany w trybie definicji _zasad_ `Microsoft.Kubernetes.Data` . Jest on używany do przekazania reguł kontroli dostępu programu Gatekeeper w wersji [3](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) zdefiniowanych za pomocą OPA Constraint Framework do programu [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) do klastrów Kubernetes na platformie Azure.

> [!IMPORTANT]
> Ograniczone definicje zasad w wersji zapoznawczej **z efektem EnforceOPAConstraint** i powiązaną **kategorią usługi Kubernetes Service** są _przestarzałe._ Zamiast tego użyj efektów inspekcji _i_ odmowy _w_ trybie dostawcy `Microsoft.Kubernetes.Data` zasobów.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint evaluation (Ocena EnforceOPAConstraint)

Kontroler wpływ agentów zasad open ocenia wszystkie nowe żądania w klastrze w czasie rzeczywistym.
Co 15 minut jest ukończone pełne skanowanie klastra, a wyniki są zgłaszane do Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Właściwości EnforceOPAConstraint

Właściwość **details** efektu EnforceOPAConstraint ma właściwości podrzędne, które opisują regułę kontroli wpływu wpływu danych programu Gatekeeper w wersji 3.

- **constraintTemplate** (wymagane)
  - Szablon ograniczenia CustomResourceDefinition (CRD) definiujący nowe ograniczenia. Szablon definiuje logikę Rego, schemat ograniczenia i parametry ograniczenia,  które są przekazywane za pośrednictwem wartości z Azure Policy.
- **ograniczenie** (wymagane)
  - Implementacja CRD szablonu ograniczenia. Używa parametrów przekazywanych za **pośrednictwem wartości** jako `{{ .Values.<valuename> }}` . W poniższym przykładzie te wartości to `{{ .Values.cpuLimit }}` i `{{ .Values.memoryLimit }}` .
- **wartości** (opcjonalnie)
  - Definiuje wszelkie parametry i wartości do przekazania do ograniczenia. Każda wartość musi istnieć w szablonie ograniczenia CRD.

### <a name="enforceopaconstraint-example"></a>Przykład EnforceOPAConstraint

Przykład: reguła kontroli dostępu do danych programu Gatekeeper w wersji 3 służąca do ustawienia limitów zasobów pamięci i procesora CPU kontenera na kubernetes.

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

Ten efekt jest używany w trybie definicji _zasad_ `Microsoft.ContainerService.Data` . Jest on używany do przekazania reguł kontroli dostępu w wersji 2 do open [policy agent](https://www.openpolicyagent.org/) (OPA) w programie Gatekeeper w wersji 2 zdefiniowanych za pomocą [rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) na Azure Kubernetes Service . [](../../../aks/intro-kubernetes.md)

> [!IMPORTANT]
> Ograniczone definicje zasad w wersji zapoznawczej z efektem **EnforceRegoPolicy** i powiązaną **kategorią usługi Kubernetes Service** są _przestarzałe._ Zamiast tego użyj efektów inspekcji _i_ odmowy _w_ trybie dostawcy `Microsoft.Kubernetes.Data` zasobów.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy evaluation (Ocena zasad EnforceRegoPolicy)

Kontroler wpływ agentów zasad open ocenia wszystkie nowe żądania w klastrze w czasie rzeczywistym.
Co 15 minut jest ukończone pełne skanowanie klastra, a wyniki są zgłaszane do Azure Policy.

### <a name="enforceregopolicy-properties"></a>Właściwości EnforceRegoPolicy

Właściwość **details** efektu EnforceRegoPolicy ma właściwości podrzędne, które opisują regułę kontroli wpływu wpływu danych programu Gatekeeper w wersji 2.

- **policyId** (wymagane)
  - Unikatowa nazwa przekazana jako parametr do reguły kontroli nabierania danych Rego.
- **zasady** (wymagane)
  - Określa URI reguły kontroli nabierania danych Rego.
- **policyParameters** (opcjonalnie)
  - Definiuje wszystkie parametry i wartości do przekazania do zasad rego.

### <a name="enforceregopolicy-example"></a>Przykład zasad EnforceRegoPolicy

Przykład: reguła kontroli nabierania danych w programie Gatekeeper w wersji 2, która zezwala tylko na określone obrazy kontenerów w użytą usługę AKS.

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

Modyfikowanie służy do dodawania, aktualizowania lub usuwania właściwości lub tagów w subskrypcji lub zasobie podczas tworzenia lub aktualizowania. Częstym przykładem jest aktualizowanie tagów zasobów, takich jak costCenter. Istniejące niezgodne zasoby można skorygować za pomocą [zadania korygowania](../how-to/remediate-resources.md). Pojedyncza reguła Modyfikuj może mieć dowolną liczbę operacji.

Następujące operacje są obsługiwane przez program Modify:

- Dodawanie, zastępowanie lub usuwanie tagów zasobów. W przypadku tagów zasady Modyfikuj powinny mieć `mode` wartość _Indeksowane,_ chyba że zasób docelowy jest grupą zasobów.
- Dodaj lub zastąp wartość typu tożsamości zarządzanej () maszyn wirtualnych `identity.type` i zestawów skalowania maszyn wirtualnych.
- Dodaj lub zamień wartości niektórych aliasów (wersja zapoznawcza).
  - Korzystanie z polecenia `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    w Azure PowerShell **4.6.0** lub wyższej, aby uzyskać listę aliasów, które mogą być używane z modify.

> [!IMPORTANT]
> Jeśli zarządzasz tagami, zaleca się użycie funkcji Modyfikuj zamiast Dołączanie jako Modyfikuj, aby uzyskać dodatkowe typy operacji i możliwość korygowania istniejących zasobów. Jednak dołączenie jest zalecane, jeśli nie można utworzyć tożsamości zarządzanej lub modyfikowanie nie obsługuje jeszcze aliasu właściwości zasobu.

### <a name="modify-evaluation"></a>Modyfikowanie oceny

Modyfikowanie jest oceniane przed przetworzeniem żądania przez dostawcę zasobów podczas tworzenia lub aktualizowania zasobu. Operacje Modyfikuj są stosowane do zawartości żądania, **jeśli** zostanie spełniony warunek reguły zasad. Każda operacja Modify może określić warunek, który określa, kiedy jest stosowany. Operacje z warunkami, które są oceniane jako _false,_ są pomijane.

Po podaniem aliasu są wykonywane następujące dodatkowe kontrole, aby upewnić się, że operacja Modify nie zmienia zawartości żądania w sposób, który powoduje odrzucenie go przez dostawcę zasobów:

- Właściwość, na która jest mapowany alias, jest oznaczona jako "Modyfikowalna" w wersji interfejsu API żądania.
- Typ tokenu w operacji Modify odpowiada oczekiwanej wartości typu tokenu dla właściwości w wersji interfejsu API żądania.

Jeśli którekolwiek z tych testów nie powiedzie się, ocena zasad powróci do określonego **conflictEffect**.

> [!IMPORTANT]
> Należy pamiętać, że definicje Modify, które zawierają   aliasy, używają efektu konfliktu inspekcji, aby uniknąć błędów żądań przy użyciu wersji interfejsu API, w których zamapowana właściwość nie jest "modyfikowalna". Jeśli ten sam alias działa inaczej w różnych wersjach interfejsu API, można użyć operacji modyfikowania warunkowego w celu określenia operacji modyfikowania dla każdej wersji interfejsu API.

Gdy definicja zasad używająca efektu Modify jest uruchamiana w ramach cyklu oceny, nie wprowadza zmian w już istniejących zasobach. Zamiast tego oznacza każdy zasób, który spełnia **warunek if,** jako niezgodny.

### <a name="modify-properties"></a>Modyfikowanie właściwości

Właściwość **details** efektu Modify zawiera wszystkie podwłaściwości definiujące uprawnienia  wymagane do korygowania oraz operacje używane do dodawania, aktualizowania lub usuwania wartości tagów.

- **roleDefinitionIds** (wymagane)
  - Ta właściwość musi zawierać tablicę ciągów, które pasują do identyfikatora roli kontroli dostępu opartej na rolach dostępnego dla subskrypcji. Aby uzyskać więcej informacji, zobacz [korygowanie — konfigurowanie definicji zasad](../how-to/remediate-resources.md#configure-policy-definition).
  - Zdefiniowana rola musi zawierać wszystkie operacje przyznane roli [Współautor.](../../../role-based-access-control/built-in-roles.md#contributor)
- **conflictEffect** (opcjonalnie)
  - Określa, która definicja zasad "wygrywa", jeśli więcej niż jedna definicja zasad modyfikuje tę samą właściwość lub gdy operacja Modify nie działa na określonym aliasie.
    - W przypadku nowych lub zaktualizowanych zasobów pierwszeństwo ma definicja zasad _z_ odmową. Definicje zasad z _inspekcją_ pomijają **wszystkie operacje**. Jeśli więcej niż jedna definicja zasad ma _odmów_, żądanie zostanie odrzucone jako konflikt. Jeśli wszystkie definicje zasad mają _inspekcję_, **żadna** z operacji definicji zasad powodującej konflikt nie jest przetwarzana.
    - W przypadku istniejących zasobów, jeśli więcej niż jedna definicja zasad _ma_ odmówić , stanem zgodności będzie _Konflikt_. Jeśli co najmniej jedna definicja zasad _odmawia_, każde przypisanie zwraca stan zgodności _Niezgodne_.
  - Dostępne wartości: _audit,_ _deny,_ _disabled_.
  - Wartość domyślna to _deny_.
- **operacje** (wymagane)
  - Tablica wszystkich operacji tagów do ukończenia na pasujących zasobach.
  - Właściwości:
    - **operation** (wymagane)
      - Definiuje akcję do podjęcia w przypadku pasującego zasobu. Dostępne opcje to: _addOrReplace,_ _Add,_ _Remove_. _Dodawanie_ działa podobnie do [efektu Dołączanie.](#append)
    - **pole** (wymagane)
      - Tag do dodania, zastąpienia lub usunięcia. Nazwy tagów muszą być zgodne z taką samą konwencją nazewnictwa dla innych [pól](./definition-structure.md#fields).
    - **value** (opcjonalnie)
      - Wartość, na która ma być ustawiony tag.
      - Ta właściwość jest wymagana, **jeśli operacja to** _addOrReplace_ lub _Add_.
    - **warunek** (opcjonalnie)
      - Ciąg zawierający wyrażenie języka Azure Policy z funkcjami [zasad,](./definition-structure.md#policy-functions) które mają wartość _true_ lub _false._
      - Nie obsługuje następujących funkcji zasad: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Modyfikowanie operacji

**Tablica** właściwości operacji umożliwia zmianę kilku tagów na różne sposoby z definicji pojedynczych zasad. Każda operacja składa się z **właściwości operacji**, **pola** **i** wartości. Operacja określa działanie zadania korygowania w tagach, pole określa, który tag jest zmieniany, a wartość definiuje nowe ustawienie dla tego tagu. Poniższy przykład wprowadza następujące zmiany w tagach:

- Ustawia `environment` tag na "Test", nawet jeśli już istnieje z inną wartością.
- Usuwa tag `TempResource` .
- Ustawia `Dept` tag na parametr zasad _DeptName_ skonfigurowany w przypisaniu zasad.

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

Właściwość **operation** ma następujące opcje:

|Operacja |Opis |
|-|-|
|addOrReplace |Dodaje zdefiniowaną właściwość lub tag i wartość do zasobu, nawet jeśli właściwość lub tag już istnieje z inną wartością. |
|Dodaj |Dodaje zdefiniowaną właściwość lub tag i wartość do zasobu. |
|Usuń |Usuwa zdefiniowaną właściwość lub tag z zasobu. |

### <a name="modify-examples"></a>Modyfikowanie przykładów

Przykład 1. Dodawanie `environment` tagu i zastępowanie istniejących `environment` tagów tagiem "Test":

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

Przykład 2. Usuwanie tagu i dodawanie tagu lub zastępowanie istniejących `env` `environment` `environment` tagów wartością sparametryzowanej:

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

Przykład 3: Upewnij się, że konto magazynu nie zezwala na publiczny dostęp do obiektów blob. Operacja Modify jest stosowana tylko podczas oceniania żądań z wersją interfejsu API większą lub równą "2019-04-01":

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

## <a name="layering-policy-definitions"></a>Definicje zasad warstw

Na zasób może mieć wpływ kilka przypisań. Te przypisania mogą być w tym samym zakresie lub w różnych zakresach. Każde z tych przypisań prawdopodobnie również będzie mieć zdefiniowany inny efekt. Warunek i efekt dla każdej zasady są oceniane niezależnie. Na przykład:

- Zasady 1
  - Ogranicza lokalizację zasobu do lokalizacji "westus"
  - Przypisane do subskrypcji A
  - Efekt odmów
- Zasady 2
  - Ogranicza lokalizację zasobu do lokalizacji "eastus"
  - Przypisane do grupy zasobów B w subskrypcji A
  - Efekt inspekcji
  
Ta konfiguracja spowoduje następujące wyniki:

- Każdy zasób już w grupie zasobów B w grupie "eastus" jest zgodny z zasadami 2 i niezgodny z zasadami 1
- Każdy zasób, który nie znajduje się już w grupie zasobów B w grupie "eastus", jest niezgodny z zasadami 2 i jest niezgodny z zasadami 1, jeśli nie znajduje się w grupie "westus"
- Każdy nowy zasób w subskrypcji A, który nie znajduje się w grupie "westus", zostanie odrzucony przez zasady 1
- Każdy nowy zasób w subskrypcji A i grupie zasobów B w grupie "westus" jest tworzony i niezgodny z zasadami 2

Jeśli zasady 1 i zasady 2 miały efekt odmowy, sytuacja zmieni się na:

- Każdy zasób, który nie znajduje się już w grupie zasobów B w grupie "eastus", jest niezgodny z zasadami 2
- Każdy zasób już w grupie zasobów B, który nie znajduje się w grupie "westus", jest niezgodny z zasadami 1
- Każdy nowy zasób w subskrypcji A, który nie znajduje się w grupie "westus", zostanie odrzucony przez zasady 1
- Każdy nowy zasób w grupie zasobów B subskrypcji A zostanie odrzucony

Każde przypisanie jest oceniane indywidualnie. W związku z tym nie ma możliwości, aby zasób przesunął się przez lukę między różnicami w zakresie. Wynik netto definicji zasad warstw jest uznawany za skumulowany najbardziej **restrykcyjny**. Na przykład jeśli obie zasady 1 i 2 miały efekt odmowy, zasób zostałby zablokowany przez nakładające się i powodujące konflikt definicje zasad. Jeśli nadal potrzebujesz zasobu do utworzenia w zakresie docelowym, przejrzyj wykluczenia w każdym przypisaniu, aby sprawdzić, czy odpowiednie przypisania zasad wpływają na odpowiednie zakresy.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady pod [Azure Policy przykładami.](../samples/index.md)
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Dowiedz się, [jak programowo tworzyć zasady.](../how-to/programmatically-create.md)
- Dowiedz się, jak [uzyskać dane zgodności.](../how-to/get-compliance-data.md)
- Dowiedz się, jak [korygować niezgodne zasoby.](../how-to/remediate-resources.md)
- Aby sprawdzić, czym jest grupa zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](../../management-groups/overview.md)
