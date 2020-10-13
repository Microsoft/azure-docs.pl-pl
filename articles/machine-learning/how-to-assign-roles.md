---
title: Zarządzanie rolami w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać dostęp do obszaru roboczego Azure Machine Learning przy użyciu kontroli dostępu opartej na rolach (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: ab94af9ec172a3e88d523024c1e00d3a0d944798
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873085"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning

W tym artykule dowiesz się, jak zarządzać dostępem do obszaru roboczego Azure Machine Learning. [Kontrola dostępu oparta na rolach (Azure RBAC)](/azure/role-based-access-control/overview) służy do zarządzania dostępem do zasobów platformy Azure. Użytkownicy w Azure Active Directory są przypisani do określonych ról, które przyznają dostęp do zasobów. Platforma Azure udostępnia wbudowane role i możliwość tworzenia ról niestandardowych.

## <a name="default-roles"></a>Role domyślne

Obszar roboczy usługi Azure Machine Learning to zasób platformy Azure. Tak jak w przypadku innych zasobów platformy Azure nowo utworzony obszar roboczy usługi Azure Machine Learning ma trzy domyślne role. Możesz dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

| Rola | Poziom dostępu |
| --- | --- |
| **Czytelnik** | Akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą wyświetlać i przeglądać zasoby, w tym poświadczenia [magazynu](how-to-access-data.md) danych, w obszarze roboczym. Czytelnicy nie mogą tworzyć ani aktualizować tych zasobów. |
| **Współautor** | Wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Współautorzy mogą na przykład tworzyć eksperymenty, tworzyć lub dołączać klastry obliczeniowe, przesyłać przebiegi i wdrażać usługi internetowe. |
| **Właściciel** | Pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania (jeśli dotyczy) zasobów w obszarze roboczym. Dodatkowo może zmieniać przypisania ról. |
| **Rola niestandardowa** | Umożliwia dostosowanie dostępu do określonych operacji kontroli lub płaszczyzny danych w obszarze roboczym. Na przykład przesyłanie przebiegu, tworzenie obliczeń, Wdrażanie modelu lub rejestrowanie zestawu danych. |

> [!IMPORTANT]
> Dostęp do roli można ograniczyć do wielu poziomów na platformie Azure. Na przykład ktoś z dostępem właściciela do obszaru roboczego może nie mieć dostępu właściciela do grupy zasobów, która zawiera obszar roboczy. Aby uzyskać więcej informacji, zobacz [jak działa RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Aby uzyskać więcej informacji na temat określonych ról wbudowanych, zobacz [role wbudowane dla platformy Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Zarządzanie dostępem do obszaru roboczego

Jeśli jesteś właścicielem obszaru roboczego, możesz dodawać i usuwać role dla obszaru roboczego. Możesz również przypisywać role do użytkowników. Skorzystaj z poniższych linków, aby dowiedzieć się, jak zarządzać dostępem:
- [Interfejs użytkownika Azure Portal](/azure/role-based-access-control/role-assignments-portal)
- [Program PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [Interfejs API REST](/azure/role-based-access-control/role-assignments-rest)
- [Szablony usługi Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Jeśli zainstalowano [interfejs wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md), do przypisywania ról użytkownikom można używać poleceń CLI:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`Pole jest adresem e-mail istniejącego użytkownika w wystąpieniu Azure Active Directory, w którym znajduje się subskrypcja nadrzędna obszaru roboczego. Oto przykład użycia tego polecenia:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> polecenie "AZ ml Workspace Share" nie działa w przypadku konta federacyjnego przez Azure Active Directory B2B. Zamiast polecenia Użyj portalu interfejsu użytkownika platformy Azure.


## <a name="azure-machine-learning-operations"></a>Operacje Azure Machine Learning

Azure Machine Learning wbudowane akcje dla wielu operacji i zadań. Aby uzyskać pełną listę, zobacz [operacje związane z dostawcą zasobów platformy Azure](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Tworzenie roli niestandardowej

Jeśli wbudowane role nie są wystarczające, można tworzyć role niestandardowe. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu, usuwania i zasobów obliczeniowych w tym obszarze roboczym. Rolę można udostępnić na określonym poziomie obszaru roboczego, na poziomie określonego grupy zasobów lub na określonym poziomie subskrypcji.

> [!NOTE]
> Aby utworzyć role niestandardowe w ramach tego zasobu, musisz być właścicielem zasobu na tym poziomie.

Aby utworzyć rolę niestandardową, należy najpierw skonstruować plik JSON definicji roli, który określa uprawnienie i zakres roli. Poniższy przykład definiuje rolę niestandardową o nazwie "niestandardowa analityków danych" na określonym poziomie obszaru roboczego:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Można zmienić pole, `AssignableScopes` Aby ustawić zakres tej roli niestandardowej na poziomie subskrypcji, na poziomie grupy zasobów lub na określonym poziomie obszaru roboczego.
> Powyższa rola niestandardowa jest tylko przykładem, zapoznaj się z sugerowanymi [rolami niestandardowymi dla usługi Azure Machine Learning](#customroles).

Ta rola niestandardowa może wykonywać wszystkie czynności w obszarze roboczym, z wyjątkiem następujących akcji:

- Nie można utworzyć ani zaktualizować zasobu obliczeniowego.
- Nie można usunąć zasobu obliczeniowego.
- Nie można dodać, usunąć ani zmienić przypisań ról.
- Nie można usunąć obszaru roboczego.

Aby wdrożyć tę rolę niestandardową, użyj następującego polecenia platformy Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po wdrożeniu ta rola zostanie udostępniona w określonym obszarze roboczym. Teraz możesz dodać tę rolę i przypisać ją do Azure Portal. Można też przypisać tę rolę do użytkownika za pomocą `az ml workspace share` polecenia interfejsu wiersza poleceń:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe platformy Azure](/azure/role-based-access-control/custom-roles). Aby uzyskać więcej informacji o operacjach (akcjach i akcjach), których można użyć do użycia z rolami niestandardowymi, zobacz [operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="frequently-asked-questions"></a>Często zadawane pytania


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>PYTANIE: Jakie są uprawnienia potrzebne do wykonywania niektórych typowych scenariuszy w ramach usługi Azure Machine Learning?

Poniższa tabela zawiera podsumowanie działań Azure Machine Learning i uprawnienia wymagane do ich wykonania w najniższym zakresie. Na przykład, jeśli działanie może być wykonywane z zakresem obszaru roboczego (kolumna 4), wówczas cały wyższy zakres z tym uprawnieniem będzie również działać automatycznie:

> [!IMPORTANT]
> Wszystkie ścieżki w tej tabeli, które zaczynają `/` się od są **ścieżkami względnymi** do `Microsoft.MachineLearningServices/` :

| Działanie | Zakres poziomu subskrypcji | Zakres poziomu grupy zasobów | Zakres obszaru roboczego |
| ----- | ----- | ----- | ----- |
| Utwórz nowy obszar roboczy | Niewymagane | Właściciel lub współautor | Nie dotyczy (jest właścicielem lub dziedziczy wyższy zakres roli po utworzeniu) |
| Żądaj przydziału Amlcompute poziomu subskrypcji lub ustawienia przydziału poziomu obszaru roboczego | Właściciel lub współautor lub rola niestandardowa </br>umożliwiające `/locations/updateQuotas/action`</br> w zakresie subskrypcji | Brak autoryzacji | Brak autoryzacji |
| Utwórz nowy klaster obliczeniowy | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `/workspaces/computes/write` |
| Utwórz nowe wystąpienie obliczeniowe | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `/workspaces/computes/write` |
| Przesyłanie dowolnego typu przebiegu | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publikowanie punktu końcowego potoku | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Wdrażanie zarejestrowanego modelu w zasobie AKS/ACI | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Ocenianie względem wdrożonego punktu końcowego AKS | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (jeśli nie używasz uwierzytelniania Azure Active Directory) lub `"/workspaces/read"` (Jeśli używasz uwierzytelniania tokenu) |
| Uzyskiwanie dostępu do magazynu przy użyciu notesów interaktywnych | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Utwórz nową rolę niestandardową | Właściciel, współautor lub rola niestandardowa zezwalająca na `Microsoft.Authorization/roleDefinitions/write` | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `/workspaces/computes/write` |

> [!TIP]
> Jeśli wystąpi błąd podczas próby utworzenia obszaru roboczego po raz pierwszy, upewnij się, że rola zezwala `Microsoft.MachineLearningServices/register/action` . Ta akcja umożliwia zarejestrowanie dostawcy zasobów Azure Machine Learning w ramach subskrypcji platformy Azure.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>PYTANIE: Czy publikujemy wbudowane role platformy Azure dla usługi Machine Learning?

Obecnie nie publikujemy [wbudowanych ról platformy Azure](/azure/role-based-access-control/built-in-roles) dla usługi Machine Learning. Nie można zaktualizować wbudowanej roli po opublikowaniu, a firma Microsoft nadal ustala definicje ról na podstawie scenariuszy klientów i informacji zwrotnych. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>PYTANIE: Czy istnieją pewne szablony ról niestandardowych dla najpopularniejszych scenariuszy usługi Machine Learning?

Tak tutaj są niektóre typowe scenariusze z niestandardowymi, proponowanymi definicjami ról, których można użyć jako podstawy do definiowania własnych ról niestandardowych:

* __Niestandardowa analityk danych__: umożliwia analitykowi danych wykonywanie wszystkich operacji w obszarze roboczym **z wyjątkiem**:

    * Tworzenie obliczeń
    * Wdrażanie modeli w środowisku produkcyjnym AKS
    * Wdrażanie punktu końcowego potoku w środowisku produkcyjnym

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Niestandardowa analityk danych z ograniczeniami__: bardziej ograniczoną definicją roli bez symboli wieloznacznych w dozwolonych akcjach. Może wykonywać wszystkie operacje w obszarze roboczym, **z wyjątkiem**:

    * Tworzenie obliczeń
    * Wdrażanie modeli w środowisku produkcyjnym AKS
    * Wdrażanie punktu końcowego potoku w środowisku produkcyjnym

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __MLOps niestandardowy__: umożliwia przypisanie roli do jednostki usługi i używanie jej do automatyzowania potoków MLOps. Na przykład, aby przesłać przebiegi z już opublikowanym potokiem:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Administrator obszaru roboczego__: umożliwia wykonywanie wszystkich operacji w zakresie obszaru roboczego, **z wyjątkiem**:

    * Tworzenie nowego obszaru roboczego
    * Przypisywanie przydziałów poziomu subskrypcji lub obszaru roboczego

    Administrator obszaru roboczego nie może również utworzyć nowej roli. Może przypisywać tylko istniejące wbudowane lub niestandardowe role w zakresie ich obszaru roboczego:

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler niestandardowy__: umożliwia zdefiniowanie roli w zakresie tylko do etykietowania danych:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>PYTANIE: Jak mogę wyświetlić listę wszystkich ról niestandardowych w mojej subskrypcji?

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>PYTANIE: Jak mogę znaleźć operacje obsługiwane przez usługę Machine Learning?

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Można je również znaleźć na liście [operacji dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>PYTANIE: Jakie są typowe pytania dotyczące usługi w przypadku korzystania z usługi Azure RBAC?

Poniżej przedstawiono kilka kwestii, z którymi należy się zapoznać w przypadku korzystania z kontroli dostępu opartej na rolach (Azure RBAC):

- Podczas tworzenia zasobu na platformie Azure Załóżmy, że nie jesteś bezpośrednio właścicielem obszaru roboczego. Rola jest dziedziczona od najwyższej roli zakresu, do której masz autoryzację w ramach tej subskrypcji. Przykładowo jeśli jesteś administratorem sieci i masz uprawnienia do tworzenia obszaru roboczego Machine Learning, przypiszesz rolę administratora sieci do tego obszaru roboczego, a nie rolę właściciela.
- Jeśli istnieją dwa przypisania ról do tego samego Azure Active Directory użytkownika, które powodują konflikt z sekcjami akcji/nienaruszonych, operacje wymienione w zaistnieniu z jednej roli mogą nie zostać zastosowane, jeśli są również wyświetlane jako akcje w innej roli. Aby dowiedzieć się więcej o tym, jak platforma Azure analizuje przypisania ról, Przeczytaj, w [jaki sposób kontrola RBAC na platformie Azure określa, czy użytkownik ma dostęp do zasobu](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- Aby wdrożyć zasoby obliczeniowe w sieci wirtualnej, należy jawnie mieć uprawnienia do następujących akcji:
    - "Microsoft. Network/virtualNetworks/Join/Action" w zasobie sieci wirtualnej.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" w zasobie podsieci.
    
    Aby uzyskać więcej informacji na temat RBAC w sieci, zobacz [wbudowane role sieciowe](/azure/role-based-access-control/built-in-roles#networking).

- Wykonanie nowych przypisań ról w ramach stosu może czasami zająć maksymalnie 1 godzinę.

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>PYTANIE: Jakie uprawnienia są potrzebne do użycia tożsamości zarządzanej przez użytkownika z moimi klastrami Amlcompute?

Aby przypisać tożsamość przypisaną do użytkownika w klastrach Amlcompute, jeden musi mieć uprawnienia do zapisu do tworzenia obliczeń i mieć [rolę operatora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Aby uzyskać więcej informacji na temat RBAC z tożsamościami zarządzanymi, przeczytaj artykuł [jak zarządzać tożsamością przypisaną przez użytkownika](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>PYTANIE: Czy obsługujemy kontrolę dostępu opartą na rolach w portalu Studio?

Azure Machine Learning Studio obsługuje kontrolę dostępu opartą na rolach (Azure RBAC). 

> [!IMPORTANT]
> Po przypisaniu roli niestandardowej z określonymi uprawnieniami do Analityka danych w obszarze roboczym odpowiednie akcje (takie jak dodanie przycisku obliczeń) są automatycznie ukrywane dla użytkowników. Ukrycie tych elementów zapobiega ewentualnemu niepomyleniu kontroli, które zwracają nieautoryzowane powiadomienie o dostępie z usługi, gdy jest używana.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>PYTANIE: Jak mogę znaleźć definicję roli dla roli w mojej subskrypcji?

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie. `<role-name>`Powinien być w tym samym formacie zwracanym przez polecenie powyżej.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>PYTANIE: Jak mogę zaktualizować definicję roli?

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Musisz mieć uprawnienia do całego zakresu nowej definicji roli. Jeśli na przykład ta nowa rola ma zakres między trzema subskrypcjami, musisz mieć uprawnienia do wszystkich trzech subskrypcji. 

> [!NOTE]
> Zastosowanie aktualizacji ról może zająć od 15 minut do godziny.


### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>PYTANIE: Jakie uprawnienia są potrzebne do wykonania operacji przydziałów w obszarze roboczym? 

Aby wykonać wszystkie operacje związane z przydziałami w obszarze roboczym, musisz mieć uprawnienia na poziomie subskrypcji. Oznacza to, że limity przydziału poziomu subskrypcji lub poziomu obszaru roboczego dla zarządzanych zasobów obliczeniowych mogą wystąpić tylko wtedy, gdy masz uprawnienia do zapisu w zakresie subskrypcji. 


## <a name="next-steps"></a>Następne kroki

- [Przegląd zabezpieczeń przedsiębiorstwa](concept-enterprise-security.md)
- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)
- [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)
- [Operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
