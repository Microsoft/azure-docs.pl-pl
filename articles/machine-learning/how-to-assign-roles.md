---
title: Zarządzanie rolami w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać dostęp do obszaru roboczego Azure Machine Learning przy użyciu kontroli dostępu opartej na rolach (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: f289be1b3432d9c62b4841c513088afa16e0e447
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609252"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Zarządzanie dostępem do obszaru roboczego Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zarządzać dostępem do obszaru roboczego Azure Machine Learning. [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview) służy do zarządzania dostępem do zasobów platformy Azure. Użytkownicy w Azure Active Directory są przypisani do określonych ról, które przyznają dostęp do zasobów. Platforma Azure udostępnia wbudowane role i możliwość tworzenia ról niestandardowych.

## <a name="default-roles"></a>Role domyślne

Obszar roboczy Azure Machine Learning jest zasobem platformy Azure. Podobnie jak w przypadku innych zasobów platformy Azure, podczas tworzenia nowego obszaru roboczego Azure Machine Learning dostępne są trzy domyślne role. Możesz dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

| Rola | Poziom dostępu |
| --- | --- |
| **Czytelnik** | Akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą wyświetlać i przeglądać zasoby (w tym poświadczenia [magazynu](how-to-access-data.md) danych) w obszarze roboczym, ale nie mogą tworzyć ani aktualizować tych zasobów. |
| **Współautor** | Wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Można na przykład utworzyć eksperyment, utworzyć lub dołączyć klaster obliczeniowy, przesłać przebieg i wdrożyć usługę sieci Web. |
| **Właściciel** | Pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania (jeśli dotyczy) zasobów w obszarze roboczym. Ponadto można zmienić przypisania ról. |

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

Jeśli zainstalowano [interfejs wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md), można także przypisać role do użytkowników za pomocą interfejsu CLI.

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

Azure Machine Learning wbudowane akcje dla wielu operacji i zadań. Aby uzyskać pełną listę, zobacz [operacje związane z dostawcami zasobów platformy Azure](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Tworzenie roli niestandardowej

Jeśli wbudowane role są niewystarczające, można utworzyć role niestandardowe. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu, usuwania i zasobów obliczeniowych w tym obszarze roboczym. Rolę można udostępnić na określonym poziomie obszaru roboczego, na poziomie określonego grupy zasobów lub na określonym poziomie subskrypcji.

> [!NOTE]
> Aby utworzyć role niestandardowe w ramach tego zasobu, musisz być właścicielem zasobu na tym poziomie.

Aby utworzyć rolę niestandardową, należy najpierw skonstruować plik JSON definicji roli, który określa uprawnienie i zakres roli. W poniższym przykładzie zdefiniowano rolę niestandardową o nazwie "analityk danych" objętą zakresem na określonym poziomie obszaru roboczego:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
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

Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe dla zasobów platformy Azure](/azure/role-based-access-control/custom-roles).

## <a name="frequently-asked-questions"></a>Często zadawane pytania


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>PYTANIE: Jakie są uprawnienia potrzebne do wykonywania różnych akcji w ramach usługi Azure Machine Learning?

Poniższa tabela zawiera podsumowanie działań Azure Machine Learning i uprawnienia wymagane do ich wykonania w najniższym zakresie. Przykładowo, jeśli działanie może być wykonywane z zakresem obszaru roboczego (kolumna 4), wówczas cały wyższy zakres z tym uprawnieniem będzie również działać automatycznie. Wszystkie ścieżki w tej tabeli są **ścieżkami względnymi** do `Microsoft.MachineLearningServices/` .

| Działanie | Zakres poziomu subskrypcji | Zakres poziomu grupy zasobów | Zakres obszaru roboczego |
|---|---|---|---|
| Utwórz nowy obszar roboczy | Niewymagane | Właściciel lub współautor | Nie dotyczy (jest właścicielem lub dziedziczy wyższy zakres roli po utworzeniu) |
| Utwórz nowy klaster obliczeniowy | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na:`workspaces/computes/write` |
| Utwórz nową maszynę wirtualną notesu | Niewymagane | Właściciel lub współautor | Niemożliwe |
| Utwórz nowe wystąpienie obliczeniowe | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na:`workspaces/computes/write` |
| Działanie płaszczyzny danych, takie jak przesyłanie, uzyskiwanie dostępu do danych, Wdrażanie modelu lub potok publikowania | Niewymagane | Niewymagane | Właściciel, współautor lub rola niestandardowa zezwalająca na:`workspaces/*/write` <br/> Musisz również mieć magazyn danych zarejestrowany w obszarze roboczym, aby umożliwić programowi MSI dostęp do danych na koncie magazynu. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>PYTANIE: Jak mogę wyświetlić listę wszystkich ról niestandardowych w mojej subskrypcji?

W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

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
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>PYTANIE: Czy mogę zdefiniować rolę, która uniemożliwia Aktualizowanie wersji obszaru roboczego? 

Tak, możesz zdefiniować rolę, która uniemożliwi Aktualizowanie wersji obszaru roboczego. Ponieważ aktualizacja obszaru roboczego jest wywołaniem poprawki w obiekcie obszaru roboczego, można to zrobić, umieszczając w tablicy następującą akcję w `"NotActions"` definicji JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>PYTANIE: Jakie uprawnienia są potrzebne do wykonania operacji przydziałów w obszarze roboczym? 

Aby wykonać wszystkie operacje związane z przydziałami w obszarze roboczym, musisz mieć uprawnienia na poziomie subskrypcji. Oznacza to, że limity przydziału poziomu subskrypcji lub poziomu obszaru roboczego dla zarządzanych zasobów obliczeniowych mogą wystąpić tylko wtedy, gdy masz uprawnienia do zapisu w zakresie subskrypcji. 


## <a name="next-steps"></a>Następne kroki

- [Przegląd zabezpieczeń przedsiębiorstwa](concept-enterprise-security.md)
- [Bezpieczne uruchamianie eksperymentów i wnioskowania/wyniku w sieci wirtualnej](how-to-enable-virtual-network.md)
- [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)
- [Operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)