---
title: Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD
description: Dowiedz się, jak przenieść subskrypcję platformy Azure i znane powiązane zasoby do innego Azure Active Directory (Azure AD).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 72dc92ae211034e2a49bc77f60880f17ab15dec7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868181"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD

Organizacje mogą mieć kilka subskrypcji platformy Azure. Każda subskrypcja jest skojarzona z określonym Azure Active Directory (Azure AD). Aby ułatwić zarządzanie, warto przenieść subskrypcję do innego katalogu usługi Azure AD. Podczas przenoszenia subskrypcji do innego katalogu usługi Azure AD niektóre zasoby nie są przenoszone do katalogu docelowego. Na przykład wszystkie przypisania ról i role niestandardowe w kontroli dostępu na  podstawie ról (RBAC) platformy Azure są trwale usuwane z katalogu źródłowego i nie są przenoszone do katalogu docelowego.

W tym artykule opisano podstawowe kroki, które można wykonać, aby przenieść subskrypcję do innego katalogu usługi Azure AD i ponownie utworzyć niektóre zasoby po przeniesieniu.

> [!NOTE]
> W przypadku subskrypcji dostawców rozwiązań w chmurze platformy Azure zmiana katalogu usługi Azure AD dla subskrypcji nie jest obsługiwana.

## <a name="overview"></a>Omówienie

Przeniesienie subskrypcji platformy Azure do innego katalogu usługi Azure AD to złożony proces, który należy starannie zaplanować i wykonać. Wiele usług platformy Azure wymaga, aby podmioty zabezpieczeń (tożsamości) działały normalnie, a nawet zarządzały innymi zasobami platformy Azure. Ten artykuł próbuje obejmować większość usług platformy Azure, które w dużym stopniu zależą od podmiotów zabezpieczeń, ale nie są kompleksowe.

> [!IMPORTANT]
> W niektórych scenariuszach przeniesienie subskrypcji może wymagać przestoju do ukończenia procesu. Dokładne planowanie jest wymagane do oceny, czy do przeniesienia będzie wymagany przestój.

Na poniższym diagramie przedstawiono podstawowe kroki, które należy wykonać podczas przenoszenia subskrypcji do innego katalogu.

1. Przygotowanie do przeniesienia

1. Przenoszenie subskrypcji platformy Azure do innego katalogu

