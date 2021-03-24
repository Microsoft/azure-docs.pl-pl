---
title: Zarządzanie rolą płaszczyzny danych modułu HSM — Azure Key Vault | Microsoft Docs
description: Ten artykuł służy do zarządzania przypisaniami ról dla zarządzanego modułu HSM
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 4d36b2c2178c7205246cd7c59aefedef3358e473
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951746"
---
# <a name="managed-hsm-role-management"></a>Zarządzanie rolami zarządzanego modułu HSM

> [!NOTE]
> Key Vault obsługuje dwa typy zasobów: magazyny i zarządzane sprzętowych modułów zabezpieczeń. Ten artykuł zawiera informacje o **zarządzanym module HSM**. Jeśli chcesz dowiedzieć się, jak zarządzać magazynem, zobacz [zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../general/manage-with-cli2.md).

Aby zapoznać się z omówieniem zarządzanego modułu HSM, zobacz [co to jest zarządzany moduł HSM?](overview.md). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

W tym artykule pokazano, jak zarządzać rolami dla zarządzanej płaszczyzny danych modułu HSM. Aby dowiedzieć się więcej o zarządzanym modelu kontroli dostępu modułu HSM, zobacz [zarządzana kontrola dostępu modułu HSM](access-control.md).

Aby zezwolić podmiotowi zabezpieczeń (takim jak użytkownik, nazwa główna usługi, Grupa lub tożsamość zarządzana) na wykonywanie zarządzanych operacji na płaszczyźnie danych modułu HSM, należy przypisać im rolę umożliwiającą wykonywanie tych operacji. Na przykład, jeśli chcesz zezwolić aplikacji na wykonywanie operacji podpisywania przy użyciu klucza, musi mieć przypisaną rolę, która zawiera "Microsoft. Key/managedHSM/Keys/Sign/Action" jako jedną z akcji danych. Rolę można przypisać w określonym zakresie. Lokalna RBAC modułu HSM obsługuje dwa zakresy, cały moduł HSM ( `/` lub `/keys` ) i na klucz ( `/keys/<keyname>` ).

Aby zapoznać się z listą wszystkich zarządzanych wbudowanych ról modułu HSM i wykonywanych przez nie operacji, zobacz [wbudowane role modułu HSM](built-in-roles.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z poleceń interfejsu wiersza polecenia platformy Azure w tym artykule, należy dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.21.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM w ramach subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)

## <a name="create-a-new-role-assignment"></a>Utwórz nowe przypisanie roli

### <a name="assign-roles-for-all-keys"></a>Przypisz role dla wszystkich kluczy

Użyj `az keyvault role assignment create` polecenia, aby przypisać **zarządzaną rolę oficera kryptograficznego modułu HSM** do użytkownika identyfikowanego przez główną nazwę użytkownika **\@ contoso.com** dla wszystkich  **kluczy** (zakres `/keys` ) w ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Przypisywanie roli dla określonego klucza

Użyj `az keyvault role assignment create` polecenia, aby przypisać **zarządzaną rolę oficera kryptograficznego modułu HSM** do użytkownika identyfikowanego przez główną nazwę użytkownika o nazwie **\@ contoso.com** dla określonego klucza o nazwie **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Wyświetlanie listy istniejących przypisań ról

Służy `az keyvault role assignment list` do wyświetlania listy przypisań ról.

Wszystkie przypisania ról w zakresie/(domyślnie, gdy nie określono zakresu) dla wszystkich użytkowników (wartość domyślna, gdy jest określona przystawka brak)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Wszystkie przypisania ról na poziomie modułu HSM dla określonego użytkownika **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Gdy zakresem jest/(lub/Keys), polecenie listy wyświetla tylko wszystkie przypisania roli na najwyższym poziomie i nie wyświetla przypisań ról na poszczególnych poziomach kluczy.

Wszystkie przypisania ról dla określonego użytkownika **user2@contoso.com** dla określonego **myrsakey** usługi Key.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Przypisanie konkretnej roli dla **oficera kryptograficznego modułu HSM zarządzanej** przez rolę dla określonego użytkownika **user2@contoso.com** w określonym kluczu **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Usuwanie przypisania roli

Użyj `az keyvault role assignment delete` polecenia, aby usunąć **zarządzaną rolę oficera kryptograficznego modułu HSM** przypisaną do użytkownika **\@ contoso.com** dla usługi Key **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Wyświetl wszystkie dostępne definicje ról

Użyj `az keyvault role definition list` polecenia, aby wyświetlić listę wszystkich definicji ról.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>Utwórz nową definicję roli

Zarządzany moduł HSM ma kilka wbudowanych (wstępnie zdefiniowanych) ról, które są przydatne w przypadku większości typowych scenariuszy użycia. Można zdefiniować własną rolę z listą określonych akcji, które może wykonywać rola. Następnie można przypisać tę rolę do podmiotów zabezpieczeń, aby przyznać im uprawnienia do określonych akcji. 

Użyj `az keyvault role definition create` polecenia do roli o nazwie **moja rola niestandardowa** przy użyciu ciągu JSON.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

Użyj `az keyvault role definition create` polecenia do roli z pliku o nazwie **my-custom-role-definition.jsna** zawierający ciąg JSON dla definicji roli. Zobacz przykład powyżej.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>Pokaż szczegóły definicji roli

Użyj `az keyvault role definition show` polecenia, aby wyświetlić szczegóły konkretnej definicji roli przy użyciu nazwy (GUID).

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>Aktualizowanie definicji roli niestandardowej

Użyj `az keyvault role definition update` polecenia, aby zaktualizować rolę o nazwie **moja rola niestandardowa** przy użyciu ciągu JSON.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>Usuń definicję roli niestandardowej

Użyj `az keyvault role definition delete` polecenia, aby wyświetlić szczegóły konkretnej definicji roli przy użyciu nazwy (GUID). 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> Nie można usunąć ról wbudowanych. Po usunięciu ról niestandardowych wszystkie przypisania ról używające tej roli niestandardowej stają się unieczynnione.


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z omówieniem [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md).
- Zobacz Samouczek dotyczący [zarządzanego zarządzania rolami modułu HSM](role-management.md)
- Dowiedz się więcej o [modelu kontroli dostępu zarządzanego modułu HSM](access-control.md)
- Zobacz wszystkie [wbudowane role dla zarządzanego modułu HSM Local RBAC](built-in-roles.md)
