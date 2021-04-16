---
title: Udzielanie aplikacjom uprawnień dostępu do usługi Azure Key Vault przy użyciu kontroli dostępu na | Microsoft Docs
description: Dowiedz się, jak zapewnić dostęp do kluczy, wpisów tajnych i certyfikatów przy użyciu kontroli dostępu opartej na rolach platformy Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a1e9f499b54b0cf3b75a78e76a44c1df6e3e3b3e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479852"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Zapewnianie dostępu do Key Vault, certyfikatów i wpisów tajnych przy użyciu kontroli dostępu opartej na rolach platformy Azure

> [!NOTE]
> Key Vault zasobów obsługuje dwa typy zasobów: **magazyny** i **zarządzane moduły HSM.** Kontrola dostępu opisana w tym artykule ma zastosowanie tylko **do magazynów**. Aby dowiedzieć się więcej na temat kontroli dostępu do zarządzanego modułu HSM, zobacz [Managed HSM access control (Kontrola dostępu](../managed-hsm/access-control.md)zarządzanego modułu HSM).

Kontrola dostępu oparta na rolach (RBAC) platformy Azure [to](../../azure-resource-manager/management/overview.md) system autoryzacji oparty na Azure Resource Manager, który umożliwia szczegółowe zarządzanie dostępem do zasobów platformy Azure.

Funkcja RBAC platformy Azure umożliwia użytkownikom zarządzanie uprawnieniami do kluczy, wpisów tajnych i certyfikatów. Zapewnia jedno miejsce do zarządzania wszystkimi uprawnieniami we wszystkich magazynach kluczy. 

Model RBAC platformy Azure umożliwia ustawianie uprawnień na różnych poziomach zakresu: grupy zarządzania, subskrypcji, grupy zasobów lub poszczególnych zasobów.  RBAC platformy Azure dla magazynu kluczy zapewnia również możliwość oddzielnych uprawnień do poszczególnych kluczy, wpisów tajnych i certyfikatów

Aby uzyskać więcej informacji, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../../role-based-access-control/overview.md)dostępu oparta na rolach na platformie Azure).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Najlepsze rozwiązania dotyczące poszczególnych kluczy, wpisów tajnych i certyfikatów

Naszym zaleceniem jest użycie magazynu na aplikację na środowisko (deweloperskie, przedprodukcyjne i produkcyjne).

Uprawnienia do poszczególnych kluczy, wpisów tajnych i certyfikatów powinny być używane tylko w określonych scenariuszach:

-   Aplikacje wielowarstwowe, które muszą oddzielić kontrolę dostępu między warstwami

-   Udostępnianie poszczególnych tajnych danych między wieloma aplikacjami

Więcej informacji na Azure Key Vault zarządzania zasobami, zobacz:

- [Azure Key Vault omówienie zabezpieczeń](security-overview.md)
- [Azure Key Vault limity usługi](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Wbudowane role platformy Azure dla operacji Key Vault płaszczyzny danych
> [!NOTE]
> `Key Vault Contributor` Rola jest dla operacji płaszczyzny zarządzania do zarządzania magazynami kluczy. Nie zezwala na dostęp do kluczy, wpisów tajnych i certyfikatów.

| Rola wbudowana | Opis | ID (Identyfikator) |
| --- | --- | --- |
| Key Vault administratora| Wykonaj wszystkie operacje płaszczyzny danych w magazynie kluczy i wszystkich znajdujących się w nim obiektach, w tym certyfikatach, kluczach i wpisach tajnych. Nie można zarządzać zasobami magazynu kluczy ani zarządzać przypisaniami ról. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault certyfikatów | Wykonaj dowolną akcję na certyfikatach magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer | Wykonaj dowolną akcję na kluczach magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault szyfrowania usługi kryptograficznych | Odczytywanie metadanych kluczy i wykonywanie operacji opakowuj/odpakowuj. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault użytkownik kryptograficzny  | Wykonywanie operacji kryptograficznych przy użyciu kluczy. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault czytelnika | Odczytywanie metadanych magazynów kluczy i ich certyfikatów, kluczy i wpisów tajnych. Nie można odczytać poufnych wartości, takich jak zawartość klucza tajnego lub materiał klucza. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer| Wykonaj dowolną akcję na wpisach tajnych magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault wpisów tajnych | Odczytywanie zawartości tajnej. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |

Aby uzyskać więcej informacji na temat wbudowanych definicji ról platformy Azure, zobacz Role wbudowane [platformy Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Używanie uprawnień do klucza tajnego, klucza i certyfikatu RBAC platformy Azure z Key Vault

Nowy model uprawnień RBAC platformy Azure dla magazynu kluczy stanowi alternatywę dla modelu uprawnień zasad dostępu do magazynu. 

### <a name="prerequisites"></a>Wymagania wstępne

Aby dodać przypisania ról, musisz mieć:

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) lub [właściciel](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Włączanie uprawnień RBAC platformy Azure na Key Vault

> [!NOTE]
> Zmiana modelu uprawnień wymaga uprawnienia "Microsoft.Authorization/roleAssignments/write", które jest częścią [ról Właściciel](../../role-based-access-control/built-in-roles.md#owner) i [Administrator dostępu](../../role-based-access-control/built-in-roles.md#user-access-administrator) użytkowników. Role klasycznego administratora subskrypcji, takie jak "Administrator usługi" i "Współ-administrator", nie są obsługiwane.

1.  Włącz uprawnienia RBAC platformy Azure w nowym magazynie kluczy:

    ![Włączanie uprawnień RBAC platformy Azure — nowy magazyn](../media/rbac/image-1.png)

2.  Włącz uprawnienia RBAC platformy Azure w istniejącym magazynie kluczy:

    ![Włączanie uprawnień RBAC platformy Azure — istniejący magazyn](../media/rbac/image-2.png)

> [!IMPORTANT]
> Ustawienie modelu uprawnień RBAC platformy Azure unieważnia wszystkie uprawnienia zasad dostępu. Może to spowodować błędy, gdy równoważne role platformy Azure nie zostaną przypisane.

### <a name="assign-role"></a>Przypisywanie roli

> [!Note]
> W skryptach zaleca się użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym jeśli nazwa roli zostanie zmieniona, skrypty będą nadal działać. W tym dokumencie nazwa roli jest używana tylko w celu czytelności.

Uruchom następujące polecenie, aby utworzyć przypisanie roli:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

W witrynie Azure Portal ekranu przypisań ról platformy Azure jest dostępny dla wszystkich zasobów na karcie Kontrola dostępu (IAM).

![Przypisanie roli — karta (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Przypisanie roli zakresu grupy zasobów

1.  Przejdź do grupy zasobów magazynu kluczy.
    ![Przypisanie roli — grupa zasobów](../media/rbac/image-4.png)

2.  Kliknij pozycję Kontrola dostępu (IAM) \> Dodaj przypisanie roli \> Dodaj

3.  Tworzenie Key Vault czytelnika "Key Vault Czytelnik" dla bieżącego użytkownika

    ![Dodawanie roli — grupa zasobów](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

Powyższe przypisanie roli umożliwia listę obiektów magazynu kluczy w magazynie kluczy.

### <a name="key-vault-scope-role-assignment"></a>Key Vault przypisania roli zakresu

1. Przejdź do Key Vault \> kontrola dostępu (IAM)

2. Kliknij pozycję Dodaj przypisanie roli \> Dodaj

3. Utwórz rolę "Key Vault Secrets Officer" dla bieżącego użytkownika.

    ![Przypisanie roli — magazyn kluczy](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

Po utworzeniu powyższego przypisania roli możesz tworzyć/aktualizować/usuwać wpisy tajne.

4. Utwórz nowy wpis tajny (Wpisy tajne \> +Generuj/Importuj) do testowania przypisania roli na poziomie wpisów tajnych.

    ![Dodawanie roli — magazyn kluczy](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Przypisanie roli zakresu tajnego

1. Otwórz jeden z wcześniej utworzonych wpisów tajnych. Zwróć uwagę na przegląd i kontrolę dostępu (IAM) 

2. Kliknij kartę Kontrola dostępu (IAM)

    ![Przypisanie roli — klucz tajny](../media/rbac/image-8.png)

3. Utwórz rolę "Key Vault Secrets Officer" dla bieżącego użytkownika, tak samo jak w przypadku Key Vault.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>Testowanie i weryfikowanie

> [!NOTE]
> Przeglądarki używają buforowania, a odświeżanie strony jest wymagane po usunięciu przypisań ról.<br>
> Odświęć kilka minut na odświeżenie przypisań ról

1. Zweryfikuj dodawanie nowego klucza tajnego bez Key Vault "Tajnego inspektora" na poziomie magazynu kluczy.

Przejdź do karty Kontrola dostępu (IAM) magazynu kluczy i usuń przypisanie roli "Key Vault Secrets Officer" dla tego zasobu.

![Usuwanie przypisania — magazyn kluczy](../media/rbac/image-9.png)

Przejdź do wcześniej utworzonego tajnego. Można wyświetlić wszystkie właściwości tajne.

![Widok klucz tajny z dostępem](../media/rbac/image-10.png)

W przypadku tworzenia nowego wpisów \> tajnych (Wpisy tajne +Generuj/Importuj) powinien być wyświetlany poniższy błąd:

   ![Tworzenie nowego tajnego](../media/rbac/image-11.png)

2.  Zweryfikuj edytowanie informacji tajnych Key Vault tajnego" na poziomie tajnego.

-   Przejdź do wcześniej utworzonej karty Access Control tajnego (IAM) i usuń przypisanie roli "Key Vault Secrets Officer" dla tego zasobu.

-   Przejdź do wcześniej utworzonego tajnego. Możesz zobaczyć właściwości tajne.

   ![Widok tajny bez dostępu](../media/rbac/image-12.png)

3. Weryfikowanie wpisów tajnych odczytanych bez roli czytelnika na poziomie magazynu kluczy.

-   Przejdź do karty Kontrola dostępu (IAM) grupy zasobów magazynu kluczy i usuń przypisanie roli "Key Vault czytelnika".

-   Przejście do karty Wpisy tajne magazynu kluczy powinno wyświetlić poniższy błąd:

   ![Karta Klucz tajny — błąd](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Tworzenie ról niestandardowych 

[az role definition create, polecenie](/cli/azure/role/definition#az-role-definition-create)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych, zobacz:

[Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Znane limity i wydajność

-   2000 przypisań ról platformy Azure na subskrypcję

-   Opóźnienie przypisań ról: przy bieżącej oczekiwanej wydajności zastosowanie roli potrwa do 10 minut (600 sekund) po zmianie przypisań ról

## <a name="learn-more"></a>Więcej tutaj

- [Omówienie kontroli RBAC platformy Azure](../../role-based-access-control/overview.md)
- [Samouczek dotyczący ról niestandardowych](../../role-based-access-control/tutorial-custom-role-cli.md)
