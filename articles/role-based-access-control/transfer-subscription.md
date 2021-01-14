---
title: Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD
description: Dowiedz się, jak przetransferować subskrypcję platformy Azure i znane powiązane zasoby do innego katalogu Azure Active Directory (Azure AD).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2020
ms.author: rolyon
ms.openlocfilehash: 81224b5e16f3bca5da641bbb2e9c82dd59000e79
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185890"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD

Organizacje mogą mieć kilka subskrypcji platformy Azure. Każda subskrypcja jest skojarzona z określonym katalogiem Azure Active Directory (Azure AD). Aby ułatwić zarządzanie, możesz chcieć przenieść subskrypcję do innego katalogu usługi Azure AD. Gdy przesyłasz subskrypcję do innego katalogu usługi Azure AD, niektóre zasoby nie są transferowane do katalogu docelowego. Na przykład wszystkie przypisania ról i role niestandardowe w ramach kontroli dostępu opartej na rolach na platformie Azure (RBAC) są **trwale** usuwane z katalogu źródłowego i nie są transferowane do katalogu docelowego.

W tym artykule opisano podstawowe czynności, które można wykonać w celu przeniesienia subskrypcji do innego katalogu usługi Azure AD i ponownego utworzenia niektórych zasobów po przeniesieniu.

> [!NOTE]
> W przypadku subskrypcji dostawcy rozwiązań w chmurze platformy Azure zmiana katalogu usługi Azure AD dla subskrypcji nie jest obsługiwana.

## <a name="overview"></a>Przegląd

Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD to złożony proces, który musi być starannie planowany i wykonywany. Wiele usług platformy Azure wymaga, aby podmioty zabezpieczeń (tożsamości) działały normalnie lub nawet zarządzać innymi zasobami platformy Azure. Ten artykuł próbuje uwzględnić większość usług platformy Azure, które są zależne od podmiotów zabezpieczeń, ale nie są wyczerpujące.

> [!IMPORTANT]
> W niektórych scenariuszach transfer subskrypcji może wymagać przestoju w celu ukończenia procesu. Dokładne planowanie jest wymagane do oceny, czy na potrzeby transferu będzie wymagane przestoje.

Na poniższym diagramie przedstawiono podstawowe kroki, które należy wykonać w przypadku przeniesienia subskrypcji do innego katalogu.

1. Przygotuj do przeniesienia

1. Przenoszenie subskrypcji platformy Azure do innego katalogu

