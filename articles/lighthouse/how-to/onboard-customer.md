---
title: Dołączanie klienta do usługi Azure Lighthouse
description: Dowiedz się, jak dołączyć klienta do usługi Azure Lighthouse, umożliwiając dostęp do zasobów i zarządzanie nimi za pośrednictwem własnej dzierżawy przy użyciu funkcji zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 09/24/2020
ms.topic: how-to
ms.openlocfilehash: 2e404c471990575460ccda3a26e7cdcb3ec1cf1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396675"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Dołączanie klienta do usługi Azure Lighthouse

W tym artykule wyjaśniono, jak usługodawca może dołączyć klienta do usługi Azure Lighthouse. Po wykonaniu tej czynności dostęp do delegowanych zasobów (subskrypcji i/lub grup zasobów) klienta jest możliwy i zarządzany za pośrednictwem własnej dzierżawy usługi Azure Active Directory (Azure AD) przy użyciu funkcji [zarządzania zasobami delegowanych przez platformę Azure](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z tego samego procesu, aby skonfigurować usługę Azure Lighthouse i skonsolidować swoje środowisko zarządzania.

Proces dołączania można powtórzyć dla wielu klientów. Gdy użytkownik z odpowiednimi uprawnieniami loguje się do dzierżawy zarządzającej, ten użytkownik może być autoryzowany w zakresach dzierżawy klienta w celu wykonywania operacji zarządzania, bez konieczności logowania się do każdej indywidualnej dzierżawy klienta.

Aby śledzić wpływ na zaangażowanie klientów i odbierać rozpoznawanie, skojarz swój identyfikator Microsoft Partner Network (MPN) z co najmniej jednym kontem użytkownika, które ma dostęp do każdej z dołączanych subskrypcji. Musisz wykonać to skojarzenie w dzierżawie dostawcy usług. Zalecamy utworzenie konta nazwy głównej usługi w dzierżawie skojarzonej z IDENTYFIKATORem MPN, a następnie użycie tej jednostki usługi przy każdym dołączeniu klienta. Aby uzyskać więcej informacji, zobacz [łączenie identyfikatora partnera, aby umożliwić partnerowi uzyskiwanie środków na zasoby delegowane.

> [!NOTE]
> Klienci mogą również zostać dołączeni do usługi Azure Lighthouse, gdy kupują ofertę usługi zarządzanej (publiczną lub prywatną) [publikowaną w witrynie Azure Marketplace](publish-managed-services-offers.md). Możesz również użyć procesu dołączania opisanego tutaj razem z ofertami opublikowanymi w witrynie Azure Marketplace.

Proces dołączania wymaga wykonania akcji z poziomu dzierżawy dostawcy usług i dzierżawy klienta. Wszystkie te kroki opisano w tym artykule.

## <a name="gather-tenant-and-subscription-details"></a>Zbierz szczegóły dzierżawy i subskrypcji

Aby dołączyć dzierżawcę klienta, musi on mieć aktywną subskrypcję platformy Azure. Musisz znać następujące informacje:

- Identyfikator dzierżawy dzierżawy dostawcy usług (gdzie będziesz zarządzać zasobami klienta)
- Identyfikator dzierżawy dzierżawy klienta (który będzie mieć zasoby zarządzane przez dostawcę usługi)
- Identyfikatory subskrypcji dla każdej określonej subskrypcji w dzierżawie klienta, która będzie zarządzana przez dostawcę usług (lub zawierający grupy zasobów, które będą zarządzane przez dostawcę usług).

Jeśli nie masz już tych wartości identyfikatorów, możesz je pobrać w jeden z następujących sposobów. Upewnij się, że te dokładne wartości są używane w danym wdrożeniu.

### <a name="azure-portal"></a>Azure Portal

Identyfikator dzierżawy może być widoczny przez umieszczenie kursora nad nazwą konta w prawym górnym rogu Azure Portal lub przez wybranie pozycji **Przełącz katalog**. Aby wybrać i skopiować identyfikator dzierżawy, wyszukaj frazę "Azure Active Directory" w portalu, a następnie wybierz pozycję **Właściwości** i skopiuj wartość podaną w polu **Identyfikator katalogu** . Aby znaleźć identyfikator subskrypcji w dzierżawie klienta, wyszukaj frazę "subskrypcje", a następnie wybierz odpowiedni identyfikator subskrypcji.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Podczas dołączania subskrypcji (lub jednej lub większej liczby grup zasobów w ramach subskrypcji) przy użyciu procesu opisanego w tym miejscu dostawca zasobów **Microsoft. ManagedServices** zostanie zarejestrowany dla tej subskrypcji.

## <a name="define-roles-and-permissions"></a>Definiowanie ról i uprawnień

Jako dostawca usług możesz chcieć wykonać wiele zadań dla pojedynczego klienta, wymagając innego dostępu dla różnych zakresów. Można zdefiniować dowolną liczbę autoryzacji w celu przypisania do użytkowników w dzierżawie odpowiednich [ról wbudowanej kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/built-in-roles.md) .

Aby ułatwić zarządzanie, zalecamy korzystanie z grup użytkowników usługi Azure AD dla każdej roli. Zapewnia to elastyczność dodawania lub usuwania poszczególnych użytkowników do grupy mającej dostęp, dzięki czemu nie trzeba powtarzać procesu dołączania w celu wprowadzenia zmian przez użytkownika. Role można przypisywać do jednostki usługi, co może być przydatne w scenariuszach automatyzacji.

Podczas definiowania autoryzacji należy przestrzegać zasad najniższych uprawnień, aby użytkownicy mieli tylko uprawnienia do wykonywania swoich zadań. Aby uzyskać wskazówki i informacje o obsługiwanych rolach, zobacz [dzierżawy, użytkowników i role w scenariuszach usługi Azure Lighthouse](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Aby można było dodać uprawnienia dla grupy usługi Azure AD, **Typ grupy** musi być ustawiony na **zabezpieczenia**. Ta opcja jest wybierana podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Aby zdefiniować autoryzacje, musisz znać wartości identyfikatora dla każdego użytkownika, grupy użytkowników lub nazwy głównej usługi w dzierżawie dostawcy usług, do której chcesz udzielić dostępu. Wymagany jest również identyfikator definicji roli dla każdej wbudowanej roli, która ma zostać przypisana. Jeśli ich nie masz, możesz je pobrać, uruchamiając poniższe polecenia z poziomu dzierżawy dostawcy usług.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> Zalecamy przypisanie [roli usuwania przypisania rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) podczas dołączania klienta, dzięki czemu użytkownicy w dzierżawie mogą później w razie potrzeby [usunąć dostęp do delegowania](remove-delegation.md) . Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager

Aby dołączyć klienta, musisz utworzyć szablon [Azure Resource Manager](../../azure-resource-manager/index.yml) dla oferty z następującymi informacjami. Wartości **mspOfferName** i **mspOfferDescription** będą widoczne dla klienta na [stronie dostawcy usług](view-manage-service-providers.md) Azure Portal.

|Pole  |Definicja  |
|---------|---------|
|**mspOfferName**     |Nazwa opisująca tę definicję. Ta wartość jest wyświetlana klientowi jako tytuł oferty i musi być unikatową wartością.        |
|**mspOfferDescription**     |Krótki opis oferty (na przykład "Oferta zarządzania maszyną wirtualną firmy Contoso").      |
|**managedByTenantId**     |Identyfikator dzierżawy.          |
|**autoryzacji**     |Wartości **principalId** dla użytkowników/grup/nazw SPN z dzierżawy, z których każda jest **principalIdDisplayName** , aby pomóc klientowi zrozumieć cel autoryzacji i zamapować na wbudowaną wartość **zduplikowanych** w celu określenia poziomu dostępu.      |

Proces dołączania wymaga szablonu Azure Resource Manager (dostępnego w naszym [repozytorium przykładów](https://github.com/Azure/Azure-Lighthouse-samples/)) i odpowiedniego pliku parametrów modyfikowanego w celu dopasowania do konfiguracji i zdefiniowania autoryzacji.

> [!IMPORTANT]
> Opisany tutaj proces wymaga oddzielnego wdrożenia dla każdej subskrypcji, która jest dołączana, nawet w przypadku dołączania subskrypcji w tej samej dzierżawie klienta. W przypadku dołączania wielu grup zasobów w ramach różnych subskrypcji w tej samej dzierżawie klienta wymagane są również oddzielne wdrożenia. Istnieje jednak możliwość dołączania wielu grup zasobów w ramach jednej subskrypcji w jednym wdrożeniu.
>
> Oddzielne wdrożenia są również wymagane dla wielu ofert, które są stosowane do tej samej subskrypcji (lub grup zasobów w ramach subskrypcji). Każda zastosowana oferta musi używać innego **mspOfferName**.

Wybrany szablon będzie zależeć od tego, czy dołączysz całą subskrypcję, grupę zasobów lub wiele grup zasobów w ramach subskrypcji. Udostępniamy również szablon, który może być używany dla klientów, którzy kupili ofertę usługi zarządzanej opublikowaną w portalu Azure Marketplace, jeśli wolisz dołączyć swoje subskrypcje w ten sposób.

|Aby dołączyć to  |Użyj tego szablonu Azure Resource Manager  |I zmodyfikuj ten plik parametrów |
|---------|---------|---------|
|Subskrypcja   |[delegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupa zasobów   |[rgDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Wiele grup zasobów w ramach subskrypcji   |[multipleRgDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subskrypcja (w przypadku korzystania z oferty opublikowanej w portalu Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Chociaż nie można dołączyć całej grupy zarządzania w jednym wdrożeniu, można [wdrożyć zasady na poziomie grupy zarządzania](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). Zasady te sprawdzają, czy każda subskrypcja w grupie zarządzania została oddelegowana do określonej dzierżawy zarządzającej, a jeśli nie, program utworzy przypisanie na podstawie podanych wartości.

Poniższy przykład przedstawia zmodyfikowaną **delegatedResourceManagement.parameters.jsw** pliku, którego można użyć do dołączenia subskrypcji. Pliki parametrów grupy zasobów (znajdujące się w folderze [RG-delegowani-Resource-Management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) ) są podobne, ale zawierają także parametr **rgName** w celu zidentyfikowania określonych grup zasobów, które mają zostać dołączone.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Ostatnia autoryzacja w powyższym przykładzie dodaje **principalId** z rolą administratora dostępu użytkownika (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Podczas przypisywania tej roli należy uwzględnić Właściwość **delegatedRoleDefinitionIds** i jedną lub więcej ról wbudowanych. Użytkownik utworzony w ramach tej autoryzacji będzie mógł przypisać te wbudowane role do [zarządzanych tożsamości](../../active-directory/managed-identities-azure-resources/overview.md) w dzierżawie klienta, co jest wymagane w celu [wdrożenia zasad, które można skorygować](deploy-policy-remediation.md).  Użytkownik może również tworzyć zdarzenia pomocy technicznej.  Dla tego użytkownika nie są stosowane żadne inne uprawnienia zwykle skojarzone z rolą administratora dostępu użytkownika.

## <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów Azure Resource Manager

Po zaktualizowaniu pliku parametrów użytkownik w dzierżawie klienta musi wdrożyć szablon Azure Resource Manager w ramach swojej dzierżawy. Dla każdej subskrypcji, która ma zostać dołączona (lub dla każdej subskrypcji zawierającej grupy zasobów, które mają zostać dołączone) wymagane jest oddzielne wdrożenie.

> [!IMPORTANT]
> To wdrożenie musi zostać wykonane przez konto niebędące Gośćmi w dzierżawie klienta, które ma [wbudowaną rolę właściciela](../../role-based-access-control/built-in-roles.md#owner) subskrypcji (lub która zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie może wybrać subskrypcję w Azure Portal, otworzyć funkcję **Kontrola dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą właściciela](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Jeśli subskrypcja została utworzona za pomocą [programu Cloud Solution Provider (CSP)](../concepts/cloud-solution-provider.md), każdy użytkownik, który ma rolę [Agent administracyjny](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) w dzierżawie dostawcy usług, może wykonać wdrożenie.

Wdrożenie może odbywać się w Azure Portal, przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej.

### <a name="azure-portal"></a>Azure Portal

1. W naszym [repozytorium GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)wybierz przycisk **Wdróż na platformie Azure** widoczny obok szablonu, którego chcesz użyć. Szablon zostanie otwarty w witrynie Azure Portal.
1. Wprowadź wartości w **pozycji Nazwa oferty msp**, **opis oferty msp**, **zarządzane przez identyfikator dzierżawy**i **autoryzacje**. Jeśli wolisz, możesz wybrać opcję **Edytuj parametry** , aby wprowadzić wartości dla `mspOfferName` ,, `mspOfferDescription` `managedbyTenantId` i `authorizations` bezpośrednio w pliku parametrów. Należy pamiętać, aby zaktualizować te wartości zamiast używać wartości domyślnych z szablonu.
1. Wybierz pozycję **Przejrzyj i Utwórz**, a następnie wybierz pozycję **Utwórz**.

Po kilku minutach powinna zostać wyświetlona powiadomienie o zakończeniu wdrożenia.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Potwierdzenie pomyślnego dołączenia

Po pomyślnym dołączeniu subskrypcji klienta do usługi Azure Lighthouse użytkownicy w dzierżawie dostawcy usług będą mogli zobaczyć subskrypcję i jej zasoby (Jeśli udzielono im dostępu za pomocą powyższego procesu, indywidualnie lub jako członek grupy usługi Azure AD z odpowiednimi uprawnieniami). Aby to potwierdzić, upewnij się, że subskrypcja jest wyświetlana w jeden z następujących sposobów.  

### <a name="azure-portal"></a>Azure Portal

W dzierżawie dostawcy usług:

1. Przejdź do [strony moi klienci](view-manage-customers.md).
2. Wybierz pozycję **Klienci**.
3. Upewnij się, że można zobaczyć subskrypcje o nazwie oferty podanej w szablonie Menedżer zasobów.

> [!IMPORTANT]
> Aby można było wyświetlić delegowaną subskrypcję w [swoich klientach](view-manage-customers.md), użytkownicy w dzierżawie dostawcy usługi muszą mieć przydaną rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika) podczas dołączania subskrypcji.

W dzierżawie klienta:

1. Przejdź do [strony dostawcy usług](view-manage-service-providers.md).
2. Wybierz pozycję **Oferty dostawców usług**.
3. Upewnij się, że można zobaczyć subskrypcje o nazwie oferty podanej w szablonie Menedżer zasobów.

> [!NOTE]
> Po zakończeniu wdrożenia może upłynąć kilka minut, zanim aktualizacje zostaną odzwierciedlone w Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
- Dowiedz się, jak [usunąć dostęp do delegowania](remove-delegation.md) , który został wcześniej dołączony.
