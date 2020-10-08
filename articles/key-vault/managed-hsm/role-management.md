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
ms.openlocfilehash: 814167425fcd39e90edccd952e1a3e4fbd570988
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818019"
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
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM w ramach subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-new-role-assignment"></a>Utwórz nowe przypisanie roli

### <a name="assign-roles-for-all-keys"></a>Przypisz role dla wszystkich kluczy

Użyj `az keyvault role assignment create` polecenia, aby przypisać **zarządzaną rolę oficera kryptograficznego modułu HSM** do użytkownika identyfikowanego przez główną nazwę użytkownika ** \@ contoso.com** dla wszystkich  **kluczy** (zakres `/keys` ) w ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Przypisywanie roli dla określonego klucza

Użyj `az keyvault role assignment create` polecenia, aby przypisać **zarządzaną rolę oficera kryptograficznego modułu HSM** do użytkownika identyfikowanego przez główną nazwę użytkownika o nazwie ** \@ contoso.com** dla określonego klucza o nazwie **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Wyświetlanie listy istniejących przypisań ról

Służy `az keyvault role assignment list` do wyświetlania listy przypisań ról.

Wszystkie przypisania ról w zakresie/(domyślnie, gdy nie określono zakresu) dla wszystkich użytkowników (wartość domyślna gdy--zostanie określona przystawka)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Wszystkie przypisania ról na poziomie modułu HSM dla określonego użytkownika **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

Wszystkie przypisania ról dla określonego użytkownika **user2@contoso.com** dla określonego **myrsakey**usługi Key.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Przypisanie konkretnej roli dla **oficera kryptograficznego modułu HSM zarządzanej** przez rolę dla określonego użytkownika **user2@contoso.com** w określonym kluczu **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Usuwanie przypisania roli

Użyj `az keyvault role assignment delete` polecenia, aby usunąć **zarządzaną rolę oficera kryptograficznego modułu HSM** przypisaną do użytkownika ** \@ contoso.com** dla usługi Key **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Wyświetl wszystkie dostępne definicje ról

Użyj `az keyvault role definition list` polecenia, aby wyświetlić listę wszystkich definicji ról.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>Następne kroki

- Zobacz Omówienie [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md).
- Zobacz Samouczek dotyczący [zarządzanego zarządzania rolami modułu HSM](role-management.md)
- Dowiedz się więcej o [modelu kontroli dostępu zarządzanego modułu HSM](access-control.md)
- Zobacz wszystkie [wbudowane role dla zarządzanego modułu HSM Local RBAC](built-in-roles.md)