1. Ponowne tworzenie zasobów w katalogu docelowym, takich jak przypisania ról, role niestandardowe i zarządzane tożsamości

    ![Przenoszenie diagramu subskrypcji](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Decydowanie o tym, czy chcesz przenieść subskrypcję do innego katalogu

Poniżej przedstawiono kilka powodów, dla których warto przenieść subskrypcję:

- Ze względu na fuzję lub pozyskiwanie firmy chcesz zarządzać uzyskaną subskrypcją w podstawowym katalogu usługi Azure AD.
- Ktoś w organizacji utworzył subskrypcję i chcesz skonsolidować zarządzanie do określonego katalogu usługi Azure AD.
- Masz aplikacje, które są zależne od określonego identyfikatora subskrypcji lub adresu URL i nie można łatwo modyfikować konfiguracji lub kodu aplikacji.
- Część firmy została podzielona na osobną firmę i należy przenieść niektóre zasoby do innego katalogu usługi Azure AD.
- Chcesz zarządzać niektórymi zasobami w innym katalogu usługi Azure AD na potrzeby izolacji zabezpieczeń.

### <a name="alternate-approaches"></a>Alternatywne podejścia

Przeniesienie subskrypcji wymaga przestoju, aby ukończyć proces. W zależności od danego scenariusza można wziąć pod uwagę następujące alternatywne podejścia:

- Utwórz ponownie zasoby i skopiuj dane do katalogu docelowego i subskrypcji.
- Zastosuj architekturę z obsługą kilku katalogów i pozostaw subskrypcję w katalogu źródłowym. Użyj usługi Azure Lighthouse, aby delegować zasoby, aby użytkownicy w katalogu docelowym mogli uzyskiwać dostęp do subskrypcji w katalogu źródłowym. Aby uzyskać więcej informacji, zobacz [Azure Lighthouse w scenariuszach dla przedsiębiorstw](../lighthouse/concepts/enterprise.md).

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Zrozumienie wpływu przesyłania subskrypcji

Kilka zasobów platformy Azure ma zależność od subskrypcji lub katalogu. W zależności od sytuacji w poniższej tabeli przedstawiono znany wpływ transferu subskrypcji. Wykonując kroki opisane w tym artykule, można ponownie utworzyć niektóre zasoby, które istniały przed przeniesieniem subskrypcji.

> [!IMPORTANT]
> Ta sekcja zawiera listę znanych usług lub zasobów platformy Azure, które są zależne od subskrypcji. Ponieważ typy zasobów na platformie Azure są stale rozwijane, mogą istnieć dodatkowe zależności, które nie są wymienione w tym miejscu, które mogą spowodować istotną zmianę w danym środowisku. 

| Usługa lub zasób | Wpływ na | Odzyskiwaln | Czy na pewno chcesz mieć wpływ? | Co możesz zrobić |
| --------- | --------- | --------- | --------- | --------- |
| Przypisania ról | Tak | Tak | [Lista przypisań ról](#save-all-role-assignments) | Wszystkie przypisania ról są trwale usuwane. Należy zamapować użytkowników, grupy i jednostki usługi na odpowiednie obiekty w katalogu docelowym. Należy ponownie utworzyć przypisania ról. |
| Role niestandardowe | Tak | Tak | [Wyświetlanie ról niestandardowych](#save-custom-roles) | Wszystkie role niestandardowe są trwale usuwane. Należy ponownie utworzyć role niestandardowe i dowolnych przypisań ról. |
| Zarządzane tożsamości przypisane do systemu | Tak | Tak | [Wyświetl listę tożsamości zarządzanych](#list-role-assignments-for-managed-identities) | Należy wyłączyć i ponownie włączyć zarządzane tożsamości. Należy ponownie utworzyć przypisania ról. |
| Tożsamości zarządzane przypisane przez użytkownika | Tak | Tak | [Wyświetl listę tożsamości zarządzanych](#list-role-assignments-for-managed-identities) | Należy usunąć, utworzyć ponownie i dołączyć zarządzane tożsamości do odpowiedniego zasobu. Należy ponownie utworzyć przypisania ról. |
| Azure Key Vault | Tak | Tak | [Wyświetlanie listy zasad dostępu Key Vault](#list-key-vaults) | Musisz zaktualizować identyfikator dzierżawy skojarzony z magazynami kluczy. Należy usunąć i dodać nowe zasady dostępu. |
| Bazy danych SQL Azure z włączoną integracją uwierzytelniania usługi Azure AD | Tak | Nie | [Sprawdzanie baz danych Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Usługa Azure Storage i Azure Data Lake Storage Gen2 | Tak | Tak |  | Należy ponownie utworzyć wszystkie listy ACL. |
| Azure Data Lake Storage Gen1 | Tak | Tak |  | Należy ponownie utworzyć wszystkie listy ACL. |
| Azure Files | Tak | Tak |  | Należy ponownie utworzyć wszystkie listy ACL. |
| Azure File Sync | Tak | Tak |  |  |
| Dyski zarządzane platformy Azure | Tak | Tak |  |  Jeśli używasz zestawów szyfrowania dysków do szyfrowania Managed Disks przy użyciu kluczy zarządzanych przez klienta, musisz wyłączyć i ponownie włączyć tożsamości przypisane do systemu skojarzone z zestawami szyfrowania dysków. I należy ponownie utworzyć przypisania ról, a tym samym ponownie przyznać wymagane uprawnienia do zestawów szyfrowania dysków w magazynach kluczy. |
| Azure Kubernetes Service | Tak | Tak |  |  |
| Azure Policy | Tak | Nie | Wszystkie obiekty Azure Policy, łącznie z definicjami niestandardowymi, przypisaniami, wykluczeniami i danymi zgodności. | Należy [wyeksportować](../governance/policy/how-to/export-resources.md), zaimportować i ponownie przypisać definicje. Następnie utwórz nowe przypisania zasad i wszelkie potrzebne [wykluczenia zasad](../governance/policy/concepts/exemption-structure.md). |
| Azure Active Directory Domain Services | Tak | Nie |  |  |
| Rejestracje aplikacji | Tak | Tak |  |  |

> [!WARNING]
> Jeśli używasz szyfrowania dla zasobu, takiego jak konto magazynu lub baza danych SQL, która ma zależność od magazynu kluczy, który **nie** znajduje się w tej samej subskrypcji, która jest transferowana, może prowadzić do nieodwracalnego scenariusza. W przypadku takiej sytuacji należy wykonać kroki w celu użycia innego magazynu kluczy lub tymczasowo wyłączyć klucze zarządzane przez klienta, aby uniknąć tego nieodwracalnego scenariusza.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

- [Bash w Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure)
- Administrator konta subskrypcji, która ma zostać przetransferowana w katalogu źródłowym
- Rola [właściciela](built-in-roles.md#owner) w katalogu docelowym

## <a name="step-1-prepare-for-the-transfer"></a>Krok 1. Przygotowanie do przeniesienia

### <a name="sign-in-to-source-directory"></a>Zaloguj się do katalogu źródłowego

1. Zaloguj się do platformy Azure jako administrator.

1. Pobierz listę subskrypcji za pomocą polecenia [AZ Account List](/cli/azure/account#az_account_list) .

    ```azurecli
    az account list --output table
    ```

1. Użyj [AZ Account Set](/cli/azure/account#az_account_set) , aby ustawić aktywną subskrypcję, którą chcesz przenieść.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Zainstaluj rozszerzenie grafu zasobów platformy Azure

 Rozszerzenie interfejsu wiersza polecenia platformy Azure dla [wykresu zasobów platformy Azure](../governance/resource-graph/index.yml), *grafy zasobów*, umożliwia używanie [AZ Graph](/cli/azure/ext/resource-graph/graph) Command do wykonywania zapytań dotyczących zasobów zarządzanych przez Azure Resource Manager. To polecenie będzie używane w dalszych krokach.

1. Użyj [AZ Extension list](/cli/azure/extension#az_extension_list) , aby sprawdzić, czy masz zainstalowane rozszerzenie *grafu zasobów* .

    ```azurecli
    az extension list
    ```

1. Jeśli nie, zainstaluj rozszerzenie *grafu zasobów* .

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Zapisz wszystkie przypisania ról

1. Użyj [AZ role przypisanie list](/cli/azure/role/assignment#az_role_assignment_list) , aby wyświetlić listę wszystkich przypisań ról (łącznie z dziedziczonymi przypisaniami ról).

    Aby ułatwić zapoznanie się z listą, można wyeksportować dane wyjściowe w formacie JSON, TSV lub tabelę. Aby uzyskać więcej informacji, zobacz [Wyświetlanie listy przypisań ról przy użyciu funkcji Azure RBAC i interfejsu wiersza polecenia platformy Azure](role-assignments-list-cli.md).

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Zapisz listę przypisań ról.

    Podczas transferu subskrypcji wszystkie przypisania ról są **trwale** usuwane, dlatego ważne jest, aby zapisać kopię.

1. Przejrzyj listę przypisań ról. W katalogu docelowym mogą istnieć przydziały ról, które nie będą potrzebne.

### <a name="save-custom-roles"></a>Zapisz role niestandardowe

1. Użyj [listy AZ role Definition](/cli/azure/role/definition#az_role_definition_list) , aby wyświetlić listę ról niestandardowych. Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](custom-roles-cli.md).

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Zapisz każdą rolę niestandardową, która będzie potrzebna w katalogu docelowym jako oddzielny plik JSON.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Utwórz kopie niestandardowych plików ról.

1. Zmodyfikuj każdą kopię, tak aby korzystała z następującego formatu.

    Te pliki będą używane później, aby ponownie utworzyć role niestandardowe w katalogu docelowym.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Określanie mapowań użytkowników, grup i jednostek usługi

1. Na podstawie listy przypisań ról Określ użytkowników, grupy i jednostki usługi, w których będziesz mapować w katalogu docelowym.

    Typ podmiotu zabezpieczeń można zidentyfikować, przeglądając `principalType` Właściwość w poszczególnych przypisaniach ról.

1. Jeśli to konieczne, w katalogu docelowym Utwórz wszystkich użytkowników, grupy lub jednostki usługi, które będą potrzebne.

### <a name="list-role-assignments-for-managed-identities"></a>Wyświetl listę przypisań ról dla tożsamości zarządzanych

Tożsamości zarządzane nie są aktualizowane, gdy subskrypcja zostanie przetransferowana do innego katalogu. W związku z tym wszystkie istniejące tożsamości zarządzane przypisane do systemu lub przypisane przez użytkownika zostaną przerwane. Po przeniesieniu można ponownie włączyć wszelkie zarządzane tożsamości przypisane do systemu. W przypadku tożsamości zarządzanych przypisanych przez użytkownika należy ponownie utworzyć i dołączyć je do katalogu docelowego.

1. Przejrzyj [listę usług platformy Azure, które obsługują tożsamości zarządzane,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) aby zauważyć, gdzie mogą być używane tożsamości zarządzane.

1. Użyj [AZ AD Sp list](/cli/azure/ad/sp#az_ad_sp_list) , aby wyświetlić zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Na liście zarządzanych tożsamości Ustal, które są przypisane do systemu i które są przypisane przez użytkownika. Aby określić typ, można użyć następujących kryteriów.

    | Kryteria | Typ tożsamości zarządzanej |
    | --- | --- |
    | `alternativeNames` Właściwość obejmuje `isExplicit=False` | Przypisane przez system |
    | `alternativeNames` Właściwość nie zawiera `isExplicit` | Przypisane przez system |
    | `alternativeNames` Właściwość obejmuje `isExplicit=True` | Przypisane przez użytkownika |

    Możesz również użyć [AZ Identity list](/cli/azure/identity#az_identity_list) , aby tylko wyświetlić tożsamości zarządzane przypisane przez użytkownika. Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md).

    ```azurecli
    az identity list
    ```

1. Pobierz listę `objectId` wartości dla zarządzanych tożsamości.

1. Przeszukaj listę przypisań ról, aby sprawdzić, czy istnieją przydziały ról dla zarządzanych tożsamości.

### <a name="list-key-vaults"></a>Utwórz listę magazynów kluczy

Podczas tworzenia magazynu kluczy jest on automatycznie powiązany z domyślnym IDENTYFIKATORem dzierżawy Azure Active Directory dla subskrypcji, w której został utworzony. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. Aby uzyskać więcej informacji, zobacz [Przechodzenie Azure Key Vault do innej subskrypcji](../key-vault/general/move-subscription.md).

> [!WARNING]
> Jeśli używasz szyfrowania dla zasobu, takiego jak konto magazynu lub baza danych SQL, która ma zależność od magazynu kluczy, który **nie** znajduje się w tej samej subskrypcji, która jest transferowana, może prowadzić do nieodwracalnego scenariusza. W przypadku takiej sytuacji należy wykonać kroki w celu użycia innego magazynu kluczy lub tymczasowo wyłączyć klucze zarządzane przez klienta, aby uniknąć tego nieodwracalnego scenariusza.

- Jeśli masz Magazyn kluczy, użyj AZ Key [magazynu show](/cli/azure/keyvault#az_keyvault_show) , aby wyświetlić listę zasad dostępu. Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu Key Vault](../key-vault/general/assign-access-policy-cli.md).

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Wyświetlanie listy baz danych Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD

- Użyj polecenia [AZ SQL Server AD-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) i [AZ Graph](/cli/azure/ext/resource-graph/graph) Extension, aby sprawdzić, czy są używane bazy danych Azure SQL Database z włączoną integracją uwierzytelniania usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim przy użyciu programu SQL Server](../azure-sql/database/authentication-aad-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Listy ACL

1. Jeśli używasz Azure Data Lake Storage Gen1, Utwórz listę list kontroli dostępu, które są stosowane do każdego pliku przy użyciu Azure Portal lub programu PowerShell.

1. Jeśli używasz Azure Data Lake Storage Gen2, Utwórz listę list kontroli dostępu, które są stosowane do każdego pliku przy użyciu Azure Portal lub programu PowerShell.

1. Jeśli używasz Azure Files, lista list kontroli dostępu, które są stosowane do każdego pliku.

### <a name="list-other-known-resources"></a>Wyświetlanie listy innych znanych zasobów

1. Użyj [AZ Account show](/cli/azure/account#az_account_show) , aby uzyskać identyfikator subskrypcji.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Użyj rozszerzenia [AZ Graph](/cli/azure/ext/resource-graph/graph) , aby wyświetlić listę innych zasobów platformy Azure ze znanymi zależnościami w katalogu usługi Azure AD.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Krok 2. przeniesienie subskrypcji

W tym kroku przeniesiesz subskrypcję z katalogu źródłowego do katalogu docelowego. Kroki będą się różnić w zależności od tego, czy chcesz również przenieść własność rozliczeń.

> [!WARNING]
> Po przeniesieniu subskrypcji wszystkie przypisania ról w katalogu źródłowym są **trwale** usuwane i nie można ich przywrócić. Po przeniesieniu subskrypcji nie można jej cofnąć. Przed wykonaniem tego kroku upewnij się, że wykonano poprzednie kroki.

1. Określ, czy chcesz również przetransferować własność rozliczeń na inne konto.

1. Przenieś subskrypcję do innego katalogu.

    - Jeśli chcesz zachować bieżącą własność rozliczeń, postępuj zgodnie z instrukcjami w temacie [kojarzenie lub Dodawanie subskrypcji platformy Azure do dzierżawy Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    - Jeśli chcesz również przetransferować własność rozliczeń, postępuj zgodnie z instrukcjami w sekcji [przenoszenie własności rozliczeń subskrypcji platformy Azure na inne konto](../cost-management-billing/manage/billing-subscription-transfer.md). Aby przenieść subskrypcję do innego katalogu, należy zaznaczyć pole wyboru **subskrypcja usługi Azure AD** .

1. Po zakończeniu transferu subskrypcji Wróć do tego artykułu, aby ponownie utworzyć zasoby w katalogu docelowym.

## <a name="step-3-re-create-resources"></a>Krok 3. ponowne tworzenie zasobów

### <a name="sign-in-to-target-directory"></a>Zaloguj się do katalogu docelowego

1. W katalogu docelowym Zaloguj się jako użytkownik, który zaakceptował żądanie transferu.

    Tylko użytkownik na nowym koncie, który zaakceptował żądanie transferu, będzie miał dostęp do zarządzania zasobami.

1. Pobierz listę subskrypcji za pomocą polecenia [AZ Account List](/cli/azure/account#az_account_list) .

    ```azurecli
    az account list --output table
    ```

1. Użyj [AZ Account Set](/cli/azure/account#az_account_set) , aby ustawić aktywną subskrypcję, której chcesz użyć.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Tworzenie ról niestandardowych
        
- Użyj [AZ role Definition Create](/cli/azure/role/definition#az_role_definition_create) , aby utworzyć każdą rolę niestandardową z utworzonych wcześniej plików. Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](custom-roles-cli.md).

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>Tworzenie przypisań roli

- Użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az_role_assignment_create) , aby utworzyć przypisania roli dla użytkowników, grup i jednostek usługi. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról przy użyciu usług Azure RBAC i interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md).

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Aktualizowanie tożsamości zarządzanych przypisanych przez system

1. Wyłącz i ponownie włącz zarządzane tożsamości przypisane do systemu.

    | Usługa platformy Azure | Więcej informacji | 
    | --- | --- |
    | Maszyny wirtualne | [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Zestawy skalowania maszyn wirtualnych | [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Inne usługi | [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az_role_assignment_create) , aby utworzyć przypisania roli dla tożsamości zarządzanych przypisanych do systemu. Aby uzyskać więcej informacji, zobacz [przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Aktualizowanie tożsamości zarządzanych przypisanych przez użytkownika

1. Usuń, ponownie utwórz i Dołącz tożsamości zarządzane przypisane przez użytkownika.

    | Usługa platformy Azure | Więcej informacji | 
    | --- | --- |
    | Maszyny wirtualne | [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Zestawy skalowania maszyn wirtualnych | [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Inne usługi | [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Tworzenie, wyświetlanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az_role_assignment_create) , aby utworzyć przypisania roli dla tożsamości zarządzanych przypisanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Aktualizowanie magazynów kluczy

W tej sekcji opisano podstawowe kroki aktualizowania magazynów kluczy. Aby uzyskać więcej informacji, zobacz [Przechodzenie Azure Key Vault do innej subskrypcji](../key-vault/general/move-subscription.md).

1. Zaktualizuj identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji do katalogu docelowego.

1. usunąć wszystkie istniejące wpisy zasad dostępu,

1. Dodaj nowe wpisy zasad dostępu skojarzone z katalogiem docelowym.

### <a name="update-acls"></a>Aktualizowanie list ACL

1. Jeśli używasz Azure Data Lake Storage Gen1, przypisz odpowiednie listy ACL. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych przechowywanych w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Jeśli używasz Azure Data Lake Storage Gen2, przypisz odpowiednie listy ACL. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

1. Jeśli używasz Azure Files, przypisz odpowiednie listy ACL.

### <a name="review-other-security-methods"></a>Zapoznaj się z innymi metodami zabezpieczeń

Mimo że przypisania ról są usuwane podczas transferu, użytkownicy z oryginalnego konta właściciela mogą nadal mieć dostęp do subskrypcji za pośrednictwem innych metod zabezpieczeń, w tym:

- Klucze dostępu dla usług, takich jak Storage.
- [Certyfikaty zarządzania](../cloud-services/cloud-services-certs-create.md) , które umożliwiają administratorowi użytkowników dostęp do zasobów subskrypcji.
- Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli zamiarem jest usunięcie dostępu użytkowników w katalogu źródłowym, aby nie mieli dostępu do katalogu docelowego, należy rozważyć obrócenie wszelkich poświadczeń. Dopóki poświadczenia nie zostaną zaktualizowane, użytkownicy będą nadal mieć dostęp po przeniesieniu.

1. Obróć klucze dostępu do konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).

1. Jeśli używasz kluczy dostępu dla innych usług, takich jak Azure SQL Database lub Azure Service Bus Messaging, Obróć klucze dostępu.

1. W przypadku zasobów korzystających z kluczy tajnych Otwórz ustawienia zasobu i zaktualizuj klucz tajny.

1. W przypadku zasobów, które używają certyfikatów, zaktualizuj certyfikat.

## <a name="next-steps"></a>Następne kroki

- [Przeniesienie własności rozliczeń subskrypcji platformy Azure na inne konto](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Przenoszenie subskrypcji platformy Azure między subskrybentami i dostawcami usług w chmurze](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie subskrypcji](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw](../lighthouse/concepts/enterprise.md)