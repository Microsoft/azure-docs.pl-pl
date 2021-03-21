---
title: Przyznawanie uprawnień aplikacji do uzyskiwania dostępu do magazynu kluczy platformy Azure przy użyciu usługi Azure RBAC | Microsoft Docs
description: Dowiedz się, jak zapewnić dostęp do kluczy, wpisów tajnych i certyfikatów przy użyciu kontroli dostępu opartej na rolach na platformie Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 886b87adeabdc0aadde04c189b78739435aabede
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100527068"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Zapewnianie dostępu do kluczy Key Vault, certyfikatów i wpisów tajnych za pomocą kontroli dostępu opartej na rolach na platformie Azure

> [!NOTE]
> Dostawca zasobów Key Vault obsługuje dwa typy zasobów: **magazyny** i **zarządzane sprzętowych modułów zabezpieczeń**. Kontrola dostępu opisana w tym artykule dotyczy tylko **magazynów**. Aby dowiedzieć się więcej o kontroli dostępu dla zarządzanego modułu HSM, zobacz [zarządzana kontrola dostępu modułu HSM](../managed-hsm/access-control.md).

Kontrola dostępu oparta na rolach (Azure RBAC) to system autoryzacji oparty na [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , który zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure.

Usługa Azure RBAC umożliwia użytkownikom zarządzanie uprawnieniami kluczy, wpisów tajnych i certyfikatów. Oferuje jedno miejsce do zarządzania wszystkimi uprawnieniami w ramach wszystkich magazynów kluczy. 

Model RBAC platformy Azure umożliwia ustawianie uprawnień na różnych poziomach zakresu: grupy zarządzania, subskrypcji, grupy zasobów lub poszczególnych zasobów.  Funkcja RBAC systemu Azure dla magazynu kluczy zapewnia również możliwość posiadania oddzielnych uprawnień do poszczególnych kluczy, wpisów tajnych i certyfikatów.

Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Najlepsze rozwiązania dotyczące poszczególnych kluczy, wpisów tajnych i certyfikatów

Nasze zalecenie polega na użyciu magazynu dla każdej aplikacji na środowisko (projektowanie, przedprodukcyjne i produkcyjne).

Poszczególne klucze, wpisy tajne i certyfikaty powinny być używane tylko w określonych scenariuszach:

-   Aplikacje wielowarstwowe, które muszą oddzielić kontrolę dostępu między warstwami

-   Udostępnianie pojedynczego wpisu tajnego między wieloma aplikacjami

Więcej informacji na temat wytycznych dotyczących zarządzania Azure Key Vault można znaleźć w temacie:

- [Omówienie zabezpieczeń Azure Key Vault](security-overview.md)
- [Limity usługi Azure Key Vault](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Wbudowane role platformy Azure dla operacji Key Vault płaszczyzny danych
> [!NOTE]
> `Key Vault Contributor` Rola dotyczy operacji płaszczyzny zarządzania w celu zarządzania magazynami kluczy. Nie zezwala na dostęp do kluczy, wpisów tajnych i certyfikatów.

| Wbudowana rola | Opis | ID (Identyfikator) |
| --- | --- | --- |
| Key Vault administrator| Wykonaj wszystkie operacje płaszczyzny danych w magazynie kluczy i wszystkie obiekty w nim, w tym certyfikaty, klucze i wpisy tajne. Nie można zarządzać zasobami magazynu kluczy ani zarządzać przypisaniami ról. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Oficer certyfikatów Key Vault | Wykonaj dowolną akcję dotyczącą certyfikatów magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault oficer kryptograficzny | Wykonaj dowolną akcję dotyczącą kluczy magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault użytkownika szyfrowania usługi kryptograficznej | Odczytywanie metadanych kluczy i wykonywanie operacji zawijania/odpakowania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault użytkownika kryptograficznego  | Wykonywanie operacji kryptograficznych przy użyciu kluczy. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault czytelnik | Odczytywanie metadanych magazynów kluczy oraz jego certyfikatów, kluczy i wpisów tajnych. Nie można odczytać wartości poufnych, takich jak zawartość wpisu tajnego lub materiał klucza. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault oficerów tajnych| Wykonaj wszelkie działania dotyczące wpisów tajnych magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Użytkownik Key Vault Secret | Odczytaj zawartość wpisu tajnego. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |

Aby uzyskać więcej informacji na temat definicji ról wbudowanych platformy Azure, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Korzystanie z usługi Azure RBAC Secret, klucza i certyfikatu z Key Vault

Nowy model uprawnień usługi Azure RBAC dla magazynu kluczy zapewnia alternatywę dla modelu uprawnień zasad dostępu do magazynu. 

### <a name="prerequisites"></a>Wymagania wstępne

Aby dodać przypisania ról, musisz mieć:

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) lub [właściciel](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Włącz uprawnienia usługi Azure RBAC na Key Vault

> [!NOTE]
> Zmiana modelu uprawnień wymaga uprawnienia "Microsoft. Authorization/roleAssignments/Write", które jest częścią ról [administratorów dostępu](../../role-based-access-control/built-in-roles.md#user-access-administrator) [właściciela](../../role-based-access-control/built-in-roles.md#owner) i użytkownika. Role administratora klasycznej subskrypcji, takie jak "administrator usługi" i "współadministrator", nie są obsługiwane.

1.  Włącz uprawnienia kontroli RBAC platformy Azure dla nowego magazynu kluczy:

    ![Włączanie uprawnień RBAC platformy Azure — nowy magazyn](../media/rbac/image-1.png)

2.  Włącz uprawnienia kontroli RBAC platformy Azure dla istniejącego magazynu kluczy:

    ![Włączanie uprawnień RBAC platformy Azure — istniejący magazyn](../media/rbac/image-2.png)

> [!IMPORTANT]
> Ustawienie modelu uprawnień RBAC platformy Azure unieważnia wszystkie uprawnienia zasad dostępu. Może to spowodować awarię, gdy równoważne role platformy Azure nie są przypisane.

### <a name="assign-role"></a>Przypisywanie roli

> [!Note]
> Zaleca się używanie unikatowego identyfikatora roli zamiast nazwy roli w skryptach. W związku z tym, jeśli zostanie zmieniona nazwa roli, skrypty nadal będą działały. Ta nazwa roli dokumentu jest używana tylko na potrzeby czytelności.

Polecenie interfejsu wiersza polecenia platformy Azure do utworzenia przypisania roli:

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

W Azure Portal ekran przypisań ról platformy Azure jest dostępny dla wszystkich zasobów na karcie kontroli dostępu (IAM).

![Przypisanie roli — karta (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Przypisanie roli zakresu grupy zasobów

1.  Przejdź do grupy zasobów magazynu kluczy.
    ![Przypisanie roli — Grupa zasobów](../media/rbac/image-4.png)

2.  Kliknij kolejno pozycje kontrola dostępu (IAM) Dodaj \> przypisanie roli \> Dodaj

3.  Utwórz Key Vault rolę czytnika "Key Vault Reader" dla bieżącego użytkownika

    ![Dodaj rolę — grupę zasobów](../media/rbac/image-5.png)

Interfejs wiersza polecenia platformy Azure:
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

Powyższe przypisanie roli umożliwia wyświetlanie listy obiektów magazynu kluczy w magazynie kluczy.

### <a name="key-vault-scope-role-assignment"></a>Przypisanie roli zakresu Key Vault

1. Przejdź do \> karty Key Vault Access Control (IAM)

2. Kliknij przycisk Dodaj przypisanie roli \> Dodaj

3. Utwórz rolę oficera klucza tajnego "Key Vault tajemnicy" dla bieżącego użytkownika.

    ![Przypisanie roli — Magazyn kluczy](../media/rbac/image-6.png)

 Interfejs wiersza polecenia platformy Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Po utworzeniu przypisaniu roli można utworzyć/zaktualizować/usunąć wpisy tajne.

4. Utwórz nowy wpis tajny (Secret \> + Generate + Create/import) do testowania przypisania roli poziomu tajnego.

    ![Dodaj magazyn kluczy roli](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Przypisanie roli zakresu wpisów tajnych

1. Otwórz jeden z wcześniej utworzonych wpisów tajnych, sprawdź przegląd i kontrolę dostępu (IAM) 

2. Kliknij kartę kontrola dostępu (IAM)

    ![Przypisanie roli — klucz tajny](../media/rbac/image-8.png)

3. Utwórz rolę oficera klucza tajnego "Key Vault oficerów tajnych" dla bieżącego użytkownika, tak jak powyżej, została przeprowadzona powyżej dla Key Vault.

Interfejs wiersza polecenia platformy Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>Testowanie i weryfikowanie

> [!NOTE]
> Przeglądarki używają pamięci podręcznej, a odświeżanie strony jest wymagane po usunięciu przypisań ról.<br>
> Poczekaj kilka minut na odświeżenie przypisań ról

1. Sprawdź poprawność dodawania nowego wpisu tajnego bez roli "Key Vault tajemnicy" na poziomie magazynu kluczy.

Przejdź do karty kontrola dostępu do magazynu kluczy (IAM) i usuń przypisanie roli "Key Vault tajemnicy dla tego zasobu.

![Usuwanie magazynu kluczy przypisania](../media/rbac/image-9.png)

Przejdź do wcześniej utworzonego klucza tajnego. Wszystkie właściwości klucza tajnego są widoczne.

![Widok wpisów tajnych z dostępem](../media/rbac/image-10.png)

Utwórz nowy wpis tajny (wpisy tajne \> + generowanie/import) powinien zostać wyświetlony następujący błąd:

   ![Utwórz nowy wpis tajny](../media/rbac/image-11.png)

2.  Weryfikuj tajne edytowanie bez "Key Vault tajemnicy" na poziomie tajnym.

-   Przejdź do wcześniej utworzonego wpisu tajnego Access Control (IAM) i usuń przypisanie roli "Key Vault tajemnicy" dla tego zasobu.

-   Przejdź do wcześniej utworzonego klucza tajnego. Można wyświetlić właściwości klucza tajnego.

   ![Widok tajny bez dostępu](../media/rbac/image-12.png)

3. Sprawdź poprawność odczytu wpisów tajnych bez roli czytnika na poziomie magazynu kluczy.

-   Przejdź do karty kontrola dostępu grupy zasobów magazynu kluczy (IAM) i usuń przypisanie roli "Key Vault Reader".

-   Przejdź do karty wpisy tajne magazynu kluczy, który powinien zostać wyświetlony poniżej:

   ![Karta klucza tajnego — błąd](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Tworzenie ról niestandardowych 

[AZ role Definition Create — polecenie](/cli/azure/role/definition#az-role-definition-create)

**(Skrypt bash interfejsu wiersza polecenia)</br>**
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

Aby uzyskać więcej informacji o sposobach tworzenia ról niestandardowych, zobacz:

[Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Znane limity i wydajność

-   2000 przypisań ról na platformie Azure na subskrypcję

-   Opóźnienie przypisań ról: w bieżącej oczekiwanej wydajności zajmie to 10 minut (600 sekund) po zmianie przypisań ról dla roli do zastosowania

## <a name="learn-more"></a>Dowiedz się więcej

- [Przegląd RBAC platformy Azure](../../role-based-access-control/overview.md)
- [Samouczek ról niestandardowych](../../role-based-access-control/tutorial-custom-role-cli.md)