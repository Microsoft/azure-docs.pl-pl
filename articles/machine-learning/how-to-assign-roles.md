---
title: Zarządzanie rolami w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać dostęp do obszaru roboczego Azure Machine Learning przy użyciu kontroli dostępu opartej na rolach (Azure RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 03/26/2021
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 4d037bdf266e70a2621b2627bc88abce30af652c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612623"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning

W tym artykule dowiesz się, jak zarządzać dostępem (autoryzacją) do obszaru roboczego Azure Machine Learning. [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) służy do zarządzania dostępem do zasobów platformy Azure, takich jak możliwość tworzenia nowych zasobów lub używania istniejących. Użytkownikom w Azure Active Directory (Azure AD) są przypisane określone role, które zapewniają dostęp do zasobów. Platforma Azure udostępnia wbudowane role i możliwość tworzenia ról niestandardowych.

> [!TIP]
> Ten artykuł koncentruje się na Azure Machine Learning, poszczególnych usługach, na których opiera się platforma Azure ML, dostarczając własne ustawienia RBAC. Na przykład korzystając z informacji zawartych w tym artykule, można skonfigurować, kto może przesyłać żądania oceniania do modelu wdrożonego jako usługa sieci Web w usłudze Azure Kubernetes Service. Jednak usługa Azure Kubernetes udostępnia swój własny zestaw ról platformy Azure. Aby uzyskać informacje dotyczące poszczególnych usług, które mogą być przydatne w przypadku Azure Machine Learning, zobacz następujące linki:
>
> * [Kontrola dostępu do zasobów klastra usługi Azure Kubernetes](../aks/azure-ad-rbac.md)
> * [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](../aks/manage-azure-rbac.md)
> * [Korzystanie z usługi Azure RBAC na potrzeby dostępu do danych obiektów BLOB](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> Zastosowanie niektórych ról może ograniczyć funkcjonalność interfejsu użytkownika w programie Azure Machine Learning Studio dla innych użytkowników. Na przykład jeśli rola użytkownika nie ma możliwości utworzenia wystąpienia obliczeniowego, opcja tworzenia wystąpienia obliczeniowego nie będzie dostępna w programie Studio. To zachowanie jest oczekiwane i uniemożliwia użytkownikowi podejmowanie prób wykonania operacji, które zwracają błąd odmowy dostępu.

## <a name="default-roles"></a>Role domyślne

Obszar roboczy usługi Azure Machine Learning to zasób platformy Azure. Tak jak w przypadku innych zasobów platformy Azure nowo utworzony obszar roboczy usługi Azure Machine Learning ma trzy domyślne role. Możesz dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

| Rola | Poziom dostępu |
| --- | --- |
| **Czytelnik** | Akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą wyświetlać i przeglądać zasoby, w tym poświadczenia [magazynu](how-to-access-data.md) danych, w obszarze roboczym. Czytelnicy nie mogą tworzyć ani aktualizować tych zasobów. |
| **Współautor** | Wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Współautorzy mogą na przykład tworzyć eksperymenty, tworzyć lub dołączać klastry obliczeniowe, przesyłać przebiegi i wdrażać usługi internetowe. |
| **Właściciel** | Pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania (jeśli dotyczy) zasobów w obszarze roboczym. Dodatkowo może zmieniać przypisania ról. |
| **Rola niestandardowa** | Umożliwia dostosowanie dostępu do określonych operacji kontroli lub płaszczyzny danych w obszarze roboczym. Na przykład przesyłanie przebiegu, tworzenie obliczeń, Wdrażanie modelu lub rejestrowanie zestawu danych. |

> [!IMPORTANT]
> Dostęp do roli można ograniczyć do wielu poziomów na platformie Azure. Na przykład ktoś z dostępem właściciela do obszaru roboczego może nie mieć dostępu właściciela do grupy zasobów, która zawiera obszar roboczy. Aby uzyskać więcej informacji, zobacz [jak działa usługa Azure RBAC](../role-based-access-control/overview.md#how-azure-rbac-works).

Obecnie nie ma żadnych dodatkowych wbudowanych ról, które są specyficzne dla Azure Machine Learning. Aby uzyskać więcej informacji na temat ról wbudowanych, zobacz [role wbudowane platformy Azure](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Zarządzanie dostępem do obszaru roboczego

Jeśli jesteś właścicielem obszaru roboczego, możesz dodawać i usuwać role dla obszaru roboczego. Możesz również przypisywać role do użytkowników. Skorzystaj z poniższych linków, aby dowiedzieć się, jak zarządzać dostępem:
- [Interfejs użytkownika Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
- [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
- [Szablony usługi Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

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

Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Operacje Azure Machine Learning

Aby uzyskać więcej informacji o operacjach (akcjach i akcjach), których można użyć do użycia z rolami niestandardowymi, zobacz [operacje dostawcy zasobów](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). Aby wyświetlić listę operacji, można także użyć poniższego polecenia interfejsu CLI platformy Azure:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Aby wyświetlić definicję roli dla określonej roli niestandardowej, użyj poniższego polecenia platformy Azure. `<role-name>`Powinien być w tym samym formacie zwracanym przez polecenie powyżej:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Musisz mieć uprawnienia do całego zakresu nowej definicji roli. Jeśli na przykład ta nowa rola ma zakres między trzema subskrypcjami, musisz mieć uprawnienia do wszystkich trzech subskrypcji. 

> [!NOTE]
> Zastosowanie aktualizacji ról może zająć od 15 minut do godziny.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Użyj szablonów Azure Resource Manager do powtarzalności

Jeśli przewidujesz, że będzie konieczne ponowne utworzenie złożonych przypisań ról, szablon Azure Resource Manager może być dużą pomoc. W [szablonie 201-Machine-Learning-](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) Resources-przypisań ról można określić, w jaki sposób przypisania ról mogą być określone w kodzie źródłowym do ponownego użycia. 

## <a name="common-scenarios"></a>Typowe scenariusze

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
| Publikowanie potoków i punktów końcowych | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Wdrażanie zarejestrowanego modelu w zasobie AKS/ACI | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Ocenianie względem wdrożonego punktu końcowego AKS | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (jeśli nie używasz uwierzytelniania Azure Active Directory) lub `"/workspaces/read"` (Jeśli używasz uwierzytelniania tokenu) |
| Uzyskiwanie dostępu do magazynu przy użyciu notesów interaktywnych | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Utwórz nową rolę niestandardową | Właściciel, współautor lub rola niestandardowa zezwalająca na `Microsoft.Authorization/roleDefinitions/write` | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na: `/workspaces/computes/write` |

> [!TIP]
> Jeśli wystąpi błąd podczas próby utworzenia obszaru roboczego po raz pierwszy, upewnij się, że rola zezwala `Microsoft.MachineLearningServices/register/action` . Ta akcja umożliwia zarejestrowanie dostawcy zasobów Azure Machine Learning w ramach subskrypcji platformy Azure.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Tożsamość zarządzana przypisana przez użytkownika z klastrem obliczeniowym usługi Azure ML

Aby przypisać tożsamość przypisaną użytkownikowi do klastra obliczeniowego Azure Machine Learning, musisz mieć uprawnienia do zapisu, aby utworzyć [rolę operatora obliczeń i zarządzanej tożsamości](../role-based-access-control/built-in-roles.md#managed-identity-operator). Aby uzyskać więcej informacji na temat kontroli RBAC platformy Azure z tożsamościami zarządzanymi, przeczytaj artykuł [jak zarządzać tożsamością przypisaną przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>Operacje MLflow

Aby wykonać operacje MLflow z obszarem roboczym Azure Machine Learning, użyj następujących zakresów roli niestandardowej:

| MLflow, operacja | Zakres |
| --- | --- |
| Wyświetl listę wszystkich eksperymentów w magazynie śledzenia obszarów roboczych, uzyskaj eksperyment według identyfikatora, uzyskaj eksperyment według nazwy | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Utwórz eksperyment z nazwą, ustaw tag na eksperymentie, Przywróć eksperyment oznaczony do usunięcia| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Usuwanie eksperymentu | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Pobierz dane dotyczące uruchamiania i pokrewnych metadanych, Pobierz listę wszystkich wartości dla określonej metryki dla danego przebiegu, Lista artefaktów do uruchomienia | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Tworzenie nowego przebiegu w ramach eksperymentu, usuwanie przebiegów, Przywracanie usuniętych przebiegów, rejestrowanie metryk w bieżącym przebiegu, Ustawianie tagów w przebiegu, Usuwanie tagów w przebiegu, parametry dziennika (para klucz-wartość) używanych do uruchomienia, rejestrowanie partii metryk, parametrów i tagów dla przebiegu, stan przebiegu aktualizacji | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Pobierz zarejestrowany model według nazwy, Pobierz listę wszystkich zarejestrowanych modeli w rejestrze, Wyszukaj zarejestrowane modele, najnowsze modele wersji dla każdego etapu żądania, uzyskaj wersję zarejestrowanego modelu, wersje modeli wyszukiwania, Uzyskaj identyfikator URI, w którym są przechowywane artefakty wersji modelu, Wyszukaj uruchomienia według identyfikatorów eksperymentów | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Utwórz nowy zarejestrowany model, zaktualizuj nazwę/opis zarejestrowanego modelu, Zmień nazwę istniejącego zarejestrowanego modelu, Utwórz nową wersję modelu, zaktualizuj Opis wersji modelu i przeniesie zarejestrowany model na jeden z etapów | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Usuwanie zarejestrowanego modelu wraz ze wszystkimi jego wersjami, usuwanie określonych wersji zarejestrowanego modelu | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Przykładowe role niestandardowe

### <a name="data-scientist"></a>Analityk danych

Umożliwia analitykowi danych wykonywanie wszystkich operacji w obszarze roboczym **z wyjątkiem**:

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

### <a name="data-scientist-restricted"></a>Ograniczone przez analityka danych

Bardziej ograniczona definicja roli bez symboli wieloznacznych w dozwolonych akcjach. Może wykonywać wszystkie operacje w obszarze roboczym, **z wyjątkiem**:

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
     
### <a name="mlflow-data-scientist"></a>Analityk danych MLflow

Zezwala analitykom danych na wykonywanie wszystkich MLflow obsługiwanych operacji Azure **z wyjątkiem**:

* Tworzenie obliczeń
* Wdrażanie modeli w środowisku produkcyjnym AKS
* Wdrażanie punktu końcowego potoku w środowisku produkcyjnym

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
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

### <a name="mlops"></a>MLOps

Umożliwia przypisanie roli do jednostki usługi i używanie jej do automatyzowania potoków MLOps. Na przykład, aby przesłać przebiegi z już opublikowanym potokiem:

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

### <a name="workspace-admin"></a>Administrator obszaru roboczego

Umożliwia wykonywanie wszystkich operacji w zakresie obszaru roboczego, **z wyjątkiem**:

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
### <a name="data-labeler"></a>Labeler danych

Umożliwia zdefiniowanie roli w zakresie tylko do etykietowania danych:

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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono kilka kwestii, z którymi należy się zapoznać w przypadku korzystania z kontroli dostępu opartej na rolach (Azure RBAC):

- Po utworzeniu zasobu na platformie Azure, na przykład w obszarze roboczym, nie jesteś bezpośrednio właścicielem zasobu. Rola jest dziedziczona od najwyższej roli zakresu, do której masz autoryzację w ramach tej subskrypcji. Przykładowo, jeśli jesteś administratorem sieci i masz uprawnienia do tworzenia obszaru roboczego Machine Learning, przypiszesz rolę Administrator sieci do tego obszaru roboczego, a nie rolę właściciela.

- Aby wykonać operacje przydziałów w obszarze roboczym, musisz mieć uprawnienia na poziomie subskrypcji. Oznacza to, że limity przydziału poziomu subskrypcji lub poziomu obszaru roboczego dla zarządzanych zasobów obliczeniowych mogą wystąpić tylko wtedy, gdy masz uprawnienia do zapisu w zakresie subskrypcji.

- Jeśli istnieją dwa przypisania ról do tego samego Azure Active Directory użytkownika, które powodują konflikt z sekcjami akcji/nienaruszonych, operacje wymienione w zaistnieniu z jednej roli mogą nie zostać zastosowane, jeśli są również wyświetlane jako akcje w innej roli. Aby dowiedzieć się więcej o tym, jak platforma Azure analizuje przypisania ról, Przeczytaj, w [jaki sposób kontrola RBAC na platformie Azure określa, czy użytkownik ma dostęp do zasobu](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- Aby wdrożyć zasoby obliczeniowe w sieci wirtualnej, należy jawnie mieć uprawnienia do następujących akcji:
    - `Microsoft.Network/virtualNetworks/*/read` w obszarze Zasoby sieci wirtualnej.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` w zasobie podsieci.
    
    Aby uzyskać więcej informacji na temat usługi Azure RBAC z obsługą sieci, zobacz [wbudowane role sieciowe](../role-based-access-control/built-in-roles.md#networking).

- Wykonanie nowych przypisań ról w ramach stosu może czasami potrwać do 1 godziny.
- [Dostęp warunkowy](../role-based-access-control/conditional-access-azure-management.md) nie jest obecnie obsługiwany w Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

- [Przegląd zabezpieczeń przedsiębiorstwa](concept-enterprise-security.md)
- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)
- [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)
- [Operacje dostawcy zasobów](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