1. Ponowne tworzenie zasobów w katalogu docelowym, takich jak przypisania ról, role niestandardowe i tożsamości zarządzane

    ![Diagram przenoszenia subskrypcji](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Podejmowanie decyzji o przeniesieniu subskrypcji do innego katalogu

Poniżej przedstawiono kilka powodów, dla których warto przenieść subskrypcję:

- Z powodu koncentracji lub przejęcia firmy chcesz zarządzać uzyskaną subskrypcją w podstawowym katalogu usługi Azure AD.
- Ktoś w organizacji utworzył subskrypcję i chcesz skonsolidować zarządzanie w określonym katalogu usługi Azure AD.
- Masz aplikacje, które zależą od określonego identyfikatora subskrypcji lub adresu URL i niełagodne jest modyfikowanie konfiguracji lub kodu aplikacji.
- Część twojej firmy została podzielona na osobną firmę i musisz przenieść niektóre zasoby do innego katalogu usługi Azure AD.
- Chcesz zarządzać niektórymi zasobami w innym katalogu usługi Azure AD na potrzeby izolacji zabezpieczeń.

### <a name="alternate-approaches"></a>Alternatywne podejścia

Przeniesienie subskrypcji wymaga przestoju w celu ukończenia procesu. W zależności od scenariusza można rozważyć następujące alternatywne podejścia:

- Utwórz ponownie zasoby i skopiuj dane do katalogu docelowego i subskrypcji.
- Zastosuj architekturę z wieloma katalogami i pozostaw subskrypcję w katalogu źródłowym. Użyj Azure Lighthouse, aby delegować zasoby, aby użytkownicy w katalogu docelowym mogą uzyskać dostęp do subskrypcji w katalogu źródłowym. Aby uzyskać więcej informacji, zobacz [Azure Lighthouse w scenariuszach dla przedsiębiorstw.](../lighthouse/concepts/enterprise.md)

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Zrozumienie wpływu przenoszenia subskrypcji

Niektóre zasoby platformy Azure są zależne od subskrypcji lub katalogu. W zależności od sytuacji w poniższej tabeli wymieniono znany wpływ przenoszenia subskrypcji. Wykonując kroki opisane w tym artykule, możesz ponownie utworzyć niektóre zasoby, które istniały przed przeniesieniem subskrypcji.

> [!IMPORTANT]
> W tej sekcji wymieniono znane usługi lub zasoby platformy Azure, które zależą od Twojej subskrypcji. Ze względu na to, że typy zasobów na platformie Azure stale ewoluują, mogą wystąpić dodatkowe zależności, które mogą spowodować przełomową zmianę w środowisku. 

| Usługa lub zasób | Wpływ | Odzyskania | Czy masz wpływ? | Co możesz zrobić |
| --------- | --------- | --------- | --------- | --------- |
| Przypisania ról | Tak | Tak | [Lista przypisań ról](#save-all-role-assignments) | Wszystkie przypisania ról są trwale usuwane. Należy mapować użytkowników, grupy i jednostki usługi na odpowiednie obiekty w katalogu docelowym. Należy ponownie utworzyć przypisania ról. |
| Role niestandardowe | Tak | Tak | [Wyświetlanie ról niestandardowych](#save-custom-roles) | Wszystkie role niestandardowe są trwale usuwane. Należy ponownie utworzyć role niestandardowe i wszelkie przypisania ról. |
| Tożsamości zarządzane przypisane przez system | Tak | Tak | [Lista tożsamości zarządzanych](#list-role-assignments-for-managed-identities) | Tożsamości zarządzane należy wyłączyć i ponownie włączyć. Należy ponownie utworzyć przypisania ról. |
| Tożsamości zarządzane przypisane przez użytkownika | Tak | Tak | [Lista tożsamości zarządzanych](#list-role-assignments-for-managed-identities) | Musisz usunąć, ponownie utworzyć i dołączyć tożsamości zarządzane do odpowiedniego zasobu. Należy ponownie utworzyć przypisania ról. |
| Azure Key Vault | Tak | Tak | [Lista Key Vault dostępu](#list-key-vaults) | Należy zaktualizować identyfikator dzierżawy skojarzony z magazynami kluczy. Należy usunąć i dodać nowe zasady dostępu. |
| Azure SQL baz danych z włączoną integracją uwierzytelniania usługi Azure AD | Tak | Nie | [Sprawdzanie Azure SQL baz danych przy użyciu uwierzytelniania usługi Azure AD](#list-azure-sql-databases-with-azure-ad-authentication) | Nie można przenieść bazy danych Azure SQL z włączonym uwierzytelnianiem usługi Azure AD do innego katalogu. Aby uzyskać więcej informacji, zobacz Use Azure Active Directory authentication (Używanie [Azure Active Directory uwierzytelniania).](../azure-sql/database/authentication-aad-overview.md) | 
| Azure Storage i Azure Data Lake Storage Gen2 | Tak | Tak |  | Należy ponownie utworzyć wszystkie adresy ACL. |
| Azure Data Lake Storage Gen1 | Tak | Tak |  | Należy ponownie utworzyć wszystkie adresy ACL. |
| Azure Files | Tak | Tak |  | Należy ponownie utworzyć wszystkie adresy ACL. |
| Azure File Sync | Tak | Tak |  | Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innego katalogu. Aby uzyskać więcej informacji, zobacz [często zadawane pytania](../storage/files/storage-files-faq.md#azure-file-sync) dotyczące Azure Files |
| Dyski zarządzane platformy Azure | Tak | Tak |  |  Jeśli używasz zestawów szyfrowania dysków do szyfrowania Dyski zarządzane kluczami zarządzanymi przez klienta, musisz wyłączyć i ponownie włączyć tożsamości przypisane przez system skojarzone z zestawami szyfrowania dysków. Należy ponownie utworzyć przypisania ról, np. ponownie udzielić wymaganych uprawnień do zestawów szyfrowania dysków w magazynach Kluczy. |
| Azure Kubernetes Service | Tak | Nie |  | Nie można przenieść klastra usługi AKS i skojarzonych z nim zasobów do innego katalogu. Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące Azure Kubernetes Service (AKS)](../aks/faq.md) |
| Azure Policy | Tak | Nie | Wszystkie Azure Policy, w tym definicje niestandardowe, przypisania, wyjątki i dane zgodności. | Należy [wyeksportować,](../governance/policy/how-to/export-resources.md)zaimportować i ponownie przypisać definicje. Następnie utwórz nowe przypisania zasad i wszelkie wymagane [wyjątki od zasad.](../governance/policy/concepts/exemption-structure.md) |
| Azure Active Directory Domain Services | Tak | Nie |  | Nie można przenieść Azure AD Domain Services zarządzanej do innego katalogu. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące usług [domenowych Azure Active Directory (AD)](../active-directory-domain-services/faqs.md) |
| Rejestracje aplikacji | Tak | Tak |  |  |

> [!WARNING]
> Jeśli używasz szyfrowania w spoczynku dla zasobu, takiego jak konto magazynu lub baza danych SQL, który jest zależny od magazynu kluczy, który nie znajduje się w tej samej subskrypcji, która jest transferowana, może to prowadzić do nieodwracalnego scenariusza.  W takiej sytuacji należy wykonać kroki, aby użyć innego magazynu kluczy lub tymczasowo wyłączyć klucze zarządzane przez klienta, aby uniknąć tego nieodwracalnego scenariusza.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tych kroków potrzebne są:

- [Powłoka Bash w programie Azure Cloud Shell](../cloud-shell/overview.md) lub interfejsie [wiersza polecenia platformy Azure](/cli/azure)
- Administrator konta subskrypcji, którą chcesz przenieść w katalogu źródłowym
- [Rola](built-in-roles.md#owner) właściciela w katalogu docelowym

## <a name="step-1-prepare-for-the-transfer"></a>Krok 1. Przygotowanie do transferu

### <a name="sign-in-to-source-directory"></a>Zaloguj się do katalogu źródłowego

1. Zaloguj się do platformy Azure jako administrator.

1. Pobierz listę subskrypcji za pomocą polecenia [az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Użyj [az account set,](/cli/azure/account#az_account_set) aby ustawić aktywną subskrypcję, którą chcesz przenieść.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Instalowanie Azure Resource Graph rozszerzenia

 Rozszerzenie interfejsu wiersza polecenia platformy Azure [Azure Resource Graph](../governance/resource-graph/index.yml), *resource-graph*, umożliwia wykonywanie zapytań o zasoby zarządzane przez usługę Azure Resource Manager. [](/cli/azure/graph) Użyjesz tego polecenia w kolejnych krokach.

1. Użyj [az extension list,](/cli/azure/extension#az_extension_list) aby sprawdzić, czy masz zainstalowane *rozszerzenie resource-graph.*

    ```azurecli
    az extension list
    ```

1. Jeśli nie, zainstaluj *rozszerzenie grafów* zasobów.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Zapisywanie wszystkich przypisań ról

1. Użyj [az role assignment list,](/cli/azure/role/assignment#az_role_assignment_list) aby wyświetlić listę wszystkich przypisań ról (w tym odziedziczonych przypisań ról).

    Aby ułatwić przeglądanie listy, możesz wyeksportować dane wyjściowe jako dane JSON, TSV lub tabelę. Aby uzyskać więcej informacji, zobacz List role assignments using Azure RBAC and Azure CLI (Wyświetlanie listy [przypisań ról przy użyciu kontroli RBAC platformy Azure i interfejsu wiersza polecenia platformy Azure).](role-assignments-list-cli.md)

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Zapisz listę przypisań ról.

    Podczas przenoszenia subskrypcji wszystkie przypisania ról  są trwale usuwane, dlatego ważne jest zapisanie kopii.

1. Przejrzyj listę przypisań ról. W katalogu docelowym mogą wystąpić przypisania ról, które nie będą potrzebne.

### <a name="save-custom-roles"></a>Zapisywanie ról niestandardowych

1. Użyj listy [az role definition list,](/cli/azure/role/definition#az_role_definition_list) aby wyświetlić listę ról niestandardowych. Aby uzyskać więcej informacji, zobacz Create or update Azure custom roles using Azure CLI (Tworzenie [lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure).](custom-roles-cli.md)

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Zapisz każdą rolę niestandardową, która będzie potrzebna w katalogu docelowym, jako oddzielny plik JSON.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Tworzyć kopie plików roli niestandardowej.

1. Zmodyfikuj każdą kopię, aby użyć następującego formatu.

    Później użyjesz tych plików do ponownego utworzenia ról niestandardowych w katalogu docelowym.

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

### <a name="determine-user-group-and-service-principal-mappings"></a>Określanie mapowań użytkowników, grup i nazw głównych usług

1. Na podstawie listy przypisań ról określ użytkowników, grupy i jednostki usługi, na które będziesz mapować w katalogu docelowym.

    Typ podmiotu zabezpieczeń można zidentyfikować, patrząc na właściwość `principalType` w każdym przypisaniu roli.

1. W razie potrzeby w katalogu docelowym utwórz wszystkich potrzebnych użytkowników, grupy lub jednostki usługi.

### <a name="list-role-assignments-for-managed-identities"></a>Lista przypisań ról dla tożsamości zarządzanych

Tożsamości zarządzane nie są aktualizowane po przeniesieniu subskrypcji do innego katalogu. W rezultacie wszystkie istniejące tożsamości zarządzane przypisane przez system lub przypisane przez użytkownika zostaną uszkodzone. Po przeniesieniu można ponownie włączyć wszystkie tożsamości zarządzane przypisane przez system. W przypadku tożsamości zarządzanych przypisanych przez użytkownika należy ponownie utworzyć i dołączyć je w katalogu docelowym.

1. Przejrzyj listę [usług platformy Azure, które obsługują tożsamości zarządzane,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) aby zanotować, gdzie możesz używać tożsamości zarządzanych.

1. Użyj [az ad sp list,](/cli/azure/ad/sp#az_ad_sp_list) aby wyświetlić listę tożsamości zarządzanych przypisanych przez system i przypisanych przez użytkownika.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Na liście tożsamości zarządzanych określ, które z nich są przypisane przez system, a które przypisane przez użytkownika. Aby określić typ, można użyć następujących kryteriów.

    | Kryteria | Typ tożsamości zarządzanej |
    | --- | --- |
    | `alternativeNames` właściwość obejmuje `isExplicit=False` | Przypisane przez system |
    | `alternativeNames` właściwość nie zawiera `isExplicit` | Przypisane przez system |
    | `alternativeNames` właściwość obejmuje `isExplicit=True` | Przypisane przez użytkownika |

    Możesz również użyć az [identity list,](/cli/azure/identity#az_identity_list) aby po prostu wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika. Aby uzyskać więcej informacji, zobacz Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy [użyciu interfejsu wiersza polecenia platformy Azure.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

    ```azurecli
    az identity list
    ```

1. Pobierz listę wartości `objectId` dla tożsamości zarządzanych.

1. Przeszukaj listę przypisań ról, aby sprawdzić, czy istnieją przypisania ról dla tożsamości zarządzanych.

### <a name="list-key-vaults"></a>Lista magazynów kluczy

Po utworzeniu magazynu kluczy jest on automatycznie powiązany z domyślnym identyfikatorem Azure Active Directory dzierżawy dla subskrypcji, w której został utworzony. Wszystkie wpisy zasad dostępu również zostają powiązane z tym identyfikatorem dzierżawy. Aby uzyskać więcej informacji, zobacz [Przenoszenie Azure Key Vault do innej subskrypcji.](../key-vault/general/move-subscription.md)

> [!WARNING]
> Jeśli używasz szyfrowania w spoczynku dla zasobu, takiego jak konto magazynu lub baza danych SQL, który jest zależny od magazynu kluczy, który nie znajduje się w tej samej subskrypcji, która jest transferowana, może to prowadzić do nieodwracalnego scenariusza.  W takiej sytuacji należy wykonać kroki, aby użyć innego magazynu kluczy lub tymczasowo wyłączyć klucze zarządzane przez klienta, aby uniknąć tego nieodwracalnego scenariusza.

- Jeśli masz magazyn kluczy, użyj az [keyvault show,](/cli/azure/keyvault#az_keyvault_show) aby wyświetlić listę zasad dostępu. Aby uzyskać więcej informacji, zobacz [Przypisywanie Key Vault dostępu.](../key-vault/general/assign-access-policy-cli.md)

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Lista baz Azure SQL z uwierzytelnianiem usługi Azure AD

- Użyj [poleceń az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) i az [graph](/cli/azure/graph) extension, aby sprawdzić, czy używasz baz danych Azure SQL z włączoną integracją uwierzytelniania usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania przy użyciu języka SQL i zarządzanie nim).](../azure-sql/database/authentication-aad-configure.md)

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Listy list ACL

1. Jeśli używasz programu Azure Data Lake Storage Gen1, lista list ACL, które są stosowane do dowolnego pliku, przy użyciu programu Azure Portal lub PowerShell.

1. Jeśli używasz programu Azure Data Lake Storage Gen2, lista list ACL, które są stosowane do dowolnego pliku, przy użyciu programu Azure Portal lub PowerShell.

1. Jeśli używasz usługi Azure Files, lista list ACL, które są stosowane do dowolnego pliku.

### <a name="list-other-known-resources"></a>Lista innych znanych zasobów

1. Użyj [az account show,](/cli/azure/account#az_account_show) aby uzyskać identyfikator subskrypcji.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Użyj rozszerzenia [az graph,](/cli/azure/graph) aby wyświetlić listę innych zasobów platformy Azure ze znanymi zależnościami katalogu usługi Azure AD.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Krok 2. Przenoszenie subskrypcji

W tym kroku przenosisz subskrypcję z katalogu źródłowego do katalogu docelowego. Kroki będą się różnić w zależności od tego, czy chcesz również przenieść własność rozliczeń.

> [!WARNING]
> Po przeniesieniu subskrypcji wszystkie przypisania ról w katalogu  źródłowym są trwale usuwane i nie można ich przywrócić. Po przeniesieniu subskrypcji nie można wrócić. Pamiętaj, aby przed wykonaniem tego kroku wykonać poprzednie kroki.

1. Określ, czy chcesz również przenieść własność rozliczeń na inne konto.

1. Przenieś subskrypcję do innego katalogu.

    - Jeśli chcesz zachować bieżącą własność rozliczeń, wykonaj kroki opisane w tece Kojarzenie lub dodawanie subskrypcji platformy [Azure do Azure Active Directory dzierżawy.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
    - Jeśli chcesz również przenieść własność rozliczeń, wykonaj kroki opisane w tece Przenoszenie własności rozliczeń [subskrypcji platformy Azure na inne konto.](../cost-management-billing/manage/billing-subscription-transfer.md) Aby przenieść subskrypcję do innego katalogu, należy zaznacz pole wyboru **Subskrypcja dzierżawy usługi Azure AD.**

1. Po zakończeniu przenoszenia subskrypcji wróć do tego artykułu, aby ponownie utworzyć zasoby w katalogu docelowym.

## <a name="step-3-re-create-resources"></a>Krok 3. Ponowne tworzenie zasobów

### <a name="sign-in-to-target-directory"></a>Zaloguj się do katalogu docelowego

1. W katalogu docelowym zaloguj się jako użytkownik, który zaakceptował żądanie przeniesienia.

    Tylko użytkownik nowego konta, który zaakceptował żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami.

1. Pobierz listę subskrypcji za pomocą polecenia [az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Użyj [az account set,](/cli/azure/account#az_account_set) aby ustawić aktywną subskrypcję, której chcesz użyć.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Tworzenie ról niestandardowych
        
- Użyj [az role definition create,](/cli/azure/role/definition#az_role_definition_create) aby utworzyć każdą rolę niestandardową na podstawie utworzonych wcześniej plików. Aby uzyskać więcej informacji, zobacz [Create or update Azure custom roles using Azure CLI (Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure).](custom-roles-cli.md)

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Przypisywanie ról

- Użyj [az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby przypisać role użytkownikom, grupom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](role-assignments-cli.md)

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Aktualizowanie tożsamości zarządzanych przypisanych przez system

1. Wyłączanie i ponowne włączanie tożsamości zarządzanych przypisanych przez system.

    | Usługa platformy Azure | Więcej informacji | 
    | --- | --- |
    | Maszyny wirtualne | [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Zestawy skalowania maszyn wirtualnych | [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Inne usługi | [Usługi, które obsługują tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Użyj [az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby przypisać role do tożsamości zarządzanych przypisanych przez system. Aby uzyskać więcej informacji, zobacz [Assign a managed identity access to a resource using Azure CLI (Przypisywanie dostępu tożsamości zarządzanej do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure).](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Aktualizowanie tożsamości zarządzanych przypisanych przez użytkownika

1. Usuwanie, ponowne tworzenie i dołączanie tożsamości zarządzanych przypisanych przez użytkownika.

    | Usługa platformy Azure | Więcej informacji | 
    | --- | --- |
    | Maszyny wirtualne | [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Zestawy skalowania maszyn wirtualnych | [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Inne usługi | [Usługi, które obsługują tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Tworzenie, tworzenie, tworzenie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Użyj [az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby przypisać role do tożsamości zarządzanych przypisanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [Assign a managed identity access to a resource using Azure CLI (Przypisywanie dostępu tożsamości zarządzanej do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure).](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Aktualizowanie magazynów kluczy

W tej sekcji opisano podstawowe kroki aktualizowania magazynów kluczy. Aby uzyskać więcej informacji, [zobacz Przenoszenie Azure Key Vault do innej subskrypcji.](../key-vault/general/move-subscription.md)

1. Zaktualizuj identyfikator dzierżawy skojarzony ze wszystkimi istniejącymi magazynami kluczy w subskrypcji do katalogu docelowego.

1. usunąć wszystkie istniejące wpisy zasad dostępu,

1. Dodaj nowe wpisy zasad dostępu skojarzone z katalogiem docelowym.

### <a name="update-acls"></a>Aktualizowanie acl ACL

1. Jeśli używasz usługi Azure Data Lake Storage Gen1, przypisz odpowiednie adresy ACL. Aby uzyskać więcej informacji, zobacz [Securing data stored in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Jeśli używasz usługi Azure Data Lake Storage Gen2, przypisz odpowiednie adresy ACL. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

1. Jeśli używasz usługi Azure Files, przypisz odpowiednie adresy ACL.

### <a name="review-other-security-methods"></a>Przegląd innych metod zabezpieczeń

Mimo że przypisania ról są usuwane podczas przenoszenia, użytkownicy na oryginalnym koncie właściciela mogą nadal mieć dostęp do subskrypcji za pośrednictwem innych metod zabezpieczeń, takich jak:

- Klucze dostępu dla usług, takich jak Storage.
- [Certyfikaty zarządzania,](../cloud-services/cloud-services-certs-create.md) które przyznają administratorowi użytkownika dostęp do zasobów subskrypcji.
- Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli chcesz usunąć dostęp użytkowników w katalogu źródłowym, aby nie mieli dostępu w katalogu docelowym, rozważ obrócenie poświadczeń. Dopóki poświadczenia nie zostaną zaktualizowane, użytkownicy będą nadal mieć dostęp po przeniesieniu.

1. Rotacja kluczy dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu.](../storage/common/storage-account-keys-manage.md)

1. Jeśli używasz kluczy dostępu dla innych usług, takich jak Azure SQL Database lub Azure Service Bus Messaging, obróć klucze dostępu.

1. W przypadku zasobów, które używają wpisów tajnych, otwórz ustawienia zasobu i zaktualizuj wpis tajny.

1. W przypadku zasobów, które używają certyfikatów, zaktualizuj certyfikat.

## <a name="next-steps"></a>Następne kroki

- [Przeniesienie własności rozliczeń subskrypcji platformy Azure na inne konto](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Przenoszenie subskrypcji platformy Azure między subskrybentami i dostawcami usług w chmurze](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie subskrypcji](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw](../lighthouse/concepts/enterprise.md)
